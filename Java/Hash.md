# Hash - hash table, hash map, hash set

💡 해시가 들어가있는 컬렉션들은 어떻게 O(1)라는 시간에 데이터 검색,삭제가 가능한것일까? 그 구조를 파헤쳐보자

---

### HashSet

#### ❗ hashSet은 사실 hashMap의 구조를 따라간다. 다만 key값만 set에 들어가는 객체를 넣어주고 value는 dummy값으로 채워준다.
<img width="375" alt="스크린샷 2023-07-24 오후 11 39 55" src="https://github.com/YongNyeo/TIL/assets/109174778/6b0f4995-636d-4215-8732-9f7aef67f1e0">
<img width="411" alt="스크린샷 2023-07-24 오후 11 39 52" src="https://github.com/YongNyeo/TIL/assets/109174778/4d781d14-9fd2-42f9-908f-ffd4016fc306">
<img width="576" alt="스크린샷 2023-07-24 오후 11 50 06" src="https://github.com/YongNyeo/TIL/assets/109174778/2d957cef-252f-43c0-9878-25a1511e046b">

값을 넣는 add() 메서드를 보면 그 정체를 알수 있다. 

---
### HashMap

그럼 이제 hashMap의 구조에 대해서 정확히 알아보자. (hash table과 hash map은 기능이 비슷해서 묶어서 설명한뒤 나중에 차이점을 서술하려한다.)

우리가 아는 hash map은 key와 value로 이뤄진다. 하지만 더 들여다보면 더 복잡하게 되어있다. 

<img width="325" alt="스크린샷 2023-07-24 오후 5 49 02" src="https://github.com/YongNyeo/TIL/assets/109174778/39132d65-269c-4ea9-8c4e-054df2af4a05">

다음 그림은 {key:사람이름,value:전화번호}과 같은 구조의 hashMap이다.

---

### ➡️ 저장과정

1. key값을 입력한다.
   - 참고로 key값은 중복 불가능하다. key에 들어가는 값은 hashCode()와 equals() 연산을 거치기 때문에 절대로 중복된 객체값이 들어갈 수 없다.

2. key값을 hash함수 연산을 통해 hash값으로 바꿔준다.

       final int hash(Object k) {    //이하 자바 map내부의 hash()함수
          // Java 7부터는 JRE를 실행할 때, 데이터 개수가 일정 이상이면
          // String 객체에 대해서 JVM에서 제공하는 별도의 옵션으로
          // 해시 함수를 사용하도록 할 수 있다.
          // 만약 이 옵션을 사용하지 않으면 hashSeed의 값은 0이다.
          int h = hashSeed;
          if (0 != h && k instanceof String) {
              return sun.misc.Hashing.stringHash32((String) k);
          }
          h ^= k.hashCode();
          // 해시 버킷의 개수가 2a이기 때문에 해시 값의 a비트 값만을 
          // 해시 버킷의 인덱스로 사용한다. 따라서 상위 비트의 값이 
          // 해시 버킷의 인덱스 값을 결정할 때 반영될 수 있도록
          // shift 연산과 XOR 연산을 사용하여, 원래의 해시 값이 a비트 내에서 
          // 최대한 값이 겹치지 않고 구별되게 한다.
          h ^= (h >>> 20) ^ (h >>> 12);
          return h ^ (h >>> 7) ^ (h >>> 4);
       }
   

4. 바꿔준 hash값을 우리가 map의 정보를 저장할 테이블의 인덱스 값으로 쓴다.

   - 여기서 hash 충돌이 일어날 수 있다. 예를들면, a,b가 각각 다른 값을 가지고있었다 하더라도 f(a)와 f(b)의 결과가 같을 수 있다. 이런 경우에 같은 인덱스에 저장되는 현상이 일어나는데 이것을 해시 충돌이라 한다.

5. 충돌이 날 경우를 생각해 인덱스마다 버킷(value가 저장될공간)에 복수의 데이터를 저장할 수 있다.
  
   - 이러한 경우가 극단적으로 일어나는경우가 있는데  인덱스를 통한 접근으로 O(1)로 버킷에 왔지만, 특정 버킷에만 수많은 데이터가 있다면 O(N)까지 늘어날 수 있다.


#### 🎱 hashmap은 다음과 같은 방식으로 저장된다. hashmap 빠른 동작의 비결은 hash연산을 이용한 index 활용이었다.

---

### HashTable vs HashMap ?
그렇다면 이 둘의 차이점은 뭘까?

1. Thread-safe

   - HashTable은 멀티쓰레드 환경에서 모든 메서드에 syncronized(동기화) 기능이 있는데, 쓰레드간 락을 걸어서 데이터의 무결성을 보장한다. 따라서 Thread-safe를 보장한다
  
   - HashMap에는 syncronized 기능이 따로 없다. 따라서 멀티 쓰레드 환경에서 사용하기엔 불안하고, 싱글쓰레드 환경에서 빠르게 데이터 찾기에 적합하다.


2. key와 value에 null값 허용

   - hashTable은 null값을 허용하지 않는다.
  
   - hashMap 은 null값을 허용한다.

---

### ➡️ 그렇다면 멀티쓰레드에선 무조건 HashTable? 

해시테이블은 컬렉션 프레임워크가 나오기전부터 있었던만큼 오래되었다. 멀티쓰레드 환경에서 lock을 걸고 풀고 하는 과정에서 많은 시간을 잡아먹는다.

이를 해결하기 위해 ConcurrentHashMap 이 나왔다.  

어떤 Entry를 조작하는 경우에 해당 Entry에 대해서만 락을 건다. 그래서 HashTable보다 데이터를 다루는 속도가 빠르다. 즉, Entry 아이템별로 락을 걸어 멀티 쓰레드 환경에서의 성능을 향상시킨다.


### 결론
➡️ 싱글 쓰레드 환경이라면 HashMap, 멀티쓰레드 환경이라면 ConcurrentHashMap을 사용하는것이 베스트다!

------ 

📘 Reference

- https://tecoble.techcourse.co.kr/post/2021-11-26-hashmap-hashtable-concurrenthashmap/
- https://devlog-wjdrbs96.tistory.com/253
