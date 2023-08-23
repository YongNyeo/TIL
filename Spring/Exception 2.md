# Exception 2 

API의 경우(단순 화면 보여주는 상황이 아닌, 서버간의 통신,API끼리의 통신)

단순 화면으로 처리하는것이 아니라, 각 오류 상황에 맞는 오류 응답 스펙을 정하고, 값을 JSON 등으로 내려주어야 한다.

---

API처리 경우에도 스프링부트가 지원하는 BasicErrorController 를 활용할 수 있다. 


<img width="950" alt="스크린샷 2023-08-23 오후 5 01 41" src="https://github.com/YongNyeo/TIL/assets/109174778/e71253e7-136e-4d9e-86c7-43c11d44a7e2">

error()와 errorHtml() 두 메서드가 있는데, request 의 헤더에 있는 accept 값에 따라 다르다. 

__accept = text/html 인경우 errorHtml()가 호출되고, 그 외에 error()가 호출되어서 ResponseEntity로 Json 타입을 반환한다.__


하지만 이 방법의 경우 예외처리를 처리하는데 한계가 있다.

>예를들어 IllegalArgumentException 예외를 터트리고 400으로 처리하고싶지만 결과는 500으로 나온다.
>
>예외가 발생한 지역에서 바로 예외를 자유롭게 다루지 못한다.

스프링은 위의 예시를 해결할 방법으로 컨트롤러(핸들러) 밖으로 예외가 던져진 경우 예외를 해결하고, 동작을 새로 정의할 수 있는 방법을 제공한다.    

---

## HandlerExceptionResolver

<img width="816" alt="스크린샷 2023-08-23 오후 5 12 08" src="https://github.com/YongNyeo/TIL/assets/109174778/988ce688-69f3-468f-9884-bb66b1bc287f">

컨트롤러에서 서블릿까지 전달된 예외를 자유롭게 처리할수 있는 HandlerExceptionResolver 라는 기능이 있다.

  
    public interface HandlerExceptionResolver {
        @Nullable
	    ModelAndView resolveException(
			HttpServletRequest request, HttpServletResponse response, @Nullable Object handler, Exception ex);
    }

ModelAndView 타입으로 반환을 하는데 다음과 같은 의미를 가진다.

- 빈 ModelAndView: new ModelAndView() 처럼 빈 ModelAndView 를 반환하면 뷰를 렌더링 하지 않고, 정상 흐름으로 서블릿이 리턴된다.
      - 주로 response에 json값을 담은채 그대로 실행시키려 할때 이용한다.
      - ex) response.getWriter().println("hello"); 처럼 HTTP 응답 바디에 직접 데이터를 넣어준다.
  
- ModelAndView 지정: ModelAndView 에 View , Model 등의 정보를 지정해서 반환하면 뷰를 렌더링한다.

- null: null 을 반환하면, 다음 ExceptionResolver 를 찾아서 실행한다. 만약 처리할 수 있는 ExceptionResolver 가 없으면 예외 처리가 안되고, 기존에 발생한 예외를 서블릿 밖으로 던진다.

_* 예외를 response.sendError(상태코드) 호출로 변경해서 서블릿에서 상태 코드에 따른 오류를 처리할수도 있다._

__HandlerExceptionResolver를 이용한 예시 코드__

    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        try {
            if (ex instanceof UserException) {
                log.info("UserException resolver to 400");
                String acceptHeader = request.getHeader("accept");
                response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
                if ("application/json".equals(acceptHeader)) {
                    Map<String, Object> errorResult = new HashMap<>();
                    errorResult.put("ex", ex.getClass());
                    errorResult.put("message", ex.getMessage());
                    String result = objectMapper.writeValueAsString(errorResult);
                    response.setContentType("application/json");
                    response.setCharacterEncoding("utf-8");
                    response.getWriter().write(result); //json 값은 response에 저장
                    return new ModelAndView();  //정상흐름으로 바꿈 
                } else {
                    // text/html
                    return new ModelAndView("error/500");  //이것은 html 렌더링
                }
            }
        } catch (IOException e) {
            log.error("resolver ex", e);
        }
        return null; //여기서 에러 처리하지 못하고 서블릿 밖으로 던져짐
    }

HandlerExceptionResolver를 이용하면 response를 이용해 Json 응답을 보낼수는 있다. 

하지만 저 방식은 복잡하고, API 통신에서 ModelAndView는 불필요하다. 이것을 스프링이 간단하게 구현할수 있도록 도와준다.

---

우리가 직접 구현하는게 할 필요없이 , 스프링이 구현해놓은것을 쓰면된다!

1. DefaultHandlerExceptionResolver (우선순위 가장 낮음)
2. ResponseStatusExceptionResolver 
3. ExceptionHandlerExceptionResolver (우선순위 가장 높음)

---

### DefaultHandlerExceptionResolver

<img width="951" alt="스크린샷 2023-08-23 오후 9 07 24" src="https://github.com/YongNyeo/TIL/assets/109174778/af3b0f16-d6ac-467a-a296-b90270c84fbd">


스프링 내부 기본 예외를 처리한다.  일부 코드만 가져왔다.

예를들어, 컨트롤러에서 TypeMistmatchException이 터지고 예외처리를 하지 않아 서블릿까지 올라오게 되면 500(서버)에러가 발생한다.

실상은 클라이언트가 잘못입력한 경우여서 400 에러로 다뤄야하는데, 이런 경우를 대비해서 스프링이 내부에서 에러 상태코드를 바꿔주는 것이다. 

---

### ResponseStatusException

@ResponseStatus 어노테이션으로 간단하게 예외에 대한 상태코드를 지정할 수 있다.

    @ResponseStatus(code = HttpStatus.BAD_REQUEST, reason = "잘못된 요청 오류")  //여기서 reason 은 MessageSource 기능을 이용할 수 있음.
    public class BadRequestException extends RuntimeException {
    }

다만 어노테이션은 외부 라이브러리에는 적용하지 못할때는 ResponseStatusException(상태,메시지,예외) 를 이용하면 된다.

    @GetMapping("/api/response-status-ex2")
    public String responseStatusEx2() { 
        throw new ResponseStatusException(HttpStatus.NOT_FOUND, "error.bad", new IllegalArgumentException());
    }
---

### ExceptionHandler 

빠른 이해를 위해 예시 코드를 만들어보았다.

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(RuntimeException.class)
    public ErrorResult Error1(RuntimeException e) {
        return new ErrorResult("BAD",e.getMessage());
    }
  
    @GetMapping("/hello/{id}")
    public String api1(@PathVariable("id") String id) {
        if (id.equals("1")) {
            System.out.println(1);
            throw new RuntimeException("잘못된 사용자");
        } else if (id.equals("a")) {
            throw new IllegalStateException("잘못된 입력값");
        }
        return id+"님 어서오세요";
    }
    @Getter
    class ErrorResult{
        String name;
        String message;

        public ErrorResult(String name, String message) {
            this.name = name;
            this.message = message;
        }
    }
    
결과 - ![스크린샷 2023-08-23 오후 11 33 53](https://github.com/YongNyeo/TIL/assets/109174778/e91ab1c9-13a3-4b0e-abb7-a1accdd0e0ca)

흐름은 다음과 같다. 

1. 컨트롤러에서 특정 Exception을 터트린다.

2. 같은클래스 내의 @ExceptionHandler((특정)Exception.class) 이 붙은 메서드를 작성해놓는다.
   - @ExceptionHandler가 붙어있는 클래스를 찾아간다. 그리고 여기서 API 통신 결과(Json)와 에러코드( @ResponseStatus를 이용해)를 작성해 반환한다.
   - 만약 에러코드를 따로 변경하지 않는다면 200 코드로 정상 반환처리돼어 서블릿에 도달한다.
  
3. 이제 서블릿에서 Json을 반환하면 된다.

---

<img width="655" alt="스크린샷 2023-08-23 오후 11 39 39" src="https://github.com/YongNyeo/TIL/assets/109174778/2887013e-fce7-433e-91f6-3b6db8d0265a">

주의해야할 점은 Exception는 계층형 구조를 가지고 있기 때문에, Exception 작성시 더 구체적으로 적은 Exception을 찾아가는것이 원칙이다. 

만약 IllegalArgumentException을 처리하고싶을때 Exception과 RuntimeException 각각 @ExceptionHandler를 작성했다면, RuntimeException을 먼저 찾아간다.


---

## 💡 @ControllerAdvice

@ExceptionHandler는 기본적으로 같은 클래스 내의 컨트롤러에 대해서 작동한다. 따라서 각 컨트롤러클래스마다 같은 예외처리 코드를 작성하다보면 어마어마한 중복코드가 생겨날 수 있다. 

@ControllerAdvice와 @RestControllerAdvice를 이용하여 한곳에서 예외처리를 할 수 있다. 둘의 차이는 @ResponseBody의 유무다.

    @RestControllerAdvice
    public class ExControllerAdvice {
        @ResponseStatus(HttpStatus.BAD_REQUEST)
        @ExceptionHandler(IllegalArgumentException.class)
        public ErrorResult illegalExHandle(IllegalArgumentException e) {
            log.error("[exceptionHandle] ex", e);
            return new ErrorResult("BAD", e.getMessage());
        }
    }

별다른 차이 없이 @RestControllerAdvice만 붙이면 된다.    

위와 같이 그냥 사용하면 모든 컨트롤러(글로벌)에 적용된다. 만약 범위를 지정하고 싶다면 세가지 방법이 있다.

    @ControllerAdvice(annotations = RestController.class) //특정 어노테이션이 있는 클래스에 적용
    public class ExampleAdvice1 {}
    
    @ControllerAdvice("org.example.controllers") //특정 패키지에 적용(하위까지)
    public class ExampleAdvice2 {}

    @ControllerAdvice(assignableTypes = {ControllerInterface.class, AbstractController.class}) //구체적인 클래스네임에 적용
    public class ExampleAdvice3 {}

---

📘 Reference
- https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard
- https://jeong-pro.tistory.com/195
- https://mangkyu.tistory.com/204
