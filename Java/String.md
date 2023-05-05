# 💡 Scanner VS BufferedReader
자바에서 어떤 데이터를 읽을때 대부분 InputStream을 이용해 입력받고 Scanner와 BufferedReader를 사용해서 읽습니다.

알고리즘을 풀다가 Scanner를 썼는데 시간초과가 나와서 BufferReader를 이용해서 풀었는데 시간 단축이 많이 되는것을 보고,

이 원리가 궁금해서 알아보게 되었습니다.

#### Scanner 
    Scanner sc = new Scanner(System.in);
    String input = sc.nextLine();

#### BufferedReader
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    String input = br.readLine();
    
### 비슷해보이는데 왜 BufferReader가 빠를까 ❓

차이점 ❗
- Scanner의 버퍼사이즈는 1바이트(1024)이고, BufferedReader가 갖는 버퍼사이즈는 8바이트입니다.

  BufferedReader는 버퍼를 모아서 한번에 처리하기 때문에 Scanner보다 성능면에서 더 빠르게 효율적으로 처리할수 있습니다.
  
  또한, Scanner는 입력을 읽는 과정에서 내부에서 정규 표현식 적용, 입력값 분할, 파싱 과정 등을 거치기 때문에 속도가 비교적 느립니다.

 
<img width="842" alt="스크린샷 2023-05-05 오후 9 03 05" src="https://user-images.githubusercontent.com/109174778/236453985-aa22d627-5ab4-45c2-a692-07bd77e310d7.png">
<img width="400" alt="스크린샷 2023-05-05 오후 9 07 39" src="https://user-images.githubusercontent.com/109174778/236453996-f86f5efd-4930-4921-aafb-2fb3e7c94533.png">

 - Scanner는 파라미터에 InputStream 타입이 Notnull로 정의되어있기 때문에, BufferedReader와 다르게 InputStreamReader가 필요하지 않습니다.
 - Scanner는 System.in을 받아올때 try-catch문을 사용하기에 따로 예외처리가 필요하지 않지만, BufferedReader에서는 따로 IOException 예외처리가 필요합니다
