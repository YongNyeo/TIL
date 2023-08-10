# 검증(Valid) 기능 - 1

사용자로부터 어떤 값을 받을때 그 값이 범위에 맞는지, 유효한지, 올바른지 확인하는 것을 검증 기능이라 한다.

<img width="601" alt="스크린샷 2023-08-10 오후 1 47 49" src="https://github.com/YongNyeo/TIL/assets/109174778/4a14876b-20d3-4dcd-9c8c-ba4d895e9896">

그림에서는 폼을 받을때 일부 값이 틀리다면 Model에 틀린 값의 원인(결과)를 담아 사용자에게 보여줘야하는 과정을 보여주고 있다.

---

## 💡 BindingResult

스프링이 제공하는 검증 기능중 하나다.

    @PostMapping("/add")
    public String addItemV1(@ModelAttribute Item item, BindingResult bindingResult,RedirectAttributes redirectAttributes) {
        if (!StringUtils.hasText(item.getItemName())) {
            bindingResult.addError(new FieldError("item", "itemName", "상품 이름은
                    필수입니다."));
        }

       //특정 필드 예외가 아닌 전체 예외
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은
                        10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
            }
        }
    }

- BindingResult는 넘어오는 데이터에 대한 검증 오류를 저장하는 기능
- 파라미터 위치(@ModelAttribute,@RequestParam 등)는 받는 데이터 다음에 있어야함.
  
---
- 필드에러 저장 방법 2가지

      public FieldError(String objectName, String field, String defaultMessage) {}
      public FieldError(String objectName, String field, @Nullable Object rejectedValue, boolean bindingFailure, @Nullable String[] codes,
      @Nullable Object[] arguments, @Nullable String defaultMessage)

      objectName : 오류가 발생한 객체 이름
      field : 오류 필드
      rejectedValue : 사용자가 입력한 값(거절된 값)
      bindingFailure : 타입 오류 같은 바인딩 실패인지, 검증 실패인지 구분 값
      codes : 메시지 코드
      arguments : 메시지에서 사용하는 인자
      defaultMessage : 기본 오류 메시지
      
- 글로벌 객체(복합필드) 에러 저장 방법 

      public ObjectError(String objectName, String defaultMessage) {}


---

여기까지 BindingResult의 fieldError()와 objectError()는 새로 객체를 생성해서 추가하는 방식이었다. 

### rejectValue() , reject()

__fieldError의 경우 메시지 경로의 인자값으로 정확한 경로로 "required.item.itemName"을 모두 적어줘야한다.__

__반면 rejectValue()에 들어가는 경로 인자 "required"만 적어주어도 계층별로 따라 찾아준다.__

계층별로 찾아준다는 뜻은? 에러 메시지에 대해  더 공부해보자.

---
## 💡 에러 메시지 추가 방법

> 이렇게 에러를 추가하고나면
> 
> properties파일에  spring.messages.basename=messages,errors  등록후,
> 
> errors.properties 에  " required.item.itemName=상품 이름은 필수입니다 " 와 같이 입력하면 나중에 에러 저장시 메시지를 사용할 수 있다.

모든 에러 메시지에 대해 입력해 놓는것은 매우 비효율적일수 있다. 

    #required.item.itemName=상품 이름은 필수입니다.
    #==FieldError==
    #Level1
    required.item.itemName=상품 이름은 필수입니다.
    #Level3
    required.java.lang.String = 필수 문자입니다.
    required.java.lang.Integer = 필수 숫자입니다.
    #Level4
    required = 필수 값 입니다.
    

다음은 메시지를 계층별로 나눈것이다. 

'required'를 인자로 넘기는 경우 위에서부터 먼저 입력되어 있는것을 찾는다. 필요에 따라 계층별로 입력해놓으면 입맛에 따라 사용할 수 있다. 

---

## ValidationUtils

기존의 방식에서 더 효율적으로 바꿀수 있다.

    if (!StringUtils.hasText(item.getItemName())) {
        bindingResult.rejectValue("itemName", "required", "기본: 상품 이름은 필수입니다.");
    }

ValidationUtils 사용 후 다음과 같이 한줄로 가능하다. 

제공하는 기능은 Empty , 공백 같은 단순한 기능만 제공한다.

    ValidationUtils.rejectIfEmptyOrWhitespace(bindingResult, "itemName", "required");


---

여기까지 검증기능을 훑어보았는데, 컨트롤러에서 작동해야하는 기능치고는 너무 무거워졌다. 컨트롤러에서 검증 기능만 분리시켜보자.

## 💡 검증 기능의 분리


    @Component
    public class ItemValidator implements Validator {
      @Override
      public boolean supports(Class<?> clazz) {
        return Item.class.isAssignableFrom(clazz);
      }
      @Override
      public void validate(Object target, Errors errors) {
        Item item = (Item) target;
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "itemName","required");
        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
            errors.rejectValue("price", "range", new Object[]{1000, 1000000}, null);
        }if (item.getQuantity() == null || item.getQuantity() > 10000) {
            errors.rejectValue("quantity", "max", new Object[]{9999}, null);
        }
      }
    }    

Validator 를 상속받아 만든 검증 클래스다.

supports()에서 어떤 클래스를 검증할지 확인해주고 , validate()에서 실질적인 검증 기능을 관리한다.

컨트롤러에서는

    private final ItemValidator itemValidator;
    
의존성만 추가해서 사용하면 된다. 

---

💡 이것을 더 효율적으로 만드려면 @InitBinder와 @Validated 를 활용한다. 

    @InitBinder
    public void init(WebDataBinder dataBinder) {
        log.info("init binder {}", dataBinder);
        dataBinder.addValidators(itemValidator);
    }

@InitBinder 어노테이션은 컨트롤러 내에서 폼 데이터 바인딩을 커스터마이징하기 위해 사용된다.

WebDataBinder 객체와 함께 사용되며, 해당 폼 데이터 바인딩이 발생하기 전에 호출된다. 


    //controller 
    controllerV1(@Validated @ModelAttribute Item item,BindingResult bindingResult)

@Validated는 WebDataBinder에서 검증기를 찾는데 , 이 코드에선 Item 검증기를 실행시키는 역할을 한다.

__여기까지 공부한 내용은  @Valid를 쓰기전에 알아야 하는 내용들이다.__
__@Valid를 통해 매우 편하게 사용 가능하지만, 복잡한 검증의 경우 해당 방법으로 구현해야하는 경우도 생기기 때문에 꼭 공부하고 넘어가야했다.__

---

📘 Reference

- https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard
- https://hooongs.tistory.com/111
- https://whitepro.tistory.com/371
