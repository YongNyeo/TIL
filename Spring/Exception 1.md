# Exception 1(예외 처리)

예외처리는 생각해야할 부분이 많다. 두가지로 나눠 설명할 예정이다.

__예외가 터졌을 때 Html으로 반환하면 되는 경우__  그리고,

__예외가 터졌을 때 단순 html이 아닌 Api 처리를 해서 반환해야 하는경우__  두가지로 나눠볼수 있다. 해당 글에선 전자의 경우만 다루겠다.

---

__1. 예외가 터졌을 때 Html으로 반환하면 되는 경우__ 

예외를 Html으로 반환하는 과정을 살펴보자.

    WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생,sendError활용가능)

컨트롤러에서 예외 발생시, WAS까지 전파된다. 
WAS 에서는 받은 예외 정보를 보고 다시 /error 페이지(컨트롤러)를 찾아간다.

    WAS(/error-page/500, dispatchType=ERROR) -> 필터(x) -> 서블릿 -> 인터셉터(x) -> 컨트롤러(/error-page/500) -> View

>여기서 dispatchType이란?
>
>>WAS가 error페이지를 찾아갈때 필터와 인터셉터로 비효율적인 방문을 줄이고자 구분하기 위한 필드다.
>>
>>dispatchType이 REQUEST라면 사용자가 처음 WAS로 요청했을 경우 필터와 인터셉터를 모두 거치고,
>>
>>dispatchType이 ERROR라면 예외처리를 위한 움직임으로 판단해 필터와 인터셉터를 방문하지 않는다.


### response.sendError(errorStatus,message)
- 컨트롤러에서 예외 발생시 자신이 Error를 임의로 설정할 수 있다.
- 해당 정보는 WAS까지 전달되고, WAS에선 sendError()안의 정보를 활용해 자동으로 해석한다.

---

### ❓ 여기서 드는 의문은, WAS에서 부터는 에러코드만 보고 어떻게 쉽게 ErrorPage(Html)를 찾아가지?

모두 스프링부트가 미리 해놓은 덕분이다.
- 서블릿 밖으로(WAS) 예외가 발생하거나, response.sendError(...) 가 호출되면 모든 오류는 /error 경로를 호출하게 된다.
- 이때 /error 라는 경로가 기본 오류 페이지로 설정되어있다.
- /error 경로가 매핑하는 BasicErrorController 라는 스프링 컨트롤러를 자동으로 등록한다.
- ErrorMvcAutoConfiguration 이라는 클래스가 오류 페이지를 자동으로 등록하는 역할을 한다.

---

❗ 개발자는 자신이 예외 발생시 보여줄 화면만 개발하면된다. 

화면을 세가지 방식으로 등록할 수 있는데, 다음 우선순위에 따라 BasicErrorController가 찾아간다.

1. 뷰 템플릿
- resources/templates/error/500.html
- resources/templates/error/5xx.html
  
2. 정적 리소스( static , public )
- resources/static/error/400.html
- resources/static/error/404.html
- resources/static/error/4xx.html

3. 적용 대상이 없을 때 뷰 이름( error )
- resources/templates/error.html

---

### BasicErrorController의 또다른 역할 

    * timestamp: Fri Feb 05 00:00:00 KST 2021
    * status: 400
    * error: Bad Request
    * exception: org.springframework.validation.BindException
    * trace: 예외 trace
    * message: Validation failed for object='data'. Error count: 1
    * errors: Errors(BindingResult)
    * path: 클라이언트 요청 경로 (`/hello`)

컨트롤러가 뷰에 넘길때 Model에 다음 정보를 담고 넘겨서, 활용(선택) 가능하다.

> server.error.whitelabel.enabled=true : 오류 처리 화면을 못 찾을 시에 나타나는 화면으로 개발자가 제일 자주 볼 수 있다.

---

📘 Reference
- https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard
