# 1. DB 연결하기
Dynamic Web Project > Ex_0103_MyBatis
Spring Framework와 MyBatis를 이용하여 DB에 연결하자.
## 1.1. 라이브러리 세팅
- [[2024-01-02 2. Spring Framework#1. Spring Framework 설정|다운로드 방법]]
- Spring_Study/util/
	- spring-framework-4.3.0.RC2
		- libs
			- <font color="#92cddc">spring-beans-4.3.0.RC2.jar</font> [[#spring-beans|설명으로 이동]]
			- <font color="#92cddc">spring-context-4.3.0.RC2.jar</font> [[#spring-context|설명으로 이동]]
			- <font color="#92cddc">spring-core-4.3.0.RC2.jar</font> [[#spring-core|설명으로 이동]]
			- <font color="#92cddc">spring-expression-4.3.0.RC2.jar</font> [[#spring-expression|설명으로 이동]]
			- <font color="#d99694">spring-aop-4.3.0.RC2.jar</font> [[#spring-aop|설명으로 이동]] <font color="#c3d69b">오늘 교육 처음 사용</font>
			- <font color="#d99694">spring-jdbc-4.3.0.RC2.jar</font> [[#spring-jdbc|설명으로 이동]] <font color="#c3d69b">오늘 교육 처음 사용</font>
			- <font color="#d99694">spring-tx-4.3.0.RC2.jar</font> [[#spring-tx|설명으로 이동]] <font color="#c3d69b">오늘 교육 처음 사용</font>
	- commons-logging-1.2
		- <font color="#92cddc">commons-logging-1.2.jar</font> [[#commons-logging|설명으로 이동]]
- MyBatis_Study/Util/mybatis-3.5.14
	- <font color="#d99694">mybatis-3.5.14.jar</font> [[#mybatis|설명으로 이동]]
- [MyBatis.org](https://blog.mybatis.org/) 접속 - <font color="#c3d69b">Spring을 지원하는 라이브러리 다운로드</font>
	- Products
	- Products > Integration > Spring > Links > download
	- mybatis-spring-1.3.2 > Assets > mybatis-spring-1.3.2.zip download
	- UPZIP <font color="#7f7f7f">(Spring_Study/util/mybatis-spring-1.3.2)</font>
	- <font color="#d99694">mybatis-spring-1.3.2.jar</font> [[#mybatis-spring|설명으로 이동]]
- [Apache.org](https://www.apache.org/) 접속 - 
	- APACHE PROJECT LIST > Commons
		- Apache Commons Proper > Component > DBCP
			- Releases > downloads link
			- Archives > archives...
			- binaries/ > commons-dbcp-1.4-bin.zip download
			- UPZIP <font color="#7f7f7f">(Spring_Study/util/commons-dbcp-1.4)</font>
			- <font color="#d99694">commons-dbcp-1.4.jar</font> [[#commons-dbcp|설명으로 이동]]
		- Apache Commons Proper > Component > Pool
			- <font color="#7f7f7f">(side bar)</font> Releases > Apache Commons Pool Downloads page
			- Archives > archives...
			- binaries/ > commons-pool-1.6-bin.zip download
			- UPZIP <font color="#7f7f7f">(Spring_Study/util/commons-pool-1.6)</font>
			- <font color="#d99694">commons-pool-1.6.jar</font> [[#commons-pool|설명으로 이동]]
- mysql-connector도 같이 있어야 하지만 Tomcat이 설치된 곳에 이미 넣어 두었기 때문에 굳이 넣을 필요가 없다. 안 넣어 뒀다면 넣어라.
- src/main/webapp/WEB-INF/lib에 넣기
## 1.2. EmpVO.java
src/main/java > mybatis.vo > Class
### 코드 전문
```java
package mybatis.vo;

public class EmpVO {
	
	private String empno, ename, deptno;

	public String getEmpno() {
		return empno;
	}

	public void setEmpno(String empno) {
		this.empno = empno;
	}

	public String getEname() {
		return ename;
	}

	public void setEname(String ename) {
		this.ename = ename;
	}

	public String getDeptno() {
		return deptno;
	}

	public void setDeptno(String deptno) {
		this.deptno = deptno;
	}

}
```
## 1.3. emp.xml
src/main/java > mybatis/mapper > XML
### 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
 	 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  	"https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace = "emp">
	<select id="all" resultType="mybatis.vo.EmpVO">
		SELECT * FROM emp
	</select>
</mapper>
```
## 1.4. EmpDAO.java
src/main/java > mybatis.dao > Class
### 1.4.1. 기존의 DAO 형태
![[2023-12-13 MVC 활용#MemoDAO.java]]
### 1.4.2. SqlSession과 FactoryService를 bean으로 만든 DAO
```java
private SqlSession ss;
```
- SqlSession을 멤버 변수로 선언한다.

```java
public SqlSession getSs() {
	return ss;
}

public void setSs(SqlSession ss) {
	this.ss = ss;
}
```
- ss에 대한 getter와 setter를 생성한다.
- config.xml 문서에서 현재 DAO를 정의할 때 `<property/>`를 정의하면서 반드시 `setSs()`를 통해 SqlSession이 저장되도록 하기 위함이다.

```java
public EmpVO[] getAll() {
	EmpVO[] ar = null;
	
	List<EmpVO> list = ss.selectList("emp.all");
	
	return ar;
}
```
- 모든 사원 정보를 가져오기 위한 `getAll()` 함수를 생성한다.
- 이때 static으로 선언할 필요가 없다.

```java
if(list != null && list.size() > 0) {
	ar = new EmpVO[list.size()];
	list.toArray(ar);
}
```
- list 아래에 if 문을 추가한다.
- 이때 ss를 닫아서는 안 된다. 계속 쓸 것이기 때문.
### 코드 전문
```java
package mybatis.dao;

import java.util.List;

import org.apache.ibatis.session.SqlSession;

import mybatis.vo.EmpVO;

public class EmpDAO {
	
	private SqlSession ss;

	public SqlSession getSs() {
		return ss;
	}

	public void setSs(SqlSession ss) {
		this.ss = ss;
	}
	
	public EmpVO[] getAll() {
		EmpVO[] ar = null;
		
		List<EmpVO> list = ss.selectList("emp.all");
		if(list != null && list.size() > 0) {
			ar = new EmpVO[list.size()];
			list.toArray(ar);
		}
				
		return ar;
	}

}
```
## 1.5. config.xml
src/main/java > XML
### 1.5.1. 가장 먼저 만들어야 할 것 - BatisDataSource
Apache의 dbcp 라이브러리가 제공하는 BasicDataSource를 생성한다.

```xml
<bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	<property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql://localhost:3306/test_db"/>
	<property name="username" value="test_admin"/>
	<property name="password" value="1111"/>
</bean>
```
- id는 DataSource의 ds로 정하였다.
- 만약 서버가 내려갈 때는 닫아야 한다. 서버가 내려간다는 것은 이벤트이며 destroy-method를 사용한다. 이를 설정하면 서버가 내려갈 때 리소스들을 수거한다.
- 필요한 DataSource의 멤버 변수를 초기화한다.
### 1.5.2. 두 번째로 만들어야 할 것 - FactoryBean
mybatis-spring 라이브러리가 가지고 있는 FactoryBean 객체를 생성한다.
이것은 DataSource가 있어야 만들 수 있다.
우리가 FactoryService를 만들었던 것이 mybatis-spring 라이브러리의 FactoryBean에 있다.

```xml
<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="ds"/>
	<property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
</bean>
```
- 처음 생성된 DataSource를 현재 객체의 dataSource라는 멤버 변수에 저장한다.
- mapper<font color="#7f7f7f">(예 : emp.xml)</font>의 위치를 지정한다.
	- classpath는 src를 의미한다. 즉 Java Resources > src/main/java를 의미한다.
	- `*.xml`은 해당 폴더의 모든 XML 파일을 mapper로 인지한다.
	- 서브 폴더도 지정이 가능하다!
### 1.5.3. 세 번째로 만들어야 할 것 - SqlSession
factory를 통해 SqlSession과 같은 객체를 생성한다.

```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg ref="factory"/>
</bean>
```
- SqlSessionTemplate는 생성자 인젝션을 주어야 한다.
### 1.5.4. 네 번째로 만들어야 할 것 - DAO
SqlSession을 가지는 DAO 객체를 생성하는 injection을 정의한다.

```xml
<bean id="empDao" class="mybatis.dao.EmpDAO">
	<property name="ss" ref="sqlSession"/>
</bean>
```
### 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
		<property name="url" value="jdbc:mysql://localhost:3306/test_db"/>
		<property name="username" value="test_admin"/>
		<property name="password" value="1111"/>
	</bean>
	<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="ds"/>
		<property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
	</bean>
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg ref="factory"/>
	</bean>
	<bean id="empDao" class="mybatis.dao.EmpDAO">
		<property name="ss" ref="sqlSession"/>
	</bean>

</beans>
```
## 1.6. all.jsp
src > main > webapp > JSP
### 1.6.1. 확인을 위한 scriptlet(스크립트릿)
```jsp
<%
	BeanFactory bf = new ClassPathXmlApplicationContext("config.xml");
	EmpDAO dao = bf.getBean(EmpDAO.class);
	EmpVO[] ar = dao.getAll();
%>
	<h1>사원 목록</h1>
	<ul>
	<%
		for(EmpVO vo : ar){
	%>
		<li><%=vo.getEmpno() %>, <%=vo.getEname() %>, <%=vo.getDeptno() %></li>
	<%
		}
	%>
	</ul>
```
- springframework에 있는 BeanFactory를 선택하자.
- ClassPathXmlApplicationContext는 기본적으로 <font color="#c3d69b">src/main/java를 인지</font>한다.
	- <font color="#c3d69b">webapp/WEB-INF/lib도 같이 인지</font>한다.
	- 따라서 "config.xml"만 입력하여도 위치를 찾을 수 있다.
- EmpDAO를 찾아 전체 사원 정보 받기 함수를 수행하였다.
### 코드 전문
```jsp
<%@page import="mybatis.vo.EmpVO"%>
<%@page import="mybatis.dao.EmpDAO"%>
<%@page import="org.springframework.context.support.ClassPathXmlApplicationContext"%>
<%@page import="org.springframework.beans.factory.BeanFactory"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<%
	BeanFactory bf = new ClassPathXmlApplicationContext("config.xml");
	EmpDAO dao = bf.getBean(EmpDAO.class);
	EmpVO[] ar = dao.getAll();
%>
	<h1>사원 목록</h1>
	<ul>
	<%
		for(EmpVO vo : ar){
	%>
		<li><%=vo.getEmpno() %>, <%=vo.getEname() %>, <%=vo.getDeptno() %></li>
	<%
		}
	%>
	</ul>
</body>
</html>
```
# 2. 실질적인 MVC 구현!
Dynamic Web Project > Ex_0103_MVC
## 2.1. 라이브러리 세팅
### 2.1.1. 필요 없는 라이브러리
- commns-dbcp
- commons-pool
- mybatis
- mybatis-spring
- spring-jdbc
- spring-tx
### 2.1.2. 추가할 라이브러리
- Spring_Study/util/spring-framework/libs
	- <font color="#92cddc">spring-web-4.3.0.RC2.jar</font>
	- <font color="#92cddc">spring-webmvc-4.3.0.RC2.jar</font>
EL이나 JSTL을 쓰고 싶다면
- JSP_Study/util/jakarta-taglibs-standard-1.1.2/lib
	- <font color="#92cddc">jstl.jar</font>
	- <font color="#92cddc">standard.jar</font>
## 2.2. Controller.java
src/main/java > Servlet
- Superclass : org.springframework.web.servlet.DispatcherServlet
	- <font color="#d99694">spring-web 라이브러리가 이 Servlet을 갖고 있으므로 얘(?) 것을 상속받는다.</font>
- URL mappings : Edit : `*.inc`
	- 확장자가 inc인 것은 전부 Servlet을 의미한다.
	- 확장자를 `*` 또는 아무거나 해도 된다.
- Which method stubs would you like to create? 부분 모두 해제
- `private static final long serialVersionUID = 1L;` 삭제
### 2.2.1 설정을 완료한 Controller 화면
```java
import javax.servlet.annotation.WebServlet;
import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;

/**
 * Servlet implementation class Controller
 */
@WebServlet("*.inc")
public class Controller extends DispatcherServlet {

}
```
- Controller가 완성되었다.
- 현재 컨트롤러는 `WEB-INF/{컨트롤러 이름}-servlet.xml`과 연동되었다.
	- 즉 `WEB-INF/Controller-servlet.xml`과 연동되었다.
	- 이제 Controller-servlet.xml을 만들어야 한다.
## 2.3. Controller-servlet.xml
src > main > webapp > WEB-INF > XML
### 2.3.1. 처음 화면 세팅
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```
- 이전에 만든 config.xml에 있는 내용과 동일하다.
### 2.3.2. 추가할 내용
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd 
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
```
- `xmlns:context="http://www.springframework.org/schema/context"` 추가
	- xmlns는 xml name space이다.
- `http://www.springframework.org/schema/context` 추가
- `http://www.springframework.org/schema/context/spring-context.xsd` 추가
### 2.3.3. Test1Action의 HttpServletRequest, HttpServletResponse를 명시하자.
```xml
<bean id="t1" class="ex1.action.Test1Action" name="/t1.inc"/>
```
### 2.3.4. View Resolver 설정
```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="suffix" value=".jsp"/>
	<property name="prefix" value="/WEB-INF/jsp/"/>
</bean>
```
- View Resolver를 설정한다.
- view 페이지의 확장자가 jsp임을 설정했다.
- view 페이지가 위치할 곳을 지정하였다. 기본 위치가 webapp이다.
### 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd 
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

	<bean id="t1" class="ex1.action.Test1Action" name="/t1.inc"/>
	
	<!-- View Resolver설정 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="suffix" value=".jsp"/> <!-- 확장자 지정 -->
        <property name="prefix" value="/WEB-INF/jsp/"/> <!-- 위치 지정 -->
    </bean>

</beans>
```
## 2.4. Test1Action.java
src/main/java > ex1.action > Class extends AbstractController
- Superclass : org.springframework.web.servlet.mvc.AbstractController
### 2.4.1. 처음 화면
```java
package ex1.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.AbstractController;

public class Test1Action extends AbstractController {

	@Override
	protected ModelAndView handleRequestInternal(HttpServletRequest arg0, HttpServletResponse arg1) throws Exception {
		// TODO Auto-generated method stub
		return null;
	}

}
```
- ModelAndView는 JSP의 경로를 담을 곳이다.
- arg0은 request, arg1은 response로 바꿔주자.
### 2.4.2. JSP에서 표현할 정보를 request에 저장하기
```java
request.setAttribute("msg", "환영합니다.");
ModelAndView mv = new ModelAndView();
mv.setViewName("ex1");

mv.addObject("str", "SpringMVC 연습");
```
- ModelAndView 반환객체를 생성한다.
- forward 할 JSP 경로를 mv에 저장한다.
	- "ex1"은 WEB-INF/jsp/ex1.jsp를 의미한다. <font color="#7f7f7f">이따가 만들거임.</font>
- mv에 필요한 정보를 저장하여 JSP에서 바로 활용할 수 있도록 한다.
- `request.setAttribute()`보다 `ModelAndView.addObject()`를 더 권장한다.
	- ModelAndView는 request보다 더 풍부한 API를 제공하며, 데이터를 이름과 함께 전달할 수 있어 컴파일 타임에 타입 안정성을 제공하며, 속성의 이름이 문자열이 아니라 직접적인 메서드 호출로 이루어지므로 오타의 위험이 감소한다는 장점이 있다.
	- 또한 request는 Servlet API에 의존하며, Spring MVC의 특정한 기능들과의 통합이 부족할 수 있다.
- 해당 Action을 Controller-servlet.xml에 명시하자.
### 코드 전문
```java
package ex1.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.AbstractController;

public class Test1Action extends AbstractController {

	@Override
	protected ModelAndView handleRequestInternal(HttpServletRequest request, HttpServletResponse response) throws Exception {
		
		request.setAttribute("msg", "환영합니다.");
		ModelAndView mv = new ModelAndView();
		mv.setViewName("ex1");
		
		mv.addObject("str", "SpringMVC 연습");
		
		return mv;
	}

}
```
## 2.5. ex1.jsp
src > main > webapp > WEB-INF > jsp > JSP
- URL에서 Controller-servlet.xml의 Test1Action의 경로를 지정한 bean 객체의 name인 "t1.inc"가 추가되어야 Test1Action이 동작하면서 수행할 수 있다.
	- `http://localhost:8080/Ex_0103_MVC/t1.inc` 이렇게 수정하면 정상 출력된다.
### 2.5.1. EL로 request, ModelAndView 파라미터 확인하기
```jsp
<h1>${str }</h1>
<h2>${msg }</h2>
```
### 코드 전문
```JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>${str }</h1>
	<h2>${msg }</h2>
</body>
</html>
```
## 2.6. 날짜 표시하기
### 2.6.1. Controller-servlet.xml
src > main > webapp > WEB-INF > XML
### 2.6.1.1. 내가 추가한 내용
```xml
<bean id="t2" class="ex1.action.Test2Action" name="/t2.inc"/>
```
#### 2.6.1.2. 강사님 코드
상동
### 2.6.2. Test2Action.java
src/main/java > ex1.action > Class extends AbstractController
#### 2.6.2.1. 내가 작성한 내용
```java
ModelAndView mv = new ModelAndView();
mv.setViewName("ex2");
Date today = new Date();
mv.addObject("today", today);
return mv;
```
#### 2.6.2.2. 강사님 코드
```java
LocalDate now = LocalDate.now();
mv.addObject("today", now.toString());
```
- LocalDate로 해도 된다.
- 이렇게 하면 "2024-01-03"와 같이 출력된다.
### 2.6.3. ex2.jsp
src > main > webapp > WEB-INF > jsp > JSP
#### 2.6.3.1. 내가 작성한 내용
```jsp
<body>
	<h1>${today }</h1>
</body>
```
## 2.7. AbstractController를 상속받지 않고 implements를 이용해 Controller를 만드는 방법
### 2.7.1. Test2Action.java
src/main/java > ex1.action > Class

```java
public class Test2Action extends AbstractController {
```
- 여기를 아래와 같이 바꾼다.

```java
public class Test2Action implements Controller {

	@Override
	public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
```
- 오버라이드 한 메서드도 private가 아닌 public으로 바꾸고 handleRequestInternal이 아닌 handleRequest로 바꾼다.
- 이는 인터페이스 기반의 프로그래밍을 지향하고, 유연성과 확장성을 높이기 위함이다.
- POJO 기반
	- Plain Old Java Object의 약자로, 단순히 자바 객체를 나타낸다.
	- 특정한 프레임워크나 환경에 종속되지 않은, 순수한 자바 객체를 의미한다.
### 2.7.2. Test3Action.java
src/main/java > ex1.action > Class
- 기본적인 클래스 생성
#### 2.7.2.1. 처음 화면
```java
package ex1.action;

public class Test3Action {

}
```
#### 2.7.2.2. 컨트롤러를 선언하고, 매핑하기
```java
package ex1.action;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class Test3Action {

	@RequestMapping("/ex3.inc")
	public String exe() {
		return "ex3";
	}
	
}
```
- "ex3"은 "/WEB-INF/jsp/ex3.jsp"를 의미한다.
- Controller-servlet.xml에서 Test3Action을 추가하자.
- `@RequestMapping`에 적힌 "/ex3.inc"를 URL에 추가하면 매핑이 된다.
- 이렇게 하면 하나의 컨트롤러 안에서 여러 개의 일처리를 할 수 있다.
#### 2.7.2.3. ModelAndView를 반환을 같이 할 수 있다.
```java
@RequestMapping("/ex4.inc")
public ModelAndView ex4() {
	ModelAndView mv = new ModelAndView();
	mv.addObject("msg", "코딩할고래");
	mv.setViewName("ex4");
	
	return mv;
}
```
- 위 내용을 추가한 후 ex4.jsp를 만들면 된다.
### 2.7.3. Controller-servlet.xml에 Test3Action 추가
```xml
<bean id="t3" class="ex1.action.Test3Action"/>
```
### 2.7.4. ex4.jsp
```jsp
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>${msg }</h1>
</body>
</html>
```
- 잘 출력됨.
## 2.8. 날짜 출력하는 페이지 추가
### 2.8.1. Test3Action.java에 ex5.jsp 추가 매핑
```java
@RequestMapping("/ex5.inc")
public ModelAndView ex5() {
	ModelAndView mv = new ModelAndView();
	LocalDate today = LocalDate.now();
	mv.addObject("today", today.toString());
	mv.setViewName("ex5");
	
	return mv;
}
```
### 2.8.2. ex5.jsp
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>현재 날짜 : ${today }</h1>
</body>
</html>
```
## 2.9. 추가 예제
### 2.9.1. ex5.jsp에 [[#form 태그가 submit 할 때 값을 가져갈 수 있는 태그|form]]을 추가하자.
```jsp
<form action="ex6.inc" method="post">
	<input type="hidden" name="c_idx" value="13"/>
	<input type="hidden" name="u_name"/>
	<input type="text" name="unit"/>
	<br/>
	<input type="checkbox" name="hobby" value="game"/><label>게임</label>
	&nbsp;
	<input type="checkbox" name="hobby" value="movie"/><label>영화감상</label>
	&nbsp;
	<input type="checkbox" name="hobby" value="tennis"/><label>테니스</label>
	&nbsp;
	<button type="button" onclick="ex6(this.form)">Send</button>
</form>
```
- u_name의 input 태그는 hidden 처리되어 있어 값을 입력할 수 없다.
- button에 연결된 ex6() 함수의 파라미터에 this.form을 주었다.

```jsp
<script>
	function ex6(ff){
		ff.u_name.value = "홍길동";
		ff.submit();
	}
</script>
```
- 함수에서 u_name에 "홍길동"을 저장하였다.
### 2.9.2. Test3Action.java에서 ex5.jsp를 매핑하면서 파라미터 받기
```java
@RequestMapping("/ex6.inc")
public ModelAndView ex6(String c_idx, String u_name, String unit, String[] hobby) {
	ModelAndView mv = new ModelAndView();
	mv.addObject("c_idx", c_idx);
	mv.addObject("u_name", u_name);
	mv.addObject("unit", unit);
	mv.addObject("hobby", hobby);
	mv.setViewName("ex6");
	
	return mv;
}
```
- 파라미터값을 ex5.jsp의 form의 name과 같은 이름으로 지정하였다.
### 2.9.3. ex6.jsp에서 확인하기
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h2>과정코드: ${c_idx }</h2>
	<h2>사용자명: ${u_name }</h2>
	<h2>분류: ${unit }</h2>
	<h2>취미:</h2>
	<ol>
	<c:forEach var="str" items="${hobby }">
		<li>${str }</li>	
	</c:forEach>
	</ol>
</body>
</html>
```
## 2.10. ModelAndView에 파라미터가 있을 수도 있고 없을 수도 있다면
### 2.10.1. TestVO.java
src/main/java > ex1.vo > Class
- 파라미터로 넘어오는 이름들을 멤버변수로 선언하자.
```java
package ex1.vo;

public class TestVO {
	
	private String c_idx, u_name, unit, category;
	private String[] hobby;
	
	public String getC_idx() {
		return c_idx;
	}
	public void setC_idx(String c_idx) {
		this.c_idx = c_idx;
	}
	public String getU_name() {
		return u_name;
	}
	public void setU_name(String u_name) {
		this.u_name = u_name;
	}
	public String getUnit() {
		return unit;
	}
	public void setUnit(String unit) {
		this.unit = unit;
	}
	public String getCategory() {
		return category;
	}
	public void setCategory(String category) {
		this.category = category;
	}
	public String[] getHobby() {
		return hobby;
	}
	public void setHobby(String[] hobby) {
		this.hobby = hobby;
	}

}
```
### 2.10.2. Test3Action.java에서 ex7.jsp mapper를 만들자.
- 위에서 만든 vo 객체를 이용해서 파라미터를 넣어 본다.
```java
@RequestMapping("/ex7.inc")
public ModelAndView ex7(TestVO vo) {
	ModelAndView mv = new ModelAndView();
	mv.addObject("vo", vo);
	mv.setViewName("ex7");
	
	return mv;
}
```
### 2.10.3. ex7.jsp
src > main > webapp > WEB-INF > jsp > JSP
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h2>과정코드: ${vo.c_idx }</h2>
	<h2>사용자명: ${vo.u_name }</h2>
	<h2>분류: ${vo.unit }</h2>
	<h2>취미:</h2>
	<ol>
	<c:forEach var="str" items="${vo.hobby }">
		<li>${str }</li>	
	</c:forEach>
	</ol>
</body>
</html>
```
### 2.10.4. ex5.jsp
- form의 action을 수정하자.

```jsp
<form action="ex6.inc" method="post">
```
- 이것을 아래와 같이 바꾼다.

```jsp
<form action="ex7.inc" method="post">
```
### 2.10.5. TestVO 객체가 어떻게 생성됐을까
1. ex5.jsp > form > action에서 "ex7.inc"에 의해 Controller.java로 간다.
2. Controller.java는 Controller-servlet.xml과 연동된다.
3. Controller-servlet.xml에서 "ex7.inc"로 매핑되어 있는 녀석을 부른다.
4. Test3Action에 "ex7.inc" 요청 매핑이 걸려 있으므로 이를 보고 동작한다.
5. <font color="#92cddc">파라미터에 있는 TestVO 객체를 생성하여 같은 이름끼리 매칭하여 자동으로 넣어준다.</font>
6. 이런 구조로 유효성 검사까지!?!?!?
## 2.11. Filter 만들기 - EncodingFilter
src/main/java > ex1.filter > Filter
- <font color="#c3d69b">한글 인코딩을 위한 필터를 생성하자.</font>
	- Superclass : org.springframework.web.filter.CharacterEncodingFilter
	- Filter mappings : Edit : `/*`
	- Initialization parameters : Add : 
		- Name : encoding
		- Value : utf-8
		- <font color="#d99694">이렇게 하면 encoding을 갖고 가서 문자 형식을 그렇게(?) 갖춰 버린다.</font>
	- Interfaces 삭제, Which method stubs would you like to create? 모두 해제
- 서버가 구동될 때 Filter가 가장 빨리 움직인다. 딱 한 번 움직이고 끝.
	- 서버가 구동되면서 Filter를 거쳐간다고 보면 됨.
- 필요 없는 메서드 삭제
### 2.11.1. 설정 완료 화면
```java
package ex1.filter;

import javax.servlet.annotation.WebFilter;
import javax.servlet.annotation.WebInitParam;

import org.springframework.web.filter.CharacterEncodingFilter;

/**
 * Servlet Filter implementation class EncodingFilter
 */
@WebFilter(
		urlPatterns = { "/*" }, 
		initParams = { 
				@WebInitParam(name = "encoding", value = "utf-8")
		})
public class EncodingFilter extends CharacterEncodingFilter {
	
}
```
- `encoding="utf-8"`이 CharacterEncodingFilter에 들어 있다.
# 3. 연습 문제
1. client가 "ex8.inc"를 요청한다.
2. input type="text" : EMPNO, ENAME, JOB, HIREDATE, SAL, DEPTNO
3. "저장" 버튼 누르면 "ex9.inc"
4. ex9.jsp에서 사번, 이름, 직종, 입사일, 급여, 부서코드가 "입력한 값"이라는 제목 아래에 출력된다.

# 참고
## 라이브러리 용도
### spring-beans
- 스프링 프레임워크에서 Bean 컨테이너와 빈 관리에 관련된 기능을 제공한다.
### spring-context
- 스프링 프레임워크의 애플리케이션 컨텍스트를 정의하고 관리한다.
### spring-core
- 스프링의 핵심 유틸리티 및 기본적인 기능을 제공한다.
- 프레임워크의 다양한 모듈에서 공통으로 사용되는 핵심 클래스들을 포함한다.
### spring-expression
- 스프링의 표현 언어<font color="#7f7f7f">(EL)</font>를 지원하는 데 사용된다.
### spring-aop
- Spring AOP<font color="#7f7f7f">(Aspect-Oriented Programming)</font>은 관점 지향 프로그래밍을 지원하는 모듈
- 횡단 관심사<font color="#7f7f7f">(cross-cutting concerns)</font>를 모듈화하고 코드 중복을 방지하기 위해 사용한다.
#### cross-cutting concerns
소프트웨어에서 여러 모듈이나 컴포넌트에 공통적으로 적용되는 관심사
### spring-jdbc
- JDBC<font color="#7f7f7f">(Java Database Connectivity)</font>를 쉽게 사용할 수 있도록 도와주는 모듈
- Spring의 JDBC 모듈은 데이터베이스 연동 및 관리를 단순화하고 트랜잭션 관리를 제공한다.
### spring-tx
- 선언적 트랜잭션 관리를 지원하며, 데이터베이스 및 기타 리소스에 대한 트랜잭션을 관리한다.
### commons-logging
- 로깅에 사용되는 라이브러리로, 스프링 프레임워크 초기에 사용되었지만 최근의 스프링은 SLF4J와 Logback을 주로 사용하고 있다.
#### logging
프로그램이 실행될 때 발생하는 <font color="#c3d69b">이벤트나 상태를 기록하는 행위</font>
주로 애플리케이션의 실행 로그, 에러 로 그 등을 생성하여 기록하는데 사용된다.
### mybatis
- 데이터베이스 연동을 위한 SQL 매핑 프레임워크
- SQL 쿼리를 자바 객체에 매핑하는 기능을 제공한다.
### mybatis-spring
- MyBatis와 Spring을 통합하는 데 사용되는 라이브러리
- MyBatis를 Spring과 통합하여 트랜잭션 관리 및 데이터베이스 연결 등의 작업을 스프링의 트랜잭션 매니저 및 데이터소스와 함께 사용할 수 있도록 지원한다.
### commons-dbcp
- 데이터베이스 커넥션 풀링을 제공하는 라이브러리
- 데이터베이스 연결을 관리하고 성능을 향상시킨다.
### commons-pool
- 객체 풀링을 제공하는 라이브러리
- 데이터베이스 커넥션 풀링과 같이 자주 사용되는 리소스의 풀링을 통해 성능을 최적화 할 수 있도록 도와준다.
#### pooling
비용이 높은 자원을 효율적으로 관리하기 위한 기술
데이터베이스 커넥션 풀링이나 객체 풀링과 같이 <font color="#c3d69b">자주 사용되는 리소스를 미리 생성하고 필요할 때 재사용</font>함으로써 선응을 향상시키는 기법
데이터베이스 커넥션 풀은 여러 클라이언트가 동시에 데이터베이스에 접속할 수 있도록 미리 일정 수의 커넥션을 생성하고, 필요할 때마다 클라이언트에게 재사용되도록 재공한다.
### spring-web
- Spring Web Application 개발에 필요한 기본적인 웹 지원 기능을 제공하는 라이브러리
- 웹 애플리케이션 컨텍스트 설정, 웹 애플리케이션 이벤트 처리 등
### spring-webmvc
- 스프링의 MVC 프레임워크를 지원하는 라이브러리
- 스프링 기반의 웹 애플리케잇연에서 MVC 아키텍쳐를 구현하고 컨트롤러, 뷰, 모델을 지원한다.
- `DispatcherServlet`이나 `@Controller` 어노테이션과 같은 웹 MVC 핵심 기능
### jstl
- JSP Standard Tag Library는 JSP 페이지에서 사용할 수 있는 태그 라이브러리
### standard
- JSTL의 구현체인 Jakarta Standard Tag Library를 포함하는 라이브러리
- JSTL을 사용하기 위해 필요한 라이브러리
## ModelAndView
Spring Framework에서 Controller가 처리한 결과 데이터 및 뷰 정보를 담기 위한 객체이다.
이 객체는 <font color="#92cddc">Controller가 처리한 모델 데이터를 뷰에 전달</font>하고, <font color="#92cddc">뷰의 이름을 지정</font>할 수 있도록 설계되어 있다.
### mv. View 이름 지정
- `ModelAndView`의 생성자 또는 `setViewName` 메서드를 통해 지정한다.
	- `ModelAndView`의 생성자는 뷰의 이름을 바로 설정할 수 있다.
- 뷰의 이름은 실제로 사용될 View Resolver에 의해 해석되어 실제 뷰 객체를 결정한다.
### mv. 데이터 추가
- `addObject` 메서드로 모델 데이터를 추가할 수 있다. 이 모델에 여러 데이터를 추가할 수 있다.
### mv. View Resolver
Spring MVC에서 Controller가 반환한 뷰의 이름을 <font color="#fac08f">실제 뷰 객체로 매핑</font>하는 역할을 한다.
#### mv.vr. View Resolver의 종류
- InternalResourceViewResolver
	- 주로 JSP<font color="#7f7f7f">와 같은 웹 애플리케이션</font>에서 사용된다.
	- 뷰 이름에 접두어<font color="#7f7f7f">(prefix)</font>와 접미어<font color="#7f7f7f">(suffix)</font>를 추가하여 실제 JSP 파일의 경로를 결정한다.
- FreeMarkerViewResolver
- TilesViewResolver
## form 태그가 submit 할 때 값을 가져갈 수 있는 태그