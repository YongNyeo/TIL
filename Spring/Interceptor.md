# Interceptor란?

인터셉터는 필터와 달리 서블릿 기술이 아닌, 스프링 기술이다. 즉 실행 시점이 다른것인데, 그림을 보며 알아보자.

<img width="680" alt="스크린샷 2023-08-20 오후 9 59 52" src="https://github.com/YongNyeo/TIL/assets/109174778/6d5ddee7-b0e6-49f4-8d3c-234cfcdcc818">

---

### - preHandle 

무조건 서블릿에 도달한 뒤, 핸들러(컨트롤러) 도달전에 PreHandle()이 한번 호출된다.

preHandle 의 응답값이 true 이면 다음으로 진행하고, false 이면 더는 진행하지 않는다. 

false인 경우 나머지 인터셉터는 물론이고, 핸들러 어댑터도 호출되지 않는다. 그림에서 1번에서 끝나버린다.


### - posHandle

컨트롤러 호출후, 실행된다.


### - afterCompletion

뷰 렌더링까지 끝난 후 호출된다. 

---

## 예외처리

<img width="563" alt="스크린샷 2023-08-20 오후 10 14 38" src="https://github.com/YongNyeo/TIL/assets/109174778/e6ae195d-d6f9-4c30-b391-5860fe3399b2">

컨트롤러에서 예외가 터졌을 때, 앞단의 디스패쳐 서블릿까지 예외가 넘어가고, postHandle가 예외 영향을 받아 호출되지 않는다.

반면 afterCompletion 는 예외에 영향 받지않고 반드시 실행된다.

---

<img width="1267" alt="스크린샷 2023-08-20 오후 10 21 20" src="https://github.com/YongNyeo/TIL/assets/109174778/6c38f3b2-6b5d-41bd-9cfc-4c1e962c633e">

_그렇다면 각 구간에선 어떤것을 처리해야할까?_

- preHandle 은 컨트롤러 호출전에 동작되기 때문에 주로 인증,권한 기능과 관련해 사용된다.

- postHandle은 컨트롤러 호출되고 나서 뷰가 렌더링되기 전에 실행되므로 주로 모델 데이터 추가, 뷰 선택 등의 작업에 사용된다.

- afterCompletion은 뷰 렌더링까지 완료된 후에 실행되기에 주로 로깅, 리소스 정리 등을 수행할 때 사용된다. exception이 터져서 정리하지 못한것들을 afterCompletion에서 정리한다.

- 필터와 다르게 Object handler 파라미터가 있다. 이것은 어떤 핸들러에 대한 정보를 담고 있는데, 핸들러 매핑방식 (ex @Controller, @RequestMapping...) 에 따라서 다른 정보를 담을 수 있는데, 이에 대한 파악을 돕는 객체다.

        Bean: com.gngsn.app.controller.Home@23af30c9
        Method: public java.lang.String com.gngsn.app.controller.Home.getHomePage(java.lang.Long,java.lang.Long,org.springframework.ui.Model) 


>그리고 그림에서 봤다시피 지역변수만 이용하면 되는 필터와 다르게
>
>각 메서드가 처리하는 구간이 달라서 변수를 주고 받을때 넘겨줄 수단이 필요하다. 그때는 보통 reqeust setAttribute()로 주고 받는다.
>

---

## 필터와의 또다른 차이점

필터와의 비교를 위해 간단한 로그인 인증만 하는 인터셉터를 작성한다.(prehandle만 있으면 된다)

_인터셉터 bean에 등록_

    @Configuration
    public class WebConfig implements WebMvcConfigurer {
        @Override
        public void addInterceptors(InterceptorRegistry registry) {
            registry.addInterceptor(new LogInterceptor())
                    .order(1)
                    .addPathPatterns("/**")
                    .excludePathPatterns("/css/**", "/*.ico", "/error");
        }
    }

_인터셉터 작성_

    public class LoginCheckInterceptor implements HandlerInterceptor {
        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
            String requestURI = request.getRequestURI();
            log.info("인증 체크 인터셉터 실행 {}", requestURI);
            HttpSession session = request.getSession(false);
            if (session == null || session.getAttribute(SessionConst.LOGIN_MEMBER) == null) {
                log.info("미인증 사용자 요청");
                //로그인으로 redirect
                response.sendRedirect("/login?redirectURL=" + requestURI);
                return false;
            }return true;
        }
    }

bean등록할때 바로 제외 url 패턴과 등록 url 패턴을 등록할수 있어서 , 인터셉터를 작성할때 필터처럼 복잡하게 로직을 짜지 않아도 된다.

---

📘 Reference
- https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard
- https://gngsn.tistory.com/153
