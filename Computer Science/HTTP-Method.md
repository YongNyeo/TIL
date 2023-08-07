# Http란?

>HTTP(HyperText Transfer Protocol)는 WWW 상에서 정보를 주고받을 수 있는 프로토콜이다.
>
> HTML 문서를 주고받는 데에 쓰인다. 주로 TCP를 사용하고 HTTP/3부터는 UDP를 사용하며, 80번 포트를 사용한다.
>
>HTTP는 클라이언트와 서버 사이에 이루어지는 요청/응답(request/response) 프로토콜이다.
>
>_예를 들면, 클라이언트인 웹 브라우저가 HTTP를 통하여 서버로부터 웹페이지(HTML)나 그림 정보를 요청하면, 서버는 이 요청에 응답하여 필요한 정보를 해당 사용자에게 전달하게 된다. 이 정보가 모니터와 같은 출력 장치를 통해 사용자에게 나타나는 것이다._
>
>HTTP를 통해 전달되는 자료는 http:로 시작하는 URL(인터넷 주소)로 조회할 수 있다.


---

<img width="667" alt="스크린샷 2023-08-07 오후 4 18 07" src="https://github.com/YongNyeo/TIL/assets/109174778/fdbe157b-5c9b-43e6-bedb-963218608987">

### ❗ Http 에서 중요한건?

정보를 주고 받을때 각자의 요구사항을 확실히 만드는것이다. 이때 각 웹 서버는 자신만의 고유 리소스(URI)를 갖고있다. 

클라이언트는 어떤 리소스가 필요한지, 서버는 어떤 리소스를 어떻게 줄건지 응답하는 방법이 필요하다. 

__따라서 URI 의 설계가 매우 중요하다__

대부분 그 방법으로 URL(Uniform Resource Locator - 자원의 위치)을 사용하고 있다.


- Http Method
- Http 헤더

이 두가지를 이용하면 서로의 요구사항을 확실히 나타낼 수 있다.

---

## 💡Http Method

URI 설계시 중요한 것 중 하나는 리소스와 행위의 분리다. http Method를 통해

EX)

    localhost:8080/getMember/1   --> member 리소스와 add행위 분리 X

    localhost:8080/member/1 , http method = 'get'  --> member 리소스와 add행위 분리 O
    
- URI는 리소스 식별만!
- 행위는 Http method 이용! //  GET,POST,PUT,PATCH,DELETE 5가지가 대표적으로 사용하는 메서드다.

---

### GET

- 주로 리소스 조회할때 이용
- 서버에 전달하고 싶은 데이터는 query(쿼리 파라미터, 쿼리 스트링)를 통해서 전달
- 메시지 바디를 사용해서 데이터를 전달할 수 있지만, 지원하지 않는 곳이 많아서 권장하지 않음

<img width="804" alt="스크린샷 2023-08-07 오후 4 58 28" src="https://github.com/YongNyeo/TIL/assets/109174778/efe81359-ef6d-4e23-b7d9-1a6e7828968b">

왼쪽의 /members/100 , method='get'을 이용해 오른쪽의 자원을 얻고자하는 경우다.

<img width="755" alt="스크린샷 2023-08-07 오후 4 59 32" src="https://github.com/YongNyeo/TIL/assets/109174778/86e24ca1-a54f-42f9-8620-1287f93f87c2">

다음과 같이 클라이언트가 서버의 데이터를 얻고자할때 주로 사용한다.

---

### POST

- 요청 데이터 처리할때 이용
- 메시지 바디를 통해 서버로 요청 데이터 전달
- 서버는 요청 데이터를 처리
- 메시지 바디를 통해 들어온 데이터를 처리하는 모든 기능을 수행한다.
- 주로 전달된 데이터로 신규 리소스 등록, 프로세스 처리에 사용

<img width="741" alt="스크린샷 2023-08-07 오후 5 01 43" src="https://github.com/YongNyeo/TIL/assets/109174778/549de3fa-b143-4597-bd4d-31628e8c1290">

/members 의 URI로 내가 보내고자하는 데이터를 담아 method='post' 로 보낸다.

<img width="750" alt="스크린샷 2023-08-07 오후 5 04 47" src="https://github.com/YongNyeo/TIL/assets/109174778/31c60a3c-fbb6-4fee-ae01-f47a13791ced">

서버에 새로운 데이터가 저장되었고, 100번째 회원으로 확인하여 members/100 으로 처리해주었다.

<img width="748" alt="스크린샷 2023-08-07 오후 5 04 59" src="https://github.com/YongNyeo/TIL/assets/109174778/fdc41970-ad5a-4dc2-aa06-95e68649406e">

- HTML FORM에 입력한 정보로 회원 가입, 주문 등에서 사용
- 서버가 아직 식별하지 않은 새 리소스 생성
- 기존 자원에 데이터 추가
- 다른 메서드로 처리하기 애매한 경우
  - 예를 들어, JSON으로 조회 데이터를 넘겨야 하는데 GET 메서드를 사용하기 어려운 경우에도 사용
- URI에 POST 요청이 오면 요청 데이터를 어떻게 처리할지 리소스마다 따로 정해야 한다 -> 정해진 것이 없음

---

### PUT

- 리소스가 있으면 대체하고, 리소스가 없으면 생성 (==뎦어버림)
- ⭐ 클라이언트가 리소스를 식별(클라이언트가 리소스 위치를 알고 URI 지정) --> POST와 차이점

<img width="613" alt="스크린샷 2023-08-07 오후 5 22 44" src="https://github.com/YongNyeo/TIL/assets/109174778/51032e7e-4eea-4211-a578-457654ca85d7">

클라이언트가 보내는 URI를 보면 리소스위치(100)까지 알고있다.

<img width="575" alt="스크린샷 2023-08-07 오후 5 23 25" src="https://github.com/YongNyeo/TIL/assets/109174778/c58c0c4e-96bd-4724-ab8e-6334491e966a">

기존 리소스가 없는경우에는 POST처럼 만들어낸다.

주의점)

<img width="654" alt="스크린샷 2023-08-07 오후 5 24 18" src="https://github.com/YongNyeo/TIL/assets/109174778/5df24964-90c7-4ed1-9d73-03c2a99a0183">

리소스중 일부 필드만 바꾼다면?

<img width="577" alt="스크린샷 2023-08-07 오후 5 24 59" src="https://github.com/YongNyeo/TIL/assets/109174778/fcd67342-dbe0-47bd-b02d-c4d58810ec80">

완전히 덮어쓰기때문에 일부 필드가 사라진다.

---

### PATCH 

- 위의 리소스의 일부 필드를 바꿀때 나타나는 에러사항을 극복하기 위해 나온 PATCH

<img width="651" alt="스크린샷 2023-08-07 오후 5 26 21" src="https://github.com/YongNyeo/TIL/assets/109174778/e97ba874-3475-4e45-a173-24ed734c2e7d">

<img width="586" alt="스크린샷 2023-08-07 오후 5 26 41" src="https://github.com/YongNyeo/TIL/assets/109174778/98a4a58e-dee7-4288-a818-40c60f17b490">

일부 필드만 변경된다.

---

### DELETE

- 리소스를 삭제하기 위한 method 

<img width="616" alt="스크린샷 2023-08-07 오후 5 28 34" src="https://github.com/YongNyeo/TIL/assets/109174778/c5af0ac1-96b5-41ec-a962-c4e1ec4b0931">

<img width="518" alt="스크린샷 2023-08-07 오후 5 28 48" src="https://github.com/YongNyeo/TIL/assets/109174778/80fd7cbb-7824-44cd-bf2c-c299c9aea545">

---

📘 Reference
- https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard
