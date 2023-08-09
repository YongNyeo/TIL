Http header를 공부하면서 쿠키가 어떤 방식으로 이뤄지는지 알아보았다.

다만 로그인 정보같은 중요 정보는 단순 쿠키만으로 구현하기는 위험하기 때문에 다른 방식으로 구현해야함을 깨달았다.

![image (1)](https://github.com/YongNyeo/TIL/assets/109174778/e6810845-5711-4212-80b0-bb4b42b9b8ce)

미리 말하자면, 세션과 토큰도 쿠키의 기능을 사용해야한다.

# Session

세션방식의 큰 프로세스는 다음과 같다. 

<img width="617" alt="스크린샷 2023-08-08 오후 3 12 39" src="https://github.com/YongNyeo/TIL/assets/109174778/b8ac2b57-0ee6-4bee-9811-a1c26fca82a6">

1. 사용자가 로그인 요청
2. 서버에서 사용자 정보를 sessionId 값으로 바꾸어서 세션 저장소에 저장
    - 이때 세션 저장소는 memory 영역이다. disk 영역을 사용하게 되면 세션값을 확인하고 저장하는데 너무 많은 시간이 소요되기 때문에 비효율적임.
3. sessionId를 쿠키에 담아서 사용자에게 넘겨주면 쿠키저장소에 sessionId를 저장
4. 사용자는 쿠키 저장소에서 session 값을 가져와서 서버에 재접근
5. 서버에서는 헤더값으로 받은 sessionId와 세션 저장소의 값을 비교하여 검증

### 한계점

- 서버의 session저장소가 memory 영역을 사용하기 때문에, 세션을 저장하는데 한계가 있다.
- 따라서 사용자 수마다 다른 선택을 해야한다.


--- 

# Token 

토큰방식의 큰 프로세스는 다음과 같다.

<img width="642" alt="스크린샷 2023-08-08 오후 4 30 22" src="https://github.com/YongNyeo/TIL/assets/109174778/101ee461-4d36-4c4c-bef4-b50530df9d98">

세션의 한계점인 메모리 사용을 해결하고자, 토큰에서는 메모리를 사용하지 않는다.

1. 사용자가 로그인 요청
2. 서버에서 특정 알고리즘을 토대로 생성한 토큰(다른곳에선 못 풀고 오직 발급한 서버에서만 풀수 있다)을 넘겨준다.
3. 사용자가 토큰을 받아 쿠키저장소에 저장한다.
4. 웹 서버에 재접근시 토큰으로 서버에 접근한다.
5. 서버는 토큰 검증(알고리즘)

### 한계점

- 서버에서 토큰을 생성해 넘겨주고난 후부터 서버에서는 따로 관리하지 않기때문에 해당 토큰을 통제할 수 없다.
- 따라서 탈취당할 가능성이 더 높어진다. 이 점은 토큰 만료 기간을 짧게 설정하므로써 보완할 수 있다.

---

# Cache

그동안 쿠키,세션,토큰을 배웠는데 이것들의 목적은 사용자의 인증 과정에서 기존 정보를 재사용해서 간편하게 만드는데 목적을 두었다.

반면 캐시는 웹페이지가 빠르고,효율적으로 렌더링하는데 목적을 두고있다.

- 캐시 덕분에 캐시 가능 시간동안 네트워크를 사용하지 않아도 된다.
- 비싼 네트워크 사용량을 줄일 수 있다.
- 브라우저 로딩 속도가 매우 빠르다.

<img width="739" alt="스크린샷 2023-08-08 오후 5 47 14" src="https://github.com/YongNyeo/TIL/assets/109174778/8a3fd59c-a83c-49ce-9e38-845302e4545d">

<img width="742" alt="스크린샷 2023-08-08 오후 5 47 29" src="https://github.com/YongNyeo/TIL/assets/109174778/b40074b1-93c7-4aa9-b085-a4cc6a7e1140">


기본적인 캐시의 동작은 다음과 같다.
1. 특정 URI 리소스를 서버에 요청
2. 리소스가 만약 캐시 저장소(사용자 웹 브라우저에 있음)에 없다면 서버에서 받아온다.
3. 받아온 리소스를 캐시 저장소에 저장.

---

### ❓ 캐시 저장소에 저장되는 캐시는 항상 유효기간을 갖고있다. 그럼 유효기간이 초과된 캐시는 사용을 못하나?

<img width="743" alt="스크린샷 2023-08-08 오후 5 53 05" src="https://github.com/YongNyeo/TIL/assets/109174778/6bc78b49-c21e-454d-adc3-c6e158cce295">

다음은 찾는 캐시의 유효기간이 초과되어 다시 서버로 접근한 상황이다.

<img width="746" alt="스크린샷 2023-08-08 오후 5 52 46" src="https://github.com/YongNyeo/TIL/assets/109174778/9f6cf839-3387-4f7c-97bf-c2e2bea870c4">

서버를 통해 데이터를 다시 조회하고, 캐시를 갱신한다. 이때 다시 네트워크 다운로드가 발생한다.

하지만 이런 비효율적인 방식을 개선하고자 조건부 요청이라는 기능이 있다.

---

### 1) Last-Modified , If-Modified-Since

![스크린샷 2023-08-08 오후 9 54 47](https://github.com/YongNyeo/TIL/assets/109174778/10f2ed9e-266f-4acb-bdd8-701ac1135c5f)

서버로부터 처음 리소스를 받을때 Last-Modified라는 헤더값을 받는다. 리소스가 가장 최근에 수정된 시각을 의미하고, 이 값은 브라우저의 캐시저장소에도 입력된다.

![스크린샷 2023-08-08 오후 9 57 49](https://github.com/YongNyeo/TIL/assets/109174778/44fbe246-ed54-4fd7-8f9e-88324b38d81d)

이후 해당 리소스에 재접근시 캐시 유효시간이 끝났을 경우 서버로 재다운로드를 받아야한다.

하지만 if-modified-since 라는 헤더를 사용하여 서버에 접근하여 데이터의 최종 수정일(헤더정보)만 다운로드한다..

![스크린샷 2023-08-08 오후 10 04 44](https://github.com/YongNyeo/TIL/assets/109174778/41b79a1d-b75a-474b-bcee-d3f8212a96c0)

이 때 만약 캐시 최종 수정일과 서버의 최종 수정일이 같다면 서버로부터 리소스의 바디값은 새로 다운로드 받을 필요가 없다. 
  - 이때 사용하는 http status =  304 not modified 

이러한 방식으로 효율성을 높일 수 있다. 

### Last-Modified, If-Modified-Since 단점
- 데이터 수정날짜는 다르지만 데이터는 그대로인경우( 원본->수정->원본)
- 스페이스나 주석같이 크게 영향이 없는 변경의 경우에 캐시 유지하고싶은 경우

---

### 2) E-tag ,  If-None-Match

<img width="747" alt="스크린샷 2023-08-09 오전 9 21 54" src="https://github.com/YongNyeo/TIL/assets/109174778/b485fd93-6a5d-4467-bd20-5701724ff2b4">

처음 리소스를 받을때 E-tag라는 헤더와 함께 받는다.

이때 E-tag는 해시 연산 또는 계정번호 등 여러 방법으로 생성된다. 그리고 E-tag를 캐시 저장소에 같이 저장한다.

서버에서 리소스의 변경이 있을경우 E-tag가 변경된다.

<img width="743" alt="스크린샷 2023-08-09 오전 9 22 11" src="https://github.com/YongNyeo/TIL/assets/109174778/47214e4d-6db5-4664-9721-32ed5ede9dd4">

캐시의 유효시간이 끝났을때 If-None-Match 를 이용하여 서버의 헤더를 다운받는다.

<img width="741" alt="스크린샷 2023-08-09 오전 9 22 28" src="https://github.com/YongNyeo/TIL/assets/109174778/c887cc03-6dab-4b83-a5d2-59adfdcc5036">

서버의 E-tag와 캐시의 E-tag가 같은지만 확인한다.

<img width="744" alt="스크린샷 2023-08-09 오전 9 22 38" src="https://github.com/YongNyeo/TIL/assets/109174778/00942268-021b-4995-93d1-fd42e5fa472e">

만약 바뀐것이 없다면 304 redirect 처리된다. 결과적으로 Last-Modified와 같이 헤더값만 다운로드 받으면 되는것이다.

(물론 바뀌었다면 바디까지 재 다운로드)

---
- 캐시 서버의 구분(Proxy Cache)
<img width="668" alt="스크린샷 2023-08-09 오전 10 10 30" src="https://github.com/YongNyeo/TIL/assets/109174778/33a19829-cbc8-444e-9bf1-f71606e9d34c">

서버 접근성을 높이기 위해 프록시 캐시 서버를 두고있다. 

---

## Cache 지시어

### Cache-Control: max-age
- 캐시 유효 시간, 초 단위
  
### Cache-Control: no-cache
- 데이터는 캐시해도 되지만, 항상 원(origin) 서버에 검증하고 사용

### Cache-Control: no-store
- 데이터에 민감한 정보가 있으므로 저장하면 안됨  (메모리에서 사용하고 최대한 빨리 삭제)

### Cache-Control: public
- 응답이 public 캐시에 저장되어도 됨
  
### Cache-Control: private
- 응답이 해당 사용자만을 위한 것임, private 캐시에 저장해야 함(기본값)

### Expires : 유효기간 지정
- expires: Mon, 01 Jan 1990 00:00:00 GMT
- 단, max-age와 겹치면 expire는 무시

---

📘 Reference
- https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard
- https://hongong.hanbit.co.kr/%EC%99%84%EB%B2%BD-%EC%A0%95%EB%A6%AC-%EC%BF%A0%ED%82%A4-%EC%84%B8%EC%85%98-%ED%86%A0%ED%81%B0-%EC%BA%90%EC%8B%9C-%EA%B7%B8%EB%A6%AC%EA%B3%A0-cdn/
