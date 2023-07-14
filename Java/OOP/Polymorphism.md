# 다형성(Polymorphism)

스프링과 자바 공부를 하면서 여러 강의를 들을 때 많은분들이 강조한 객체지향의 꽃, 다형성이다. 

사실 다른 객체지향 개념 공부하면서 이것도 다형성 아닌가? 저것도? 이런느낌을 받았다. 그만큼 다형성이 어디에서나 쓰이고 강조되는 것이라 느꼈다.

### 다형성이란?
---

'다형성이란 프로그램 언어 각 요소들(상수, 변수, 식, 객체, 메소드 등)이 다양한 자료형(type)에 속하는 것이 허가되는 성질을 가리킨다'가 사전적 의미다.

<img width="500" alt="스크린샷 2023-07-14 오후 1 09 28" src="https://github.com/YongNyeo/TIL/assets/109174778/56197f78-a4f3-4f0f-a7df-0fa715ea95e6">

추상화 설명할때 썼던 김영한 선생님 강의다..ㅎ

운전자는 자동차라는 인터페이스에(k3,아반떼,테슬라의 공통적인 부분을 집약한)의존하고 있다. 따라서 차가 무엇이고, 그 내부가 어떻게 바뀌던지간에 운전자는 운전이 가능하다.

해당 예시를 통해 코드를 짜보려한다.

### Driver 클래스

    public class Driver {
      private Car car;  // 💡 운전자는 구체화된 차가 아니라 인터페이스에 의존하고있다. 어떤 차를 운전할지는 나중에 자유롭게 결정할 수 있다.
      private String name;
      private int age;
      public Driver(Car car, String name, int age) {
        this.car = car;
        this.name = name;
        this.age = age;
      }
      public Car getCar() {
        return car;
      }
    }

### Car인터페이스

    public interface Car {
      String axel = "axel";
      void startCar();
      void openWindow();
    }

### Car 구현체인 K3
  
    public class K3 implements Car{

      @Override
      public void startCar() {
        System.out.println("차키를 꽂아 돌려서 시동");
      }
      @Override
      public void openWindow() {
        System.out.println("고리를 돌려서 수동으로 창문 내리기");
      }
    }
### Main함수

    Driver driver = new Driver(new K3(), "kim", 15);
    driver.getCar().startCar();
    driver.getCar().openWindow();

위와같이 코드를 짜게 되면 운전자는 컴파일시점이 아닌 런타임시점에 K3라는 차를 운전하는것을 선택할수 있다. 

---------
## 🔥 Overriding vs Overloading

### 1. 메서드 오버라이딩 
  오버라이딩이란?
  
  - 상위 클래스(또는 인터페이스)의 메서드의 body부분을 바꿔 사용하는것이다. 
   
  위의 예시에서는 K3가 Car의 메서드를 오버라이딩해서 사용하고 있다.

### 2. 메서드 오버로딩
  오버로딩이란?

  - 같은 클래스 내의 매개변수 타입,매개변수 개수,매개변수 순서중 하나가 달라야한다.
 
        public interface Car {
          String axel = "axel";
          void startCar();
          void startCar(String order);
          void openWindow();
        }
    Car의 인터페이스중 startCar()에 order 매개변수가 추가되어 다른 메서드가 되는것이 오버로딩이다.


이와같이 다형성을 사용하는 방법들을 알아보았다. 내가 든 예시는 정말 간단한 수준이라 쉽지만, 수십가지 수백가지의 인터페이스가 있는 현업에 적응하려면 기본을 탄탄히 해서 키워나가야 할것이다.

📘 Reference:

https://steady-coding.tistory.com/446

   
