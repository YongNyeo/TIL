# Jpa에서 동작하는 값 타입 컬렉션의 원리!

** 시작에 앞서 이글은 값타입( @Embedded )가 아닌 값타입 컬렉션(List,Set...) 에 대한 문제점을 정리한 글이다. 값타입은 사용해도 괜찮다!

<img width="583" alt="스크린샷 2023-11-16 오후 2 35 01" src="https://github.com/YongNyeo/TIL/assets/109174778/62fd2015-0da2-4e54-8c6e-d6a9e116d1eb">

다음과 같이 Member 클래스는 favoriteFoods와 addHistory 라는 두가지 컬렉션 필드를 갖고있다.  

과연 다음 컬렉션들은 어떻게 저장이 될까?

---

당연하게도 데이터베이스는 컬렉션을 같은 테이블에 저장할 수 없다. 

항상 컬렉션을 저장하기 위한 별도의 테이블이 존재한다.

이때 @ElementCollection 또는 @CollectionTable을 이용할 수 있다.

Address를 이용하는 Person이라는 클래스가 있는 상황이다. 


    @Entity
    public class Person {

      @Id
      @GeneratedValue
      private Long id;

      private String name;

      @ElementCollection
      @CollectionTable(
            name = "foods",
            joinColumns = @JoinColumn(name = "person_id")
      )
      @Column(name = "food_name") //값이 하나고 내가 정의한 것이 아니기 때문에 예외적으로 컬럼명 변경 허용
      Set<String> favoriteFoods = new HashSet<>();

      @ElementCollection
      @CollectionTable(
            name = "address",
            joinColumns = @JoinColumn(name = "person_id")
      )
      List<Address> addressList = new ArrayList<>();
    }

@ElementCollection 을 통해 컬렉션임을 나타내주고,

@CollectionTable 을 이용해서 컬렉션 값이 있는 테이블 명과, 외래키 이름을 지정해주고있다. 

> 값타입 컬렉션도 결국은 값 타입이기 때문에 따로 생명주기를 가지지 않고, 엔티티와 같은 생명주기를 따라간다.
>
> 그렇기 때문에 값을 저장하거나 삭제하고자 할 때 일반적으로 컬렉션을 사용하는 것과 마찬가지로 단순히 값을 추가하거나 삭제하기만 하면 데이터베이스에 반영이 된다.
> 
> 일대다 관계에서 cascade = ALL, orphanRemoval = true를 설정한 것과 같다고 볼 수 있다.

## 값타입 컬렉션의 치명적인 단점

-  값 타입 컬렉션은 결국엔 '값'이다. 엔티티와 다르ㄱp id식별자가 없다.

-  따라서 변경시 추적할 수가 없다.

-  변경사항 발생시 값 타입 내 값들이 의도하지 않은 행위가 일어난다.

-  null 불가


---


그래서 뭐쓰라고?

Address 를 클래스로 만들고, 테이블을 만들서 아예 1:N으로 풀어내는것이 옳은 방식이다.

아예 값 타입 컬렉션을 쓰지말라는것은 아니다. 값에 대한 히스토리를 찾을 필요도 없는 정말 단순한 ex) select box같은 상황에선 써도 무방하다.

되도록이면 1:N 방식을 고려하자.


Reference

- https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21716&category=questionDetail&tab=community
  
