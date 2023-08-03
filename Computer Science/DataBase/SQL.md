# SQL (Structured Query Language)

SQL 언어에서도 Join에 대해 정리하려한다.

<img width="456" alt="스크린샷 2023-08-03 오후 4 23 03" src="https://github.com/YongNyeo/TIL/assets/109174778/a6b1e104-cc09-49b1-8d6a-0720d8f856c6">

가장 흔하게 쓰이는 네가지 join에 대해 알아보자.

---

## 1. Inner Join 

<img width="425" alt="스크린샷 2023-08-03 오후 4 22 57" src="https://github.com/YongNyeo/TIL/assets/109174778/2e452fc4-86e6-41c0-9ac5-73c70d3b5cb1">

두 테이블의 교집합된 부분이 있을때만 합친다. 보통 그냥 조인이라고 부르면 내부 조인을 의미한다.



<img width="710" alt="스크린샷 2023-08-03 오후 4 25 49" src="https://github.com/YongNyeo/TIL/assets/109174778/7dba7c2b-403f-4144-afd7-b9a7dfd35d7d">

상품id가 두 테이블을 합치는 필드다. 이때 상품 id의 교집합으로는 1,2,3만 해당돼서 상품id가 4,5인 튜플은 조인결과에 포함되지 않는다.

<img width="460" alt="스크린샷 2023-08-03 오후 4 34 49" src="https://github.com/YongNyeo/TIL/assets/109174778/ea0ed5fb-8a37-44bb-a237-3be0c94b00f6">

코드는 다음과 같다.

    1)
      SELECT 조회할 컬럼
      FROM 테이블1, 테이블2
      WHERE 테이블1.공통컬럼 = 테이블2.공통컬럼


    2)
      SELECT 조회할 컬럼
      FROM 테이블1
      (INNER,생략가능) JOIN 테이블2
      ON 테이블1.공통컬럼 = 테이블2.공통컬럼
      WHERE 추가조건

---

## 2. Left (Outer) Join
      
<img width="197" alt="스크린샷 2023-08-03 오후 4 44 57" src="https://github.com/YongNyeo/TIL/assets/109174778/b12bcf94-22e9-4e40-8288-7b0d19f3491d">

왼쪽이 기준 테이블이 되어서 오른쪽을 왼쪽에 붙인다.

<img width="462" alt="스크린샷 2023-08-03 오후 4 46 26" src="https://github.com/YongNyeo/TIL/assets/109174778/35740d35-fbe5-4d70-ba59-38d4c3d1e8fb">

위의 예시를 레프트조인하고나면 다음과 같은 형태가 된다. 오른쪽에만 있는 상품id 5는 삭제되고 왼쪽에만 있던 id 4는 null로 채워진다.

    SELECT 조회할 컬럼
    FROM 기준테이블1 
    LEFT OUTER JOIN 테이블2
    ON 테이블1.공통컬럼 = 테이블2.공통컬럼
    WHERE 추가조건문  // 여기서 테이블2.공통컬럼 is null을 하게되면 차집합(테이블1-테이블2) 형태를 띄게 된다.

## 3. Right (Outer) Join

<img width="219" alt="스크린샷 2023-08-03 오후 5 04 35" src="https://github.com/YongNyeo/TIL/assets/109174778/4baa0f9a-a312-4661-b4c5-af5e3e181c6d">

 left join 의 반대라고 생각하면된다. 주로 left 조인을 사용한다.

### 조심해야할점
- outer join은 참조하는(기준이 아닌) 테이블에 여러 튜플이 있다면, 조인이 되면서 튜플이 뻥튀기가 될수 있다.
- 따라서 참조할 테이블의 중복값 검사를 잘하는것이 중요하다.

<img width="509" alt="스크린샷 2023-08-03 오후 4 53 58" src="https://github.com/YongNyeo/TIL/assets/109174778/ddb15fd4-ecea-4f62-8152-2098f0f091b1">

<img width="314" alt="스크린샷 2023-08-03 오후 4 54 16" src="https://github.com/YongNyeo/TIL/assets/109174778/d1fed5e0-db50-4929-bc6b-03cfdaaed198">


## 4. Full (Outer) Join
<img width="586" alt="스크린샷 2023-08-03 오후 5 10 05" src="https://github.com/YongNyeo/TIL/assets/109174778/a6b17d4c-50cf-4157-946e-93aaec652197">

합집합의 연산과 같다. (A+B-(A교집합B)) 따라서 빈칸이 생긴다.


# ➡️ 주로 left와 inner조인을 쓰고 나머지는 잘 안쓴다고 한다. 그렇지만 개념정도는 익혀두자.

---
📘 Reference
- https://hongong.hanbit.co.kr/sql-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95-joininner-outer-cross-self-join


