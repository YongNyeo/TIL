# Dijkstra (다익스트라)란? 💡

출발점에서 모든 노드에 대한 최단 거리를 구할수 있는 방식이다.

다익스트라의 대표적 구현방법은 PriorityQueue를 이용한다. 
1. distance는 최단 거리만을 저장하는 배열, graph는 주어진 그래프의 인접노드와 거리다
2. 다익스트라는 최초로 한번 방문을 했을때가 해당 노드에 대한 거리의 최솟값이 될수밖에 없다. ⭐ 그래서 한번 방문하면, 다시 방문할 필요가 없다 ⭐

  PriorityQueue를 통해 항상 최소 간선거리만 poll 되기때문에 더 먼 거리의 경로는 항상 거절처리된다.    
    
     if (!visited[x.num]) visited[x.num] = true;
    //한번 방문했으면 또 안해도됨
   
3. 만약 기존의 거리보다 작거나 같다면 인접한 노드에서도 검사를 한다.
    
        if (!visited[k.num]&&distance[k.num] > distance[x.num] + k.weight) {
            distance[k.num] = distance[x.num] + k.weight;
                pq.offer(new Node(k.num, distance[k.num]));
       }
   
   



### 🔥 주요 로직

      while (!pq.isEmpty()) {
            Node x = pq.poll();

            if (!visited[x.num]) visited[x.num] = true;
            //뽑은 노드에서 인접한것들
            for (Node k : graph[x.num]) {
                //인접한 노드의 거리가(가중치 더한것) 기존에 입력된 거리보다 작다면
                if (!visited[k.num]&&distance[k.num] > distance[x.num] + k.weight) {
                    distance[k.num] = distance[x.num] + k.weight;
                    pq.offer(new Node(k.num, distance[k.num]));
                }
            }
        }
        
        
관련 문제 : https://www.acmicpc.net/problem/1753    최단경로
