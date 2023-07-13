#  상속 

#### 객체 지향 프로그래밍(OOP)에서, 상속(inheritance)은 객체들 간의 관계를 구축하는 방법이다. 말로하는것보다 코드로 알아보자


    public class Kim {
      private String name;

      public Kim(String name) {
        this.name = name;
      }

      public void eat() {
        System.out.println(name+"씨는 밥 먹는다");
      }
      public void sleep() {
        System.out.println(name+"씨는 잠을 잔다");
      }
    }

    public class Lee {

      private String name;

      public Lee(String name) {
        this.name = name;
      }

      public void eat() {
        System.out.println(name+"씨는 밥 먹는다");
      }
      public void sleep() {
        System.out.println(name+"씨는 잠을 잔다");
      }
    }


     public static void main(String[] args) throws IOException {
        Kim kim = new Kim("김");
        Lee lee = new Lee("이");

        kim.eat();
        kim.sleep();
        lee.eat();
        lee.sleep();
    }
상속을 이용하지 않고 Kim씨와 Lee씨가 밥먹고 자는것을 구현하려면 이런 중복코드가 나온다. 

자 그럼 상속을 이용해보자.

    public class Person {
      private String name;

      public Person(String name) {
        this.name = name;
      }
      public void eat() {
        System.out.println(name+"씨는 밥 먹는다");
      }
      public void sleep() {
        System.out.println(name+"씨는 잠을 잔다");
      }
    }

    public class Kim extends Person{
      public Kim(String name) {
        super(name);
      }
    }

    public class Lee extends Person{
      public Lee(String name) {
        super(name);
      }
    }

    public static void main(String[] args) throws IOException {
        List<Person> people = Arrays.asList(new Kim("김"), new Lee("이"));
        for (Person person : people) {
            person.eat();
            person.sleep();
        }
    }

#### Person이라는 클래스에 상속을 받아 중복코드를 줄일수 있다.

    public class Lee extends Person{
      public Lee(String name) {
        super(name);
      }
      @Override
      public void eat() {
        System.out.println("이씨는 밥을 못먹는 상태입니다.");
      }
    }
    
#### 다음과 같이 오버라이딩으로 재사용함으로써 바꿔줄수도 있다. 이런 방법의 코드는 다형성을 극대화시켜줄수 있다.

#### ❗ ... 둘 사이의 관계가 너무 결합된거 아니야?
상속은 결국 상위 클래스와 하위클래스의 결합으로 쉽게 코드를 재사용하는것이다. 쉽게 재사용하는만큼 두 클래스 사이의 의존도(결합도)는 올라갈수밖에 없게 된다. 이렇게 되면 캡슐화(은닉)을 깨버릴수있다.

부모클래스를 변경하게되면 모든 자식클래스를 영향을 미치게된다. 이렇게 되면 많은 테스트와 찾기어려운 버그를 만들 수 있다는 많은 문제점이 있다.

###### 어쩌자는거냐말이다?!

답은 상속을 '잘'쓰거나 합성(compostion)을 사용하는 것이다. 

#### 상속을 잘 쓰는경우는 사실 부모-자식의 관계에서  자식이 부모와 거의 같은 경우에 사용할 수 있다.

SOLID원칙의 Liskov Subsustitution Principle을 따르면, 자식클래스가 부모 클래스를 대체할수 있는 경우에만 상속을 사용해야한다고 나와있다. 

------


### ⭐ 합성(compostion)이란?

- 객체가 다른 객체의 참조자를 얻는 방식으로 런타임시에 동적으로 이뤄진다.
- 컴파일 시점에 관계가 이미 정해져서 변경될수 없는 상속과 다르게, 런타임 시점에 동적으로 관계가 정해지면서 유연하게 관계를 가질 수 있다.

- 수직적 관계가 아닌 수평적 관계인만큼 클래스간 관계를 파악하는데 있어 시간이 걸린다.

  예시)

      public class Man {
        public void move() {
          System.out.println("걷는다");
        }

        public void eat() {
          System.out.println("먹는다");
        }
      }

      class SuperMan {
        private final Man man = new Man();
          public void move() {
            man.move();
          }

        public void eat() {
          man.eat();
        }

        public boolean canTouchKryptonite(){
          return false;
        }

        public void fly() {
          System.out.println("날아간다.");
        }
      }

superman은 man을 상속받지 않고, man객체를 필드로 두고 man이 필요할때 메서드를 가져다 쓰는 방식으로 사용하면 되는것이다. 은닉을  깔끔하게 지켜낼수 있다.

------
결론: 상속을 쓰지 말라는것이 아니다. 객체지향에서 추구하는 다형성을 지키고자할때는 상속이 문제가 있을수 있지만 상속이 편한 상황도 있는만큼 그 상황에 맞춰 적절히 가져다 써야한다.

나 또한 무언가를 가져다쓰기전에 왜 가져다써야하는지 고민하는 습관을 들여야겠다.

📘 Reference: https://tecoble.techcourse.co.kr/post/2020-05-18-inheritance-vs-composition
https://unluckyjung.github.io/oop/2021/03/15/Inheritance-Coposition/




