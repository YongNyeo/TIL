# Spring Container
⭐ 스프링을 사용하기에 앞서 자바를 이용할 때 왜 쓰는지 짚고 넘어가자 

스프링 컨테이너가 나오기전에는 개발자가 직접 자바 객체를 관리해줘야했다. 하지만 객체지향 원칙을 모두 지켜가면서 객체 관계의 연결,관리까지하기에는 너무 어려웠고 그것을 해결해준것이 
스프링 빈을 관리해주는 스프링 컨테이너다.

이때 스프링 컨테이너의 효과가 IOC,DI 이다.

- IOC는 Inversion of control 의 약자로 제어의역전, 스프링 프레임워크가 우리가 생성한것들을 제어해주는것이다. 우리가 프레임워크를 이용한다는 개념보다는 우리가 프레임워크 안에서 관리된다는 것이다.
  -  기존에 우리가 생성한 객체를 모두 관리해줘야했다면 스프링 컨테이너에서는 객체의 생명주기를 관리해준다.

- DI는 Dependency Injection 의 약자로 의존성주입, 객체들끼리는 추상화된것에 의존해서 다형성을 지켜줄수 있으며 객체들끼리의 의존관계의 주입 DIP(Dependency Injection Priciple)을 보장해주는 개념이다.
  - 기존에는 객체의 연관관계(설정정보)를 한쪽에 모두 정리하고 그것을 토대로 개발하는 방식이었다. 그러나 스프링 컨테이너(스프링 빈) 이후에는 @어노테이션만 작성하면 스프링 컨테이너 안에서 객체간의 연관관계가 모두 정리된다는 큰 장점이 있다.

<img width="661" alt="스크린샷 2023-07-16 오후 3 49 49" src="https://github.com/YongNyeo/TIL/assets/109174778/f8db2750-d129-4635-b1e4-736dd42bc45e">

다음 그림과 우리는 추상화된 존재에만 의존하면, 스프링 컨테이너에서 의존관계를 주입시켜주는 방식이다. DIP와 OCP(open-close-principle)을 지킬수 있다.



### Spring Container 의 종류

 BeanFactory는 스프링 컨테이너의 최상단 인터페이스다.
- BeanFactory 계열의 인터페이스만 구현한 클래스는 단순히 컨테이너에서 객체를 생성하고 DI를 처리하는 기능만 제공한다.

<img width="854" alt="스크린샷 2023-07-16 오후 4 17 56" src="https://github.com/YongNyeo/TIL/assets/109174778/b0cf6037-dfc9-46c9-a4da-207dd91afcf1">

가장 많이 사용하는것이 ApplicationContext이고, BeanFactory 보다 더 추가적으로 제공하는 기능들이 있다.
- 국제화가 지원되는 텍스트 메시지를 관리 해준다.
- 이미지같은 파일 자원을 로드할 수 있는 포괄적인 방법을 제공해준다.
- 리스너로 등록된 빈에게 이벤트 발생을 알려준다.


---

# 🔢 스프링 컨테이너의 싱글톤 비밀 @Configuration

## @Bean vs @Component vs @Configuration  3가지로 나눠 이해하기!

우선 세 어노테이션 모두 스프링 컨테이너에 빈으로 등록 해주도록 도와준다.

왜냐? 최상단 @ComponentScan에서 하위 패키지의 @Bean,@Component (@configuration) 을 스캔해준다.

@Bean은 메서드 단위에 붙여서 외부 라이브러리를 주입시키거나 다형성으로 여러 구현체를 등록할때 이용할 수 있다.

    public class MyBeanConfiguration { 

    @Bean 
    public MangKyuResource mangKyuResource() {
        return new MangKyuResource(); 
    } 
만약 여기서 리턴하는 MangKyuResource값이 외부 라이브러리라면, @Bean을 이용해서 수동 등록해주는 것이 편할 것이다.

---

@Component 

컴포넌트는 클래스 단위에 붙이는 어노테이션이다. 보통 개발자가 작성하는 클래스에 직접 붙이고, 자동으로 스프링컨테이너에 등록해준다.

<img width="276" alt="스크린샷 2023-11-02 오후 7 37 31" src="https://github.com/YongNyeo/TIL/assets/109174778/12deaf62-4370-41fb-8c9e-2b7f8518aa30">

컴포넌트를 상속받는 어노테이션으로 우리가 많이 사용하는 @Configuration, @Service, @Controller...가 있다.

---

@Configuration의 비밀

우선 스프링 컨테이너는 (빈스코프에 따라) 싱글톤을 유지하고있다.

여기서 가장 중요한것은 @Configuration인데, 수동으로 등록하는 스프링 빈이 싱글톤으로 유지되는데 아주 중요한 역할을 해준다. 

@Configuration은 보통 @Bean이 달려있는 여러 얽히고 설킨 환경 설정 정보들의 집합체다. 

예시를 하나 보자. 


    public class MyBeanConfiguration { 

    @Bean 
    public MangKyuResource mangKyuResource() {
        return new MangKyuResource(); 
    } 
    
    @Bean 
    public MyFirstBean myFirstBean() { 
        return new MyFirstBean(mangKyuResource()); 
    } 
    
    @Bean 
    public MySecondBean mySecondBean() { 
        return new MySecondBean(mangKyuResource()); 
    } 

기본적으로 Bean은 메서드 이름으로 등록이 된다. 

그리고 계속 강조하지만, 해당 Bean은 항상 같은 객체(싱글톤)을 반환해서 싱글톤 (빈스코프) 빈이라고 하는것이다. 

하지만 위와 같은 코드에서는 싱글톤이 깨질 수 있다.

왜냐하면 mangKyuResource 빈에서 호출한 것의 결과는 new MangKyuResource() 일것이고,

그 아래 메서드 myFirstBean 빈에서 호출한것 결과 또한  new MangKyuResource()인데, 

이때 @Configuration이 없으면 각 빈은 각자 new로 새로운 객체를 생성하게 돼서 싱글톤이 깨지는것이다.

    @Configuration
    public class MyBeanConfigurationProxy extends MyBeanConfiguration { 

    private Object source;

    @Override
    public MangKyuResource mangKyuResource() {
        if (mangKyuResource == null) {
            source = super.mangKyuResource();
        }
        
        return source; 
    } 
    
    @Override
    public MyFirstBean myFirstBean() { 
        return super.myFirstBean();
    } 
    
    @Override
    public MySecondBean mySecondBean() { 
        return super.mySecondBean();
    } 

@Configuation 을 붙이면 대충 이러한 프록시 코드가 생성된다. 

정리하자면 @Configuation은 각자 다른 빈을 통해 객체를 생성하더라도 항상 같은 결과(싱글톤)를 유지할 수 있도록 환경 설정을 모두 관리해준다고 생각하면 된다.



