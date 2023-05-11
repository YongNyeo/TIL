# 💡 Comparator , Comparable 와 sort
알고리즘을 풀다보면 나타나는 인터페이스다.

    class Compare1 implements Comparable<Member>{
        @Override
        public int compareTo(Member o) {
            return 0;
        }
    }
    //     public int compareTo(T o);


    class Compare2 implements Comparator<Member>{
        @Override
        public int compareTo(Member o1,Member o2) {
            return o1.getId()-o2.getId();
        }
    }
    //    int compare(T o1, T o2);
    
Comaparable과 Comparator은 근본적으로 값을 비교한다는 점. compareTo를 오버라이드한다는점이 같다.

다만, Comparable은 파라미터가 1개로 지정되어있고, Comparator는 파라미터가 2개로 지정되어있다.

---
👍 Comparable은 파라미터 값이 하나이기때문에, 자기 자신이 기존에 갖고있던 값과 비교할수밖에 없다.

👍 반면, Comparator는 파라미터 두개가 들어오기때문에, 자신이 갖고있던 값이 아니더라도 파라미터끼리 비교시킬수있다.

return값은 int형으로, 값 비교시 앞에것이 큰경우 1을, 앞에것이 작은경우 -1을 , 같은경우 0을 return한다.
 
---

이를 이용해서 정렬에서 많이 사용한다.

    Arrays.sort( arr ,(a,b)->
        if (a>b) return 1;
        else if (a==b) return 0;
        else return -1;);
        
위 코드는 오름차순정렬이다. 정렬의 기본값이 오름차순이기때문에  Arrays.sort() 의 lambda 정렬을 이용할때

양수가 return되면 객체a,b의 순서가 바뀐다. -1이 return되면 그대로 진행된다. 위의  코드를 더 간략하게 하게되면

    Arrays.sort(arr,(a,b)->
        return a-b;);
        
와 같이 표현할수있다. 다만 a-b의 값이 자료형의 범위를 벗어나는 경우에는 다른 방식을 생각해야한다.
          
            
    
