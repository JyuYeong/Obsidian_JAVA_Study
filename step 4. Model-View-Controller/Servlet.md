Servlet은 Java 언어를 기반으로 하는 [[#1.2. 웹 애플리케이션과 웹 애플리케이션 서버|웹 애플리케이션]]에서 동적인 웹 페이지를 생성하거나 웹 서버에서 요청을 처리하는 데 사용되는 자바 프로그래밍 기술이다.
Servlet은 [[#1.1. Java Servlet API|Java Servlet API]]를 구현하는 클래스로 작성되며, 주로 [[#1.2. 웹 애플리케이션과 웹 애플리케이션 서버|웹 애플리케이션 서버]]에서 실행된다.
<font color="#fac08f">Servlet은</font> 일종의 [[#1.3. 서버 사이드 컴포넌트|서버 사이드 컴포넌트]]로, <font color="#fac08f">클라이언트의 요청에 응답</font>하여 <font color="#fac08f">동적인 콘텐츠를 생성하고 처리</font>하는 역할을 한다.
* 정적인 웹 페이지는 미리 만들어져 저장되어 있지만, 동적인 웹 페이지는 클라이언트의 요청에 따라 생성되므로 실시간으로 데이터를 처리하거나 사용자에게 맞춤형 응답을 제공하는 데 유용하다.

Servlet은 주로 다음과 같은 작업에 사용된다.
* **웹 페이지 생성** : 데이터베이스와 연동하여 동적인 웹 페이지를 생성한다.
* **사용자 양식 처리** : 사용자로부터 입력을 받아 처리하고 응답한다.
* **세션 관련** : 사용자 세션을 추적하고 유지한다.
* **인증 및 보안** : 사용자의 인증을 처리하고 보안 기능을 추가할 수 있다.

Servlet은 Java EE<font color="#7f7f7f">(Enterprise Edition)</font> 스펙의 일부이며, Java Web Application에서 사용되는 주요 기술 중 하나이다. [[#1.1. Java Servlet API|Java Servlet]]는 [[#1.4. HTTP 프로토콜|HTTP 프로토콜]]을 기반으로 하며, <font color="#fac08f">서블릿 컨테이너</font><font color="#7f7f7f">(예 : Apache Tomcat)</font>에서 실행된다.

# 1. Java Servlet API

> [!summary]
> Java Servlet API는 Client의 HTTP request를 처리하고 response를 생성하는 데 활용된다.
> HttpServlet 클래스는 MVC pattern에서 Controller에 해당한다.

Java Servlet API는 Java Web Application을 개발하기 위한 API<font color="#7f7f7f">(응용 프로그래밍 인터페이스)</font>를 제공하는 Java 표준 스펙 중 하나이다.
Java Servlet API는 Java EE<font color="#7f7f7f">(Enterprise Edition)</font> 스펙의 일부로서, 동적인 웹 페이지를 생성하고 Web Application의 서버 측 로직을 구현하는 데 사용된다. Servlet은 주로 클라이언트의 HTTP 요청을 처리하고 응답을 생성하는 데 활용된다.

주요 특징과 개념은 다음과 같다.
## 1.1. Servlet 인터페이스

`javax.servlet.Servlet` 인터페이스는 모든 Servlet 클래스가 구현해야 하는 기본 인터페이스이다. `service` 메서드를 포함하고 있으며, 이 메서드에서 클라이언트의 요청을 처리하고 응답을 생성한다.
## 1.2. GenericServlet 클래스

`javax.servlet.GenericServlet`은 `Servlet` 인터페이스를 구현한 추상 클래스로, 기본적인 Servlet의 동작을 구현한다. 개발자는 이 클래스를 상속하여 사용자 정의 Servlet 클래스를 작성할 수 있다.
## 1.3. HttpServlet 클래스

`javax.servlet.http.HttpServlet`은 `GenericServlet`을 상속한 클래스로서, HTTP 프로토콜을 기반으로 하는 웹 애플리케이션을 개발할 때 사용된다. `doGet`, `doPost`, `doPut`, `doDelete` 등의 HTTP 메서드에 대한 처리를 담당하는 메서드를 제공한다.

<font color="#d99694">이를 상속받아 구현한 사용자 정의 Servlet이 MVC 패턴에서 Controller를 담당한다.</font>
- MVC 패턴에서 Controller는 사용자의 입력을 받아 처리하고, 그 결과에 따라 모델을 업데이트하거나, 뷰를 선택하는 부분을 의미한다.
- 사용자가 요청한 URL에 따라 적절한 Servlet이 호출되고, 해당 Servlet은 요청을 처리하고 모델을 업데이트하거나 다른 비즈니스 로직을 수행한 뒤, 적절한 뷰를 선택하여 클라이언트에게 응답을 생성한다.
## 1.4. ServletRequest 및 ServletResponse 인터페이스

`javax.servlet.ServletRequest`와 `javax.servlet.ServletResponse`는 각각 클라이언트의 요청과 서버의 응답을 나타내는 인터페이스이다. 이를 확장한 구체적인 클래스로는 `HttpServletRequest`, `HttpServletResponse` 등이 있다.
## 1.5. Servlet 컨테이너

<font color="#92cddc">Servlet은 웹 애플리케이션 서버의 Servlet 컨테이너에서 실행</font>된다. 대표적인 Servlet 컨테이너로는 <font color="#92cddc">Apache Tomcat</font>, Jetty, IBM WebSphere 등이 있다. <font color="#c3d69b">Servlet 컨테이너는 Servlet의 생명주기를 관리</font>하고, 요청에 대한 스레드를 생성하여 Servlet을 실행한다.
## 1.6. Servlet 생명주기

Servlet은 <font color="#fac08f">초기화</font><font color="#7f7f7f">(init)</font>, <font color="#fac08f">서비스</font><font color="#7f7f7f">(service)</font>, <font color="#fac08f">소멸</font><font color="#7f7f7f">(destroy)</font>과 같은 생명주기를 가지고 있다. <font color="#c3d69b">Servlet 컨테이너는 이러한 생명주기 메서드를 호출</font>하여 Servlet의 인스턴스를 관리한다.
## 1.7. 세션 관리

Servlet API는 클라이언트의 세션을 관리하는 기능도 제공한다. 세션을 사용하여 클라이언트와 서버 간의 상태를 유지할 수 있다.
# 2. 웹 애플리케이션과 웹 애플리케이션 서버
## 2.1. Web Application

웹 애플리케이션은 웹을 통해 사용자에게 서비를 제공하는 소프트웨어 응용 프로그램이다. 온라인 쇼핑, 소셜 미디어, 뉴스, 온라인 은행, 비즈니스 도구, 게임 등이 있다.
웹 애플리케이션은 Client-Server 아키텍쳐를 기반으로 하며, 클라이언트는 웹 브라우저를 통해 액세스하고, 서버는 요청을 받아 처리하여 결과를 클라이언트에게 제공한다.
## 2.2. Web Application Server, WAS

웹 애플리케이션 서버는 웹 애플리케이션을 실행하고 관리하는 소프트웨어 환경이다.

웹 애플리케이션 서버의 주요 기능은 다음과 같다.

### 2.2.1. Servlet 및 JSP 실행
- Servlet과 JSP<font color="#7f7f7f">(JavaServer Pages)</font>와 같은 웹 컴포넌트들을 실행하여 동적인 웹 페이지를 생성한다. 이를 통해 Java 언어로 작성된 웹 애플리케이션을 실행할 수 있다.

### 2.2.2. 데이터베이스 연동
- 데이터베이스와의 통신을 관리하여 데이터를 읽고 쓰는 데 사용된다.

### 2.2.3. 세션 관리
- 클라이언트와 서버 간의 세션을 관리하여 상태를 유지한다.

### 2.2.4. 보안
- 사용자 인증, 권한 부여 및 기타 보안 기능을 제공한다.

### 2.2.5. 네트워크 서비스
- HTTP 프로토콜을 기반으로 클라이언트와 통신하며, HTTPS와 같은 보안 프로토콜을 지원할 수 있다.

### 2.2.6. 스레드 관리
- 다수의 동시 요청을 처리하기 위한 스레드 풀을 관리한다.
# 3. 서버 사이드 컴포넌트

Server-Side Component는 Web Application의 <font color="#95b3d7">서버 측에서 실행되고 동작하는 소프트웨어 모듈 또는 구성 요소</font>를 나타낸다. 클라이언트의 요청을 받아들이고, 데이터 처리, 비즈니스 로직 실행, 데이터베이스 엑세스, 동적 콘텐츠 생성, 세션 관리 등과 같은 서버 측 작업을 수행한다.

서버 사이드 컴포넌트는 주로 다음과 같은 형태로 나타날 수 있다.
## 3.1. Servlet
Java 언어로 작성된 서버 사이드 컴포넌트로, Java Servlet API에 기반하여 동작한다.
주로 웹 애플리케이션의 로직을 처리하고 동적인 콘텐츠를 생성하는 데 사용된다.
## 3.2. Server-Side Script
PHP, Python, Ruby 등과 같은 스크립트 언어로 작성된 코드를 서버에서 실행하는 방식이다.
서버 사이드 스크립트는 클라이언트에게 동적인 HTML 페이지를 전송하거나 데이터베이스와 상호 작용하는 등의 작업을 수행한다.
## 3.3. Active Server Pages.NET
Microsoft의 ASP.NET 기술을 사용하여 개발된 서버 사이드 컴포넌트이다.
ASP.NET은 C# 또는 VB.NET과 같은 언어로 작성된 코드를 실행하여 웹 애플리케이션을 처리한다.
## 3.4. JavaServer Pages
Java 언어를 기반으로 하는 서버 사이드 컴포넌트로, Java 코드를 HTML 문서 안에 삽입하여 동적인 웹 페이지를 생성한다.
# 4. HTTP 프로토콜
<font color="#92cddc">Hypertext Transfer Protocol</font>, HTTP는 웹에서 데이터를 주고받기 위한 프로토콜로, 클라이언트와 서버 간의 통신을 위한 규약을 정의한다.
HTTP는 주로 HTML 문서를 전송하는 데 사용되지만, 이미지, 비디오, 오디오, 텍스트 등의 다른 형식의 데이터도 전송할 수 있다.
HTTP는 클라이언트와 서버 간에 요청<font color="#7f7f7f">(Request)</font>과 응답<font color="#7f7f7f">(Response)</font>을 주고받는데, 이는 일반적으로 다음과 같은 단계를 따른다.

클라이언트 요청(Request)
- 클라이언트<font color="#7f7f7f">(예 : 웹 브라우저)</font>가 서버에게 원하는 리소스<font color="#7f7f7f">(예 : 웹 페이지)</font>에 대한 요청을 보낸다. 요청은 HTTP 메서드<font color="#7f7f7f">(GET, POST, PUT, DELETE 등)</font>와 헤더, 본문 데이터 등으로 구성된다.

서버 응답(Response)
- 서버는 클라이언트의 요청을 받아 해당하는 응답을 생성한다. 응답은 HTTP 상태 코드, 헤더, 본문 데 이터 등으로 구성된다. 상태 코드는 요청의 성공 여부나 실패 원인을 나타낸다.

 클라이언트 처리
- 클라이언트는 받은 응답을 처리하고, 필요에 따라 다시 서버에 추가적인 요청을 보낼 수 있다. 브라우저는 응답을 렌더링하여 사용자게에 보여준다.

HTTP는 상태를 유지하지 않는<font color="#7f7f7f">(stateless)</font> 프로토콜로 알려져 있다. 이는 각각의 요청이 서로 독립적이며 이전의 요청과 아무런 관련이 없다는 것을 의미한다. 따라서 클라이언트와 서버는 각 요청에 대한 필요한 정보를 함께 주고 받아야 한다.

HTTPS는 하이퍼텍스트 전송 프로토콜 보안<font color="#7f7f7f">(Hypertext Transfer Protocol Secure)</font>, 즉 HTTP의 보안 버전으로 데이터의 암호화를 통해 통신의 기밀성을 보장한다. HTTPS는 TLS<font color="#7f7f7f">(Transport Layter Security)</font> 또는 이전에 사용되던 SSL<font color="#7f7f7f">(Secure Sockets Layer)</font> 프로토콜을 사용하여 보안을 강화한다.

# 참고
---
## 비즈니스 로직이란?
- 데이터를 <font color="#95b3d7">DB에 저장</font>하거나, DB로부터 데이터를 <font color="#95b3d7">추출 및 가공</font>한다.
- 데이터의 <font color="#95b3d7">유효성 감사</font>를 하고, 필요에 따라 <font color="#95b3d7">오류 메시지</font>를 생성하여 사용자에게 알려준다.
- 할인 계산, 주문 처리, 결제 로직 등의 <font color="#95b3d7">비즈니스 규칙</font>을 적용한다.
- 사용자의 <font color="#95b3d7">권한 확인</font>을 하고, 접근 권한이 필요한 기능에 대한 검사를 수행한다.
- 비즈니스 프로세스에 따라 작업이 진행되는 흐름을 관리한다.