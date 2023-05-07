# 💡 Equals and HashCode

❓ 자바에서 객체끼리 비교할때 == 연산자 대신 equals를 이용해서 하는것은 알고있었지만, 정확한 원리를 이해하지 못한채 사용하고있다가, 알고리즘 푸는데 저것때문에 난관에 부딪혔다.
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

이러한 이유때문에 hashCode() 를 오버라이딩해주면 equals()또한 반드시 오버라이딩해줘야합니다.
그리고 이것을 편리하게 쓰고싶다면, Lombok에서 @EqualsAndHashCode를 사용할수있습니다.
      


##### Reference
<https://mangkyu.tistory.com/101>

<https://jaehoney.tistory.com/168>                

***




# 💡 Serializable - 자바 직렬화
❓ 오늘 login 기능을 구현하면서 session관련 코드들을 보다가 Session관련 객체들에 Serializable 을 사용하는것을 보고 왜 사용하는지 궁금해졌다.


### 📘 정의 (위키디피아)
--- 
직렬화(直列化) 또는 시리얼라이제이션(serialization)은 컴퓨터 과학의 데이터 스토리지 문맥에서 데이터 구조나 오브젝트 상태를 동일하거나 다른 컴퓨터 환경에 저장(이를테면 파일이나 메모리 버퍼에서, 또는 네트워크 연결 링크 간 전송)하고 나중에 재구성할 수 있는 포맷으로 변환하는 과정이다.

쉽게말해  JVM(Java Virtual Machine)의 메모리에 상주(힙 또는 스택)되어 있는 객체를  바이트 형태로 변환하는 기술과

직렬화된 바이트 형태의 데이터를 객체로 변환해서 JVM으로 상주시키는 형태를 같이 이야기합니다.

### ⭐ 그렇다면 언제 사용되나요?
---
JVM 메모리에 올라가있는 데이터를 영속화(Persistance)해서 사용해야되는 경우에 사용됩니다.

- 서블릿 세션 (Servlet Session)
---

##### 서블릿 기반의 WAS(톰캣, 웹로직 등)들은 대부분 세션의 자바 직렬화를 지원하고 있습니다. 물론 단순히 세션을 서블릿 메모리 위에서 운용한다면 직렬화를 필요로 하지 않지만,  파일로 저장하거나 세션 클러스터링, DB를 저장하는 옵션 등을 선택하게 되면 세션 자체가 직렬화가 되어 저장되어 전달됩니다

- 캐시 (Cache)
---
##### 자바 시스템에서 퍼포먼스를 위해 캐시(Ehcache, Redis, Memcached, …)라이브러리를 시스템을 많이 이용하게 됩니다. \n 자바 시스템을 개발하다 보면 상당수의 클래스가 만들어지게 됩니다.
##### 예를 들면 DB를 조회한 후 가져온 데이터 객체 같은 경우 실시간 형태로 요구하는 데이터가 아니라면
##### 메모리, 외부 저장소, 파일 등을 저장소를 이용해서 데이터 객체를 저장한 후 동일한 요청이 오면 DB를 다시 요청하는 것이 아니라 저장된 객체를 찾아서 응답하게 하는 형태를 보통 캐시를 사용한다고 합니다.
##### 캐시를 이용하면 DB에 대한 리소스를 절약할 수 있기 때문에 많은 시스템에서 자주 활용됩니다. (사실 이렇게 간단하진 않습니다만 간단하게 설명했습니다.)
##### 이렇게 캐시 할 부분을 자바 직렬화된 데이터를 저장해서 사용됩니다. 물론 자바 직렬 화만 이용해서만 캐시를 저장하지 않지만 가장 간편하기 때문에 많이 사용됩니다.


<img width="342" alt="스크린샷 2023-05-07 오후 7 52 48" src="https://user-images.githubusercontent.com/109174778/236673208-cecf8f85-89d8-4b54-b0de-0f2d02f61ebc.png">

사실 자바시스템에서나 Serializable을 사용합니다. 다른 시스템은 JSON,CSV등 다른 형태로 사용할 수 있습니다.

그렇지만 개발환경이 자바인만큼, 자바직렬화를 이용하는것이 좋다고합니다.

#### Reference
https://techblog.woowahan.com/2550/ 
