# Index 란?
>인덱스는 데이터베이스 분야에 있어서 테이블에 대한 동작의 속도를 높여주는 자료 구조를 일컫는다.
>
>인덱스는 테이블 내의 1개의 컬럼, 혹은 여러 개의 컬럼을 이용하여 생성될 수 있다. 고속의 검색 동작뿐만 아니라 레코드 접근과 관련 효율적인 순서 매김 동작에 대한 기초를 제공한다. 

DB 공부를 해봤다면 인덱스를 한번쯤은 다 들어본다.

하지만 인덱스가 어떻게 작동하는지는 몰랐고, 어떻게,어떤 경우에 사용해야하는지 잘 몰라서 정리하고 가려한다.

---

## 자료구조를 이용한 DBIndex

<img width="462" alt="스크린샷 2023-08-21 오후 4 21 56" src="https://github.com/YongNyeo/TIL/assets/109174778/8c634c5b-b431-41ae-b361-a4270b6f2f7e">

### 💡 Hash Table

해시 테이블은 key-value를 O(1)로 찾을수 있는 아주 효과적인 자료구조다.

물론 하나의 key값에 대한 해시 연산결과가 중복될수록 해시 충돌로 인해 O(N)에 가까워진다.

__하지만 더 큰 문제는 하나의 글자만 달라져도 엄청나게 다른 결과를 초래하는 Hash연산 자체가 갖는 한계점이다.__

예를 들어 'A'로 시작하는 글자를 찾고싶지만 'AB','A','AC'는 완전히 다른 해시값을 도출한다. 이러한 문제점 때문에 해시테이블은 거의 사용되지 않는다.

__또한 해시결과-버킷 리스트는 정렬되지 않기에 비효율적이다__

---

### 💡 B-Tree

<img width="717" alt="스크린샷 2023-08-21 오후 4 48 37" src="https://github.com/YongNyeo/TIL/assets/109174778/7a1ccf5d-573d-4952-8d0f-31dd0d9fed2f">

B-Tree는 이진탐색트리에 각 노드의 자식노드를 두개 이상 가질 수 있는 트리다.


<img width="725" alt="스크린샷 2023-08-21 오후 4 49 02" src="https://github.com/YongNyeo/TIL/assets/109174778/76cafc66-76ab-4863-926d-dae7caa51a71">

키값을 기준으로 이진트리 형태로 저장된다.

- 특정 인덱스 노드의 탐색의 경우 Key를 기준으로 항상 오름차순 정렬되어있기 때문에, 트리의 높이만큼만 돌면돼서 O(log N)에 해결된다. 

- 풀스캔시에 전체 노드를 중위 순회를 해야한다.

---

### 💡 B+Tree 

<img width="801" alt="스크린샷 2023-08-21 오후 5 09 55" src="https://github.com/YongNyeo/TIL/assets/109174778/65b6a61f-21ca-48b8-8fda-798b377f02d8">

B+Tree는 B-Tree를 다소 개선한 방식이다.

- B-Tree는 루트 노드부터 리프노드까지 모든 노드가 key-value를 갖고있는 반면 B+Tree는 모든 value를 리프노드에만 저장한다. 

- 중간 브랜치 노드에 Value가 없어서 B-Tree보다 메모리를 덜 차지하는 만큼, 노드의 메모리에 더 많은 Key를 저장할 수 있다. 

- 하나의 노드에 더 많은 Key를 저장하는 만큼 트리의 높이가 더 낮아진다.

- 리프노드끼리는 LinkedList로 이으면서 B-Tree에 비해 풀스캔시 더 효율적이다.

---

# DB Index를 적용할때 고려사항 

여기까지 인덱스를 DB에 적용하는 방식을 알아보았다. 그렇다고 무턱대고 모든 컬럼에 인덱스를 적용하는것은 좋지 않은 생각이다.

그동안 내가 본것은 트리구조의 index의 __'조회 시'__ 강점이다.

__Insert,Delete,Update 문이 자주일어나는 경우는 적합하지 않다.__

- 삽입 과정
<img width="713" alt="스크린샷 2023-08-21 오후 5 26 27" src="https://github.com/YongNyeo/TIL/assets/109174778/60a78866-8732-4fcc-ac2f-988e5d0bb498">
<img width="715" alt="스크린샷 2023-08-21 오후 5 26 38" src="https://github.com/YongNyeo/TIL/assets/109174778/83b60454-b6c8-4142-b850-c397e0614656">
<img width="715" alt="스크린샷 2023-08-21 오후 5 27 03" src="https://github.com/YongNyeo/TIL/assets/109174778/d5b5db21-0d4c-4cf5-a11f-4e777e250d01">

트리는 항상 오름차순을 유지해야하기 때문에 삽입,삭제,수정시에 생각보다 구조 변경이 필요하다.

따라서 DB의 인덱스 생성시, 구조 변경이 필요한 삽입,삭제,수정이 많은 컬럼에는 적합하지 않다.

---

### 카디널리티 (= 유니크한 데이터의 개수)

<img width="550" alt="스크린샷 2023-08-21 오후 10 26 04" src="https://github.com/YongNyeo/TIL/assets/109174778/a3e68385-645a-4a43-a8dc-f10b7d2467ec">

성별이라는 컬럼에 올 수 있는 데이터는 남/여 2개다. 

index 테이블을 생성했을때 Tree를 생각해보면, 거의 중복된 값(남/여) 밖에 없어서 인덱스 사용의 이점이 없다.

반면 학번 컬럼을 인덱스로 가지면, 모두 다른 값을 가지기에 인덱스 사용의 이점이 클 것이다.

따라서 카디널리티 수가 높을수록 인덱스를 적용하기 좋은 컬럼이라 할 수 있다.

---

📘 Reference

- https://yurimkoo.github.io/db/2020/03/14/db-index.html
- https://tecoble.techcourse.co.kr/post/2021-09-18-db-index
- https://mangkyu.tistory.com/96
