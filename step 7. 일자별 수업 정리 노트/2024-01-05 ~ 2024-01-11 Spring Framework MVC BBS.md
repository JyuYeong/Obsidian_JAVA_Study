eclipse Spring을 이용한 게시판 만들기를 수행했다.
기존의 JSP, Action, jQuery 등으로만 수행했던 게시판 리소스를 활용하여 다시 만들어본다.
# 1. 비동기식 통신
- Spring에서 비동기식 통신은 비동기 처리<font color="#7f7f7f">(Asynchronous Processing)</font> 또는 비동기 프로그래밍<font color="#7f7f7f">(Asynchronous Programming)</font>으로 불린다.
- 작업을 <font color="#95b3d7">동시에 처리</font>하고, 결과를 기다리지 않고,<font color="#95b3d7"> 계속 다른 작업을 수행</font>할 수 있는 <font color="#95b3d7">프로그래밍 방식</font>을 일컫는다.
- @Async annotation, DeferredResult, Callable, WebFlux framework 등의 메커니즘이 있다.
# 2. Spring MVC Project - Spring Legacy Project
- 각종 설정 파일의 기본 정보와 설정 방법을 알아보자.
## 2.1. pom.xml
### 2.1.1. pom.xml 기본 정보
- Maven 프로젝트에서 사용되는 Project Object Model 파일이다.
- <font color="#95b3d7">프로젝트의 구성 정보와 의존성 관리에 대한 설정</font>을 담고 있는 XML 파일이다.
- Maven 기반의 프로젝트에서 공통으로 사용되며, 프로젝트를 빌드하고 관리하기 위해 사용한다.
- 주로 다음과 같은 내용을 포함한다.
	- 프로젝트의 기본 정보<font color="#7f7f7f">(Project coordinates)</font>
		- 프로젝트의 group id, artifact id, version 등의 기본적인 정보 정의
			```xml
			<groupId>com.example</groupId>
			<artifactId>my-spring-mvc-project</artifactId>
			<version>1.0.0</version>
			```
	- 프로젝트의 의존성 관리<font color="#7f7f7f">(Dependencies)</font>
		- 프로젝트가 의존하는 라이브러리 및 프레임워크의 정보 정의
			```xml
			<dependencies>
			    <dependency>
			        <groupId>org.springframework</groupId>
			        <artifactId>spring-webmvc</artifactId>
			        <version>5.3.10.RELEASE</version>
			    </dependency>
			    <!-- 다른 의존성들... -->
			</dependencies>
			```
	- 프로젝트의 빌드 설정<font color="#7f7f7f">(Build configurations)</font>
		- Maven이 프로젝트를 빌드하는 데 필요한 설정
			```xml
			<build>
			    <plugins>
			        <!-- 빌드 플러그인 설정 -->
			    </plugins>
			</build>
			```
### 2.1.2. pom.xml 작업 내용
- 이번 프로젝트에 필요한 라이브러리와 버전을 설정하였다.
- ---
- [MVNRepository](https://mvnrepository.com/)에서 다음의 라이브러리를 검색하여 Maven Dependency 태그를 복사하자.
	- Spring JDBC : ${org.springframework-version}
	- Spring Transaction : ${org.springframework-version}
	- Commons DBCP : 1.4
	- Commons Logging : 1.2
	- Commons Pool : 1.6
	- MyBatis : 3.5.14
	- MyBatis Spring : 1.3.2
- [Apache](https://www.apache.org/)의 APACHE PROJECT LIST에 있는 Commons에서 다음의 라이브러리를 검색하여 Maven Dependency 태그를 복사하자.
	- Apache Commons FileUpload : 1.4
	- Apache Commons IO : 2.11.0
- ---
- 해당 라이브러리의 Maven Dependency 태그<font color="#7f7f7f">(groupId, artifactId, version)</font> 정보를 알고 있다면 직접 dependencies 태그 내에 입력하여도 된다.
### 2.1.3. 추가한 dependency
```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-jdbc</artifactId>
	<version>${org.springframework-version}</version>
</dependency>
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-tx</artifactId>
	<version>${org.springframework-version}</version>
</dependency>
<dependency>
	<groupId>commons-dbcp</groupId>
	<artifactId>commons-dbcp</artifactId>
	<version>1.4</version>
</dependency>
<dependency>
	<groupId>commons-logging</groupId>
	<artifactId>commons-logging</artifactId>
	<version>1.2</version>
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

<!-- Spring Environments when file upload -->
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.4</version>
</dependency>
<dependency>
	<groupId>commons-io</groupId>
	<artifactId>commons-io</artifactId>
	<version>2.11.0</version>
</dependency>
```
### 2.1.4. Jackson Library
```xml
<dependency>
	<groupId>org.codehaus.jackson</groupId>
	<artifactId>jackson-mapper-asl</artifactId>
	<version>1.9.13</version>
</dependency>
```
- Spring 3.x에 가능한 방법이다.
- Java 객체와 JSON 간의 변환을 담당하는 라이브러리이다.
- JSON 데이터의 직렬화 및 역직렬화를 지원한다.
- [[#JSON 데이터를 처리하기 위한 MessageConverter 설정]]을 할 때 같이 해야 한다.
#### Jackson Library for Spring 4.x
- [MVNRepository](https://mvnrepository.com/)에서 Jackson Databind, Jackson Core, Jackson Annotations를 받아 등록해야 한다.
- 이후는 [[#for Spring 4.x|servlet-context.xml에서 MessageConverter 설정]] 절차를 참고하자.

## 2.2. root-context.xml
### 2.2.1. root-context.xml 기본 정보
- 서비스 계층과 데이터 계층에 관련된 Bean들을 정의한다.
- Web Application의 전역적인 설정을 다루며, 데이터베이스 연결, 트랜젝션 관리, 서비스 빈, 데이터 엑세스 빈 등을 설정한다.
- applicationContext.xml 또는 root-servlet.xml과 같은 이름으로 사용될 수 있다.
- 구조와 주요 요소에 대한 설명은 다음과 같다.
	```xml
	<beans xmlns="http://www.springframework.org/schema/beans"
		   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		   xmlns:context="http://www.springframework.org/schema/context"
		   xsi:schemaLocation="http://www.springframework.org/schema/beans
							   http://www.springframework.org/schema/beans/spring-beans.xsd
							   http://www.springframework.org/schema/context
							   http://www.springframework.org/schema/context/spring-context.xsd">
	
		<!-- 데이터 소스 설정 -->
		<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
			<!-- DataSource 속성 설정 -->
		</bean>
	
		<!-- JPA 설정 (예: Hibernate) -->
		<bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
			<!-- EntityManagerFactory 속성 설정 -->
		</bean>
	
		<!-- 트랜잭션 관리자 설정 -->
		<bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
			<property name="entityManagerFactory" ref="entityManagerFactory" />
		</bean>
	
		<!-- 트랜잭션 어노테이션 활성화 -->
		<tx:annotation-driven transaction-manager="transactionManager" />
	
		<!-- 서비스 빈 등록 -->
		<context:component-scan base-package="com.example.service" />
	
		<!-- 데이터 액세스 빈 등록 -->
		<context:component-scan base-package="com.example.repository" />
	
		<!-- 기타 전역 설정 및 빈 등록 -->
	
	</beans>
	```
### 2.2.2. Apache DBCP Library가 제공하는 BasicDataSource 생성
```xml
<bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	<property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql://localhost:3306/test_db"/>
	<property name="username" value="test_admin"/>
	<property name="password" value="1111"/>
	<property name="maxActive" value="30"/>
	<property name="maxIdle" value="20"/>
	<property name="minIdle" value="10"/>
</bean>
```
- <font color="#95b3d7">maxActive</font> : 동시에 사용할 수 있는 <font color="#95b3d7">최대 커넥션</font> 수
- <font color="#95b3d7">maxIdle</font> : 커넥션 풀에 반납 시 <font color="#95b3d7">유지되는 최대 커넥션</font> 수
- <font color="#95b3d7">minIdle</font> : 커넥션 풀에 반납 시 <font color="#95b3d7">유지되는 최소 커넥션</font> 수
### 2.2.3. SqlSessionFactoryBean 생성
```xml
<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="ds"/>
	<property name="mapperLocations" value="classpath:mybatis/mapper/**/*.xml"/>
</bean>
```
- <font color="#95b3d7">dataSource</font> : <font color="#95b3d7">BasicDataSource를 참조</font>한다.
- <font color="#95b3d7">mapperLocations</font> : <font color="#95b3d7">mapper가 위치한 곳</font>을 지정한다.
	- "classpath:mybatis/mapper/" 하위에 있는 모든 xml 파일을 mapper로 인지한다.
### 2.2.4. SqlSessionTemplate 생성
```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg ref="factory"/>
</bean>
```
### 2.2.5. DAO 등록
```xml
<context:component-scan base-package="mybatis.dao"/>
```
- component-scan으로 mybatis.dao package에 있는 모든 dao들을 등록한다.
	- 경로는 원하는 대로 지정할 수 있다.
	- 여러 개의 태그를 생성할 수 있다.
- <font color="#d99694">Namespaces</font>에서 <font color="#d99694">context를 선택</font>해야 한다.
### 2.2.6. 민감한 정보를 숨기는 실무적인 방법
- 현재는 driverClassName, url, username, password같은 민감한 정보가 XML 파일에 그대로 노출되어 있다.
- XML 요소는 접근하기 편리하므로 XML 파일에 민감한 정보를 그대로 노출시키면 위험하다.
- ---
1. 아래 위치에 jdbc.properties 파일을 생성한다.
	- src/main/resources > mybatis/props > 
2. jdbc.properties 파일에 아래 내용을 입력한다.
	```
	jdbc.driver=com.mysql.cj.jdbc.Driver
	jdbc.url=jdbc:mysql://localhost:3306/test_db
	jdbc.user=test_admin
	jdbc.pwd=1111
	jdbc.maxActive=30
	jdbc.maxIdle=20
	jdbc.minIdle=10
	```
3. root-context.xml에서 다음을 추가한다.
	```xml
	<context:annotation-config/>
	<context:property-placeholder location="classpath:mybatis/props/jdbc.properties"/>
	```
	- 하는 김에 annotation도 같이 쓸 수 있도록 추가한다.
		- <font color="#c3d69b">context:annotation-config는 annotation의 자동완성</font> 및 다양한 Bean 설정 기능을 사용할 수 있도록 한다.
	- jdbc.properties 파일의 로드를 위해서는 context라는 namespace와 schema 객체가 필요하다. <font color="#7f7f7f">이미 beans 요소에 있다.</font>
	- <font color="#95b3d7">context:property-placeholder</font>로 jdbc.properties에 있는 내용을 읽어온다.
4. BasicDataSource를 아래와 같이 수정한다.
	```xml
	<bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}"/>
		<property name="url" value="${jdbc.url}"/>
		<property name="username" value="${jdbc.user}"/>
		<property name="password" value="${jdbc.pwd}"/>
		<property name="maxActive" value="${jdbc.maxActive}"/>
		<property name="maxIdle" value="${jdbc.maxIdle}"/>
		<property name="minIdle" value="${jdbc.minIdle}"/>
	</bean>
	```
### 최종 코드 구성
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd">
	
	<!-- Root Context: defines shared resources visible to all other web components -->
	<context:annotation-config/>
	
	<context:property-placeholder location="classpath:mybatis/props/jdbc.properties"/>
	
	<bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}"/>
		<property name="url" value="${jdbc.url}"/>
		<property name="username" value="${jdbc.user}"/>
		<property name="password" value="${jdbc.pwd}"/>
		<property name="maxActive" value="${jdbc.maxActive}"/>
		<property name="maxIdle" value="${jdbc.maxIdle}"/>
		<property name="minIdle" value="${jdbc.minIdle}"/>
	</bean>
	<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="ds"/>
		<property name="mapperLocations" value="classpath:mybatis/mapper/**/*.xml"/>
	</bean>
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg ref="factory"/>
	</bean>
	<context:component-scan base-package="mybatis.dao"/>
</beans>
```
## 2.3. servlet-context.xml
### 2.3.1. servlet-context.xml 기본 정보
- Java Web Application에서 <font color="#c3d69b">Servlet 기반의 Web Application Context 설정을 정의</font>하는 XML 파일이다.
- Spring Framework에서 사용되며, 주로 <font color="#c3d69b">Spring Web MVC Project에서 DispatcherServlet의 설정을 담당</font>한다.
	- <font color="#95b3d7">DispatcherServlet</font>은 Client의 요청을 받아 해당 <font color="#95b3d7">요청을 처리할 컨트롤러에 전달</font>하는 역할을 한다.
- 주로 다음과 같은 역할을 한다.
	- Namespace 및 Schema 정의
	- Context 설정
		- DispatcherServlet에 의해 처리되는 Web Application의 구성을 정의
	- View Resolver 및 View 설정
	- 정적 자원(이미지, CSS, JavaScript, ...) 핸들링
		- 핸들러 매핑 및 리소스 위치 설정
	- 메시지 리소스 및 국제화 설정
### 2.3.2. Spring MVC와 DispathcerServlet이 사용할 Bean 설정
```xml
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
```
- servlet-context.xml에 이미 정의되어 있다. xsi:schemaLocation 링크가 일부 다를 수 있다.
### 2.3.3. JSON 데이터를 처리하기 위한 MessageConverter 설정
```xml
<beans:bean id="jsonHttpMessageConverter" class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"/>
<beans:bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
	<beans:property name="messageConverters">
		<beans:list>
			<beans:ref bean="jsonHttpMessageConverter"/>
		</beans:list>
	</beans:property>
</beans:bean>
```
- MappingJacksonHttpMessageConverter를 이용하여 JSON 데이터를 HTTP 메시지로 변환한다.
- 추가적으로 [[#2.1.4. Jackson Library|pom.xml에 Jackson Library를 추가]]해야 한다.
#### for Spring 4.x
```xml
<bean id="jsonHttpMessageConverter" class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter"/>
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
	<property name="messageConverters">
		<list>
			<ref bean="jsonHttpMessageConverter" />
		</list>
	</property>
</bean>
```
### 2.3.4. Spring 환경에서 파일 첨부를 위한 Resolver 설정
```xml
<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<beans:property name="defaultEncoding" value="utf-8"/>
	<beans:property name="maxUploadSize" value="-1"/>
</beans:bean>
```
# 3. DB부터 JSP까지
- [[2024-01-10 Spring Framework + 비동기 + 회원목록]] 수업 내용을 기반으로 한다.
## 3.1. DAO
- src/main/java > mybatis.dao > Java Class : MemDAO

```java
@Component
public class MemDAO {
	
	@Autowired
	private SqlSessionTemplate ss;
```
- MemDAO 클래스에 <font color="#95b3d7">Component annotation을 적용</font>한다.
	- root-context.xml에서 component-scan으로 MemDAO가 있는 폴더에 있는 모든 DAO를 등록하는데, MemDAO를 <font color="#95b3d7">DAO로 인지하기 위하여</font> Component annotation을 적용해야 한다.
	- DAO로 등록된 파일은 <font color="#95b3d7">서버 구동시 생성</font>된다.
- 멤버 변수 SqlSessionTemplate ss에 <font color="#c3d69b">Autowired annotation을 적용</font>한다.
	- <font color="#c3d69b">root-context.xml에 SqlSessionTemplate Bean이 등록</font>되어 있고, DAO보다 먼저 생성되므로 Autowired annotation을 적용하여 연결할 수 있다.

```java
public MemVO[] getList() {
	MemVO[] ar = null;
	List<MemVO> list = ss.selectList("mem.list");
	
	if(list != null && list.size() > 0) {
		ar = new MemVO[list.size()];
		list.toArray(ar);
	}
	return ar;
}
```
- MySQL에서 test_db.member 테이블의 레코드를 가져올 getList() 함수를 만든다.
- 이를 위하여 MemVO와 mapper를 만들자.
### 최종 코드 구성
```java
@Component
public class MemDAO {
	
	public MemDAO() {System.out.println("MemDAO 생성!");}
	
	@Autowired
	private SqlSessionTemplate ss;
	
	public MemVO[] getList() {
		MemVO[] ar = null;
		
		List<MemVO> list = ss.selectList("mem.list");
		if(list != null && list.size() > 0) {
			ar = new MemVO[list.size()];
			list.toArray(ar);
		}
		
		return ar;
	}

}
```
## 3.2. MemVO
- src/main/java > mybatis.vo > Java Class : MemVO
### 최종 코드 구성
```java
package mybatis.vo;

public class MemVO {
	
	private String m_code, m_id, m_pw, m_name;

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

	public String getM_pw() {
		return m_pw;
	}

	public void setM_pw(String m_pw) {
		this.m_pw = m_pw;
	}

	public String getM_name() {
		return m_name;
	}

	public void setM_name(String m_name) {
		this.m_name = m_name;
	}

}
```
## 3.3. Mapper
- src/main/resources > mybatis.mapper > XML File : mem.xml
- Mapper 양식은 [MyBatis Docs](https://mybatis.org/mybatis-3/ko/getting-started.html)에서 <font color="#fac08f">매핑된 SQL 구문 살펴보기</font> 코드를 복사하자.
### 3.3.1. DTD
```dtd
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
```
### 3.3.2. 멤버 리스트를 가져오는 SELECT
```xml
<mapper namespace="mem">
	<select id="list" resultType="mybatis.vo.MemVO">
		SELECT * FROM member
	</select>
</mapper>
```
### 최종 코드 구성
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="mem">
	<select id="list" resultType="mybatis.vo.MemVO">
		SELECT * FROM member
	</select>
</mapper>
```
## 3.4. JSP
- src > main > webapp > WEB-INF > views > member > JSP File : memList.jsp
### 최종 코드 구성
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
	<h1>회원 목록</h1>
	<hr/>
	<c:forEach items="${mm}" var="mvo" varStatus="vs">
		<h2>${vs.index}, ${mvo.m_id}, ${mvo.m_name}</h2>
	</c:forEach>
</body>
</html>
```
- tag library로 JSTL을 추가하였다.
- Controller에서 member의 레코드를 MemVO 배열에 담은 mm을 넘겨주어야 한다.
## 3.5. Controller
- src/main/java > com.lgt.alf > Java Class : MemberController.java
### 최종 코드 구성
```java
package com.lgt.alf;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.MemDAO;
import mybatis.vo.MemVO;

@Controller
public class MemberController {
	
	@Autowired
	private MemDAO m_dao;
	
	@RequestMapping("memList")
	public ModelAndView memList() {
		ModelAndView mv = new ModelAndView();
		MemVO[] ar = m_dao.getList();
		
		mv.addObject("mm", ar);
		mv.setViewName("member/memList");
		
		return mv;
	}

}
```
- MemberController 클래스가 <font color="#95b3d7">Controller임을 명시하기 위해 Controller annotation</font>을 적용했다.
- MemDAO는 Tomcat이 구동될 때 같이 생성되므로 Autowired annotation을 적용해 멤버 변수로 선언한다.
- <font color="#c3d69b">RequestMapping</font> annotation을 적용하여 memList() <font color="#c3d69b">메서드를 "/memList" 경로로 들어오는 HTTP 요청을 처리하는 메서드로 지정</font>한다.
	- 경로 앞에 "/"가 있으면 절대 경로, 없으면 상대 경로가 된다.
# 4. 비동기 처리를 이용한 검색 결과 가져오기
- DB로부터 전체 리스트를 가져와 화면에 모두 뿌려주는 것과 달리 검색은 Client가 입력한 값을 DB까지 가져가고, 그 결과를 JSP까지 가져와야 한다. 또한 페이지 이동 없이 비동기식으로 페이지의 내용을 갱신해야 한다.
- DAO, VO, Mapper는 생략하고, JSP는 script 부분만 확인하자.
## 4.1. DeptController.java
```java
@RequestMapping(value="dept/search", method=RequestMethod.POST)
@ResponseBody
public Map<String, Object> searchList(String searchType, String searchValue) {
	Map<String, Object> map = new HashMap<String, Object>();
	DeptVO[] ar = d_dao.searchList(searchType, searchValue);
	
	map.put("ar", ar);
	
	return map;
}
```
- RequestMapping annotation을 적용하여 해당 메서드가 HTTP 요청을 처리하도록 하였다.
- <font color="#95b3d7">ResponseBody</font> annotation을 적용하여 해당 메서드가 <font color="#95b3d7">HTTP 응답의 본문을 직접 생성</font>하여 반환하도록 하였다.
- servlet-context.xml과 pom.xml에서 JSON 데이터 처리를 위한 Converter를 등록하였으므로 이 메서드의 반환형을 `Map<String, Object>`로 하였다. 그러면 JSON 데이터로 변형되어 전송된다.
	- Map의 value 자료형을 Object로 한 것은 배열을 받기 위함이다.
	- MappingJacksonHttpMessageConverter를 통해 형 변환을 생략할 수 있다.
	- deptList.jsp의 AJAX에서 data가 JSON 데이터를 받게 된다.
# 기타
## 1. summernote
- WYSIWYG<font color="#7f7f7f">(What You See Is What You Get)</font> Editor
- <font color="#d99694">jQuery 선행 로딩 필요</font>
### 1.1. summernote 세팅
1. [summernote](https://summernote.org/) > Getting started > Download complied
2. UNZIP: `C:\My_Study\Spring_Study\util\summernote-0.8.18-dist`
3. <font color="#92cddc">폴더</font> 또는 <font color="#c3d69b">파일</font>을 다음 위치에 배치한다.
	- src
		- main
			- webapp
				- resources
					- css
						- <font color="#c3d69b">summernote-lite.css</font>
						- <font color="#92cddc">font</font>
					- js
						- <font color="#c3d69b">summernote-lite.js</font>
						- lang
							- <font color="#c3d69b">summernote-ko-KR.js</font>
4. editor가 필요한 JSP에 CSS 링크 연결과 jQuery와 summernote 라이브러리 로딩을 한다.
	- CSS 링크 연결
		```HTML
		<link rel="stylesheet" href="resources/css/style.css"/>
		<link rel="stylesheet" href="resources/css/summernote-lite.css"/>
		```
	- jQuery 라이브러리 로딩
		```HTML
		<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
		```
	- summernote 라이브러리 로딩
		```HTML
		<script src="resources/js/summernote-lite.js"></script>
		```
	- <font color="#d99694">jQuery가 summernote보다 먼저 로딩되어야 한다.</font>
5. editor를 삽입할 textarea의 id를 이용하여 script를 작성한다.
	```javascript
	$(function(){
		$("#content").summernote();
	});
	```
### 1.2. summernote 옵션 추가 방법
- 옵션을 추가하는 두 가지 방법이 있다.

```javascript
$("#content").summernote({
	height: 200,
	focus: true
});
```
- 첫 번째 방법

```javascript
$("#content").summernote("lineHeight", .7);
```
- 두 번째 방법
- 이 방법으로만 적용할 수 있는 옵션들이 있다.
### 1.3. summernote 옵션
- height: editor의 높이를 부여한다. (예: `height: 200`)
- focus: true일 경우 커서를 미리 가져다 놓는다. (예: `focus: true`)
- lang: 언어를 선택한다. (예: `lang: "ko-KR"`)
	- 해당 summernote 언어 라이브러리 로딩을 해야 한다.
		```HTML
		<script src="resources/js/lang/summernote-ko-KR.js"></script>
		```
- <font color="#fac08f">callbacks</font>: 다양한 이벤트에 대한 콜백 함수들을 정의한다.
	- onChange: 편집기의 내용이 변경될 때
		```javascript
		callbacks: {
			onChange: function(contents, $editable){
				console.log("내용이 변경되었습니다.");
			}
		}
		```
	- <font color="#fac08f">onImageUpload</font>: 이미지가 업로드 될 때
		```javascript
		callbacks: {
			onImageUpload: function(files, editor){
				for(let i = 0 ; i < files.length ; i++)
					sendImage(files[i], editor);
			}
		}
		```
- ---
- lineHeight: 줄간격을 정의한다. (예: `("lineHeight", .7)`)
## 3. FormData
- JavaScript에서 파일을 보낼 때는 반드시 FormData 객체로 보내야 한다.

```javascript
function sendImage(file, editor){
	let frm = new FormData();
	frm.append("s_file", file);
}
```
- 보내고자 하는 파일을 FormData 객체에 key-value 형태로 저장한다.
## 4. 페이징 처리