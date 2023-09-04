# 벨만포드 알고리즘

특정 그래프에서 최단거리를 구하는 방법으로 다익스트라와 플로이드 워셜을 배웠다.

하지만 다익스트라로 구할때, 두 정점사이의 거리인 간선값이 음수로 들어오게 되면, 사이클이 발생하됐을때 사이클에 있는 값들이 음의 무한대로 치닫게 된다.

<img width="901" alt="스크린샷 2023-09-04 오후 1 14 10" src="https://github.com/YongNyeo/TIL/assets/109174778/e9d80ec6-9580-4789-8816-0a5daa1c8f51">

5번 노드에서 2번 노드로 가는 음수 값 때문에, 사이클이 발생하면 계속해서 값이 바뀔수 있다.

따라서 이런 불안정한 상태로는 다익스트라로 진행이 불가능하기에, 음수 간선을 처리하기 위해 나오게 된것이 벨만포드 알고리즘이다.

---

## 벨만포드 작동원리

간선의 수가 E이고 정점의 수가 V일떄, 다익스트라의 시간복잡도는 O(E log V)였다. 이미 방문한 곳은 다시 방문하지 않기때문에 시간을 많이 줄었다

반면 벨만포드는 O(E V) 시간복잡도를 갖는데, 모든 노드의 모든 경로를 탐색하기 때문이다. 

다익스트라의 모든 케이스는 벨만포드의 모든 케이스에 포함되어있다고 볼 수 있다.

    class Bus{
	    int u;
	    int v;
	    int val;
	    public Bus(int u,int v, int val) {
		    this.u = u;
		    this.v = v;
		    this.val = val;
	    }
    }

    public class Main {
	    static int n,m;
	    static Bus[] e;
	    static long[] dist;
	    static int INF = Integer.MAX_VALUE;
	    public static void main(String[] args) throws IOException {
		
		    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    		StringTokenizer st = new StringTokenizer(br.readLine());
	    	
    		n = Integer.parseInt(st.nextToken());
	    	m = Integer.parseInt(st.nextToken());
		
		    e = new Bus[m];
		
		
	    	// 1. 출발 노드 설정 (input값)
		    for(int i=0; i<m; i++) {
    			st = new StringTokenizer(br.readLine());
	    		int u = Integer.parseInt(st.nextToken());
		    	int v = Integer.parseInt(st.nextToken());
			    int val = Integer.parseInt(st.nextToken());
			
			    e[i] = new Bus(u,v,val);
		    }
		
    		// 2. 최단거리 테이블 초기화
	    	dist = new long[n+1];
		    for(int i=1; i<n+1; i++) {
			    dist[i] = INF;
		    }
		
    		// 벨만포드 알고리즘 실행 (true: 음수 순환 존재, false: 음수 순환 존재x)
	    	if(bellmanford(1)) { // 음수 순환 존재하면 -1 출력 
		    	System.out.println(-1);
		    }
		    else { //음수 순환 없음
    			// 1번 노드를 제외한 다른 모든 노드로 가기 위한 최단거리 출력 
	    		for(int i=2; i<n+1; i++) {
		    		if(dist[i] == INF) {// 도달할 수 없으면 -1 
			    		System.out.println("-1");
				    }
				    else { // 최단 거리 출력 
					    System.out.println(dist[i]);
				    }
			    }
		    }
    	}
     }

* 해당 문제는 백준 11657번에 대한 풀이이다. 벨만포드의 기초문제중 하나다.

* 여기까지는 벨만포드를 구현하기 전, 설정 단계다.
     
    	static boolean bellmanford(int start){
	    	
    		dist[start] = 0;
	    	
    		// n번 반복 (음수 간선 순환 체크안하려면 n-1번 반복)
	    	for(int i=0; i<n; i++) {
		    	// 매 반복마다 모든 간선을 확인 
			    for(int j=0; j<m; j++) {
				    int cur = e[j].u;
				    int next = e[j].v;
    				int cost = e[j].val;
	    					
		    		if(dist[e[j].u] == INF) 
			    		continue;
				    // 현재 간선을 거쳐서 다른 노드로 이동하는 거리가 짧은 경우 
				    if(dist[next] > (dist[cur] + cost)) {
					    dist[next] = dist[cur] + cost;
							
					    // n번째 라운드에서 값이 갱신된다면 음수 순환 존재 
					    if (i == n-1) {
						    return true;
					    }
    				}
	    		}
		    }
    		return false;
	    }

  다익스트라와 가장 큰 차이가 나타나는데, 바로 얼마나 구체적으로 반복문을 도는지이다.

  다익스트라에서는 visited과 우선순위 큐를 이용해 정말 필요한 만큼만 돌아서 O(E log V)가 가능하지만,

  벨만 포드는 O(E V)만큼 전체 다 돌아야 음수인지 아닌지 알 수 있다.

  바깥쪽 for문을 통해 각 노드별(1~N)로 , 안쪽 for문을 통해 _하나의 노드에 연결 된 간선을 기존보다 더 작은 경우에만_ 매번 값을 초기화한다.


        if (i == n-1) {
            return true;
		}

  중요한 부분은 이 부분인데, n번 도는동안 기존의 간선보다 더 작은경우를 마지막으로만 설정하는 이유는,

  첫번째는 INF(max값)이기에 무조건 체크가 되기 때문에 마지막으로 설정해야 이것이 사이클인지 아닌지 체크 가능하다.


---


📘 Reference

- https://www.acmicpc.net/problem/11657
- https://loosie.tistory.com/162
  
