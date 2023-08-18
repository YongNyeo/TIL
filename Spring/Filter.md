# Filter 

<img width="932" alt="스크린샷 2023-08-18 오후 3 02 19" src="https://github.com/YongNyeo/TIL/assets/109174778/f5a6e81d-b560-4c08-a3d6-7a5de6681a23">

필터가 하는 역할은 이름 그대로 무언가 걸러주는 역할을 한다. 

필터가 작동하는 위치는 Was에 도달했을때, 서블릿이 동작하기 전이다.

흐름도로 나타내면

    HTTP 요청 -> WAS -> 필터 -> (필터 통과) 서블릿 -> 컨트롤러 순이다.

    HTTP 요청 -> WAS -> 필터 -> (필터 통과X) 서블릿 호출하지 않고, 대게 Redirect 처리

필터를 적용하기 좋은 기능은 주로 로그인 기능으로 알려져있다. 

---

## 💡 작동원리 

웹과 관련된 공통 관심사를 처리할 때는 HTTP의 헤더나 URL의 정보들이 필요한데, 

서블릿 필터나 스프링 인터셉터는 HttpServletRequest 를 제공한다.

    public class myFilter implements Filter {

        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
            Filter.super.init(filterConfig); //서블릿 컨테이너가 생성될때 시작됨
        }

        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            
        }

        @Override
        public void destroy() {
            Filter.super.destroy();  //서블릿 컨테이너가 종료될때 필터도 종료됨
        }
    } 

doFilter()에 집중하면 되는데, 이때 파라미터로 ServletRequest,ServletResponse를 받는다.

이것을 활용해서 우리가 필터링 로직을 짜면 된다.

---

## 🔥 특정 페이지에 로그인이 필요한 케이스를 위한 doFilter() 예시를 만들어보자

    //doFilter() 
    
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        String requestURI = httpRequest.getRequestURI();
        HttpServletResponse httpResponse = (HttpServletResponse) response;

ServletRequest과 ServletResponse는 각각 다운캐스팅을 진행한다. 

이때 다운캐스팅이 가능한 이유는

dofilter를 호출할 때 request를 파라미터로 넣는데 인자로 들어가는 request는 이미 (Http요청이라서) httpServletRequest 타입으로 생성이 되어 전달이 되기 때문에,

형변환시 에러가 발생하지 않는다. 


        try {
            log.info("인증 체크 필터 시작 {}", requestURI);
            if (isLoginCheckPath(requestURI)) {
                log.info("인증 체크 로직 실행 {}", requestURI);
                HttpSession session = httpRequest.getSession(false);
                if (session == null ||
                        session.getAttribute(SessionConst.LOGIN_MEMBER) == null) {
                    log.info("미인증 사용자 요청 {}", requestURI);   //로그인으로 redirect
                    httpResponse.sendRedirect("/login?redirectURL=" +
                            requestURI);
                    return; //여기가 중요, 미인증 사용자는 다음으로 진행하지 않고 끝!
                }
            }
            chain.doFilter(request, response);

우선 로그인 체크가 필요한 URI인지 체크한다. 

이후 세션값을 확인해서 있다면, chain.doFilter()를 이용해 다음 필터로 넘어간다. 만약 다음 필터가 없다면 서블릿으로 넘어간다.

_이 때 chain.doFilter() 파라미터인 request,response는 임의로 조작해서 다음 필터로 넘길 수 있다._

세션값이 없다면 Response값에 redirect을 넣어주고 return 처리(서블릿으로 못넘어간채로 필터 끝) 해준다.

            
        } catch (Exception e) {
            throw e; //예외 로깅 가능 하지만, 톰캣까지 예외를 보내주어야 함
        } finally {
            log.info("인증 체크 필터 종료 {}", requestURI);
        }
    }


    /**
     * 화이트 리스트의 경우 인증 체크X
     */
    private boolean isLoginCheckPath(String requestURI) {
        return !PatternMatchUtils.simpleMatch(whitelist, requestURI);
    }

필터를 다 작성했다면, 필터를 Bean으로 등록해야한다.

    @Bean
    public FilterRegistrationBean loginCheckFilter() {
        FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new myFilter());
        filterRegistrationBean.setOrder(1);
        filterRegistrationBean.addUrlPatterns("/*");
        
        return filterRegistrationBean;
    }

FilterRegistrationBean 가 Filter를 Bean으로 등록하는 수단이다.

setFilter()를 통해 필터를 담고, setOrder()를 통해 필터의 순서, addUrlPatterns를 통해 필터를 적용할 URL을 설정할 수 있다.

> @ServletComponentScan
>
> @WebFilter(filterName = "logFilter", urlPatterns = "/*")
>
> 이 두가지를 통해서도 필터를 쉽게 등록가능하지만, 필터의 순서 설정이 불가능하다. 필터가 여러개일때는 FilterRegistrationBean을 사용하자.


---

📘 Reference

- https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard
- https://mangkyu.tistory.com/173
