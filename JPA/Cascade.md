# Cascade, 영속성 전이 & 고아객체

영속성 전이란?

<img width="216" alt="스크린샷 2023-11-15 오전 11 16 50" src="https://github.com/YongNyeo/TIL/assets/109174778/f5936edb-a79e-4563-a892-ee7a3edc387f">

연관관계 매핑시 부모-자식 간의 관계에서 한쪽에서 특정 동작시, 다른 한쪽에 어떤 영향을 미칠 수 있는지를 명시할 수 있는 옵션이다. 

1. Cascade.ALL
2. Cascade.PERSIST
3. Cascade.REMOVE
4. Cascade.MERGE
5. Cascade.REFRESH
6. Cascade.DETACH


Cascade All은 아래 옵션을 모두 사용하겠다는 의미다.

Persist 는 한쪽에서 영속화(persist())를 하면 다른 한쪽도 영속화를 하겠다는 의미다. 

_사실 이 글을 쓰는 이유는 REMOVE와 orphanRemoval = true 라는 옵션의 차이점 때문이다._


    @OneToMany(mappedBy="team", orphanRemoval = true,cascade = CascadeType.PERSIST)
    

이렇게도 사용할 수 있는데, 먼저 REMOVE를 알아보자.🔥


---

현재 Team 과 Member가 있고, 1:N 관계인 상황이다.

Team이 갖고있는 Member 변수에 Cascade.REMOVE 를 걸어주었다. 즉 Team이 삭제될때 Member도 삭제해주겠다. 라는 의미다. 

(orphanRemoval 옵션은 걸어주지 않는 상황이다.)

    void cascadeType_Remove_InCaseOfTeamRemoval() {
    // given
    Member member1 = new Member();
    Member member2 = new Member();

    Team team = new Team();

    team.addMember(member1);
    team.addMember(member2);

    teamRepository.save(team);

    // when
    teamRepository.delete(team);

    // then
    List<Team> teams = teamRepository.findAll();
    List<Member> members = memberRepository.findAll();

    assertThat(teams).hasSize(0);
    assertThat(members).hasSize(0);
    }

Team 객체를 삭제하면, 그에 딸린 자식 객체인 Member또한 삭제되는 쿼리가 나간다.

    Hibernate: 
        delete 
        from
            member 
        where
            id=?
    Hibernate: 
        delete 
        from
            member 
        where
            id=?
    Hibernate: 
        delete 
        from
            team 
        where
            id=?

---

이번엔 Team객체에 있는 member 만 삭제해보자.

     team.getMembers().remove(0);

실행결과는...

__DB에 아무 쿼리도 날아가지 않는다.__

cascade의 Remove옵션은 현재 Team이 갖고있는 Member는 참조값을 들고있는것도 아니고, 연관관계만 갖고있는것인데

그 연관관계를 삭제한다고해서 자식까지 삭제할 필요는 없다! 라는 입장인 것이다.

이번엔 Remove옵션과 비교되는 orphanRemoval 을 사용해보자

---

orphanRemoval = true 또한  cascade의 Remove와 같이 부모 엔티티가 삭제되면 자식 엔티티도 삭제된다.

cascade와 차이는 부모 객체에서 자식 객체를 제거할때 나타난다.

     team.getMembers().remove(0);

위 로직을 실행하면

    Hibernate: 
        delete 
        from
            member 
        where
            id=?

부모 객체에서 자식객체를 삭제하면

연관관계를 삭제함으로서 자식이 고아가 될 것을 염려해 자식을 아예 삭제해버린다.

---

### 정리

cascade = CascadeType.REMOVE 을 사용하게되면, 부모객체를 삭제할때 자식까지 전이되어 삭제되는것은 맞지만, 부모가 갖고있는 자식을 삭제할땐 관계만 끊어지고 자식 자체는 삭제되지 않아서 고아 객체가 될 가능성도 있다.

orphanRemoval = tru 을 사용하게 되면, 부모객체를 삭제할때 자식까지 전이되어 삭제되는것 뿐 아니라 , 부모가 갖고있는 자식이 삭제되면 자식 또한 삭제된다.

두 옵션 모두 자식이 하나의 부모와 연관관계를 맺었을 때 사용해야한다. 자식이 여러 부모와 관계가 있게 되면, 삭제가 전이되는것은 매우 불안정한 상태가 되기 때문이다. 


---

Reference 

- https://tecoble.techcourse.co.kr/post/2021-08-15-jpa-cascadetype-remove-vs-orphanremoval-true
- https://www.inflearn.com/course/ORM-JPA-Basic/dashboard
