# 💡 Enum 이란?
Enum은 Enumeration 의 약어로 '열거'라는 의미를 갖는다. 보통 상수를 표현하고자할때 사용한다.

---

먼저 Enum을 사용하지 않은 상황을 생각해보자.

_가장 원초적인 방법으로, type 변수를 통해 전달한다. 변수를 통해서 해당 변수가 어떤 의미를 갖고있는지 알수 없고 주석을 통해서만 알 수 있다._

     public class EnumExample {
        public static void main(String[] args) {
            /*
             * 1.사과
             * 2.바나나
             * 3.멜론
             */
            int type = 1;

            switch (type) {
                case 1:
                    System.out.println("사과입니다.");
                    break;
                case 2:
                    System.out.println("바나나입니다.");
                    break;
                case 3:
                    System.out.println("멜론입니다.");
                    break;

            }
        }
    }

---

_두번째 방법으로는 주석대신 static 변수를 이용한다. 주석없이 각 변수가 어떤 의미인지 파악할수 있지만 상수가 무수히 많아지면서 가독성이 떨어지거나, 중복되는 변수가 등장시 변수 중복 문제 생긴다._

     public class EnumExample {


      
        //과일
        private final static int APPLE = 1;
        private final static int BANANA = 2;
        private final static int MELON = 3;

        //회사
        private final static int GOOGLE = 1;
        // private final static int APPLE = 2; --> APPLE 중복됨
        private final static int MS = 3;

        public static void main(String[] args) {

            int type = 1;

            switch (type) {
                case 1:
                    System.out.println("사과입니다.");
                    break;
                case 2:
                    System.out.println("바나나입니다.");
                    break;
                case 3:
                    System.out.println("멜론입니다.");
                    break;

            }
        }
    }
    
---

_interface를 새로 생성해서 변수 중복문제를 해결하고, 변수를 분류해서 정리함으로써 문제점을 해결해 줄 수 있다._

    interface FRUIT{
        int APPLE = 1; int BANANA = 2; int MELON = 3;
    }

    interface COMPANY{
        int APPLE = 1; int GOOGLE = 2; int MS = 3;
    }
    
---

_하지만 이러한 상수끼리 비교를 할때 컴파일에러로 잡지 못한다._

    if (FRUIT.APPLE == COMPANY.APPLE) {
        System.out.println("same");
    }

_이것은 직접 class를 생성함으로써 해결할 수 있다. 다른 클래스 타입끼리 비교가 불가능하기 떄문이다._

    class FRUIT{
        public static final FRUIT APPLE = new FRUIT();
        public static final FRUIT BANANA = new FRUIT();
        public static final FRUIT MELON = new FRUIT();
    }
    
<img width="160" alt="스크린샷 2023-08-15 오후 10 57 40" src="https://github.com/YongNyeo/TIL/assets/109174778/2445feb9-2fc6-4ab3-b4db-0ca6b1903ff0">

 ---
 
_하지만 이것또한 문제가 있는데, switch 문에서는 사용하지 못한다. switch문에 들어갈수 있는 데이터 타입에 제한이 있기 때문에 다른 방법을 생각해봐야하는데, 그 해결책이 ENUM이다._


    enum  FRUIT{
        APPLE,BANANA,MELON
    }

    public static void main(String[] args) {

        FRUIT type = FRUIT.APPLE;

        switch (type) {
            case APPLE:
                System.out.println("사과입니다.");
                break;
            case BANANA:
                System.out.println("바나나입니다.");
                break;
            case MELON:
                System.out.println("멜론입니다.");
                break;
        }
    }

코드도 훨씬 짧아지고, 가독성도 훨씬 좋아졌다. Enum을 더 활용하자면, 멤버변수,생성자,메서드를 가질 수 있다.


    enum FRUIT{
        APPLE("RED"),BANANA("YELLOW"),MELON("GREEN");


        private String color;

        FRUIT(String color) {
            this.color = color;
        }

        String getColor() {
            return this.color;
        }
    }

#### 이전부터 써왔던 코드이거나, 코드를 여러곳에서 묶을때 다른 형태인 경우가 있을 수 있다. 아래 코드가 그 예시인데, Yes,1,true는 모두 긍정 신호를 뜻하는데 Enum을 활용해 하나의 값으로 묶을 수 있다.

    enum  YesOrNo{
        YES("1",true), NO("0",false);

        private String YS;
        private boolean TF;

        YesOrNo(String YS, boolean TF) {
            this.YS = YS;
            this.TF = TF;
        }
    }



---

📘 Reference
- https://www.youtube.com/watch?v=vFDsVdaZeao
- https://www.nextree.co.kr/p11686/
- https://techblog.woowahan.com/2527/
