# 추상화

추상화라는 개념은 처음엔 쉽게 감이 오지 않았다. 추상적인 표현..? 평소엔 적지 않게 쓰는 말인데, 프로그래밍에서 추상적으로 표현을 한다?

컴퓨터 과학에서 추상화는 복잡한 자료, 모듈, 시스템 등으로부터 핵심적인 개념 또는 기능을 간추려 내는 것을 말한다.

아주 대표적인 예시가 자동차다.

<img width="1029" alt="스크린샷 2023-07-13 오후 9 34 22" src="https://github.com/YongNyeo/TIL/assets/109174778/ea01ffc5-f6f6-4af4-9078-70bbbf677599">

K3,아반떼,테슬라라는 구체화된 모델을의 공통점을 모두 뽑아내면 악셀,브레이크 등 우리가 아는 모든 자동차들이 갖고있는 기능들이다. 이 기능들을 합쳐놓는것이 추상화이고, 자동차 라고 볼수 있다.

자동차(추상화된 것)만 설계를 잘 한다면 구체화된 모델이 bmw,아반떼,람보르기니,페라리 뭐가 됐든 상관없는것이다. 사용자(클라이언트))는 추상화된 것에 의존하면 되는것이다. 코드를 살펴보자

    public class SuperCar {

      public void move() {
        System.out.println("슈퍼하게 달립니다.");
      }
    }

    public class SnowCar {

      public void move() {
        System.out.println("겨울에 잘 달립니다.");
      }
    }

    public class FastCar {

      public void move() {
        System.out.println("빠르게 달립니다.");
      }
    }
    
    public class Main {

      public static void main(String[] args) {
        final SuperCar superCar = new SuperCar();
        final SnowCar snowCar = new SnowCar();
        final FastCar fastCar = new FastCar();

        superCar.move();
        snowCar.move();
        fastCar.move();
      }
    }
슈퍼카,눈차,빠른차를 각각 만들었다. 추상화뿐 아니라 캡슐화마저 지키지 못했다. 추상화시켜서 다시 만들어보자.


    public abstract class Car {

      public abstract void move();
    }

    public class FastCar extends Car{

      @Override
      public void move() {
        System.out.println("빠르게 달립니다.");
      }
    }

    public class SnowCar extends Car {

      @Override
      public void move() {
        System.out.println("겨울에 잘 달립니다.");
      }
    }

    public class SuperCar extends Car {

      @Override
      public void move() {
        System.out.println("슈퍼하게 달립니다.");
      }
    }

Car라는 공통점을 이용해 추상화시켰다.

## 💡 추상화를 위한 기능 -> 인터페이스(interface)와 추상 클래스(abstract class)의 차이점

둘다 기능적으로 비슷해보이는데, 뭐가 다른걸까?

### abstract class

      abstract class Shape { // 추상 클래스
        Shape() {}
        void edit() {}
        abstract public void draw(); // 추상 메서드
      }
- 추상 메소드(선언부만 작성하고 구현부는 작성하지 않음)를 가진다 
  
- 추상매서드가 없더라도 추상 클래스로 선언할수 있다 (추상메서드가 있다면 반드시 추상클래스가 된다)
- 추상클래스에선 일반 메서드도 작성 가능하다
  
- 추상 클래스의 목적은 자식 클래스가 추상메서드를 상속받아 확장하는데 있다.
  
- 멤버변수 타입에 있어서 있어서 static이나 final에 관계없이 사용가능하고 , 접근제한자 또한 public이 아닌 private등에 제한이 없다.
  
- 단일상속(extends)이고, 그때그때 사용한다는 느낌보다는 구체화클래스가 추상클래스에 상속받는 개념에 가깝다.
  
------

### interface

    interface Animal {
    /** public static final 생략 */ String name = "defaultName";
 
    /** public abstract 생략 */ void setName(String name);
    }
 
    interface LandAnimal extends Animal { }
    interface MarineAnimals extends Animal { }

- 인터페이스는 기본적으로 메서드앞에는 public abstract 생략이고, 멤버 변수 앞에는 public static final이 생략된 형태다.
  
-  모든 메서드가 abstract형태다보니, 무언가 구체화하려하면 'Interface abstract methods cannot have body' 에러가 난다.

- default 메서드(자바8 추가)
    - 우선 접근제한자 (public,private,default)의 default가 아니다.
    - interface는 다형성을 활용하기 매우 좋지만, 모든 메서드 형태가 abstract이다 보니 공통적으로 사용하는 메서드를 작성할수 없었다. 이점을 보완하고자 default 메서드가 나왔다.

          public interface 동물 {
            public void 종족();
            default void 먹다() { //abstract가 아닌 default형태로 구현이 가능한 메서드다.
              System.out.println("저는 입으로 먹습니다");
            }
          }
      한계점을 보완하고자 나왔다.

- ⭐  다중상속 방식이다 -> 내가 사용하고싶은 인터페이스를 가져다 쓰는 개념이다.

- 다중상속 방식이기 때문에 default 메서드의 이름이 겹친다면 다이아몬드 문제가 발생한다.(추후에 더 공부 할 예정)

### 결론: 이론으로만 알던 추상화를 코드를 통해 어떤방식으로 추상화를 적용해야하는지 감을 좀 잡았다. 사실 내가 mvc패턴을 짜는동안에도 이런 추상화 interface기능을 제대로 적용 못시켰는데 리팩토링을 좀 해봐야겠다.


📘 Reference

https://devocean.sk.com/blog/techBoardDetail.do?page=&boardType=undefined&query=&ID=164391&searchData=&subIndex=

https://wildeveloperetrain.tistory.com/112
