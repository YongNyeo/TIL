# 💡 Optional 이란?

공식문서에 따르면

_" Optional is primarily intended for use as a method return type where there is a clear need to represent "no result," and where using null is likely to cause errors. A variable whose type is Optional should never itself be null; it should always point to an Optional instance. "_

### Optional은 null을 반환하면 오류가 발생할 가능성이 매우 높은 경우에 '결과 없음'을 명확하게 드러내기 위해 메소드의 반환 타입으로 사용되도록 매우 제한적인 경우로 설계되었다는 것이다. 

---

    public final class Optional<T> {

    // If non-null, the value; if null, indicates no value is present
      private final T value;
      ...
    }

Optional에서는 value값에 값을 저장한다. private final로 불변객체다.

Optional 인스턴스는 모든 타입의 참조 변수를 저장할 수 있는 Wrapper 클래스라고도 한다.

<img width="503" alt="스크린샷 2023-08-04 오후 1 46 46" src="https://github.com/YongNyeo/TIL/assets/109174778/aed7a4de-fc4a-4be2-bec9-342af47e4b76">

쉽게 생각하면, Optional<T> 에서 T에는 모든 기본타입, 참조타입 저장할 수 있다.

### 잠깐 짚고 넘어갈것은 기본타입은 null이 불가능하고, 참조타입은 null처리가 가능하다. 

---
### 💡empty()

Optional은 Wrapper 클래스이기 때문에 값이 없을 수도 있는데, 이때는 Optional.empty()로 생성할 수 있다.
  
    public static<T> Optional<T> empty() {
      @SuppressWarnings("unchecked")
      Optional<T> t = (Optional<T>) EMPTY;
      return t;
    }

여기에 있는 EMPTY 값은 

    private static final Optional<?> EMPTY = new Optional<>();

이 비어있는 EMPTY값이다. EMPTY라는 객체는 생성되어있지만, 안에 값은 빈 상태를 의미한다.

<img width="564" alt="스크린샷 2023-08-04 오후 4 03 54" src="https://github.com/YongNyeo/TIL/assets/109174778/d948a0d9-7463-416e-995a-392b915c5e14">



#### null이랑은 다름을 주의해야한다.

    Optional<String> optional = Optional.empty();
    System.out.println(optional.isEmpty()); // true
    System.out.println(optional.isPresent()); // false
    System.out.println(optional==null); //false

---

## 💡 of() vs ofNullable()

Optional 에 값을 저장하는 방법 두가지의 차이점은?

### 1. of()
    public static <T> Optional<T> of(T value) {
        return new Optional<>(value);
    }

of 는 value가 null이 아닌경우 사용한다.

null 값일경우 NPE을 터트린다. 


### 2. ofNullable()

    public static <T> Optional<T> ofNullable(T value) {
        return value == null ? empty() : of(value);
    }
ofNullable은 null을 empty()로 처리한다.

    String name = optional.orElse("anonymous"); 

orElse() 또는 orElseGet()을 통해 null일경우에도 안전하게 처리할 수 있다. 

---

## 💡orElse() vs orElseGet()

    public T orElse(T other) {
        return value != null ? value : other;
    }

    public T orElseGet(Supplier<? extends T> other) {
        return value != null ? value : other.get();
    }

두 메서드의 큰 차이점 두가지가있다.
1. orElse()는 파라미터가 일반 래퍼클래스라면,  orElseGet()은 파라미터가 함수형 인터페이스(함수) 라는것이다.

2.  orElse는 null이던말던 항상 호출된다.

    orElseGet은 null일 때만 호출된다.

### 2번의 이유를 알아보기 위해 코드먼저 확인해보자 

예시)

    @Test
    @DisplayName("notNull테스트")
    void name() {
      String name = "snack";
      String elseName = Optional.ofNullable(name).orElse(anyName());
      System.out.println(elseName);
  
      String elseGetName = Optional.ofNullable(name).orElseGet(this::anyName);
      System.out.println(elseGetName);
    }

    private String anyName() {
      System.out.println("reach anyName");
      return "anyName";
    }

결과 

> reach anyName
> 
> snack
> 
> snack
> 
---

## orElse()에서 anyName은 대체 어떻게 실행이 되었을까? orElseGet()은 왜 안됐지?

❗orElse()의 프로세스

1. Optional.ofNullable(name) 실행 -> snack
2. snack.orElse(anyName()) 에서 orElse()실행시
  - orElse의 조건문인  _return value != null ? value : other;_  보다 orElse()의 파라미터로 들어온 anyName()이 먼저 실행된다. 여기서 print() 한번 실행된다.
  - snack.orElse("anyName") 이 되고, snack!=null 이기에 snack이 print된다.

❗orElseGet()의 프로세스

하지만 함수형 인터페이스(함수)를 파라미터로 받는 orElseGet에서는 동작이 달라진다.

1. Optional.ofNullable로 "snack"를 갖는 Optional 객체 생성
2. anyName() 함수 자체를 orElseGet 파라미터로 전달
3. orElseGet이 호출됨, "snack"이 Null이 아니므로 "snack"를 그대로 가지며 anyName()이 호출되지 않음

파라미터를 다루는 순서에 있어서 차이가 있다. 이것때문에 에러가 많이 난다고 하니 조심해서 사용하자.

---

## Optional 실사용예시

    String name = getName();
    String result = "";

    try {
      result = name.toUpperCase();
      } catch (NullPointerException e) {
        throw new CustomUpperCaseException();
    }

위와같은 코드를 아래와같이 바꿀 수 있다.

    Optional<String> nameOpt = Optional.ofNullable(getName());
    String result = nameOpt.orElseThrow(CustomUpperCaseExcpetion::new)
                  .toUpperCase();


## 💡주의점

Optional은 null 또는 값을 감싸서 NPE(NullPointerException)로부터 부담을 줄이기 위해 등장한 Wrapper 클래스이다.

Optional은 값을 Wrapping하고 다시 풀고, null 일 경우에는 대체하는 함수를 호출하는 등의 오버헤드가 있으므로 잘못 사용하면 시스템 성능이 저하된다. 

그렇기 때문에 메소드의 반환 값이 절대 null이 아니라면 Optional을 사용하지 않는 것이 좋다.

즉, Optional은 메소드의 결과가 null이 될 수 있으며, null에 의해 오류가 발생할 가능성이 매우 높을 때 반환값으로만 사용되어야 한다. 

또한 Optional은 파라미터로 넘어가는 등이 아니라 반환 타입으로써 제한적으로 사용되도록 설계되었는데, 이것은 이어지는 포스팅에서 살펴보도록 하자.


📘 Reference
- https://mangkyu.tistory.com/70
- https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html
