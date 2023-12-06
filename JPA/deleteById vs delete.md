# deleteById vs delete.md

spring data jpa에서는 개발자를 위해 수많은 메서드를 제공해준다.

다만 이 차이를 알고 써야하는데.. 이제 delete를 무작정 쓰지 않으려 정리한다!

<img width="452" alt="스크린샷 2023-12-06 오후 6 21 17" src="https://github.com/YongNyeo/TIL/assets/109174778/9911599f-37bc-4195-99a5-a9f480c77a11">
<img width="855" alt="스크린샷 2023-12-06 오후 6 21 53" src="https://github.com/YongNyeo/TIL/assets/109174778/85923aa7-3a0d-47e6-a7c2-ba2c7631c745">


deleteById()도 내부적으로 delete()를 쓰고있음을 확인할 수 있다.

다만 deleteById는 findById를 먼저 실행한 후 존재할때만 delete를 실행한다.

<img width="566" alt="스크린샷 2023-12-06 오후 6 25 10" src="https://github.com/YongNyeo/TIL/assets/109174778/1ea6b657-0cfe-44c3-af30-cdbdfbc8fe0d">

여기서 findById() 또한 Optional.ofNullable()로 묶여있어서 존재하지않는 id를 delete하더라도 예외가 발생하지 않는다.

---

delete는 결국엔 entity를 직접 넘겨줘야해서 findById를 직접 구현한 뒤  delete를 실행해야하고,

deleteById는 id로도 간단하게 실행할 수 있다.

전자는 내가 findById 예외 처리에 대한 구현이 직접 가능하다!

후자는 없는 id를 입력하더라도 그냥 무시된다. 

내 입맛따라 쓰자!
