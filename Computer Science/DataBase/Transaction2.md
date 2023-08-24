# Transaction2

### 트랜잭션의 추상화

    @Slf4j
    @RequiredArgsConstructor
    public class MemberServiceV2 {
      private final DataSource dataSource;
      private final MemberRepositoryV2 memberRepository;
      public void accountTransfer(String fromId, String toId, int money) throws SQLException {
      Connection con = dataSource.getConnection();
      try {
          con.setAutoCommit(false); //트랜잭션 시작
          //비즈니스 로직
          bizLogic(con, fromId, toId, money);
          con.commit(); //성공시 커밋
      } catch (Exception e) {
          con.rollback(); //실패시 롤백 
          throw new IllegalStateException(e);
      } finally {
          release(con);
      }
    }
    
이전에 배웠던 Transaction 코드는 Jdbc에 의존한 방법이다. 

<img width="581" alt="스크린샷 2023-08-06 오후 11 02 39" src="https://github.com/YongNyeo/TIL/assets/109174778/8b02bc7a-5416-4f0f-a738-9c90362a8146">
<img width="579" alt="스크린샷 2023-08-06 오후 11 02 50" src="https://github.com/YongNyeo/TIL/assets/109174778/b33c2df0-15ff-4f37-8972-9f72318d7c4f">

Service 계층의 코드는 순수 자바 코드로 작성하는것이 바람직하다고한다. 

또한  Service계층을 변경에 용이하고 try-catch문의 중복코드 없도록 코드를 바꿀순 없을까?

---

## 트랜잭션 동기화 매니저

<img width="564" alt="스크린샷 2023-08-06 오후 11 40 18" src="https://github.com/YongNyeo/TIL/assets/109174778/545e8e63-2fbd-4b22-b219-8649fd253f46">

각 데이터 접근 방식마다 트랜잭션의 사용 방법이 다르다. 이 점을 쉽게 해결한 방법은 트랜잭션 동기화 매니저다.

### 과정1) 트랜잭션의 시작

<img width="567" alt="스크린샷 2023-08-06 오후 11 41 02" src="https://github.com/YongNyeo/TIL/assets/109174778/3dba1f05-c5b3-40d7-ae49-2fa12ae1ea20">

> 트랜잭션 매니저는 DI로 주입을 받는다.

1. 서비스계층에서 트랜잭션 매니저로 getConnection( )실행한다.   //return TransactionStatus 
2. 트랜잭션 내부의 데이터소스를 이용해서 커넥션을 생성한다. (데이터소스는 미리 생성된것. DB에 관련된 정보)
3. con.setAutoCommit()을 통해 커밋 수동모드로 바꾸고 트랜잭션을 동작 시작한다.
4. 커넥션을 트랜잭션 동기화 매니저에 보관한다.
5. 트랜잭션 동기화 매니저는 쓰레드 로컬에 커넥션을 보관한다. 따라서 멀티 쓰레드 환경에 안전하게커넥션을 보관할 수 있다

### 과정2) 트랜잭션 로직 수행

![스크린샷 2023-08-07 오전 12 31 13](https://github.com/YongNyeo/TIL/assets/109174778/52fa5290-8acb-4cb1-9e7d-a83644c44e42)

6. 서비스는 비즈니스 로직을 실행하면서 리포지토리의 메서드들을 호출한다. 
7. 리포지토리 메서드들은 트랜잭션이 시작된 커넥션이 필요하다. 리포지토리는 DataSourceUtils.getConnection() 을 사용해서 트랜잭션 동기화 매니저에 보관된 커넥션을 꺼내서
사용한다. 이 과정을 통해서 자연스럽게 같은 커넥션을 사용하고, 트랜잭션도 유지된다.
8. 획득한 커넥션을 사용해서 SQL을 데이터베이스에 전달해서 실행한다

### 과정3) 트랜잭션 commit/rollback

![스크린샷 2023-08-07 오전 12 34 12](https://github.com/YongNyeo/TIL/assets/109174778/2c4a769f-fe26-4ff8-bddb-a983474d18bc)

9. 비즈니스 로직이 끝나고 트랜잭션을 종료한다. 트랜잭션은 커밋하거나 롤백하면 종료된다.
10. 트랜잭션을 종료하려면 동기화된 커넥션이 필요하다. 트랜잭션 동기화 매니저를 통해 동기화된 커넥션을 획득한다.
11. 획득한 커넥션을 통해 데이터베이스에 트랜잭션을 커밋하거나 롤백한다.
12. 전체 리소스를 정리한다.
    - 트랜잭션 동기화 매니저를 정리한다. 쓰레드 로컬은 사용후 꼭 정리해야 한다.
    - con.setAutoCommit(true) 로 되돌린다. 커넥션 풀을 고려해야 한다.
    - con.close() 를 호출해셔 커넥션을 종료한다. 커넥션 풀을 사용하는 경우 con.close() 를 호출하면 커넥션 풀에 반환된다

다음과 같은 과정을 통해 추상화된 트랜잭션을 사용 가능하다.

JDBC 에서 JPA로 데이터접근 방법을 바꿀때, 의존성과 datasource 정보만 몇개 바꿔주면 쉽게 갈아낄수 있다.

---

위의 과정을 코드로 바꾸면 다음과 같다. 


    public class MemberServiceV3_1 {
        private final PlatformTransactionManager transactionManager;
        private final MemberRepositoryV3 memberRepository;
        public void accountTransfer(String fromId, String toId, int money) throws SQLException {//트랜잭션 시작
            TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
            try {
                //비즈니스 로직
                bizLogic(fromId, toId, money);
                transactionManager.commit(status); //성공시 커밋
            } catch (Exception e) {
                transactionManager.rollback(status); //실패시 롤백
                throw new IllegalStateException(e);
            }
        }
        private void bizLogic(String fromId, String toId, int money) throws SQLException {
            Member fromMember = memberRepository.findById(fromId);
            Member toMember = memberRepository.findById(toId);
            memberRepository.update(fromId, fromMember.getMoney() - money);
            validation(toMember);
            memberRepository.update(toId, toMember.getMoney() + money);
        }
        private void validation(Member toMember) {
            if (toMember.getMemberId().equals("ex")) {
                throw new IllegalStateException("이체중 예외 발생");
            }
        }
    }

여기서 순수 비즈니스로직은 bizLogic()뿐이다. 나머지 부분은 service단에 있기에 적합하지 않다. 

이것을 해결한것이 스프링 AOP다. AOP 는 따로 더 공부를 할 예정인데, 가볍게 정리하자면 Aspect Oriented Programming 의 약자로 

<img width="909" alt="스크린샷 2023-08-24 오후 5 28 34" src="https://github.com/YongNyeo/TIL/assets/109174778/73f08d72-80f2-4fe5-8578-4ac597e44fc7">

핵심기능과 부가기능을 나누는것이다. 

여기서 트랜잭션을 이용하기 위해 사전 준비요소, 커밋부분을 모두 AOP 방식으로 구현한것이 __@Transaction__ 이다.

---

<img width="609" alt="스크린샷 2023-08-24 오후 5 32 35" src="https://github.com/YongNyeo/TIL/assets/109174778/e5f17366-befa-4f84-a6d6-261624712c17">

@Transaction이 메서드(클래스)에 붙어있다면, 트랜잭션 커넥션을 얻어오는 과정까지 모두 스프링이 진행시켜준다.

이후 핵심 비즈니스 로직부분(Service)을 실행시킨다. 

그렇게 데이터에 접근해서 로직을 실행한 후, Service에서 나와 트랜잭션 commit or rollback이 실행된다.

개발자가 비즈니스 로직만 구현하면 되도록 스프링이 다 정리해놓은 것이다.

---

### 데이터베이스 관련 리소스 등록 

datasource랑 platformTransactionManager는 어떻게 주입하는지 궁금했다.

DB에 대한 직접적인 정보가 필요한 __datasource__ 는 application-properties에 저장하는 정보들을 가지고 빈으로 등록한다.

만약 개발자가 등록하지 않으면 스프링에서 자동으로 컨테이너에 빈으로 등록한다.

<img width="248" alt="스크린샷 2023-08-24 오후 5 38 50" src="https://github.com/YongNyeo/TIL/assets/109174778/5a2eb1ad-8043-40a6-b033-adf19c518f24">

__platformTransactionManager__ 는 등록된 라이브러리 정보를 기준으로 빈에 등록된다.

<img width="349" alt="스크린샷 2023-08-24 오후 5 40 19" src="https://github.com/YongNyeo/TIL/assets/109174778/47640c6d-eca0-4c25-ba00-28768520de8d">

---
📘 Reference
- https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-1/dashboard
- https://shlee0882.tistory.com/206
