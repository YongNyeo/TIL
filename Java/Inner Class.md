# inner class

class 내부에 클래스를 사용하는 이유는 뭐고, 어떻게 사용해야하는지 알아보자.

---

## 💡 inner class는 왜 사용하는걸까?

우선 내 생각부터 말하자면, inner 클래스를 가독성이나 유지보수면에서 관리하기 쉬울것 같아서 inner class에 대해 알아보기 시작했다. 


### 1. 클래스를 논리적으로 그룹화한다

- 클래스가 여러 클래스와 관계를 맺지 않고 하나의 특정 클래스와만 관계를 맺는다면, 클래스를 새로 작성하는 것이 아닌 내부 클래스로 작성할 수 있다.
- 이 경우 내부 클래스와 외부 클래스를 함께 관리하는 것이 가능해 편리해질 뿐 아니라, 내부 클래스로 인해 새로운 클래스를 생성하지 않아도 되므로 패키지를 간소화할 수 있다.


### 2. 기존보다 더 많은 캡슐화의 적용이 가능하다

- 외부 클래스의 멤버를 private으로 선언하면 외부에서는 getter/setter 메서드를 사용하지 않는 한 접근이 불가능하다.
-  하지만 이 클래스 안에서 내부 클래스를 생성할 경우, 내부 클래스는 private 멤버들에 대해 아무런 제한 없이 접근이 가능하다.
-  또한, 외부 클래스의 private 멤버를 사용하면서 자기 자신의 접근 제한 역시 private으로 설정해 외부로부터의 접근을 차단할 수 있다. 

이것이 내부 클래스로 더 많은 캡슐화의 적용, 즉 외부로부터의 데이터 보호가 가능한 이유이다.

### 3, 가독성이 좋고 유지 관리가 쉬운 코드 작성이 가능하다

- 내부 클래스를 작성하는 경우, 클래스를 따로 작성하는 경우보다 상위 클래스에 더 가깝게 위치하게 된다.
- 따라서 시각적으로 읽기가 편해질 뿐 아니라 유지보수에 있어 이점을 가진다.



---

class 안의 class를 두가지로 나눠 생각해보자.

__💡1. 인스턴스(일반) inner 클래스__
- 클래스의 멤버 변수 취급을 받아서 인스턴스 클래스라 부른다.
- 인스턴스 클래스 내부에는 static 변수 사용 불가
- 주로 외부클래스와 내부 클래스의 관련된 작업을 진행할 목적으로 사용된다.

      class Outer{

          int k = 50;
          class inner{
              //static int a = 100; -불가능
              int a = 100;
              public void getK(){
                  System.out.println(k); //외부 인스턴스에 접근 쉽게 가능 -> 외부 클래스와 이너클래스가 긴밀하게 연결되어있다.
              }
          }
      }


__💡2. static inner 클래스__
- static이 붙은 클래스로, 일반 static 필드 변수나 static 메서드랑은 다르게 생각해야한다.
- static inner 클래스에는 일반 멤버, static멤버 모두 선언 가능하다.
- _static inner클래스는 static 필드와 다르게, 여러번 생성하면 여러번 객체가 생성된다. 즉, 공유자원이 아니다._
- _외부 클래스의 별도 생성없이 static inner 클래스만 생성 가능하다._  

      class PocketBall {
      int size = 100;
      static int price = 5000;

      static class PocketMonster {
        static String name = "이상해씨";
        int level = 10;

        public static void getPoketMember() {
            // 외부 클래스 인스턴스 맴버 접근 불가능
            // System.out.println(size);
            
            // 외부 클래스 스태틱 멤버 접근 가능
            System.out.println(price);
			
            // 내부 클래스 멤버도 스태틱 맴버만 접근 가능
            System.out.println(name);
            // System.out.println(level);
        }
      }
      }
  
마지막 두개의 특징이 가장 중요한데, 코드를 보며 이해하는것이 더 빠를 것 같다.

---

    public class Main {
    // 스태틱 필드 변수
    static Integer num = new Integer(0);

    // 내부 인스턴스 클래스
    class InnerClass{
    }

    // 내부 스태틱 클래스
    static class InnerStaticClass{
    }
    
    public static void main(String[] args) {

        // 스태틱 필드 변수는 유일해서 서로 같다
        Integer num1 = Main.num;
        Integer num2 = Main.num;
        System.out.println(num1 == num2); // true


        // 생성된 내부 클래스 인스턴스는 서로 다르다
        Main.InnerClass inner1 = new Main().new InnerClass();
        Main.InnerClass inner2 = new Main().new InnerClass();
        System.out.println(inner1 == inner2); // false


        // 생성된 내부 스태틱 클래스 인스턴스는 서로 다르다
        Main.InnerStaticClass static1 = new InnerStaticClass();
        Main.InnerStaticClass static2 = new InnerStaticClass();
        System.out.println(static1 == static2); // false
      }
    }

inner static는 외부클래스의 생성이 필요없지만, inner instance class는 외부 클래스 생성후에 생성되기 때문에 둘 사이의 참조값이 생기게 된다.

inner instance class를 생성하면, inner static class 사용할것을 인텔리제이(ide)에서도 경고창을 띄우며 권고한다.


- 💡static inner class를 사용해야하는 이유

>이펙티브 자바
>
>멤버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 무조건 static을 붙여서 정적 멤버 클래스로 만들자.
>
>static을 생략하면 바깥 인스턴스로의 숨은 외부 참조를 갖게 된다.
>
> 앞서도 얘기했듯 이 참조를 저장하려면 시간과 공간이 소비된다. 더 심각한 문제는 가비지 컬렉션이 바깥 클래스의 인스턴스를 수거하지 못하는 메모리 누수가 생길 수 있다는 점이다.
>
>참조가 눈에 보이지 않으니 문제의 원인을 찾기 어려워 때때로 심각한 상황을 초래하기도 한다.
>
1. 참조값을 담아야 하기 때문에, 인스턴스 생성시 시간적, 공간적으로 성능이 낮아진다.
2. 외부 인스턴스에 대한 참조가 존재하기 때문에, 가비지 컬렉션이 인스턴스 수거를 하지 못하여 메모리 누수가 생길 수 있다.

결론 : 내부클래스에서 외부 클래스의 인스턴스에 참조할일이 없다면 무조건 static 클래스로 사용하자.

---

📘 Reference
- https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EB%82%B4%EB%B6%80-%ED%81%B4%EB%9E%98%EC%8A%A4Inner-Class-%EC%9E%A5%EC%A0%90-%EC%A2%85%EB%A5%98
- https://romcanrom.tistory.com/59
- https://lasbe.tistory.com/57
