# 💡 Equals and HashCode

❓ 자바에서 객체끼리 비교할때 == 연산자 대신 equals를 이용해서 하는것은 알고있었지만, 정확한 원리를 이해하지 못한채 사용하고있다가, 알고리즘 푸는데 저것때문에 난관에 부딪혔다.

[equals()](1.equals())

[hashCode()](2.HashCode())

## 1.equals()

📑 Object.class

           public boolean equals(Object obj) {
                return (this == obj);
                }
                
최상단 Object클래스에 명시된 equals()는 기본적으로 두 객체간의 메모리 주소값을 가지고 비교한다. 하지만 우리는 객체간의 값을 비교하고싶다. 

📑 String.class

        public boolean equals(Object anObject) {
            if (this == anObject) {
                return true;
            }
            if (anObject instanceof String) {
                String aString = (String)anObject;
                if (coder() == aString.coder()) {
                    return isLatin1() ? StringLatin1.equals(value, aString.value)
                                      : StringUTF16.equals(value, aString.value);
                }
            }
            return false;
        }
String 클래스에서 이 점을 해결하고자 overide해서 주소값이 다르더라도 String값이 같다면 true 를 return하도록 했다. 

📘 Integer.class

    public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }
           
대부분의 class에선 equals를 상속받는다. 이래서 우리는 모든 객체의 equals()가 당연히 값을 비교하는줄 알고있다. 

### ❗ 그럼 내가 만든 객체는?

    public static class Node{
 	      int a;
	      public Node(int a) {
			      this.a = a;
		    }
	  }


   	static void Test1() {
    		Node node1 = new Node(1);
		    Node node2 = new Node(1);
		    if (node2.equals(node1)) {
			      System.out.println("same");
		    }else System.out.println("diff");
    }

결과는 diff다. 내가 만든 객체는 equals()가 값을 비교하도록 오버라이딩이 되어있지 않기 때문에 메모리 비교를 한다. 따라서 다른 값(주소)끼리 비교를 하게 된다. 

### 매번 오버라이딩을 해줘야하는거에요?

- 이것을 돕고자 나온것이 @EqualsAndHashCode다. 위 Node클래스에 어노테이션만 붙이면 해당 클래스(객체)가 갖고있는 필드를 기반으로 값 비교를 하도록 도와준다. 이제 위의 Test 결과값은 same이 된다. 우리가 매번 오버라이딩 하는 수고스러움을 덜어준다. 

---

## 2.HashCode()

자바에는 hashCode()는 객체의 메모리 주소를 hashFuction을 통해 해시 값으로 변경시키고 그것을 사용한다.

    public native int hashCode(); //최상단 Object.class 

📑 예시)Arrays.class

        public static int hashCode(long a[]) {
            if (a == null)
                return 0;

            int result = 1;
            for (long element : a) {
                int elementHash = (int)(element ^ (element >>> 32));
                result = 31 * result + elementHash;
            }
        
            return result;
        }
복잡한 알고리즘을 통해 식별자 값을 넘겨준다.
#### 이 해시값을 어디다가 써먹냐??

- HashMap,HashTable,HashSet 컬렉션의 기본 작동 방식이 hashCode()를 통한 hash연산 값 비교 후 equals()가 작동하는 방식이다.(자세한건 hash편에서 다룰예정이다)

➡️ 만약 내가 만든 클래스(객체)가 위에 나열한 컬렉션에 들어갈 예정이라면 hashCode()를 오버라이딩 해주는 @EqualsAndHashCode를 쓰는것도 좋은 방법일것이다!

<img width="526" alt="스크린샷 2023-07-22 오후 11 26 19" src="https://github.com/YongNyeo/TIL/assets/109174778/b27e941a-e8ad-49bc-badb-de74029a4771">

---

📘 Reference
- https://mangkyu.tistory.com/101
- https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html

