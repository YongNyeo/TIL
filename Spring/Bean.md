# 💡 Spring bean을 이용한 데이터베이스 초기화하기!
공공데이터를 이용하려해서, DB에 많은 데이터를 입력하고 시작해야했다. 그래서 어떤방식으로 하는것이 효율적일까 생각해보았다. 대표적으로 3가지 방법이 있다.

## 1.InitailizingBean,DisposableBean

- 둘다 인터페이스다.
  
	    void afterPropertiesSet() throws Exception;
    	void destroy() throws Exception;

  구현 메소드로 각각 오버라이드해서 사용하면된다.

- 이름에서 알수있다시피 InitailizingBean는 처음 BeanFactory 설정(빈 의존관계 주입) 완료 후 실행된다.
  
- DisposableBean는 스프링컨테이너가 소멸된 후 실행된다.

  ❔ 단점 ❔

- 해당 인터페이스가 spring전용 인터페이스라 spring 환경에서만 실행 가능하다.
- 메소드명이 변경불가하다
- spring 초창기 나왔던 기술이라 생각하면 좋다.



## 2. @Bean (initMethod="메소드명(ex init)",destroyMethod="ex destory")

- @Bean 어노테이션에 시작(초기화)메소드와 종료메소드를 입력하는 것이다.
- beanFactory의 설정정보를 이용하는것이기 때문에 외부라이브러리에도 적용가능하다는 장점이 있다.


## 3. @PostConstruct, @PreDestroy 

- 현재 스프링에서 가장 자주쓰이며, 가장 권고하는 방법이다.
- javax 라이브러리를 사용해서 spring 외의 환경에서도 가능하나, 외부라이브러리에 적용은 불가하다.
- @PostConstruct은 Bean 의존관계 정리가 완전히 끝난후 최초로 한번 실행되는것을 보장한다.
- @PreDestroy은 Bean이 소멸직전 한번 실행되는것을 보장한다.
  
