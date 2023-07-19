# List 

자바에서 제공하는 여러 컬렉션중 List를 알아보자

## 1.ArrayList

ArrayList는 이름에서 알수있다시피 배열(array)과 유사한 면이 있는데, 둘다 index로 접근이 가능하다. 

    int[]array = new int[10]; //array
    List<Integer>list = new ArrayList<>(); //arrayList

차이점은 array는 처음에 할당한 배열 크기에서 더이상 줄이거나 늘릴수 없다. 

반면 arrayList는 줄이거나 늘릴수있다(노드를 삭제하거나 삽입하면 동적으로 조절된다). 
<img width="430" alt="스크린샷 2023-07-19 오후 2 07 20" src="https://github.com/YongNyeo/TIL/assets/109174778/3b79d514-057a-4469-b564-f93c4bfa638b">
initalcapacity를 통해 초기용량을 미리 조절해서 효율성을 높일수있다.

#### arrayList특징
- 모든 노드간 연결이 되어있다.
- 각 인덱스를 가지고있다.  --> get(int index) 의 시간복잡도는 O(1)
- null값을 가질수 없다.  --> 삽입또는 삭제 발생시 모든 노드를 땡기거나 밀어서 인덱스를 관리한다. O(n)의 시간복잡도가 생김.
<img width="240" alt="스크린샷 2023-07-19 오후 2 13 02" src="https://github.com/YongNyeo/TIL/assets/109174778/ee5bbf0e-7738-43e7-9ba6-9a241785bcb6">


## 2. LinkedList 

모든 노드가 링크(다음 노드의 주소값)를 통해 연결되어있다. 

![Singlelinkedlist](https://github.com/YongNyeo/TIL/assets/109174778/34af16c1-15f5-4675-8343-f9cd5861875e)

이런 노드의 연결 방식으로 인한 특징이 있다.
- ArrayList에 비해 각 노드가 갖는 정보가 더 무겁다(가리키는 주소값).
- LinkedList에서 특정 노드를 찾고싶을때 첫번째 노드부터 출발해서 순차적으로 접근한다. ->시간복잡도는 O(n) 
- 삽입(또는 삭제) 발생시 해당 노드의 앞뒤가 가르키는 주소 정보만 바꿔주면 된다. ->시간복잡도는 O(1)
- LinkedList는 기본적으로 단방향(->) 이라서 뒤로 못돌아간다.
  - 이를 DoubleLinkedList를 통해 해결 가능하다. 앞뒤 노드를 참조해서 양방향으로 접근 가능하다.
 
  ![DLL1](https://github.com/YongNyeo/TIL/assets/109174778/4361b677-9c58-4905-ad61-68d2acdc75fe)
 
