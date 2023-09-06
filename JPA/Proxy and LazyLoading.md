# Proxy와 Lazy Loading(지연로딩)

<img width="804" alt="스크린샷 2023-09-01 오후 3 49 24" src="https://github.com/YongNyeo/TIL/assets/109174778/bd5701ac-14cf-4aa7-8ded-ee696ee1aba4">

데이터 조회시 성능향상을 기대할수 있는 지연로딩이라는 기술이 있는데, 프록시에 대해서 이해해야 지연로딩에 대해 완전히 이해할 수 있으므로 프록시에 대해 먼저 알아보려한다.

---
## Proxy

<img width="247" alt="스크린샷 2023-09-01 오후 3 50 09" src="https://github.com/YongNyeo/TIL/assets/109174778/4cc65c4e-8da4-49c9-9a2c-c9f23c9f9a46">

여기서 쓰이는 Proxy는 가짜라는 개념으로 쓰인다. 원본을 상속받고 있는 형태로, 원본의 참조값과 메서드만 가지고있다. 

---

이제 proxy를 통해 지연로딩과 즉시로딩을 구현할 수 있다.

## Lazy Loading(지연 로딩) , Eager Loading(즉시 로딩)

    @Entity
    public class Member {

        @Id
        @GeneratedValue
        private Long id;
        
        @Column(name = "USERNAME")
        private String name;
        
        @ManyToOne(fetch = FetchType.LAZY) //**
        @JoinColumn(name = "TEAM_ID")
        private Team team;

    }

사용법은 다음과 같이 (어노테이션 이용한) 관계 매핑시 fetch = FetchType.Lazy를 작성하면 된다.

이제 저 Team 객체는 Team team = member.getTeam() 처럼 team이 실제로 쓰일 때 초기화 된다.

    @Entity
    public class Member {
    
        @Id
        @GeneratedValue
        private Long id;
    
        @Column(name = "USERNAME")
        private String name;

        @ManyToOne(fetch = FetchType.EAGER) 
        @JoinColumn(name = "TEAM_ID")
        private Team team;
    }

즉시로딩은 fetch = FetchType.EAGER 를 이용하면 된다. 그러면 Member 객체 생성시 항상 team 객체가 초기화된다.


    SELECT 
	    ...
    FROM 
	    MEMBER M LEFT OUTER JOIN TEAM T
		    ON M.TEAM_ID=T.TEAM_ID
    WHERE
	    M.MEMBER_ID="id"

JPA 하이버네이트는 즉시 로딩을 최적화하기 위해 가능하면 조인 쿼리를 통해 연관 엔티티를 한 번에 조회한다.

>@JoinColumn(nullable= t/f) 설정에 따른 조인 전략
>
>@JoinColumn(nullable = true) : NULL 허용(기본값), 외부 조인 사용
>
>@JoinColumn(nullable = false) : NULL 허용 X, 내부 조인 사용

당연히 내부조인이 성능 최적화에는 좋겠지만, Null이 허용되는 경우도 있을테니 선택해서 정해야할 사항이다.

---

다음은 proxy 객체를 통해 원본 객체를 얻는 과정을 그림으로 나타낸 것이다.

    Member member = em.getReference( Member.class  , "id1"); //proxy 객체
    member.getId()  //proxy객체에서 메서드 실행시 아래와 같은 로직 실행됨

<img width="722" alt="스크린샷 2023-09-01 오후 3 59 54" src="https://github.com/YongNyeo/TIL/assets/109174778/53f9808f-426d-4979-890d-65c0e395c610">

실제 객체가 '생성'되고 그 실제 객체에서 값을 얻어오게 된다.

만약 영속성 컨텍스트에 이미 member가 있다면, 영속성 컨텍스트에서 값을 꺼내온다.(1차 캐시 역할)

#### 이러한 이유들로 영속성컨텍스트의 관리를 받지 못
하는 상황이라면, 프록시 객체를 통한 지연로딩은 실행될 수 없어서 LazyInitializationException 예외가 발생할 것이다.

---

### 💡결론

__실무에서는 지연로딩만 사용하는것이 옳다. 즉시로딩을 사용하게 되면 N+1 문제(상상이상으로 많은 쿼리 수가 나감)가 일어난다.__

JPA에서
- @ManyToOne, @OneToOne default가 즉시로딩이므로 지연로딩으로 바꿔 사용해야한다.
- @OneToMany, @ManyToMany default가 지연로딩이라 그대로 사용해도 무방하다.
 
---

📘 Reference

- https://tecoble.techcourse.co.kr/post/2022-10-17-jpa-hibernate-proxy
- https://www.inflearn.com/course/ORM-JPA-Basic/dashboard

