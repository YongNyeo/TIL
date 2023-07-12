# 💡 객체지향 프로그래밍이란? 좋은 객체지향 프로그래밍은? 

그동안 이론으로'만' 알고있어서 남에게 설명하긴 부끄러운 수준이었다. 코드로도 정리해서 확실히 알고가자~~~!

---

지난 람다를 공부하면서 람다가 함수형 프로그래밍을 베이스라 배웠다. 프로그램이 함수로 이뤄져서 함수가 함수를 부르는 방식이라는 명제가 담긴 패러다임이다. 

이와 다르게 객체지향은 프로그램에있는 데이터가 객체단위로 이뤄져 객체간의 상호작용으로 본다. 이러한 객체지향 프로그래밍에는 4가지 특징이 있다. 

[캡슐화](#1._캡슐화)



# 1. 캡슐화

#### 위키피디아에는 다음과 같이 나와있다. ' 객체의 속성(data fields)과 행위(메서드, methods)를 하나로 묶고 실제 구현 내용 일부를 내부에 감추어 은닉한다 ' 

우리가 어떤 기능을 만든다고할때, 응집도는 높아야하고 결합도는 낮아야한다는 말이 자주나온다. 

기능을 수많은 모듈로 나누었을때, 모듈끼리의 의존도는 낮아야하고(결합도는 낮아야하고) 하나의 모듈 내부요소끼리 의존도는 높아야한다(응집도는 높아야한다). 그래야 하나의 모듈이 자유롭게 제 기능을 하는 존재가 된다.

#### 객체 단위에서 외부에서는 쉽게 건드리지 못하지만 내부에서는 쉽게 접근할수 있도록 도와주는것이 캡슐화의 목표다. 그렇다면 어떻게 할까? 

###  ▶️ 클래스나 메서드,함수에 대한 접근제어자 (private, public, protected, default) 로 구현가능하다.

-------

#### 예시1)

    public class kim {

      public int debt;
      
      lee lee = new lee();
      
      public void run() {  
      
        lee.getMoney();
      }
    }


    public class lee {

      public int money;

      public int getMoney() {   //돈이 얼마있는지 확인하는 메서드
        return money;
      }
    }

kim과 lee클래스가 있다. public 접근자이기때문에 kim 씨가 lee씨의 내부 요소를 확인할수 있다.

#### 예시2)

money의 접근제어자를 private로 바꾼다면? kim씨가 내부요소를 직접적으로 확인할수 없어진다.

그렇다면 이것이 은닉이 잘 된 코드라고 볼수있나? 그렇지 않다. getMoney()라는 메서드명을 보고 moeny라는요소를 확인할수 있기때문에 캡슐화 되지 않았다고 볼수있다.

#### 예시3)

    public boolean isRich(int money) {
        //로직
        return true;
    }

이번엔 lee클래스에 isRich라는 부자 판별 함수를 만들었다. 함수명도 괜찮다. 하지만 이것 또한 은닉이 잘 됐다고 볼수 없다. 

바로 파라미터로 money라는 내부 요소를 노출시켜야 하기때문이다.

#### 예시4)

    public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;
    
    private MovieType movieType;
    private Money discountAmount;
    private double discountPercent;
    
    public MovieType getMovieType() { ... }
    public Money calculateAmountDiscountedFee() { ... }
    public Money calculatePercentDiscountedFee() { ... }
    public Money calculateNoneDiscountedFee() { ... }
    }

위 코드는 예시 1,2,3에서 보여준 문제점은 없다. 하지만 calculate~ 라는 3가지 메소드의 존재로 인해 3가지의 할인정책이 있다는 정보를 주게 되면서 캡슐화가 되지 않았다.  

해결하려면 할인정책을 다른 객체로 분리해서 따로 구현해주는게 옳은 방법이다.

#### 📖 소감: 어떤것들을 지켜야 캡슐화를 지킬수 있는지 알아보았다. 그동안 캡슐화에 대해 이론적으로만 생각하고 내가 제대로 지키고있는게 하나도 없었다 ... ㅋㅋㅋㅋㅋ창피하지만 지금이라도 알았으니 다행이다. 앞으로 코드 짤때는 꼭 캡슐화를 지키기 위한 고민을 해야겠다.

    
