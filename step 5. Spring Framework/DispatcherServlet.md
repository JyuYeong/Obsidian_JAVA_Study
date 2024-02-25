Spring MVC Framework에서 <font color="#c3d69b">Client의 요청을 받아 처리하는 중심적인 역할</font>을 하는 Servlet. MVC architecture에서 <font color="#c3d69b">Controller 역할을 담당</font>한다.

- Request Parsing : 클라이언트의 HTTP 요청을 분석하여, <font color="#95b3d7">요청된 URL과 HTTP 메서드에 기반</font>하여 <font color="#95b3d7">어떤 컨트롤러가 요청을 처리할지</font> 결정한다.
- Controller Invocation : 요청에 대응하는 Controller를 <font color="#95b3d7">찾아서 호출하면</font>, 해당 Controller는 <font color="#95b3d7">실제로</font> <font color="#ff0000">비즈니스 로직</font>을 <font color="#95b3d7">수행하고 모델을 구성</font>하여 반환한다.
- Model and View Integration : Controller가 <font color="#95b3d7">반환한 Model 데이터와 View를 결합</font>하여 최종적으로 클라이언트에게 전달할 결과를 완성한다.
- View Rendering : 결합된 결과를 사용하여 <font color="#95b3d7">실제 응답을 생성</font>하고, 이를 클라이언트에게 반환하는데, 주로 <font color="#ff0000">템플릿 엔진</font>을 통해 <font color="#95b3d7">동적으로 HTML을 생성</font>하게 된다.
- Interceptor Processing : <font color="#95b3d7">인터셉터를 사용</font>하여 요청 전후에 특정 작업을 수행하도록 한다.
- Exception Handling : 예외를 처리하고, 클라이언트에게 <font color="#95b3d7">적절한 오류 페이지를 제공</font>한다.

Spring MVC의 DispatcherServlet은 일반적으로 <font color="#fac08f">web.xml 파일</font>에 설정되어 있다. 이 Servlet은 클라이언트의 모든 HTTP 요청을 처리하며, HandlerMapping, HandlerAdaptor, ViewResolver 등과 함께 다양한 Bean들과 협력하여 흐름을 제어한다.