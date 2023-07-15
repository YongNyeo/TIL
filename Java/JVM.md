# 😲 JVM (Java Virtual Machine) 

자바를 계속 쓰고있음에도 jdk랑 ide만 깔았는데 어떻게 작동하는지 모르는 상태로 쓰고있었다. 

서버 개발자라면 최소한 내가 쓰는 프로그램이 어떻게 돌아가야하는지는 파악해야한다고 느꼈다. 

--- 
### 🔥 jdk
우선 자바를 설치할때 우리는 jdk (java develpment kit)의 약자로 자바로 개발을 할때 필요한 기능들을 킷으로 묶어놓은것이다.

- javac : 자바 컴파일러.  ~.java 파일을 jvm에서 사용하기 위해선 ~.class(자바 바이트코드) 파일로 바꿔야하는데, 그 역할을 한다.

- java : javac가 만든 ~.class 클래스파일을 해석 및 실행하도록 도와준다.(class loader라고 한다)

- jdb : java debugging tool

- jar : 서로 관련있는 클래스 라이브러리들과 리소스를 하나의 파일로 묶어주는 툴

- jre : java runtime environment 의 약자로, 이 JRE는 자바 가상 머신(Java Virtual Machine, JVM)과 프로덕션 환경에서 제공되는 모든 클래스 라이브러리 및 국제화나 IDL 라이브러리와 같이 개발자들에게 도움이 되는 라이브러리들로 구성된다.


# 🔥 JVM 

<img width="290" alt="스크린샷 2023-07-14 오후 4 54 01" src="https://github.com/YongNyeo/TIL/assets/109174778/7aec1e49-6a8f-45f3-9c35-601202c09902">

우리가 자바를 어느 OS든지 사용할 수 있는 이유는 운영체제별로 존재하는 JVM 덕이다.

- 처음에 자바컴파일러(javac)가 ABC.java 파일을 컴파일해서 JVM에서 해석할 수 있는 ABC.class(자바 바이트코드)파일로 바꿔준다. 

<img width="532" alt="스크린샷 2023-07-14 오후 5 04 50" src="https://github.com/YongNyeo/TIL/assets/109174778/6d7bca04-a7a4-4756-b3b2-7ada06ba168c">

자 그렇다면 지금부터 JVM을 까보자

<img width="476" alt="스크린샷 2023-07-14 오후 5 04 12" src="https://github.com/YongNyeo/TIL/assets/109174778/f3078503-545d-478b-a086-b08c17fe1aec">

- Class Loader: 바뀐 class파일이 ClassLoader에 의해 운영체제로 부터 할당받은 메모리영역인 jvm내의 Runtime Data Area(자바 메모리 영역)에 올라간다.

- Excution Engine: class loader에 의해 메모리에 적재된 클래스들을 기계어로 변경해 명령어 단위로 실행하는 역할을 한다.
  - 명령어를 하나 하나 실행하는 인터프리터(Interpreter)방식이 있고 JIT(Just-In-Time) 컴파일러를 이용하는 방식이 있다.
  - JIT 컴파일러는 적절한 시간에 전체 바이트 코드를 네이티브 코드로 변경해서 Execution Engine이 네이티브로 컴파일된 코드를 실행하는 것으로 성능을 높이는 방식이다.
  - JIT 방식은 캐시에 보관하기때문에 한번 컴파일하는 과정은 인터프리터방식보다 느리지만, 여러번 사용할거라면 JIT방식이 유리할것이다.
    
- Garbage Collector : 더 이상 사용되지 않는 데이터가 할당되어있는 메모리를 해제시켜주는 장치다. 주로 동작하는 대상은 Heap영역 내의 객체 중에서 참조 되지 않은 데이터다.

### RuntimeDataArea 내부

- RuntimeDataArea에 하나의 쓰레드가 생성되면 그 안에 생기는 요소들이다.
  -  Java stack :java 메서드 실행시 사용하는 지역 변수, 파라미터, 리턴 값, 연산에 사용되는 임시 값등이 생성되는 영역이다.
  -  Native method stacks : 바이트코드가 아닌 기계어로 작성된 프로그램을 실행시키는 영역이다. JNI(java native interface)를 통해 바이트코드로 전환하여 저장하게 된다.
  -  Pc register: thread마다 하나씩 존재하는 공간으로 thread가 어떤부분을 어떤 명령으로 실행해야할지 기록하는 부분이다. JVM 명령의 주소를 갖는다.
    
- Heap: new 키워드로 생성된 객체와 배열이 생성되는 영역이다. 메소드 영역에 로드된 클래스만 생성이 가능하고 Garbage Collector가 참조되지 않는 메모리를 확인하고 제거하는 영역이다.
- Method area :클래스 멤버 변수의 이름, 데이터 타입, 접근 제어자 정보같은 필드 정보와 메소드의 이름, 리턴 타입, 파라미터, 접근 제어자 정보같은 메소드 정보, Type정보(Interface인지 class인지), Constant Pool(상수 풀 : 문자 상수, 타입, 필드, 객체 참조가 저장됨), static 변수, final class 변수등이 생성되는 영역이다.


📘 Reference
- https://d2.naver.com/helloworld/329631
- https://jeong-pro.tistory.com/148
