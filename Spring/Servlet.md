# Servlet
그동안 내가 개발하는데 도움이 되니까~~라는 안일한 생각으로 써왔는데, 가지치기처럼 점점 많은 것에 대해 깊이 있게 공부하려다보니

결국엔 기본이 없어서 이해가 안가는것이 너무 많았다. Servlet뿐 아니라 서버가 전체적으로 어떻게 작동하는지에 대해 정리하고 넘어가자.

---

## 💡 Servlet이 생겨난 이유 (feat: 웹 서버? 웹 애플리케이션 서버?)


<img width="643" alt="스크린샷 2023-08-16 오후 2 19 44" src="https://github.com/YongNyeo/TIL/assets/109174778/7840e5dd-f3ed-4863-8375-165ee409a43b">

아주 옛날에는 클라이언트가 서버에 요청할 때, 그리 큰 요청을 하지 않았다. 요청에 대해 다른 작업 필요없이 딱 화면(정적 리소스)만 보여주면 됐다.

---

<img width="637" alt="스크린샷 2023-08-16 오후 2 21 07" src="https://github.com/YongNyeo/TIL/assets/109174778/bc5e12f1-0f4c-40fa-b1f8-3c6512effe13">

하지만 점점 인터넷이 발달되면서 동적인 요청이 생겨나기 시작했다. 더 이상 화면만 보여주는 정적리소스만으로는 해결이 불가능했다.

---
<img width="638" alt="스크린샷 2023-08-16 오후 2 21 57" src="https://github.com/YongNyeo/TIL/assets/109174778/5828e736-ff91-427c-80b4-2445f8ed37e8">

그래서 정적 리소스만 보여주는 Web Server 와 동적 리소스를 처리할 수 있는 Web Application Server로 구분하였다. 

정적 리소스만 필요한 상황이라면 웹 서버 선에서 처리하고, 동적 리소스까지 필요하다면 Web Application Server(WAS)로 들어갔다.

---
이제 WAS가 어떻게 돌아가는지 한번 알아보자.

현재 모든 통신이 HTTP로 이뤄지는데, 통신할 때 이용하는 HTTP 패킷 구조는 생각보다 복잡해서 개발자가 그때마다 헤더와 바디를 까서 분석하기엔 너무 비효율적이었다. 

__이것을 도와주는것이 Servlet이다__

<img width="637" alt="스크린샷 2023-08-16 오후 2 27 21" src="https://github.com/YongNyeo/TIL/assets/109174778/e7446c42-a327-4cb9-aae8-e1246e744592">

_Servlet은 우리가 비즈니스 로직에만 집중할 수 있도록 하는 도구다_

---

<img width="630" alt="스크린샷 2023-08-16 오후 2 37 20" src="https://github.com/YongNyeo/TIL/assets/109174778/97423590-a370-40ce-8b9e-9e7d99ed0ef5">

전체적인 구조는 WAS안에 Servlet Container가 있는 구조다. 

동적 URL요청이 들어오면 request,response를 이용해 Servlet을 호출한다. 

<img width="627" alt="스크린샷 2023-08-16 오후 2 41 12" src="https://github.com/YongNyeo/TIL/assets/109174778/c1cc3f9a-9918-40cf-9a1e-d6fe9cb77ad7">

_중요한 점은 Servlet Container는 싱글톤 방식이다보니 하나의 서블릿 객체는 공유 자원이 된다. 따라서 쓰레드 풀을 설정해서 요청이 있을때마다 적절하게 쓰레드를 사용하는 방식이다._

---

<img width="590" alt="스크린샷 2023-08-16 오후 2 31 17" src="https://github.com/YongNyeo/TIL/assets/109174778/2b4b8f97-0edf-4aa4-87ad-8f67859bed1b">

service()는 Servlet에서 주요 로직을 실행시켜주는 메서드다. 

        else if (method.equals(METHOD_POST)) {
            doPost(req, resp);

        } else if (method.equals(METHOD_PUT)) {
            doPut(req, resp);

        } else if (method.equals(METHOD_DELETE)) {
            doDelete(req, resp);

        } else if (method.equals(METHOD_OPTIONS)) {
            doOptions(req, resp);

        } else if (method.equals(METHOD_TRACE)) {
            doTrace(req, resp);

service()는 http메서드에 따라 다르게 실행되도록 구현되어있다. 

---

## Dispather Servlet

서블릿이 어떤 역할을하고 서블릿까지 어떻게 호출되는지 알아보았다. 

서블릿을 계층별로 기능에 따라 나누게 되는데, 이때 가장 앞에서 받아주는 역할을 디스패쳐 서블릿이 하게 된다.

<img width="819" alt="스크린샷 2023-08-16 오후 3 44 11" src="https://github.com/YongNyeo/TIL/assets/109174778/5ad20ab8-ff73-4853-88a9-ca535a03ade1">

서블릿의 진행 순서는 다음과 같다.

1. 들어온 URL에 따라 핸들러(컨트롤러)를 찾는다.
2. 핸들러 어댑터를 찾는다.
- _여기서 핸들러 어댑터는 이미 찾은 핸들러를 어떻게 다룰 것인지를 정하는 단계다. 핸들러 어댑터를 도입함으로써 확장에 더 유연한 설계가 되었다._
4. 찾은 핸들러를 들고 핸들러 어댑터로 간다.
5. 핸들러(컨트롤러)로 이동하여 비즈니스 로직을 처리한다.
6. 로직 처리 후, Model값과 View를 리턴한다.
6 - 7. view resolver 호출해서 View 파일을 찾아간다.
8. model값과 함께 html 파일로 넘어간다. 

---

## 그래서 WAS는 뭐지?

<img width="1056" alt="스크린샷 2023-08-16 오후 4 03 11" src="https://github.com/YongNyeo/TIL/assets/109174778/91c79513-44d9-4a0d-9886-a29866ba1a1b">

- 데이터베이스 연동
- 트랜잭션 관리
- 쓰레드 관리
- 세션 관리  
- 보안 등 다양한 웹 애플리케이션의 요구사항을 처리하는 기능

다시 WAS를 펼쳐보면, 서블릿 컨테이너를 포함하고 서블릿 객체로 로직을 구현하는것이 매우 중요한 역할이지만 그 외에도 위와 같은 개발 환경설정에도 큰 도움을 준다.

---

📘 Reference
- https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard
- https://www.youtube.com/watch?v=calGCwG_B4Y&t=532s
- https://mangkyu.tistory.com/18
