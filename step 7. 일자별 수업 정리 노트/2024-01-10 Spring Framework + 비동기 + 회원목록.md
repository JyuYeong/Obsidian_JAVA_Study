# 1. Spring STS 환경 개념 정리
![[2024-01-10_01 Spring STS Logic|800]]
## 1.1. 목적별 필요 라이브러리
|    Spring Core    | Spring DB와 MyBatis 활용 | Spring MVC 활용 | Spring JSTL |    file upload     |
|:-----------------:|:------------------------:|:---------------:|:-----------:|:------------------:|
|   spring-beans    |       commons-dbcp       |   spring-web    |    jstl     | commons-fileupload |
|  spring-context   |       commons-pool       |  spring-webmvc  |  standard   |     commons-io     |
|    spring-core    |         mybatis          |                 |             |                    |
| spring-expression |      mybatis-spring      |                 |             |                    |
|  commons-logging  |        spring-aop        |                 |             |                    |
|                   |       spring-jdbc        |                 |             |                    |
|                   |        spring-tx         |                 |             |                    |

# 2. 회원 목록 리스트 만들기

^17b726

> Spring Legacy Project(Spring MVC Project) > 0110_STS > com.lgt.alf
## 2.1. 라이브러리 및 Bean 세팅하기
### 2.1.1. pom.xml에서 라이브러리 세팅을 하자.
- [[2024-01-05 ~ 2024-01-11 Spring Framework MVC BBS#2.1.2. pom.xml 작업 내용|지난 시간]]에 설치한 라이브러리 참고
#### 설치 라이브러리
|     Library     |       groupId       |   artifactId    |            version             |
|:---------------:|:-------------------:|:---------------:|:------------------------------:|
|  commcons-dbcp  |    commons-dbcp     |  commons-dbcp   |              1.4               |
| commons-logging |   commons-logging   | commons-logging |              1.2               |
|  commons-pool   |    commons-pool     |   commons-pol   |              1.6               |
|     mybatis     |     org.mybatis     |     mybatis     |             3.5.6              |
| mybatis-spring  |     org.mybatis     | mybatis-spring  |             1.3.2              |
|   spring-jdbc   | org.springframework |   spring-jdbc   | ${org.springframework-version} |
|    spring-tx    | org.springframework |    spring-tx    | ${org.springframework-version} |
#### 정리 : 설치 코드
```xml
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
	<version>3.5.6</version>
</dependency>
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis-spring</artifactId>
	<version>1.3.2</version>
</dependency>
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
```
#### 종합 : pom.xml 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.lgt</groupId>
	<artifactId>alf</artifactId>
	<name>0110_STS</name>
	<packaging>war</packaging>
	<version>1.0.0-BUILD-SNAPSHOT</version>
	<properties>
		<java-version>1.6</java-version>
		<org.springframework-version>3.1.1.RELEASE</org.springframework-version>
		<org.aspectj-version>1.6.10</org.aspectj-version>
		<org.slf4j-version>1.6.6</org.slf4j-version>
	</properties>
	<dependencies>
		<!-- Spring -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${org.springframework-version}</version>
			<exclusions>
				<!-- Exclude Commons Logging in favor of SLF4j -->
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				 </exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
				
		<!-- AspectJ -->
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>${org.aspectj-version}</version>
		</dependency>	
		
		<!-- Logging -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${org.slf4j-version}</version>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>jcl-over-slf4j</artifactId>
			<version>${org.slf4j-version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>${org.slf4j-version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.15</version>
			<exclusions>
				<exclusion>
					<groupId>javax.mail</groupId>
					<artifactId>mail</artifactId>
				</exclusion>
				<exclusion>
					<groupId>javax.jms</groupId>
					<artifactId>jms</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.jdmk</groupId>
					<artifactId>jmxtools</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.jmx</groupId>
					<artifactId>jmxri</artifactId>
				</exclusion>
			</exclusions>
			<scope>runtime</scope>
		</dependency>

		<!-- @Inject -->
		<dependency>
			<groupId>javax.inject</groupId>
			<artifactId>javax.inject</artifactId>
			<version>1</version>
		</dependency>
				
		<!-- Servlet -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.5</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>2.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
			<version>1.2</version>
		</dependency>
	
		<!-- Test -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.7</version>
			<scope>test</scope>
		</dependency>       
		
		<!-- Spring and MyBatis Library -->
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
		    <version>3.5.6</version>
		</dependency>
		<dependency>
		    <groupId>org.mybatis</groupId>
		    <artifactId>mybatis-spring</artifactId>
		    <version>1.3.2</version>
		</dependency>
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
	</dependencies>
    <build>
        <plugins>
            <plugin>
                <artifactId>maven-eclipse-plugin</artifactId>
                <version>2.9</version>
                <configuration>
                    <additionalProjectnatures>
                        <projectnature>org.springframework.ide.eclipse.core.springnature</projectnature>
                    </additionalProjectnatures>
                    <additionalBuildcommands>
                        <buildcommand>org.springframework.ide.eclipse.core.springbuilder</buildcommand>
                    </additionalBuildcommands>
                    <downloadSources>true</downloadSources>
                    <downloadJavadocs>true</downloadJavadocs>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                    <compilerArgument>-Xlint:all</compilerArgument>
                    <showWarnings>true</showWarnings>
                    <showDeprecation>true</showDeprecation>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.2.1</version>
                <configuration>
                    <mainClass>org.test.int1.Main</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```
### 2.1.2. root-context.xml에서 datasource, factory, sqlsession, dao 생성하자.
#### Apache DBCP Library가 제공하는 BasicDataSource 생성
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
#### SqlSessionFactoryBean 생성
```xml
<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="ds"/>
	<property name="mapperLocations" value="classpath:mybatis/mapper/**/*.xml"/>
</bean>
```
- <font color="#95b3d7">dataSource</font> : <font color="#95b3d7">BasicDataSource를 참조</font>한다.
- <font color="#95b3d7">mapperLocations</font> : <font color="#95b3d7">mapper가 위치한 곳</font>을 지정한다.
	- "classpath:mybatis/mapper/" 하위에 있는 모든 xml 파일을 mapper로 인지한다.
#### SqlSessionTemplate 생성
```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg ref="factory"/>
</bean>
```
#### DAO 등록
```xml
<context:component-scan base-package="mybatis.dao"/>
```
- component-scan으로 mybatis.dao package에 있는 모든 dao들을 등록한다.
	- 경로는 원하는 대로 지정할 수 있다.
	- 여러 개의 태그를 생성할 수 있다.
- <font color="#d99694">Namespaces</font>에서 <font color="#d99694">context를 선택</font>해야 한다.
#### 정리 : 현재까지 코드
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
<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="ds"/>
	<property name="mapperLocations" value="classpath:mybatis/mapper/**/*.xml"/>
</bean>
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg ref="factory"/>
</bean>
<context:component-scan base-package="mybatis.dao"/>
```

#### 실무적인 방향으로 코드 수정을 해 보자.
- url, username, password와 같은 민감 정보는 숨겨야 한다.
1. 중요한 정보는 다른 파일에 보관해야 한다.
	- <font color="#92cddc">src/main/resources > /mybatis/props > File > File name : jdbc.properties</font>
2. 다음과 같이 입력한다.
	```
	jdbc.driver=com.mysql.cj.jdbc.Driver
	jdbc.url=jdbc:mysql://localhost:3306/test_db
	jdbc.user=test_admin
	jdbc.pwd=1111
	jdbc.maxActive=30
	jdbc.maxIdle=20
	jdbc.minIdle=10
	```
3. root-context.xml로 돌아와 다음을 입력한다.
	```xml
	<context:annotation-config/>
	<context:property-placeholder location="classpath:mybatis/props/jdbc.properties"/>
	```
	- 하는 김에 annotation도 같이 쓸 수 있도록 추가한다.
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
- XML 요소는 접근하기 편리하므로 위와 같이 하면 접근을 막을 수 있다.
#### 종합 : root-context.xml 코드 전문
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
## 2.2. DAO를 생성하자.
### 2.2.1. MemDAO
> src/main/java > mybatis.dao > Java Class
#### Component, Autowired 부여
```java
@Component
public class MemDAO {
	
	@Autowired
	private SqlSessionTemplate ss;
```
- Autowired는 변수명보다 자료형이 맞으면 된다.

#### getList 메서드 생성
```java
public MemVO[] getList() {
	MemVO[] ar = null;
	
	// HERE!
	
	return ar;
}
```
- [[#2.2.2. MemVO|MemVO]] 객체를 만들어 오자.
- ar에 null을 주는 이유는
	1. MySQL의 test_db.membet Table에서 MemVO 객체로 만든 후
	2. 하나의 List 구조로 반환되는 것을
	3. 배열로 반환하기 위함이다.

HERE
```java
List<MemVO> list = ss.selectList("mem.list");
if(list != null && list.size() > 0) {
	ar = new MemVO[list.size()];
	list.toArray(ar);
}
```
- "mem.list"는 [[#2.2.3. mem.xml|mapper에 등록]]하자.
- list가 null이 아니고, 그 길이가 0보다 크다면
	1. 배열 ar의 길이를 list의 길이와 같도록 설정한다.
	2. list에 담긴 요소를 배열 ar에 담는다.
#### 테스트
```java
public MemDAO() {System.out.println("MemDAO 생성!");}
```
- 위와 같이 생성자를 만든 후 Run on Server를 해보자.
![[Pasted image 20240110115220.png]]
- 서버가 구동되는 중간에 메시지가 출력되는 것을 알 수 있다.
- 즉 root-context.xml에 있는 내용은 서버가 구동될 때 생성됨을 알 수 있다.
#### 정리 : 현재까지 코드
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
### 2.2.2. MemVO
> src/main/java > mybatis.vo > Java Class

- MySQL test_db.member 테이블의 m_code, m_id, m_pw, m_name을 등록하자.
#### 종합 : MemVO.java 코드 전문
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
### 2.2.3. mem.xml
> src/main/resources > mybatis.mapper > XML File
#### mapper 생성
- 필요한 SQL 문장을 담을 XML 파일을 생성한다.
- [MyBatis Docs](https://mybatis.org/mybatis-3/ko/getting-started.html)에서 <font color="#fac08f">매핑된 SQL 구문 살펴보기</font> 코드를 복사하자.
#### config 생성
- mapper와 데이터베이스를 연결할 XML 파일을 생성한다.
- [MyBatis Docs](https://mybatis.org/mybatis-3/ko/getting-started.html)에서 <font color="#fac08f">XML에서 SqlSessionFactory 빌드하기</font> 코드를 복사하자.
#### 정리 : 현재까지 코드
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
## 2.3. 회원 목록을 화면에 구현하자.
### 2.3.1-1. memList.jsp
> src > main > webapp > WEB-INF > views > member > JSP File

- 회원 목록을 출력할 화면을 만들자.
	- 브라우저에서 "/memList"라고 호출하면 보여질 페이지

```HTML
<body>
	<h1>회원 목록</h1>
	<hr/>
	
</body>
```

[[#^f166c2|이어서 보기]]
### 2.3.2-1. MemberController.java
> src/main/java > com.lgt.alf > Java Class

```java
@Controller
public class MemberController {
	
	@Autowired
	private MemDAO m_dao;

}
```
- <font color="#fac08f">Controller는 DAO보다 늦게 생성되므로 Autowired로 DAO를 받을 수 있음을 기억하자.</font>
	- DAO는 root-context.xml에 의해 서버가 구동될 때 생성되기 때문이다.

```java
@RequestMapping("memList")
public ModelAndView memList() {
	ModelAndView mv = new ModelAndView();
	
	// HERE!
	
	return mv;
}
```
- memList로 request가 올 때 수행할 함수를 지정한다.
	- 이때 <font color="#95b3d7">"/"를 붙이면 절대 위치</font>를 의미한다. 안 붙이면 상대 위치이다.
- <font color="#c3d69b">ModelAndView는 JSP 지정 및 다른 데이터를 포함하는 것도 가능</font>하므로 이 객체를 반환형으로 사용하는 것이 편리하다.

HERE
```java
mv.setViewName("member/memList");
```
- 가고자 하는 View의 경로를 ModelAndView에 지정하여야 한다.
	- 지정하지 않고 URL에 memList를 추가하면 <font color="#c3d69b">"/WEB-INF/views/memList.jsp"</font>를 찾는다.
	- 즉 views 폴더 안에 모두 있다면 지정하지 않아도 된다. <font color="#7f7f7f">더 깊숙히 있다면 지정해야 한다는 뜻이다.</font>

[[#^10a37f|이어서 보기]]
### 실행 화면 : URL 연결
![[Pasted image 20240110125717.png]]
### 2.3.2-2. MemberController.java

^10a37f

- 회원 목록을 DB로부터 받아온다.

HERE
```java
MemVO[] ar = m_dao.getList();
mv.addObject("mm", ar);
```
- m_dao.getList()에서 받은 배열을 ModalAndView에 mm이라는 이름으로 저장한다.
#### 종합 : MemberController.java 코드 전문
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
### 2.3.1-2. memList.jsp

^f166c2

```HTML
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
```
- 스크립트릿보단 <font color="#95b3d7">JSTL 사용을 권장</font>하므로 taglib을 등록하자.

```HTML
<c:forEach items="${mm}" var="mvo" varStatus="vs">
	<h2>${vs.index}, ${mvo.m_id}, ${mvo.m_name}</h2>
</c:forEach>
```
- 반복문을 이용해 idx, m_id, m_name을 출력한다.
#### 종합 : memList.jsp 코드 전문
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
### 실행 화면 : 결과
![[Pasted image 20240110131528.png|300]]
# 3. 직원 목록 화면 만들기
- [[#^17b726|회원 목록 리스트 만들기]]에 이어서 진행한다.
## 3.1. EmpVO.java
> src/main/java > mybatis.vo > Java Class
- MySQL의 test_db.emp Table의 컬럼들을 멤버 변수로 지정한다.
### 종합 : EmpVO.java 코드 전문
```java
package mybatis.vo;

public class EmpVO {
	
	private String empno, ename, job, hiredate, deptno;

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

	public String getJob() {
		return job;
	}

	public void setJob(String job) {
		this.job = job;
	}

	public String getHiredate() {
		return hiredate;
	}

	public void setHiredate(String hiredate) {
		this.hiredate = hiredate;
	}

	public String getDeptno() {
		return deptno;
	}

	public void setDeptno(String deptno) {
		this.deptno = deptno;
	}

}
```
## 3.2. emp.xml
> src/main/resources > mybatis.mapper > XML File
### 3.2.1. emp.list SELECT 문
```xml
<select id="list" resultType="mybatis.vo.EmpVO">
	SELECT * FROM emp
</select>
```
### 종합 : emp.xml 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="emp">
	<select id="list" resultType="mybatis.vo.EmpVO">
		SELECT * FROM emp
	</select>
</mapper>
```
## 3.3. EmpDAO.java
> src/main/java > mybatis.dao > Java Class
### 3.3.1. SqlSessionTemplate와 getList() 생성
```java
@Autowired
private SqlSessionTemplate ss;
```
- SqlSessionTemplate 멤버 변수 생성 및 Autowired 연결

```java
public EmpVO[] getList() {
	EmpVO[] ar = null;
	List<EmpVO> list = ss.selectList("emp.list");
	
	if(list != null && list.size() > 0) {
		ar = new EmpVO[list.size()];
		list.toArray(ar);
	}
	
	return ar;
}
```
- getList() 함수 생성
### 정리 : 현재까지 코드
```java
package mybatis.dao;

import java.util.List;

import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import mybatis.vo.EmpVO;

@Component
public class EmpDAO {
	
	@Autowired
	private SqlSessionTemplate ss;
	
	public EmpVO[] getList() {
		EmpVO[] ar = null;
		List<EmpVO> list = ss.selectList("emp.list");
		
		if(list != null && list.size() > 0) {
			ar = new EmpVO[list.size()];
			list.toArray(ar);
		}
		
		return ar;
	}

}
```
## 3.4. EmpController.java
> src/main/java > com.lgt.alf > Java Class
### 3.4.1. Controller와 Autowired 어노테이션 부여
```java
@Controller
public class EmpController {
	
	@Autowired
	private EmpDAO e_dao;

}
```
### 3.4.2. empList()
```java
@RequestMapping("empList")
public ModelAndView empList() {
	ModelAndView mv = new ModelAndView();
	EmpVO[] ar = e_dao.getList();
	
	mv.addObject("ee", ar);
	mv.setViewName("emp/empList");
	
	return mv;
}
```
### 정리 : 현재까지 코드
```java
@Controller
public class EmpController {
	
	@Autowired
	private EmpDAO e_dao;
	
	@RequestMapping("empList")
	public ModelAndView empList() {
		ModelAndView mv = new ModelAndView();
		EmpVO[] ar = e_dao.getList();
		
		mv.addObject("ee", ar);
		mv.setViewName("emp/empList");
		
		return mv;
	}

}
```
### 종합 : EmpController.java 코드 전문
```java
package com.lgt.alf;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.EmpDAO;
import mybatis.vo.EmpVO;

@Controller
public class EmpController {
	
	@Autowired
	private EmpDAO e_dao;
	
	@RequestMapping("empList")
	public ModelAndView empList() {
		ModelAndView mv = new ModelAndView();
		EmpVO[] ar = e_dao.getList();
		
		mv.addObject("ee", ar);
		mv.setViewName("emp/empList");
		
		return mv;
	}

}
```
## 3.5-1. empList.jsp
> src > main > webapp > WEB-INF > views > emp > JSP File
### 3.5.1. 테스트용 화면
```HTML
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>사원 목록</h1>
	<hr/>
</body>
</html>
```

[[#^d1755c|이어서 보기]]
## 실행 화면
![[Pasted image 20240110145822.png|300]]
- URL(`http://localhost:8080/alf/empList`) 입력 시 위 화면이 나와야 한다.
## 3.5-2. empList.jsp

^d1755c` 

### 3.5-2.1. 사원 목록 출력
```HTML
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
```
- JSTL을 사용하기 위해 taglib을 등록한다.

```HTML
<h1>사원 목록</h1>
<hr/>
<c:forEach items="${ee}" var="vo">
	<h2>${vo.empno} / ${vo.ename} / ${vo.job} / ${vo.hiredate} / ${vo.deptno}</h2>
</c:forEach>
```
- body 태그를 위와 같이 작성한다.
- ee는 [[#3.4.2. empList()|EmpController의 empList()에서 ModelAndView에 담은 배열]] 이름이다.
### 종합 : empList.jsp 코드 전문
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
	<h1>사원 목록</h1>
	<hr/>
	<c:forEach items="${ee}" var="vo">
		<h2>${vo.empno} / ${vo.ename} / ${vo.job} / ${vo.hiredate} / ${vo.deptno}</h2>
	</c:forEach>
</body>
</html>
```
# 4. 부서 목록 및 검색 화면 만들기
## 4.1. DeptVO.java
> src/main/java > mybatis.vo > Java Class
### 종합 : DeptVO.java 코드 전문
```java
package mybatis.dao;

public class DeptVO {
	
	private String deptno, dname, loccode;

	public String getDeptno() {
		return deptno;
	}

	public void setDeptno(String deptno) {
		this.deptno = deptno;
	}

	public String getDname() {
		return dname;
	}

	public void setDname(String dname) {
		this.dname = dname;
	}

	public String getLoccode() {
		return loccode;
	}

	public void setLoccode(String loccode) {
		this.loccode = loccode;
	}

}
```
## 4.2. dept.xml
> src/main/resources > mybatis.mapper > XML File
### 종합 : dept.xml 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dept">
	<select id="list" resultType="mybatis.vo.DeptVO">
		SELECT * FROM dept
	</select>
	<select id="search" parameterType="Map" resultType="mybatis.vo.DeptVO">
		SELECT * FROM dept
		<trim prefix="where">
			<choose>
				<when test="searchType != null and searchType == 'deptno'">
					deptno = #{searchValue}
				</when>
				<when test="searchType != null and searchType == 'dname'">
					dname LIKE CONCAT('%', #{searchValue}, '%')
				</when>
				<when test="searchType != null and searchType == 'loccode'">
					loccode = #{searchValue}
				</when>
			</choose>
		</trim>
	</select>
</mapper>
```
## 4.3. DeptDAO.java
> src/main/java > mybatis.dao > Java Class
### 종합 : DeptDAO.java 코드 전문
```java
package mybatis.dao;

import java.util.List;

import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class DeptDAO {
	
	@Autowired
	private SqlSessionTemplate ss;
	
	public DeptVO[] getList() {
		DeptVO[] ar = null;
		List<DeptVO> list = ss.selectList("dept.list");
		
		if(list != null && list.size() > 0) {
			ar = new DeptVO[list.size()];
			list.toArray(ar);
		}
		
		return ar;
	}
	
	public DeptVO[] searchList(String searchType, String searchValue) {
		DeptVO[] ar = null;
		Map<String, String> map = new HashMap<String, String>();
		map.put("searchType", searchType);
		map.put("searchValue", searchValue);
		
		List<DeptVO> list = ss.selectList("dept.search", map);
		if(list != null && list.size() > 0) {
			ar = new DeptVO[list.size()];
			list.toArray(ar);
		}
		
		return ar;
	}

}
```
## 4.4. DeptController.java
> src/main/java > com.lgt.alf > Java Class
### 4.4.1. deptList
```java
package com.lgt.alf;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.DeptDAO;
import mybatis.dao.DeptVO;

@Controller
public class DeptController {
	
	@Autowired
	private DeptDAO d_dao;
	
	@RequestMapping("dept")
	public ModelAndView deptList() {
		ModelAndView mv = new ModelAndView();
		DeptVO[] ar = d_dao.getList();
		
		mv.addObject("ar", ar);
		mv.setViewName("dept/list");
		
		return mv;
	}

}
```
### 4.4.2. 검색을 위한 searchList() 메서드 생성
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
- list.jsp에서 "dept/search" request를 하면 여기로 오게 된다.
- 반환형은 `Map<String, Object>`이다. map에 DeptVO 배열을 받아야 하므로 Object이다.
	- 배열을 넣었으면 다시 빼서 쓸 때 형변환을 해야 할 것 같지만 이는 servlet-context.xml에 등록한 jsonHttpMessageConverter으로 인해 생략 가능하다.
- ResponseBody 어노테이션으로 이 함수의 반환값을 list.jsp의 data 변수가 받게 된다.
### 종합 : DeptController.java 코드 전문
```java
package com.lgt.alf;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.DeptDAO;
import mybatis.vo.DeptVO;

@Controller
public class DeptController {
	
	@Autowired
	private DeptDAO d_dao;
	
	@RequestMapping("list")
	public ModelAndView deptList() {
		ModelAndView mv = new ModelAndView();
		DeptVO[] ar = d_dao.getList();
		
		mv.addObject("ar", ar);
		mv.setViewName("dept/list");
		
		return mv;
	}
	
	@RequestMapping(value="dept/search", method=RequestMethod.POST)
	@ResponseBody
	public Map<String, Object> searchList(String searchType, String searchValue) {
		Map<String, Object> map = new HashMap<String, Object>();
		DeptVO[] ar = d_dao.searchList(searchType, searchValue);
		
		map.put("ar", ar);
		
		return map;
	}

}
```
## 4.5. list.jsp
> src > main > webapp > WEB-INF > views > dept > JSP File
### 4.5.1. Dept 목록 출력하기
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
	table{
		border-collapse: collapse;
	}
	table>tbody tr, table>tbody td{
		border: 1px solid #aaa;
	}
</style>
</head>
<body>
	<table>
		<tbody>
			<tr>
				<th>부서코드</th>
				<th>부서명</th>
				<th>도시코드</th>
			</tr>
			<c:forEach var="vo" items="${ar}">
			<tr>
				<td>${vo.deptno}</td>
				<td>${vo.dname}</td>
				<td>${vo.loccode}</td>
			</tr>
			</c:forEach>
		</tbody>
	</table>
</body>
</html>
```
### 4.5.1. 검색 기능 추가를 위한 코드 수정
```HTML
<form action="dept/search" method="post">
	<select id="searchType" name="searchType">
		<option>::선택::</option>
		<option value="deptno">부서코드</option>
		<option value="dname">부서명</option>
		<option value="loccode">도시코드</option>
	</select>
	<input type="text" id="searchValue" name="searchValue"/>
	<input type="button" id="btn" value="검색"/>
</form>
<br/>
<div>
	<table id="listPage">
		<colgroup>
			<col width="100px"/>
			<col width="*"/>
			<col width="100px"/>
		</colgroup>
		<thead>
			<tr>
				<th>부서코드</th>
				<th>부서명</th>
				<th>도시코드</th>
			</tr>
		</thead>
		<tbody>
			<c:forEach var="vo" items="${ar}">
			<tr>
				<td>${vo.deptno}</td>
				<td>${vo.dname}</td>
				<td>${vo.loccode}</td>
			</tr>
			</c:forEach>
		</tbody>
	</table>
</div>
```
- HTML 영역을 위와 같이 수정했다. <font color="#7f7f7f">위치 수정 정도?</font>

```javascript
$(function(){
	$("#btn").click(function(){
		var searchType = $("#searchType").val();
		var searchValue = $("#searchValue").val();
		
		$.ajax({
			url: "dept/search",
			type: "post",
			data: {"searchType":searchType, "searchValue":searchValue},
			dataType: "json"
		}).done(function(data){
			let str = "";
			for(var i = 0 ; i < data.ar.length ; i++){
				str += "<tr><td>" + data.ar[i].deptno + "</td><td>" + data.ar[i].dname + "</td><td>" + data.ar[i].loccode + "</td></tr>";
			}
			$("#listPage tbody").html(str);
		});
	});
});
```
- form 태그의 value를 searchType, searchValue에 저장하고, 비동기 처리를 이용했다.
- 이때 data에 들어오는 데이터는 json 형식이고, key는 ar, value는 DeptVO 배열이므로 data.ar은 DeptVO 배열이다.
- str에 HTML 태그와 각 배열의 멤버변수를 이어서 HTML 코드를 완성한다.
- html() 메서드를 이용하여 리스트가 출력되던 위치에 검색된 목록을 출력하도록 하였다.
### 종합 : list.jsp 코드 전문
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
	table{
		border-collapse: collapse;
		width: 400px;
	}
	table>tbody tr, table>tbody td, table>tbody th{
		border: 1px solid #aaa;
	}
</style>
</head>
<body>
	<form action="dept/search" method="post">
		<select id="searchType" name="searchType">
			<option>::선택::</option>
			<option value="deptno">부서코드</option>
			<option value="dname">부서명</option>
			<option value="loccode">도시코드</option>
		</select>
		<input type="text" id="searchValue" name="searchValue"/>
		<input type="button" id="btn" value="검색"/>
	</form>
	<br/>
	<div>
		<table id="listPage">
			<colgroup>
				<col width="100px"/>
				<col width="*"/>
				<col width="100px"/>
			</colgroup>
			<thead>
				<tr>
					<th>부서코드</th>
					<th>부서명</th>
					<th>도시코드</th>
				</tr>
			</thead>
			<tbody>
				<c:forEach var="vo" items="${ar}">
				<tr>
					<td>${vo.deptno}</td>
					<td>${vo.dname}</td>
					<td>${vo.loccode}</td>
				</tr>
				</c:forEach>
			</tbody>
		</table>
	</div>
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script>
		$(function(){
			$("#btn").click(function(){
				var searchType = $("#searchType").val();
				var searchValue = $("#searchValue").val();
				
				$.ajax({
					url: "dept/search",
					type: "post",
					data: {"searchType":searchType, "searchValue":searchValue},
					dataType: "json"
				}).done(function(data){
					let str = "";
					for(var i = 0 ; i < data.ar.length ; i++){
						str += "<tr><td>" + data.ar[i].deptno + "</td><td>" + data.ar[i].dname + "</td><td>" + data.ar[i].loccode + "</td></tr>";
					}
					$("#listPage tbody").html(str);
				});
			});
		});
	</script>
</body>
</html>
```