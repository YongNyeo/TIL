# Valid(검증)-2

이전에 배웠던 검증 기능 방식은 직접 로직을 적어야하는 부분이 너무 많아 번거로울 수 있었다. 

오늘은 어노테이션을 활용한 Bean Validation에 대해 공부해보려한다. 

정확한 작동원리는 모른채 사용하던 기능중 하나가 검증 기능이었다. 이제는 알고 사용하자~!

---

    spring-boot-starter-validation  

라이브러리를 등록하면 스프링 환경에 BeanValidator를 알아서 인지한다. 

그리고 스프링부트는 LocalValidatorFactoryBean을 글로벌 Validator로 등록한다. 

해당 Validator는 @Valid,@Validated 가 있는곳을 찾아가 해당 모델의 어노테이션(ex. @NotNull 등..) 검증을 시작한다.




_❗주의해야할 점은 글로벌 Validator를 수동으로 등록시, LocalValidatorFactoryBean는 글로벌 Validator로 등록되지 않는다._

    @Controller
    public class ControllerV1(@Valid @ModelAttribute Item item){
       // 컨트롤러 로직
    }

    @Data
    public class Item {
    
      private Long id;
      
      @NotBlank
      private String itemName;
      
      @NotNull
      @Range(min = 1000, max = 1000000)
      private Integer price;
    }

필요로 하는 모델에 어노테이션을 붙이고 조건들을 사용한다.

### 작동순서
  - @ModelAttribute 각각의 필드에 타입 변환 시도
    - 성공하면 다음으로, 실패하면 typeMismatch 로 FieldError 추가
  - Validator 적용

---

### 💡 Message

그동안 properties 파일을에서 계층별로 메시지를 정리했다. 물론 이것도 수동으로 잘 정리하면 편하지만 이것보다 더 편한 기능이 있다. 

    @NotNull(message="빈칸 안돼요")

다음과 같이 message 파라미터를 입력하면 알아서 에러 메시지를 저장해준다. 

물론 수동으로 입력해놓은 에러메시지가 우선순위가 더 높다. 수동입력한것과 어노테이션 message에서 입력한것 모두 없을 경우엔 라이브러리가 제공하는 에러 메시지가 저장된다.

---
### 💡 복합 필드 에러 검증

위에서 사용했던 어노테이션들은 전부 필드에만 적용가능했다. 만약 가격*수량 같은 복합 필드의 에러를 검증할땐 어떻게 해야할까?

1. @ScriptAssertions 
2. 처음에 배운 bindingResult와 reject()를 이용한 검증

1번 방법의 경우 자바스크립트를 이용하는데,  현재 스프링부트 3이상 버전에서 이슈가 있고 실무에서 제약사항이 많다고 한다.
따라서 안정적으로 검증 가능한 2번으로 섞어 사용하면 된다.

    @PostMapping("/add")
    public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
    //특정 필드 예외가 아닌 전체 예외
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.reject("totalPriceMin", new Object[]{10000,resultPrice}, null);
            }
        }
    }

다음과 같이 필드 부분은 @Validated로 처리하고 복합필드는 직접 처리해주면된다.

---

## 한계점과 해결방법

Item 모델에 price 필드가 있다고 가정할때 ,

가격을 등록할때는 100만원이지만, 수정시에는 500만원으로 가능하게 하고싶다. 

이렇게 케이스마다 다른 방식의 검증로직을 적용하는것은 Bean Validation만으로는 해결할 방법이 없다.

- 해결방법

1. @Validated(groups=)

        //model
        @NotBlank(groups = {SaveCheck.class, UpdateCheck.class})
        private String itemName;

        //controller
        @PostMapping("/{itemId}/edit")
        public String editV2(@PathVariable Long itemId, @Validated(UpdateCheck.class) @ModelAttribute Item item, BindingResult bindingResult) {
        //...}


groups로 해결할 수 있지만, 위의 짧은 코드만 봐도 덕지덕지 써야할것이 많다. 

더 중요한 이유는 대부분의 실무 개발환경에선 무언가를 처음 저장할때랑 수정할때 전달받아야하는 데이터가 다르다. 그래서 대부분 아예 폼 객체를 분리한다.

2. Form 객체의 분리 (SaveForm, UpdateForm)

처음에 저장하는 ItemSaveForm객체, 수정용 ItemUpdateForm , 그리고 그냥 Item 객체

    @Data
    public class ItemSaveForm {
        @NotBlank
        private String itemName;
        
        @NotNull
        @Range(min = 1000, max = 1000000)
        private Integer price;
        
        @NotNull
        @Max(value = 9999)
        private Integer quantity;}
    }

    @Data
    public class ItemUpdateForm {
    
        @NotNull
        private Long id;
        
        @NotBlank
        private String itemName;
        
        @NotNull
        @Range(min = 1000, max = 1000000)
        private Integer price;
        
        //수정에서는 수량은 자유롭게 변경할 수 있다.
        private Integer quantity;
    }

    @Data
    public class Item {
        private Long id;
        private String itemName;
        private Integer price;
        private Integer quantity;
    }

Item객체에선 검증을 하지 않는다. 저장,수정용 Form에서 이미 검증을 마친뒤, Item으로 바인딩해서 변환하는 방식이다.

    @PostMapping("/add")
    public String addItem(@Validated @ModelAttribute("item") ItemSaveForm form,BindingResult bindingResult, RedirectAttributes redirectAttributes) {
        Item item = new Item();
        item.setItemName(form.getItemName())
    }
    
## 💡 @Valid vs @Validated 

사실 이 둘 차이는 거의 없다고 보면된다. Valid 는 java 표준 기술이고 Validated는 spring에 스프링 전용 기술이다. 

__거의다 동일하게 동작하기 때문에 둘을 구분지어서 사용할 필요는 없다.__

차이가 있다면, Validated에는 groups 라는 기능을 갖고 있다. 


---
📘 Reference
- https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard
