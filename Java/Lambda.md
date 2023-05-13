# 💡 람다. 차근차근 이해하자

나는 람다를 많이 보았지만 [(매개변수)-> 구현 ]을 제대로 이해하지 못한채 이용하고있었다. 😰

람다는 어떻게 괄호와 화살표만으로 함수를 실행시키지?
자바가 추구하는 함수형 프로그래밍은?

***

### :accessibility: 함수형 프로그래밍이란?
- 함수형 프로그래밍은 함수의 입력만을 의존하여 출력을 만드는 구조로 외부에 상태를 변경하는 것을 지양하는 패러다임으로 부작용(Side-effect) 발생을 최소화 하는 방법론이라 할 수 있다

- 함수형 프로그래밍은 특정기능의 메서드 호출(명령)하는 형식이 아닌 데이터가 입력으로 주어지고 데이터를 다루는 과정(흐름)을 정의하는 형식

- 별개의 함수 작성(선언)없이, 데이터의 흐름을 나타내는 코드로 가독성을 올리고, 외부의 상태를 변경시키지 않아 Side-effect를 최소화하기 위해 나타난 것이 람다입니다.

---

### ❗ 람다를 이용하기 위해서는 몇가지 특징이 있습니다.
1. 순수 함수(pure function)
- 같은 값에 대해서는 항상 같은 결과를 return해야합니다
2. 고차 함수
- 함수를 객체로 취급하여 인자 또는  리턴값으로 사용할수 있어야 합니다
3. 익명 함수
- 람다식으로 이뤄진 함수는 모두 익명함수이고, 고차함수(객체취급)입니다.

---
###### 람다는 그때그때 유동적인 코드를 만들수 있어야합니다. 따라서 정해진 함수가 아닌, 넘어오는 파라미터에 따라 추상 메소드를 가져다 쓰고자합니다.

###### 이때 쓰는 용어가 바로 '함수형 인터페이스' 입니다. 


### 🎱 함수형 인터페이스

한개의 추상 메소드가 선언된 인터페이스를 함수형 인터페이스라고 합니다. 람다식에서 쓸 함수를 구현할때 여기에 있는 추상메소드를 가져다 사용하게 됩니다. 

(자주사용하는 예시로는 Comparator(인터페이스)내의 compare이 있습니다)

함수형 인터페이스라고해서 꼭 추상메소드만 있지 않고, default Method 또는 static Method도 구현 가능합니다. 중요한것은 단 하나의 추상 메소드입니다.

아무리 유동적으로 사용하려고 해도 매번 작성하는 고생을 할순 없습니다. 그래서 기본적으로 제공되는 함수형 인터페이스들이 있습다 [이곳에서 확인!](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)

들여다 보시면 모두 @FunctionalInterface 를 붙여서 함수형 인터페이스임을 명시하고 있습니다. 

---
코드 예시

      public interface MyNumber {
            int getMax(int num1, int num2);
      }
      public class TestMyNumber {
            public static void main(String[] args) {
                  MyNumber max = (x, y) -> (x >= y) ? x : y;
                  System.out.println(max.getMax(10, 20));
            }
      }

MyNumber라는  인터페이스에 getMax()라는 추상메서드를 만들고,  MyNumber 구현체 = 람다 함수식을 작성하면 된다. 람다식==익명 객체다.
이후 값을 넣어주면 람다식은 그제서야 실행된다.


---
👍람다식의 장점:

1. 가독성이 좋습니다.
2. 지연 연산(Lazy Programming) 
      -  기존 함수(클래스 기반의)식을 사용하면, 함수식을 바로 사용하지 않아도 클래스파일을 생성해야합니다. 반면 람다식을 사용할 경우 컴파일 단계에서는 실행되지 않고 입력변수가 들어오고 나서야 그때 그때 실행하게 됨으로써 지연 연산이 실행될 수 있습니다.
4. 병렬처리 쉽게 가능 

👎람다식의 단점:
1. 람다식 호출 방식이 일반 함수 호출방식에 비해 까다롭습니다.
2. stream 이용시 for문등의 반복문에 비해 호출 비용이 높을 수 있습니다.
3. for,while문보다 가독성이 좋을수도 있지만, 경우에 따라 더 복잡해질수도 있습니다.
---


람다식을 어떻게 사용해야할지 대략적으로 알아봤습니다. 다음번엔 lambda와 함께 쓰는 stream 기능에 대해서 공부해보겠습니다.

📘 Reference:

<https://bcp0109.tistory.com/313>


****** 



# 💡 Stream 사용법과 기능 
스트림은 자바8에 컬렉션 데이터를 더 효과적으로 처리하기 위해 나타난 기능입니다.

복잡한 for - loop문을 간략하게 나타낼수 있으며, 병렬처리를 쉽게 구현할 수 있습니다.

이제부터 스트림이 무엇인지, 어떻게 사용해야하는지 알아보겠습니다.

#### 📰 예시하나 보겠습니다.

- 스트림 사용X

      // 빨간색 사과 필터링
      List<Apple> redApples = forEach(appleList, (Apple apple) -> apple.getColor().equals("RED"));

      // 무게 순서대로 정렬
      redApples.sort(Comparator.comparing(Apple::getWeight));

      // 사과 고유번호 출력
      List<Integer> redHeavyAppleUid = new ArrayList<>();
      for (Apple apple : redApples)
          redHeavyAppleUid.add(apple.getUidNum());      
- 스트림 사용O

       List<Integer> redHeavyAppleUid = appleList.stream()
          .filter(apple -> apple.getColor().equals("RED"))        // 빨간색 사과 필터링
          .sorted(Comparator.comparing(Apple::getWeight))         // 무게 순서대로 정렬
          .map(Apple::getUidNum).collect(Collectors.toList());    // 사과 고유번호 출력
          

### 🤞 스트림을 사용하는데 있어서, 배열 또는 컬렉션 타입등에 stream()을 사용하면 스트림(데이터 흐름 조작)이 시작됩니다.

데이터를 가공하는 단계에서 사용할수있는 함수는 네가지가 있습니다.
- filter()  : 조건에 맞는 원소만 추출합니다 (if문과 유사)
- map() : 객체의 각 원소들을 다른 형태로 바꿀수 있습니다. (다른 클래스의 함수를 참조하여 값을 매핑시킬수도 있습니다)
- sorted() : 정렬을 돕습니다. (Comparator 이용)
- peek() :(=iterator) 원소들을 읽어들이는 역할을 합니다.

            List<String> myList = Arrays.asList("a1","a2","b1","c1","c2");
            myList.stream()
                .filter(s -> s.startsWith(“c”))
                .map(String::toUpperCase)
                .sorted()
                .forEach(System.out::println);

각 줄마다 스트림 기능을 이용해보았습니다.

/

📘 Reference:

<https://futurecreator.github.io/2018/08/26/java-8-streams/>
