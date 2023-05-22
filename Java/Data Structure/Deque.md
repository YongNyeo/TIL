# ❓ Deque란
<img width="584" alt="스크린샷 2023-05-22 오전 9 48 04" src="https://github.com/YongNyeo/gachon-meeting/assets/109174778/bc435bfa-32ee-4d73-9afc-c130a959ed6e">

FIFO(선입선출)을 지향하는 Queue를 상속받은 interface다.

double ended queue의 줄임말로 양쪽에서 추가,삭제가 가능한 인터페이스라고 생각하면 된다. 


###  👷‍♂️ Deque의 구현체는?
<img width="310" alt="스크린샷 2023-05-22 오전 9 53 06" src="https://github.com/YongNyeo/gachon-meeting/assets/109174778/0e855246-2ed6-49ca-8e54-951b7bf43e67">

그림과 같이 ArrayDeque와 LinkedList가 있다. 의문점이 생기는데, LinkedList는 List 구현체 아닌가?

맞다. LinkedList는 List와 Deque 둘다 가능한 구현체다. 


    Deque<Integer> deque = new LinkedList<>();
    List<Integer> queue = new LinkedList<>();
    
다음과 같이 구현가능하다. 당연하게도 두 객체가 다룰수있는 함수는 각각 Deque와 List의 함수를 상속받은것이다.

### 👧 다시 돌아와서, ArrayDeque VS LinkedList 뭘 써야하나?

- ArrayDeque 은 Deque 인터페이스의 구현체이며 크기가 재설정을 할 수 있다.
- LinkedList 는 List의 구현체로 연결리스트로 구현되어있다.
- LinkedList 는 null 요소를 추가할 수 있지만 ArrayDeque 은 불가능하다.
- 인덱스로 특정 요소에 접근하는 경우, ArrayDeque는 O(1)의 시간이걸리고 LinkedList는 O(n)의 시간이 걸린다. 

  ⭐ 따라서 반복문 실행중 리스트의 현재 요소를 삭제하는 경우에는 LinkedList를 사용해도 괜찮으나, 양끝 또는 특정 인덱스로 이동해야할때는 ArrayDeque가 유리하다다.
  
  😧 그러나 큐 라는 자료구조의 특징을 생각해보면 큐는 FIFO 특성을 갖고 있다. 삽입은 큐의 맨 처음 삭제는 큐의 맨 마지막에 일어나기때문에 중간에 삽입되거나 삭제되는 경우가 없다고 생각하면 된다.
  
- LinkedList 는 ArrayDeque 보다 더 많은 메모리를 소모한다.
- 추가로 메모리 소모량이 적을 때는 iterate 효율이 좋은 ArrayDeque 를 사용하고 스택의 사이즈가 커지거나 심한 변동이 예상될 때는 즉각적인 메모리 공간 확보를 위해 LinkedList 를 사용한다.

## ▶️ 결론


대부분의 상황에서는 Deque 을 사용할 때 LinkedList 보다는 ArrayDeque 을 사용하면 된다. 


##### Reference
<https://tecoble.techcourse.co.kr/post/2021-05-10-stack-vs-deque/>
<https://chucoding.tistory.com/52>
