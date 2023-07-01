# 💡 Map 파헤쳐보기
<img width="621" alt="스크린샷 2023-07-01 오후 3 35 44" src="https://github.com/YongNyeo/TIL/assets/109174778/17258023-895c-4708-b4ef-ecf817d7ecdc">


Map은 Key와 Value를 가진 Map<Key,Value> 형태이다.

Map에는 중복된 key는 중복없이 유일한 값이다(value의 중복은 상관X). 그 key를 활용하여 value를 언제든지 구할수 있다.

---

### map의 구현방법으로는 HashMap,LinkedHashMap,TreeMap,HashTable 4가지를 대표적으로 사용한다.

1. HashMap - 흔히 사용하는 Map의 형태다. 순서나 다른 기능 보장없이 Map의 기본기능에 충실하다. hash 값으로 찾기 때문에 검색이 빠르다.

       Map<String,Integer>map = new HashMap<>();

2. LinkedHashMap - HashMap의 기능에서 키의 순서를 보장해준다.

       Map<String,Integer>map = new LinkedHashMap<>();

3. TreeMap - 이진검색트리 형태로 유지되기 때문에 저장할때 비교적 시간이 걸린다. hashMap도 데이터 검색 시간이 빠르지만, 정렬이나 범위검색이 필요한경우 TreeMap을 이용하는것이 더 효율적이다

           Map<Integer,Integer>map = new TreeMap<>();
4. HashTable - HashMap과 HashTable은 유사하지만, 차이점 또한 존재한다.
   
   -  Thread-safe 여부
   
   Hashtable은 Thread-safe하고, HashMap은 Thread-safe하지 않다는 특징을 가지고 있다. 그렇기에 멀티스레드 환경이 아니라면 Hashtable은 HashMap 보다 성능이 떨어진다는 단점을 가지고 있다.
   
   -  Null 값 허용 여부
   
   Hashtable은 key에 null을 허용하지 않지만, HashMap은 key에 null을 허용한다.

   -  HashMap은 보조해시를 사용하기 때문에 보조 해시 함수를 사용하지 않는 Hashtable에 비하여 해시 충돌(hash collision)이 덜 발생할 수 있어 상대적으로 성능상 이점이 있다.
   -  최근까지 Hashtable은 구현에 거의 변화가 없지만, HashMap은 현재까지도 지속적으로 개선되고 있다.
  

📘 Reference 

- https://docs.oracle.com/javase/8/docs/api/java/util/Map.html
- https://fruitdev.tistory.com/141
