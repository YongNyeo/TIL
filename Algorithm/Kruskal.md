# 크루스칼 알고리즘 ⭐

모든 도시를 이어주는 도로의 최소비용 문제를 만났다. 그동안 배웠던 다익스트라나 플루이드 워셜으로는 해결하기 어려워보였다.

자주 쓰는 Dfs나 Bfs처럼 출발점 또는 방향을 정해놓고 진행할수 없기에 도무지 아이디어가 떠오르지 않았다. 그러다가 MST를 배운적은 없지만 문뜩 최소비용트리라는 단어가 스치듯 지나가면서
이걸 공부해서 풀어야겠다 라는 생각이 들었다.

---

풀이 순서

<img width="628" alt="스크린샷 2023-07-23 오후 9 36 36" src="https://github.com/YongNyeo/TIL/assets/109174778/1c31dee9-6421-4d8a-a365-100d0f930c8a">

1. 노드간 거리를 오름차순으로 정렬한다.

<img width="633" alt="스크린샷 2023-07-23 오후 9 37 35" src="https://github.com/YongNyeo/TIL/assets/109174778/2cdaf6b6-bc7e-4ad7-bb5d-598d53adc6a4">

2. 제일 작은 a-b를 선택한다.

<img width="640" alt="스크린샷 2023-07-23 오후 9 37 58" src="https://github.com/YongNyeo/TIL/assets/109174778/26dca8c0-3fb9-4261-a6bc-504f151bc31e">

3. 그다음 작은 a-d를 선택한다.

<img width="643" alt="스크린샷 2023-07-23 오후 9 38 17" src="https://github.com/YongNyeo/TIL/assets/109174778/5c6e77d4-fbc3-4a12-8d23-aa71cb621f46">

4. 우리는 최소비용을 구하므로 b-d(사이클)은 선택하지 않는다.

### 이때 사이클을 제외하는 알고리즘은 [UnionFind](https://github.com/YongNyeo/TIL/blob/main/Algorithm/UnionFind.md)를 이용한다.

<img width="638" alt="스크린샷 2023-07-23 오후 9 39 21" src="https://github.com/YongNyeo/TIL/assets/109174778/69a17c43-e241-4184-98a2-351e49eabc7c">

5. 모든 간선의 최소 거리를 구했다.(사이클이 없기 때문에 N개의 노드가 있다면 N-1개의 간선이 선택됐을경우 끝난다.)

---

## 코드로 바꾸기

차근차근 코드로 바꿔보자.

1. 노드간 거리를 오름차순으로 정렬한다. (-> 나중에 최소비용 순으로 빼내야하기에 더 간단한 우선순위큐를 선택했다)

            PriorityQueue<Edge> pq = new PriorityQueue();
            //각 노드간의 모든 간선정보를 넣어야함
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    Double distance = dist(x_list[i], x_list[j], y_list[i], y_list[j],E);
                    pq.add(new Edge(i, j, distance));
                }
            }


2. 우선순위큐에서 간선을 하나씩 빼준다. 그리고 해당 간선의 ab가 합집합이라면(이미 연결되어있다면,이미 같은 부모를 가지고 있다면) pass

   2-1. 아니라면 거리값 추가 및 count 추가
   
   2-2. 만약 간선 개수가 모두 찼다면(n-1) 끝내기


            double result =0;
            int c=0;
            while (!pq.isEmpty()) {
                Edge e = pq.poll();
                if (findParent(e.a, e.b)) //합집합
                    continue;
                result+=e.dist;
                if (++c == n-1) {
                    break;
                }
            }



- 가장 중요한 부분들이 UnionFind와 우선순위큐를 이용하는것이다.

---

📘 Reference
- https://4legs-study.tistory.com/94?category=886581

