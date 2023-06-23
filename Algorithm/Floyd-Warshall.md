# 💡 floyd warshall (플로이드-워셜) 이란?
다익스트라와같이 시작노드가 주어지고 나머지 모든 노드에 대한 최소거리가 아닌, 
모든 노드끼리의 각 최소거리를 구하는것이다.

이때 다음과 같이 2차원 배열을 사용하면 편하게 구할수 있다.  
  
<img width="252" alt="스크린샷 2023-06-23 오전 11 32 04" src="https://github.com/YongNyeo/TIL/assets/109174778/877b0963-6e3d-426b-b497-bf11713ab3e7">

3단계를 통해 플로이드 배열을 완성시킬수있다. 

        //플로이드 초기화
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (i != j) {
                    floyd[i][j] = 1_000_000_000;
                }
            }
        }

플로이드간의 거리를 0또는 10억(큰수)로 설정한다. 0은 자기 자신인 경우에 해당하고. 10억은 최댓값으로 설정해놓은것이다.(최소거리를 구하는것이기때문에 최대숫자로 설정)

        
        //플로이드 값 입력
        for (int i = 0; i < k; i++) {
            String[] s = bf.readLine().split(" ");
            int a = Integer.parseInt(s[0]);
            int b = Integer.parseInt(s[1]);
            int c = Integer.parseInt(s[2]);
            if (floyd[a - 1][b - 1] > c) {
                floyd[a-1][b-1] = c;
            }
        }

주어진 노드끼리의 거리를 입력한다.

        //플로이드 구현
        for (int w = 0; w < n; w++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (floyd[i][j] > floyd[i][w] + floyd[w][j]) {
                        floyd[i][j] = floyd[i][w] + floyd[w][j];
                    }
                }
            }
        }

가장 중요한부분이다. i와j는 각 행,열이고  w는 각 노드끼리 직접연결하는게 아닌 중간다리를 이어주는것이다. 


 
     if (floyd[i][j] > floyd[i][w] + floyd[w][j]) {
          floyd[i][j] = floyd[i][w] + floyd[w][j];
    }

예를들면  1에서 4로 가는방법에는 1->2->4 가 있을것이고 1->3->4가 있다. 이런 경우를 모두 체크하는것이다.

징검다리 한번만 체크하는 알고리즘이 있다면 징검다리가 4,5개 그 이상도 자동으로 체크될수 있다. 

관련문제: https://www.acmicpc.net/problem/11404
