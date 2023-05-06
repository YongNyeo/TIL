# 💡 Equals and HashCode

자바에서 객체끼리 비교할때 == 연산자 대신 equals를 이용해서 하는것은 알고있었지만, 정확한 원리를 이해하지 못한채 사용하고있다가, 알고리즘 푸는데 저것때문에 난관에 부딪혔다.
그러고보니 다른사람들의 자바스프링 코드에서 @EqaulsAndHashCode 를 몇번 본적있었는데, 지금이라도 확실히 알아가자!!!

## equals()

📑 Object.class

           public boolean equals(Object obj) {
                return (this == obj);
                }
                
equals는 기본적으로 Object의 메모리 주소값을 가지고 비교한다. 따라서 같은 값이어도 각각 정의된 객체라면 당연히 false를 return한다.

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
이걸 해결하고자 String 클래스에서 overide해서 만든 equals다. 주소값이 다르더라도 String값이 같다면 true 를 return한다.

***
                
## hashCode()

hashCode()코드는 객체의 식별자를 int형으로 return 해줍니다.

우리가 이 해시값을 쓰려는 이유는 객체를 비교할때 속도가 매우 빠르기 때문입니다. 보통 HashMap에서 잘 사용됩니다.

📑 Arrays.class

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
복잡한 알고리즘을 통해 식별자 값을 넘겨줍니다.

---

⭐ 하나의 객체는 메모리 주소값을 갖고, 이 값으로 해시코드 값을 가집니다. 메모리주소는 항상 유일하지만, hashCode()를 거치고 나면 같은 해시코드 값이 생길수있습니다. 
###### 보통 HashTable(HashMap) 은 한정된 공간에서 객체의 해시값을 만드는데, 이때 같은 값이 생기는것을 해시 충돌이라 합니다.
따라서 hashCode()를 거친 후, equals()를 함으로써 해당 객체가 같은지 완벽하게 비교할수 있습니다.

- hashCode가 다르면 equals까지 가지않아도 다른 객체임을 확인할 수 있고
- hashCode가 같을때는 equals를 통해 같은 객체일수도, 다른 체일수도 있습니다.

이러한 이유때문에 hashCode() 를 오버라이딩해주면 equals()또한 반드시 오버라이딩해줘야합니다. 서로의 관계는 뗄레야 
그리고 이것을 편리하게 쓰고싶다면, Lombok에서 @EqualsAndHashCode를 사용할수있습니다.
      


##### Reference
<https://mangkyu.tistory.com/101>

<https://jaehoney.tistory.com/168>                
