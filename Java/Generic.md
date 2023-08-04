# Generic이란?

<img width="425" alt="스크린샷 2023-08-04 오전 9 48 41" src="https://github.com/YongNyeo/TIL/assets/109174778/a90f07db-af56-42a7-b68c-0e1d511d89e8">

List 인터페이스를 보면 꺽쇠 안에 E 라고 명시되어있다.  저게 무슨뜻일까? 

오라클에는 다음과 같이 명시되어있다.

_E - Element (used extensively by the Java Collections Framework)_

---
우리는 평소 List같은 컬렉션을 다음과같이 자연스럽게 사용한다.

예제1)

    List<Integer>list1 = new ArrayList<>();
    List<String>list2 = new ArrayList<>();


명시한 변수타입과 다른 타입을 넣게되면 
 
<img width="537" alt="스크린샷 2023-08-04 오전 9 54 17" src="https://github.com/YongNyeo/TIL/assets/109174778/1e78a96e-872d-48a4-af2b-a1fcfb883acf">

다음과같이 컴파일 단계에서 에러가난다.

하지만 다음과 같이 꺽쇠안에 변수타입을 명시하지 않아도된다.

예제2) 

    List list3 = new ArrayList(); 

별다른 타입을 선언하지 않았기 때문에 list3에는 Object의 모든 하위 타입이 들어갈 수 있다. 

<img width="557" alt="스크린샷 2023-08-04 오전 10 01 45" src="https://github.com/YongNyeo/TIL/assets/109174778/b637f344-8e33-403f-b998-09aa03dd4bec">

예제1과 달리 예제2에선 두개의 다른타입을 삽입해도 컴파일 단계에선 에러가 발생하지 않는다. 

    
        list3.add("String");
        list3.add(123);

        System.out.println(list3.get(0).getClass()); //String
        System.out.println(list3.get(1).getClass()); //Integer

        //이것만보면 정상적으로 작동하는 코드다.

하지만 이렇게 되면 문제점은 런타임시 list3의 값을 가져다 사용할때마다 타입이 다를수 있기때문에 그에 대한 검증이 필요하다.

---

### 이와같은 문제로 데이터의 타입을 일반화(Generalize)해서 사용하는것을 제네릭(Generic)이라고 한다. 

-  이러한 제네릭은 클래스나 메서드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정할 수 있다. 따라서 컴파일 시에 미리 타입 검사(Type Check)를 수행할 수 있다.

- 이러한 타입 검사를 컴파일 시에 수행하면 다음과 같은 장점을 얻을 수 있다.

    - 클래스나 메서드 내부에서 사용되는 객체의 타입 안정성을 높일 수 있다.

    - 반환값에 대한 타입 변환 및 타입 검사에 들어가는 노력을 줄일 수 있다.

    - 타입에 대해 유연성과 안정성을 확보한다.

    - 런타임 환경에 영향을 주지 않는 전처리 기술이다.

---

📘 Reference
- https://docs.oracle.com/javase/tutorial/java/generics/types.html
- https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A0%9C%EB%84%A4%EB%A6%ADGenerics-%EA%B0%9C%EB%85%90-%EB%AC%B8%EB%B2%95-%EC%A0%95%EB%B3%B5%ED%95%98%EA%B8%B0
 
