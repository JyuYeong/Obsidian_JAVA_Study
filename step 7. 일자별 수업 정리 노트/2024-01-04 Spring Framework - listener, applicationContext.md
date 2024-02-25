# 1. Intro
Client <font color="#7f7f7f">(request)</font> > DispatcherServlet <font color="#7f7f7f">-(연동)-</font> Controller-servlet.xml > beans
- DispatcherServlet을 Controller로 사용한다.
- Controller-servlet.xml의 Beans를 전부 생성 후 그것을 계속 이용한다.
- bean을 만드는 3가지 방법:
	1. implements AbstractController
	2. Controller Interface
	3. annotation `@Controller`
		- annotation:
			- 외부에서 호출 필요
			- 메타데이터
			- 기능이 부여된 주석
- Filter는 DispatcherServlet보다 먼저 생성이 된다.
	- Filter - Listener - Servlet
	- Tomcat이 구동될 때 Filter와 Listener가 생성된다.
	- Servlet은 요청해야 만들어진다.
- <font color="#fac08f">Listener는 DB에 관련된 정보를 저장한다.</font>
	- <font color="#fac08f">Listener는 applicationContext.xml과 연동된다.</font>
	- applicationContext.xml에는 ds<font color="#7f7f7f">(DataSource)</font>, factory, ss<font color="#7f7f7f">(SqlSession)</font>, dao<font color="#7f7f7f">(여러 개 있을 수 있다)</font>가 있다.
		- [[2024-01-03 Spring Framework#1.5. config.xml|여기]]에서 만든 것을 말한다.
		- DataSource -> factory -> SqlSession -> dao
- 따라서 Tomcat이 구동되면 ds, factory, ss, dao 등이 생성되고, Client의 요청이 있는 경우 DispatcherServlet이 생성되면서 Controller-servlet에 있는 bean들이 생성된다.
- <font color="#fac08f">Test4Action에 멤버변수로 dao를 넣으면 미리 생성된 applicationContext의 dao가 자동으로 연결이 되어야 한다</font>; `@Autowired`
	- 이미 context에 저장되어 있는<font color="#7f7f7f">(자신보다 먼저 저장되어 있는)</font> 것들 중에 선별해서 넣어 버려요(?)
	- 어떻게? 자료형이 기준이다?
	- <font color="#d99694">applicationContext.xml = root-context.xml</font>
# 2. Listener, applicationContext를 위한 환경 설정
1. Eclipse -> Help -> Eclilpse Marketplace...
2. search: spring
3. Install: Spring Tools 3 Add-On for Spring Tools 4
4. Confirm
5. Review Licenses Accept > Finish
6. Eclipse 화면 우측 하단 Installing Software 확인
7. Trust: Select All > Trust Selected
8. Restart Now
## 2.1. 설치 완료 후 오류 발생 시?
1. Eclipse > Help > Install New Software...
2. Work with: `2022-09 - https://download.eclipse.org/releases/2022-09`
3. Select All > Next
4. Install Remadiation Page > Next
5. Install Details > Finish
6. Accept > Finish
## 2.2. 재도전 - 이게 진짜
1. [깃허브]([https://github.com/spring-attic/toolsuite-distribution/wiki/Spring-Tool-Suite-3](https://github.com/spring-attic/toolsuite-distribution/wiki/Spring-Tool-Suite-3 "https://github.com/spring-attic/toolsuite-distribution/wiki/Spring-Tool-Suite-3"))로 이동
2. Latest STS3 Downloads > Spring Tool Suite 3.9.18 > full distribution on Eclipse 4.21
3. 첫 번째꺼 다운로드
4. Spring_Study > UNZIP
5. 경로 복사: "C:/Program Files/Eclipse Adoptium/jdk-11.0.21.9-hotspot/bin/"
6. 여기로 이동: "C:/My_Study/Spring_Study/sts-bundle/sts-3.9.18.RELEASE"
7. STS.ini 메모장으로 열기
8. 내용 추가
	```
	-vm
	C:\Program Files\Eclipse Adoptium\jdk-11.0.21.9-hotspot\bin\javaw.exe
	```
9. STS.exe 실행
10. Workspace: "C:\My_Study\Spring_Study\work"
11. Continue
### 2.2.1. STS - Spring Tool Suite
- Java 기반 웹 애플리케이션을 개발하기 위한 통합 개발 환경<font color="#7f7f7f">(IDE)</font>
- Eclipse 기반으로 만들어진 특별한 버전으로, <font color="#92cddc">스프링 프레임워크 및 관련 기술 스택을 지원</font>하는 데 중점을 둔 IDE이다.
## 2.3. 프로젝트 생성
1. Ctrl+N: Spring Legacy Project > Next
2. New Spring Legacy Project
	- Project name: 0104_MVC
	- Templates: Spring MVC Project
3. Project Settings - Spring MVC Project
	- input: com.ict.ex1
	- <font color="#fac08f">점(.) 두 개 넣어야 함.</font>
4. Finish
5. 확인하기: <font color="#7f7f7f">(프로젝트 선택하고)</font> Run on Server <font color="#7f7f7f">(Alt+Shift+X, R)</font>
6. "Hello world!"가 뜨면 성공
	- 한글을 지원하지 않는 JSP이다.
## 2.4. 구성 확인하기
project
- Spring Elements
- JAX-WS Web Services
- src/main/java
	- com.ict.ex1 - <font color="#d99694">Project Name</font>
		- HomeController.java
- src/main/resources
- src/test/java
- src/test/resources
- JRE System Library
- Maven Dependencies
- Deployment Descriptor: 0104_MVC
- Deployed Resources
- src
	- main
		- java
		- resources
		- webapp
			- resources
			- WEB-INF
				- classes
				- spring
					- appServlet
						- servlet-context.xml
					- root-context.xml
				- views
					- home.jsp
				- web.xml
- target
- pom.xml
### 2.4.1. src/main/java - 자바 관련
- 자바 소스 코드를 저장하는 폴더
- 주로 애플리케이션의 Java 소스 코드가 위치한다
- 패키지 명<font color="#7f7f7f">(예 : com.ict.ext)</font>이 나타난다.
- 패키지 안에 HomeController.java가 존재한다.
#### 2.4.1.2. Model
- ModelAndView는 Model을 구현한다?
- Model은 request에 저장되어 있어서 addAttribute 메서드 사용 가능!
- src/main/webapp/WEB-INF/spring/view/home.jsp 화깅 가능!
- src/main/sebapp/WEB-INF/spring/appServlet/servlet-context.xml
	- view resolver 존재 확인
### 2.4.2. src/main/resources - 자바 외
### 2.4.3. src/test/java, src/test/resources - 테스트용
### 2.4.4. src
- 프로젝트의 리소스 및 설정 파일을 저장하는 폴더
- 주로 프로젝트의 XML 설정 파일, property 파일, 이미지 등이 위치한다.
# 3. STS 둘러보기 - 기본

## 3.1. home.jsp
src > main > webapp > WEB-INF > views >

- 클라이언트에게 보여질 view를 정의하는 JSP 파일
- HomeController에서 반환된 뷰의 내용이 이 파일에 표현되어 클라이언트에게 제공된다.

```HTML
<context:component-scan base-package="com.ict.ex1"/>
```
- com.ict.ex1으로 들어오면 전부 Controller로 인정해준다?
- 무조건 어노테이션으로 기재 해라?

```HTML
<resources mapping="/resources/**" location="/resources/"/>
```
- 리소스 위치가 지정되어 있다.
## 3.2. HomeController.java
src/main/java > com.ict.ex1 >

- Spring MVC 아키텍처에서의 컨트롤러 역할을 하는 클래스
- 웹 요청에 대한 처리 로직이 정의되어 있다.
- Client의 요청을 받아 처리 후 적절한 뷰를 반환한다.
## 3.3. servlet-context.xml

- Spring MVC의 설정 파일 중 하나
- 서블릿과 관련된 설정을 담당
- Controller, View Resolver, Resource handler 등의 Bean을 설정한다.
- 웹 애플리케이션의 기본적인 구성을 정의한다.
## 3.4. root-context.xml

- Spring MVC의 설정 파일 중 하나
- 루트 애플리케이션 context와 관련된 설정을 담당
- 데이터베이스 연동, 서비스 계층 Bean 등을 설정한다.
## 3.5. web.xml

- Java 웹 애플리케잇연의 배치 및 구성 정보를 담고 있는 설정 파일
- 서블릿 매핑, 리스너 설정, 필터 설정 등
## 3.6. pom.xml

- Maven 프로젝트의 설정 파일
- 프로젝트의 의존성 및 빌드 설정이 정의
- 스프링 프레임워크 및 다른 라이브러리에 대한 의존성을 명시

# 4. STS 둘러보기 - 간단한 연습
## 4.1. TestController.java
src/main/java > com.ict.ex1 > Class

```java
@RequestMapping("/t1")
public ModelAndView test1() {
	ModelAndView mv = new ModelAndView();
```

뷰 페이지에서 표현할 자원을 mv에 저장한다.
```java
mv.addObject("str", "SPRING TEST");
mv.setViewName("test1");
```
- `mv.setViewName("test");`는 "WEB-INF/views/test1.jsp"를 의미한다.

### 코드 전문
```java
package com.ict.ex1;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class TestController {
	
	@RequestMapping("/t1")
	public ModelAndView test1() {
		ModelAndView mv = new ModelAndView();
		mv.addObject("str", "SPRING TEST");
		mv.setViewName("test1");
		
		return mv;
	}

}
```
## 4.2. test1.jsp
src/main/webapp/WEB-INF/views > JSP

```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>${str}</h1>
</body>
</html>
```
- `"http://localhost:8080/ex1/"` 뒤에 t1을 붙여주면 test1.jsp의 화면이 나온다.
## 4.3. home.jsp
src/main/webapp/WEB-INF/views >

test1.jsp를 참고하여 아래와 같이 바꾸자.
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<html>
<head>
	<meta charset="UTF-8">
	<title>Home</title>
</head>
<body>
<h1>
	Hello world!  
</h1>

<P>  The time on the server is ${serverTime}. </P>
</body>
</html>
```
- `<%@` 부분과 `<meta>`를 test1.jsp와 동일하게 바꿔주었다.
- 정상적으로 한글이 출력되는 것을 볼 수 있다.
# 5. DB 연결하기
## 5.1. MemVO.java
src/main/java > mybatis.vo > Class

- 멤버변수를 설정한다.
### 코드 전문
```java
package mybatis.vo;

public class MemVO {
	
	private String m_code, m_id, m_name;

	public String getM_code() {
		return m_code;
	}

	public void setM_code(String m_code) {
		this.m_code = m_code;
	}

	public String getM_id() {
		return m_id;
	}

	public void setM_id(String m_id) {
		this.m_id = m_id;
	}

	public String getM_name() {
		return m_name;
	}

	public void setM_name(String m_name) {
		this.m_name = m_name;
	}

}
```
## 5.2. mem.xml
src/main/<font color="#d99694">resources</font> > mybatis/mapper > XML
> mapper를 만든다.
> 이전까지 만들어 왔던 mapper와 똑같이 만든다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
 	 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  	"https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace = "mem">
	<select id="login" parameterType="Map" resultType="mybatis.vo.MemVO">
		SELECT * FROM member 
		WHERE m_id = #{m_id} AND m_pw = #{m_pw} 
	</select>
</mapper>
```

## 5.3. MemDAO.java
src/main/java > mybatis.dao > Class
> DAO를 만든다.

반환형이 MemVO인 login() 메서드를 생성하자.
```java
public MemVO login(String id, String pw) {
	Map<String, String> map = new HashMap<String, String>();
	map.put("m_id", id);
	map.put("m_pw", pw);
```
- HashMap에 id와 pw를 저장한다.

SqlSessionTemplate 멤버 변수를 설정하자.
```java
private SqlSessionTemplate ss;
```
- 아래 라이브러리를 모두 추가하여야 한다.

멤버 변수의 getter, setter가 없으므로, autowired하자.
```java
@Autowired
private SqlSessionTemplate ss;
```
- 이미 있어야 하는 SqlSessionTemplate가 autowired를 이용해 여기로 들어오게 된다.

Action에서도 사용하려면 Controller가 아닌 component로 등록이 되어 있어야 한다.
```java
@Component
public class MemDAO {
```
- Component 어노테이션을 추가한다.
- 이러면 Bean 객체가 된다.
### 코드 전문
```java
package mybatis.dao;

import java.util.HashMap;
import java.util.Map;

import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import mybatis.vo.MemVO;

@Component
public class MemDAO {
	
	@Autowired
	private SqlSessionTemplate ss;
	
	public MemVO login(String id, String pw) {
		Map<String, String> map = new HashMap<String, String>();
		map.put("m_id", id);
		map.put("m_pw", pw);
		
		return ss.selectOne("mem.login", map);
	}

}
```
## 5.4. pom.xml - 라이브러리 세팅
- [MVN Repository](https://mvnrepository.com/)에 접속
- search: spring-jdbc
	- Spring JDBC
	- version 6.1.2. <font color="#7f7f7f">(최신 버전)</font>
	- Maven 태그의 코드 복사
	- pom.xml > `<!-- Test -->` 아래의 `</dependencies>` 바로 위에 붙여 넣기
		- `<version>` 태그의 내용 `${org.springframework-version}`로 바꾸기
- search: spring-tx
	- Spring Transaction
	- version 6.1.2. <font color="#7f7f7f">(최신 버전)</font>
	- Maven 태그의 코드 복사
	- pom.xml로 이동하여 위와 동일 작업
- search: commons-logging
	- Apache Commons Logging
	- version 1.2
	- 이후 위와 동일 작업
- 아래와 같이 직접 작성해도 된다.
	```xml
	<dependency>
		<groupId>commons-dbcp</groupId>
		<artifactId>commons-dbcp</artifactId>
		<version>1.4</version>
	</dependency>
	
	<dependency>
		<groupId>commons-pool</groupId>
		<artifactId>commons-pool</artifactId>
		<version>1.6</version>
	</dependency>
	
	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis</artifactId>
		<version>3.5.14</version>
	</dependency>
	
	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis-spring</artifactId>
		<version>1.3.2</version>
	</dependency>
	```
	- commons-dbcp, commons-pool, mybatis, mybatis spring을 작성했다.
- 필요한 라이브러리와 버전을 알고 있다면 위와 같이 직접 작성하여 저장하면 된다.
## 5.5. root-context.xml
src > main > webapp > WEB-INF > spring >
> bean 객체들을 집어 넣자.

```xml
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
```
- [[2024-01-03 Spring Framework#1.5. config.xml|config.xml]]에서 만든 DataSource, Factory, SqlSession Bean을 등록한다.

```xml
<bean id="memDao" class="mybatis.dao.MemDAO">
	<property name="ss" ref="sqlSession"/>
</bean>
```
- DAO Bean을 등록한다.

### 5.5.1. DAO 파일을 자동 등록하고 싶다면
- 하단의 Namespaces에 가서 context를 선택한다.
- 아래와 같이 작성한다.

```xml
<context:component-scan base-package="mybatis.dao"/>
```
- "mybatis.dao" 패키지에 등록된 파일은 모두 DAO로 등록된다.
- 단, DAO로 등록할 파일은 `@Component` 어노테이션을 등록해야 한다.
	- MemDAO.java 파일에도 등록했다.
### 5.5.2. 동시에 사용할 수 있는 최대 커넥션 수 지정
DataSource bean 객체에 아래의 Property를 추가한다.
```xml
<property name="maxActive" value="20"/>
```
### 5.5.3. 커넥션 풀에 반납할 때 최대로 유지되는 커넥션 수 지정
DataSource bean 객체에 아래의 Property를 추가한다.
```xml
<property name="maxIdle" value="10"/>
```
### 5.5.4. 커넥션 풀에 반납할 때 최소로 유지되는 커넥션 수 지정
DataSource bean 객체에 아래의 Property를 추가한다.
```xml
<property name="minIdle" value="5"/>
```
### 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd">
	
	<!-- Root Context: defines shared resources visible to all other web components -->
	
	<bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
		<property name="url" value="jdbc:mysql://localhost:3306/test_db"/>
		<property name="username" value="test_admin"/>
		<property name="password" value="1111"/>
		<property name="maxActive" value="20"/>
		<property name="maxIdle" value="10"/>
		<property name="minIdle" value="5"/>
	</bean>
	<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="ds"/>
		<property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
	</bean>
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg ref="factory"/>
	</bean>
	<context:component-scan base-package="mybatis.dao"/>
		
</beans>
```
## 5.6. login.jsp
> src > main > webapp > WEB-INF > views >

### 5.6.1. 강사님이 주신 jsp 파일
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<link rel="stylesheet" href="resources/css/style.css"/>
</head>
<body>
	<div id="login_form">
		<form action="login" method="post">
			<table>
				<colgroup>
					<col width="50px"/>
					<col width="*"/>
				</colgroup>
				<tbody>
					<tr>
						<td><label for="s_id">ID:</label></td>
						<td>
							<input type="text" id="s_id" name="u_id"/>
						</td>
					</tr>
					<tr>
						<td><label for="s_pw">PW:</label></td>
						<td>
							<input type="password" id="s_pw" name="u_pw"/>
						</td>
					</tr>
					<tr>
						<td colspan="2">
							<p class="btn">
								<a href="javascript:exe()">LOGIN</a>
							</p>
						</td>
					</tr>
				</tbody>
			</table>
		</form>
	</div>
	
	
	<div id="login_success" class="box">
		<span id="name_area"></span>님 환영!
		<p>
			<img width="100%" src=""/>
		</p>
	</div>
	<div id="login_fail" class="box">
		아이디 또는 비밀번호가 틀립니다.
	</div>
	
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	
	<script>
		$(function(){
			//jQuery시작부분
			
			$("#login_success, #login_fail").css("display","none");
		});
		
		function exe(){
			//사용자가 입력한 값(id와 비밀번호)
			
			var id = $("#s_id").val();
			var pw = $("#s_pw").val();
			
			if(id.trim().length < 1){
				alert("아이디를 입력하세요");
				$("#s_id").focus();
				return;
			}
			if(pw.trim().length < 1){
				alert("비밀번호를 입력하세요");
				$("#s_pw").focus();
				return;
			}
			
			//var param = "u_id="+encodeURIComponent(id)+"&u_pw="+encodeURIComponent(pw);
			
			//비동기식 통신을 위한 내용
			$.ajax({
				url: "login.inc",
				type: "POST",
				data: {"u_id":id, "u_pw":pw},
				dataType: "json",
			}).done(function(data){
				// 사용자가 id와 비밀번호를 정확히 입력했다면
				// 서버쪽에서는 name을 보낸다.
				//alert(data.name);
				if(data.name != undefined){
					//로그인 성공
					$("#name_area").text(data.name);
					$("#login_success").css("display","block");
					$("#login_fail").css("display","none");
				}else{
					//로그인 실패
					$("#login_success").css("display","none");
					$("#login_fail").css("display","block");
				}
				
			}).fail(function(err){
				//서버쪽에서 오류가 발생시 수행함!
			});
		}
	</script>
	
	
</body>
</html>
```
## 5.7. LoginController.java
> src/main/java > com.ict.ex1 > Class

login.jsp로 넘어갈 수 있는 Controller를 만들자.
### 5.7.1. 내가 만든 LoginController
```java
package com.ict.ex1;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class LoginController {
	
	@RequestMapping("/login")
	public ModelAndView login() {
		ModelAndView mv = new ModelAndView();
		mv.setViewName("login");
		
		return mv;
	}

}
```
### 5.7.2. 센세가 만든 LoginController
```java
package com.ict.ex1;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class LoginController {
	
	@RequestMapping("/login")
	public String login() {
		
		return "login";
	}

}
```
- 전달할 데이터가 없으므로 반환형을 String으로 정하고, return을 "login"으로 하여도 된다.
### 5.7.3. 로그인 동작 추가
- request가 post 방식으로 "/login" 호출될 때 아이디가 "admin"이고, 비밀번호가 "1111"이면 해당 정보를 출력하는 기능을 만들자.

```java
@Autowired
private MemDAO m_dao;
```
- MemDAO 객체를 멤버 변수로 선언하고 값은 autowired로 연결하여 받는다.

```java
@RequestMapping(value="/login", method=RequestMethod.POST)
public String login(String u_id, String u_pw) {
	MemVO mvo = m_dao.login(u_id, u_pw);
	
	return null;
}
```
- 인자를 받을 때 login.jsp에서 넘어오는 name값<font color="#7f7f7f">(u_id, u_pw)</font>이 같아야 한다.
## 5.8. CSS 파일을 놓는 위치
> src > main > webapp > resources > css >

- jsp 파일의 `<link>` 태그를 보자.
	```jsp
	<link rel="stylesheet" href="resources/css/style.css"/>
	```
# 6. 연습문제 1
- "/memList"를 호출했을 때 회원들<font color="#7f7f7f">(member)</font> 목록이 추가되도록 하즈아.
## 6.1. MemDAO.java - 메서드 추가
```java
public MemVO[] list() {
	List<MemVO> list = ss.selectList("mem.list");
	MemVO[] ar = null;
	if(list != null && list.size() > 0) {
		ar = new MemVO[list.size()];
		list.toArray(ar);
	}
	
	return ar;
}
```
## 6.2. MemController.java - 컨트롤러 추가
```java
package com.ict.ex1;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.MemDAO;
import mybatis.vo.MemVO;

@Controller
public class MemController {
	
	@Autowired
	private MemDAO m_dao;
	
	@RequestMapping("/memList")
	public ModelAndView list() {
		ModelAndView mv = new ModelAndView();
		MemVO[] ar = m_dao.list();
		mv.addObject("ar", ar);
		mv.setViewName("memList");
		
		return mv;
	}

}
```
## 6.3. memList.jsp - 리스트 페이지 추가
```HTML
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
	<table>
		<thead>
			<tr>
				<th>회원번호</th>
				<th>ID</th>
				<th>이름</th>
			</tr>
		</thead>
		<tbody>
		<c:forEach var="vo" items="${ar}">
			<tr>
				<td>${vo.m_code}</td>
				<td>${vo.m_id}</td>
				<td>${vo.m_name}</td>
			</tr>
		</c:forEach>
		</tbody>
	</table>
</body>
</html>
```
# 7. 연습문제 2
- "/add"로 request하면 회원 추가 페이지<font color="#7f7f7f">(add.jsp)</font>로 넘어간다.
- ID, PW, Name을 입력하고, "저장" 버튼을 클릭 시 DB<font color="#7f7f7f">(member table)</font>에 저장된다.
- POST 방식으로 전달한다.
- res.jsp로 이동하여 "저장 완료" 또는 "저장 실패" 메시지가 출력되어야 한다.
	- cnt=1이면 "저장 완료", cnt!=1이면 "저장 실패"
## 7.1. mem.xml - SQL 문 추가
## 7.2. MemDAO.java - 메서드 추가
## 7.3. AddController.java - 컨트롤러 추가
## 7.4. add.jsp - 페이지 생성
## 7.5. web.xml - 한글처리 문장 추가
```xml
<filter>
	<filter-name>encodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-param>
		<param-name>encoding</param-name>
		<param-value>UTF-8</param-value>
	</init-param>
</filter>
<filter-mapping>
	<filter-name>encodingFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

