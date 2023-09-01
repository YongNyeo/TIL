# 영속성 컨텍스트(Persistence Context)란?

되게 생소한 말이라서 , 처음에 의미 이해하기가 어려웠다. 

영속성 컨텍스트의 사용 목적에는 웹 어플리케이션과 DB의 중간다리로, 기능상 여러 이점이 있다.

<img width="658" alt="스크린샷 2023-09-01 오전 10 45 30" src="https://github.com/YongNyeo/TIL/assets/109174778/f09727d0-b8ba-495f-b0f1-2573440616d1">


---

## 💡Entity Manager 

우선 애플리케이션에 있는 엔티티 매니저부터 알아야햔다.

엔티티 매니저는 이전에 배운 트랜잭션이 일어날때 작업을 도와준다.

아직도 감이 잘 안와서, 엔티티 매니저 코드를 파고 들어간다.

<img width="395" alt="스크린샷 2023-09-01 오전 10 50 07" src="https://github.com/YongNyeo/TIL/assets/109174778/c19fd9e7-bb42-4e23-b683-f6baff9b9dbc">

엔티티 매니저 인터페이스다. persist,remove,find 등의 메서드가 있다. 

더 위로 올라가보면, __EntityManagerFactory__ 에서 EntityManager를 관리하고있었다.

<img width="714" alt="스크린샷 2023-09-01 오전 10 48 03" src="https://github.com/YongNyeo/TIL/assets/109174778/4ec249ea-33c8-4a1b-9362-011cca9a367a">

그렇다면 이 __EntityManagerFactory__ 는 어디서 관리되는가?

---

<img width="786" alt="스크린샷 2023-09-01 오전 10 52 28" src="https://github.com/YongNyeo/TIL/assets/109174778/e4d3bf0b-a064-4868-b1a3-425c5056cc0e">

드디어 우리에게 익숙한 것이 나왔다.

__AbstractPlatformTransactionManager__ 은 여러 데이터접근 방식이 있고

트랜잭션을 이용하려면 특정 트랜잭션 매니저가 필요한데, 추상화된 인터페이스를 DI를 통해 주입받는것이었다.

_결과적으로 TransactionManager가 EntityManager를 포함하고 있다고 생각하면된다._

그래서 하나의 트랜잭션 작업안에 EntityManager를 통한 여러번의 작업이 일어날 수 있다.

이제 영속성 컨텍스트를 '왜' 쓰고, '어떻게' 쓰는지 알아보자.

---

### 💡Entity의 생명주기

비영속 (new/transient) 
- 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태

영속 (managed) 
- 영속성 컨텍스트에 관리되는 상태 (em.persist() 실행 시 )

준영속 (detached) 
- 영속성 컨텍스트에 저장되었다가 분리된 상태 (em.detach() 실행 시 )
  
삭제 (removed) 
- 삭제된 상태 (em.remove() 실행 시 )

<img width="378" alt="스크린샷 2023-09-01 오전 11 10 07" src="https://github.com/YongNyeo/TIL/assets/109174778/2bc3c32e-4d76-49d4-9bc3-cda61b852f55">

---

### ❓ 그래서 왜 사용하는건데?

5가지 이점이 있다.

#### 💡1차 캐시

애플리케이션과 DB의 중간에 있는 역할이기 때문에 , 캐시 역할을 해줄 수 있다. 

<img width="588" alt="스크린샷 2023-09-01 오전 11 17 08" src="https://github.com/YongNyeo/TIL/assets/109174778/a6bf2dcd-85fe-466a-b5bf-0dedc6154a73">

만약 영속성 컨텍스트에 있는 데이터라면 DB까지 가지 않더라도 데이터를 찾아올 수 있다.

<img width="877" alt="스크린샷 2023-09-01 오전 11 26 53" src="https://github.com/YongNyeo/TIL/assets/109174778/3a2ea5cb-9ecd-432b-b5f9-bb3126e58b58">

만약 영속성 컨텍스트에 없다면 DB에서 불러온 후, 영속성 컨텍스트에 저장해서 캐시로 사용할 수 있다. 

---

#### 💡동일성(identity) 보장

    Member a = em.find(Member.class, "member1");
    Member b = em.find(Member.class, "member1");
    System.out.println(a == b); //동일성 true

영속성 컨텍스트에 있는 객체는 조회시 값만 복사되어서 오는게 아니라, 실제 그 객체를 가져오기 때문에 동일성이 보장된다.

---

#### 💡쓰기 지연 (transactional write-behind)

트랜잭션을 생각해보면 한번의 트랜잭션작업 안에서 여러 작은 작업내용이 저장되고, commit/rollback으로 한번에 처리한다. 

그때 persist( sql insert문) , find (sql select문) 등의 작업은 어디에 저장될까?

<img width="595" alt="스크린샷 2023-09-01 오후 1 44 19" src="https://github.com/YongNyeo/TIL/assets/109174778/d2d50a30-1cec-47bd-aa15-3f4b1859758e">

이때 해당 작업 명령문이 저장되는곳이 쓰기 지연 SQL 저장소다.

커밋(플러시)을 하게 되면 해당 쓰기지연 저장소에 있는 명령문들이 DB로 보내진다.

---

#### 💡변경 감지(Dirty Checking)

    
    Member memberA = em.find(Member.class, "memberA");  // 영속 엔티티 조회
    memberA.setUsername("hi"); // 영속 엔티티 데이터 수정
    //em.update(member) 이런 코드가 없어도 알아서 변경을 감지한다.
    transaction.commit(); // 이때 Update 쿼리가 알아서 나감


<img width="815" alt="스크린샷 2023-09-01 오후 1 56 25" src="https://github.com/YongNyeo/TIL/assets/109174778/19988136-e1be-43bf-b04c-c60df2ced9b8">

DB에서 영속성 컨텍스트로 가져온 객체의 데이터를 변경한 뒤, 별도의 동작을 진행할 필요가 없다.

DB에서 데이터를 가져올 때, 가져온 시점에서의 객체를 저장해두고, 이와 비교해서 변경된 것이 있는지 감지한다.

그렇게 JPA는 객체 데이터가 변경되었다는 것을 감지하면, transaction 커밋 시 Update 쿼리를 실행하게끔 설계되어 있다.





---

### 💡지연 로딩(Lazy Loading) feat.즉시로딩

    class Member{
        private int age;
        private String name;

        @ManyToOne(fetch = FetchType.Lazy)  // 지연로딩을 하겠다!
        private Team team;
    }

다음과 같이 멤버에 Team이라는 필드가 있다. 이런경우에 team을 항상 참조해야하는데, 참조를 하게 되면 또 Team의 필드를 읽게된다.

만약 Team객체를 자주 안봐도 되는 상황이고 어쩌다 한번만 읽고 싶을 때 사용가능한 기능이 지연 로딩 기능이다. 

<img width="901" alt="스크린샷 2023-09-01 오후 2 39 36" src="https://github.com/YongNyeo/TIL/assets/109174778/504779fc-5c5e-444e-86a2-ea778c32044c">

team 값에는 프록시 객체를 넣어선 빈 값으로 초기화해주고, 사용하는 시점에 값을 넣어주는것이다.

하지만, team 필드를 자주써야하는 상황이라면 굳이 사용하는 시점에 넣어줄 필요가 없다. 처음부터 team 필드를 초기화해주고 사용하는 것이다.

지연로딩과 즉시로딩의 각 장단점이 있고, 활용해야 할 상황이 있는데 따로 글을 작성해서 정리할 예정이다.

---

### Flush (플러시)

플러시는 영속성 컨텍스트에 올라간 변경 내용을 데이터베이스에 반영하는 과정이다.  //em.flush()
 
트랜잭션 커밋을 하게되면 SQL저장소에 
플러시를 하도록 되어있어 우리가 인지하지 못하는 것이다.

1. DB와 변경감지
2. 쓰기지연 SQL 저장소에 수정된 엔티티를 저장
3. 쓰기 지연 SQL 저장소를 DB에서 실행하며 동기화시킴

이때 flush는 영속성 컨텍스트를 DB와 동기화시키는것이지 비우는것은 아니다.

---

📘 Reference

- https://www.inflearn.com/course/ORM-JPA-Basic/dashboard
- https://girawhale.tistory.com/129#%EC%A6%89%EC%8B%9C-%EB%A1%9C%EB%94%A9,-%EC%A7%80%EC%97%B0-%EB%A1%9C%EB%94%A9
- https://devwithpug.github.io/spring/jpa-2/
