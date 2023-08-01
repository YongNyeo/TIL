# LCA 최소 공통 조상

하나의 트리에서 각 노드에 대한 공통된 최소 거리의 조상이 무엇인지 찾아내는 알고리즘이다.


---

## 기존의 LCA

<img width="712" alt="스크린샷 2023-08-01 오전 10 25 31" src="https://github.com/YongNyeo/TIL/assets/109174778/90735ffe-60e2-4c92-9047-12cecd7f7da6">

     public static void Lca(int a, int b) {
        int cnt = 0;
        //먼저 조상이 같아지게 하기
        while (depth[a] != depth[b]) {
            cnt++;
            if (depth[a] > depth[b]) {
                a = parent[a];
            } else b = parent[b];
        }
        // 이제 동시에 끌어올려서 같을때 까지 올리기
        while (a != b) {
            cnt += 2;
            a = parent[a];
            b = parent[b];
        }
        sum += cnt;
    }

두 노드간의 깊이 레벨을 맞춘후 , 동시에 한칸씩 끌어올리면서 구하는 방법이다 . 레벨을 올리는데 N이라는 시간이 걸려서 O(N)이다.

하지만 요청 쿼리가 M개라면 O(NM)이라는 시간복잡도가 나온다.

---

## 개선된 LCA

기존의 LCA는 공통조상까지 올라가는데 N이 걸리는데, 이것을 2칸씩 올라가서 log(N)으로 개선시키려한다.

<img width="707" alt="스크린샷 2023-08-01 오전 10 27 04" src="https://github.com/YongNyeo/TIL/assets/109174778/01e744ea-fc0d-4fbf-9a4a-a734d67832de">


    private static int LCA(int node1, int node2) {
        if (depths[node1] < depths[node2]) {   
            int temp = node1;    
            node1 = node2;
            node2 = temp;        //node1을 더 깊은 곳으로 만들기
        }
 
        for (int i = MAX_DEPTH; i >= 0; i--) {
            long diff = depths[node1] - depths[node2]; //두 노드간 깊이 차이
            if (diff >= (1L << i)) {    // 깊이 차이에 가장 가까운 차이를 2제곱값으로 이동하기 --> log(n)으로 이동
                node1 = parents[node1][i];
            }
        }
 
        if (node1 == node2) { //2제곱으로 옮겼을때 같은 숫자면 끝
            return node1;
        }
 
        for (int i = MAX_DEPTH; i >= 0; i--) {  //아니라면 앞으로 한칸씩 올리기
            if (parents[node1][i] != parents[node2][i]) {
                node1 = parents[node1][i];
                node2 = parents[node2][i];
            }
        }
 
        return parents[node1][0];
    } // End of LCA


### 관련문제
  
  - [백준 LCA](https://www.acmicpc.net/problem/11437)
  - [백준 LCA2](https://www.acmicpc.net/problem/11438)
  - [swea 영준이의 진짜 bfs](https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5LnipaDvwDFAXc)
