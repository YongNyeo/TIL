# Union Find(합집합)  알고리즘

서로소 집합(Disjoint Sets Algorithm) 알고리즘이라고도 불린다. MST를 공부하다보니 유니온파인드도 필수로 공부하고 넘어가야할것 같아서 정리하고 넘어간다.

---

<img width="654" alt="스크린샷 2023-07-23 오후 10 18 52" src="https://github.com/YongNyeo/TIL/assets/109174778/f79e62bb-78ef-4924-8d94-87bc27960913">


이런 집합관계가 있을때, 각 원소가 같은 집합에 있는지(원소가 직접적으로 연결되지 않더라도 간접적으로 연결됨) 확인할때 용이한 알고리즘이다.

    public static void main(String[] args) throws IOException {
        int n = Integer.parseInt(st.nextToken());
        int m = Integer.parseInt(st.nextToken());
        parents = new int[n + 1];
        for (int i = 0; i <= n; i++) {
            parents[i] = i;
        }
        for (int i = 0; i < m; i++) {
            StringTokenizer sk = new StringTokenizer(br.readLine());
            int ask = Integer.parseInt(sk.nextToken());
            int a = Integer.parseInt(sk.nextToken());
            int b = Integer.parseInt(sk.nextToken());
            if (ask == 0) {
                unionParent(a,b);
            } else if (ask == 1) {
                if (findParent(a, b)) {
                    System.out.println("YES");
                }else System.out.println("NO");
            }
        }
    }
    
    public static void unionParent(int a,int b) {
        int a1 = getParent(a);
        int b1 = getParent(b);
        if (a1 > b1) {
            parents[a1] = b1;
        }else parents[b1] = a1;
    }

    public static int getParent(int k) {
        if (parents[k] ==k) {
            return k;
        }return parents[k] = getParent(parents[k]);
    }

    public static boolean findParent(int a, int b) {
        a = getParent(parents[a]);
        b = getParent(parents[b]);
        if (a == b) {
            return true;
        }else return false;
    }

크게 세가지 부분으로 나눌수 있다.
getParent(),unionParent(),findParent()

### unionParent(): 
- 합집합을 만든다. 이때 부모-관계로 만들어서 최상위 루트 노드를 지정한다.
- 기본적으로 더 작은값을 루트 노드로 만들고, 루트노드를 기준으로 그룹을 구별한다.
  
      public static void unionParent(int a,int b) {
        int a1 = getParent(a);
        int b1 = getParent(b);
        if (a1 > b1) {
            parents[a1] = b1;
        }else parents[b1] = a1;
      }



### getParent(): 나의 최종 부모는 무엇인가?
- 위의 unionParent()로 만든 최종 부모(그룹식별자)를 얻는 과정이다.
- 재귀를 이용한다

      public static int getParent(int k) {
        if (parents[k] ==k) {
            return k;
        }return parents[k] = getParent(parents[k]);
      }
  
### findParent(): 두 부모가 같은지 확인한다.

     public static boolean findParent(int a, int b) {
        a = getParent(parents[a]);
        b = getParent(parents[b]);
        if (a == b) {
            return true;
        }else return false;
      }
      
---

📘 Reference
- https://4legs-study.tistory.com/94?category=886581

#### 관련문제 : https://www.acmicpc.net/problem/1717
