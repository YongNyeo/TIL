# Constructor (생성자)
- @NoArgsConstructor
- @AllArgsConstructor
- @Builder

개인프로젝트 진행중에 Entity를 만들때는 @NoArgsConstructor(기본생성자)가 필수고, @Builder를 이용할땐 @AllArgsConstructor가 필수다.

이런 것 때문에 각 생성자가 어떤 특징을 갖고있고, 어떤 경우에 사용하는것이 좋은지 정리하고 넘어가자.

---

## @NoArgsConstructor 💡

기본 생성자 역할을 한다. 

    @NoArgsConstructor
    public class Customer {
        private Long id;
        private String name;
        private int age;
    }
    Customer customer = new Customer();
      
_주의점_

1. final 이 붙은 필드가 있을때 초기화 해주지 않으면 컴파일 에러가 발생할 수 있다.

<img width="160" alt="스크린샷 2023-08-18 오전 9 10 09" src="https://github.com/YongNyeo/TIL/assets/109174778/16ca806a-9167-4f71-a79d-190c4e21e922">

2. @Entity 로 사용하는 클래스엔 반드시 기본 생성자가 있어야 한다.

---
### 💡Tip - @ModelAttribute 사용시

컨트롤러에서 @ModelAttribute 를 통한 객체 바인딩 하는시점에,  어떤 생성자가 우선 적용되는지가 헷갈렸었다.

기본생성자? 전체 생성자? 

    public static <T> Constructor<T> getResolvableConstructor(Class<T> clazz) {
		Constructor<T> ctor = findPrimaryConstructor(clazz);
		if (ctor != null) {
			return ctor;
		}

		Constructor<?>[] ctors = clazz.getConstructors();
		if (ctors.length == 1) {
			// A single public constructor
			return (Constructor<T>) ctors[0];
		}
		else if (ctors.length == 0) {
			// No public constructors -> check non-public
			ctors = clazz.getDeclaredConstructors();
			if (ctors.length == 1) {
				// A single non-public constructor, e.g. from a non-public record type
				return (Constructor<T>) ctors[0];
			}
	}
 
결론은 적절한 생성자를 먼저 찾고 그 뒤에 바인딩되지 않은 값을 setter를 통해 바인딩해주는 순서로 @ModelAttribute는 동작한다.

이 때 적절한 생성자를 찾는것은 Java Reflection API 이용한다.

>Java Reflection API란?
>
>구체적인 클래스 타입을 알지 못해도 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 Java API
>
>이러한 Java Reflection을 활용하면 컴파일 시점이 아닌 런타임 시점에 동적으로 클래스를 객체화 하여 분석 및 추출 해낼 수 있다.

---

## @AllArgsConstructor 💡

전체 필드 생성자 역할을 하는데, 반드시 필드 작성 순에 맞춰서 생성자에도 적용해야 한다.

    @AllArgsConstructor
    public class Customer {
        private final Long id;
        private String name;
        private int age;
    }
    Customer customer = new Customer(2L, "김철수", 23);

전체 생성자를 쉽게 작성할 수 있다는 점에서 편하겠지만

만약, 리팩토링을 하면서 혹은 타인에 의해 필드가 추가된다거나, 해당 인스턴스 멤버의 순서가 바뀌어버렸을때 

데이터 타입이 같다면 컴파일에서 에러를 잡지 못하고 런타임 시점에 에러가 발생할 수 있다.

이 때문에 빌더 패턴 사용을 권장한다.

---

## @Builder 💡

위에서 배운 기본 생성자,전체 생성자 어노테이션 + setter면 충분할 것 같은데, Builder는 또 뭘까?

__우리는 Builder 패턴을 이용하여 생성자를 더 깔끔하게 사용할 수 있다.__

    Person person = Person.builder()
            .height(180)
            .weight(80)
            .build();

        
    @Builder
    class Person{
        int height;
        int weight;
    }

@Builder를 클래스 단위에 붙이면, 자신이 원하는 필드만 선택해서 객체를 생성할 수 있다. 

필드가 많은 경우에 생성자를 무수히 만들필요 없이, @Builder 하나로 해결된다.

여기서, Person 클래스에 String name이 추가되더라도 기존 builder()를 고칠 필요가 없다. 

물론 null값으로 채워지겠지만, 기존의 생성자를 다 갈아 엎어야하는 방식을 떠올리면 빌더 패턴은 매우 효율적이다.

매우 직관적이라 값의 순서를 바꿀일도 없을것이며, 에러 발생도 적게 일어날 것이다.

---

📘 Reference
- https://codinghejow.tistory.com/370
- https://velog.io/@maketheworldwise/Builder-AllNoArgsConstructor-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90
- https://siahn95.tistory.com/170
