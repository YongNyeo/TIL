# 연관관계 매핑 

<img width="651" alt="스크린샷 2023-11-14 오전 11 11 08" src="https://github.com/YongNyeo/TIL/assets/109174778/a073f14e-82cc-4de0-89f3-a3df8f9c627f">

객체가 갖고있는 다른 객체의 값을 찾을때 보유하고있는 참조값으로 찾는다.

반면 관계형 디비에서 테이블간의 값을 찾을때는 참조가 아닌 외래키로 조인을 걸어서 찾는다. 

__즉 테이블 연관관계는 조인을 사용해서 항상 양방향 매핑이다.(어느쪽에서든 값을 찾아낼 수 있다.)__

__반면 객체는 본인이 갖고있는 참조값에 대해서만 찾아갈수 있는 단방향 매핑이다.__


❗ 그럼 객체간의 관계를 어떻게 매핑해야 편하게 쓸 수 있는지 알아보자!

---
Team 테이블(객체)과 Member 테이블(객체)이 있는 상황이다.

Team과 Member는 1:N 관계다.

<img width="501" alt="스크린샷 2023-11-14 오전 11 23 20" src="https://github.com/YongNyeo/TIL/assets/109174778/7031b34a-b4d5-4fd8-9809-9769314742e5">

Member객체가 Team 객체를 참조하고있는 상황이다.  (이때 객체를 참조하면 , Team 엔티티를 찾아가 @Id가 붙은 기본키를 참조하게 된다)


---

## mappedBy의 필요성 

    @Entity
    public class ParentEntity {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private Long id;

      // OneToMany without mappedBy
      @OneToMany(cascade = CascadeType.ALL)
      private List<ChildEntity> children = new ArrayList<>();
    }

    @Entity
    public class ChildEntity {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private Long id;

      // ManyToOne without mappedBy
      @ManyToOne
      private ParentEntity parent;
    }

위 코드는 서로를 알고있는 양방향(단방향 참조 2개) 매핑을 하려했지만,  mappedBy로 지정해주지 않아서 서로에 대해 모른채로 단방향 참조가 2개가 있는 상태다. 

즉, 서로 관계가 없기 때문에 한쪽에서 정보 업데이트가 일어나더라도 다른 한쪽에선 알 방법이 없다.  



    @Entity 
    public class Team { 
      
      @Id @GeneratedValue  private Long id; 
      private String name; 
      
      @OneToMany(mappedBy = "team") 
      List<Member> members = new ArrayList<Member>(); 
    }

따라서 mappedBy를 놓음으로써 두 객체 간의 관계에서 mappedBy 설정한 곳의 반대편에서 FK를 관리하겠다. 라고 명시하는 것이다. 

이렇게 연관관계의 주인(FK를 가진 곳)을 정하고 나면, 양방향 매핑이 완성되는것이다.

그럼 앞으로 양방향 매핑할 때,  조금 더 편하고 확실하게 관리하는 방법을 알아보자. 


## 🥇연관관계 편의 메서드

위의 team과 member가 있고, member를 추가하면서 team을 설정하는 상황이다.

주의할 점은 양방향이므로 이때는 team의 List<member>에도 값을 추가해야줘야한다. 

    member.setTeam(team1) -> Team값을 주입시키는 메서드 setTeam()


    void setTeam(Team team1){

      this.team = team1
      team.getMembers().add(this)  
      
    }

setTeam()으로 Member의 Team만 주입시키는것이 아니라, Team에 있는 Member까지 동시에 주입시키는 것이다. 

양방향일때는 양쪽 값을 같이 넣어줘야하는데, 각각 메서드에서 실행시키는것이 아닌, 연관관계의 주인쪽(FK) 메서드에서 동시에 주입시켜주는것이 가독성도 좋고 확실할 것이다. 


---


# 양방향 vs 단방향

사실 양방향 매핑이 항상 필요한것은 아니다. 

Team과 Member 1:N 이 있는 상황에서, 만약 Team 이 Member를 몰라도 된다고하면, Member가 Team만 아는 방식으로 단방향을 매핑을 해도 무방하다. 

단방향 연관관계로 매핑만 되어있어도 JPQL과 같은 쿼리를 통해 데이터를 잘 찾아올 수 있다. 

그럼에도 양방향 매핑을 쓰는 이유는 그만큼 역방향 관계를 자주 사용하는 경우에 쿼리를 매번 짜면 불편하기도 하고, jpa가 제공하는 cascade 그리고 jpa 생명주기  옵션을 사용해서 더 효율적으로 사용할 수 있기 때문이다. 


