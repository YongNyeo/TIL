# Http-Header 

http에서 전송시 데이터에대한 부가정보를 나타내기 위한 것들을 header라고 한다. header도 몇가지 파트로 나눌 수 있다.

---

## 표현 (서버에서 보내는 데이터에 대한 설명)

<img width="421" alt="스크린샷 2023-08-08 오전 10 29 07" src="https://github.com/YongNyeo/TIL/assets/109174778/ba4d96a1-2216-40bc-9d4d-b28ef938f700">

표현 데이터(본문)을 설명해주는 헤더들이다.

- Content-Type: 표현 데이터의 형식
- Content-Encoding: 표현 데이터의 압축 방식 (전달하는 곳에서 인코딩 헤더 추가,압축)
- Content-Language: 표현 데이터의 자연 언어 (ko,en...)
- Content-Length: 표현 데이터의 길이 (바이트 단위)

---

## 협상(콘텐츠 네고시에이션): 클라이언트가 선호하는 표현 요청

- 협상 헤더는 요청시에만 사용한다.

- Accept: 클라이언트가 선호하는 미디어 타입 전달
- Accept-Charset: 클라이언트가 선호하는 문자 인코딩
- Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
- Accept-Language: 클라이언트가 선호하는 자연 언어

 <img width="737" alt="스크린샷 2023-08-08 오전 11 14 15" src="https://github.com/YongNyeo/TIL/assets/109174778/931fff01-f8cd-44fc-80b9-00b4aceb723e">

<img width="739" alt="스크린샷 2023-08-08 오전 11 15 07" src="https://github.com/YongNyeo/TIL/assets/109174778/af60a8f8-8ddd-4c5b-9215-9b0f769c6fde">

적용 전과 적용 후를 다음과 같이 나눌 수 있다. 

---

## 전송 방식

- 단순 전송: 일반 전송
- 압축 전송: Content-Encoding을 통한 전송
- 분할 전송: 바이트 단위로 짤라 나눠서 데이터를 전송
- 범위 전송: 바이트의 범위를 지정하여 데이터를 전송

---

## 헤더 정보

- Referer: 이전 페이지 정보
- User-Agent: 클라이언트 애플리케이션 정보(통계정보, 요청에서 사용)
- Host
  - 하나의 IP 주소에 여러 도메인이 적용되어 있을 때 구분자

  <img width="743" alt="스크린샷 2023-08-08 오후 1 22 34" src="https://github.com/YongNyeo/TIL/assets/109174778/84f5b246-61ba-4f23-a332-54cf597a9b29">

- Location
  -  리다이렉션에 쓰이는 헤더
  -  201(created)에도 쓰이는데, 새로 생성된 리소스의 URI값이 Location에 입력된다.

- Allow
  - URL 경로는 있으나 Method가 잘못 넘어온경우  GET,POST 등...

---

기본적으로 HTTP 는 무상태(Stateless) 프로토콜이다.

클라이언트의 요청과 응답이 끝나면 서버 연결이 끊어지는데 , 결국 서버는 이전의 요청을 기억하지 못한다.

EX) 한 사용자가 쇼핑몰에서 로그인을 하고 웹브라우저를 끄면? 이전의 기억이 모두 사라진다. 그렇게되면 매번 새로 로그인을 해야하기에 사용성이 줄어들것이다.

## ⭐ 쿠키

이를 해결하고자 쿠키가 나왔다. 쿠키는 HTTP에서 하나의 헤더값이다.

    ex) set-cookie: sessionId=abcde1234; expires=Sat, 26-Dec-2020 00:00:00 GMT; path=/; domain=.google.com; Secure

1. 사용자가 이전에 요청한 값을 서버가 set-cookie 작업을 해주면
2. 사용자 웹 브라우저의 쿠키 저장소에 요청값이 저장된다.
3. 이제 새로 웹 사이트에 접근하더라도 쿠키 저장소에 있는 값들을 활용하여 재사용할수 있다.


<img width="733" alt="스크린샷 2023-08-08 오후 2 19 53" src="https://github.com/YongNyeo/TIL/assets/109174778/2982a2ae-5b9a-4d2e-82dc-27fd20501746">
<img width="734" alt="스크린샷 2023-08-08 오후 2 20 04" src="https://github.com/YongNyeo/TIL/assets/109174778/07940fc5-9b5f-4e32-9a5d-4efa38c20005">

---

### 쿠키 활용 값

- 생명주기

  - expire : Set-Cookie: expires=Sat, 26-Dec-2020 04:39:21 GMT  -> 만료일이 되면 쿠키 삭제
  - max-age : Set-Cookie: max-age=3600 (3600초) -> 시간(초)이 되면 쿠키 삭제
    
  - 세션 쿠키: 만료 날짜를 생략하면 브라우저 종료시 까지만 유지
  - 영속 쿠키: 만료 날짜를 입력하면 해당 날짜까지 유지

- 도메인

  - 명시: 명시한 문서 기준 도메인 + 서브 도메인 포함해서 쿠키를 활용 가능하다 ex) domain=example.org

  - 생략: 현재 문서 기준 도메인만 적용 -> 'domain=' 을 생략

  - 비슷한 기능으로 Path가 있다.  일반적으로 path=/ 루트로 지정하면 이 경로를 포함한 하위 경로 페이지에만 쿠키 접근

- 보안
  - Secure : 쿠키는 본래 http/https 구분X, Secure 이용시 https에만 사용
  - HttpOnly : XSS 공격 방지, 자바스크립트에서 접근 불가
  - SameSite : XSRF 공격 방지

### ❗ 주의점

- 쿠키 정보는 항상 서버에 전송됨
  -  네트워크 트래픽 추가 유발 가능성이 있다.
  -  최소한의 정보만 사용(세션 id, 인증 토큰)
  
- 탈취 가능성이 있음. 따라서 보안에 민감한 데이터는 저장하면 안됨(주민번호, 신용카드 번호 등등)


