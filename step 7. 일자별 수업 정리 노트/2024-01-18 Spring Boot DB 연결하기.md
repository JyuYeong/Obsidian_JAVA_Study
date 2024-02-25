# 1. DB 연결 예제 1

프로젝트 이름 : ex4_db

<font color="#c3d69b">프로젝트 생성하기 참고:</font>
![[2024-01-17 2) Spring Boot#6.1. 새로운 프로젝트 생성하기]]

<font color="#c3d69b">dependencies 추가:</font>
+ MyBatis Framework
+ MySQL Driver

환경 설정 참고:
![[2024-01-17 2) Spring Boot#6.3. View를 JSP로 하겠다는 명시적인 설정]]
![[2024-01-17 2) Spring Boot#6.4. dependency 추가]]

## 1.1. application.properties에 DB 정보 입력하기
> src > main > resources > application.properties

<font color="#c3d69b">Spring Boot에서는 DB 정보를 아래와 같이 입력</font>한다.
```
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test_db?characterEncoding=UTF-8
spring.datasource.username=test_admin
spring.datasource.password=1111
spring.datasource.tomcat.max-active=30
spring.datasource.tomcat.max-idle=20
spring.datasource.tomcat.min-idle=10
```

Spring Legacy Project에서 작성했던 내용 참고:
![[2024-01-16 서비스 체제#2.3. Properties 파일 만들기]]
## 1.2. VO 객체 만들기
> src > main > java > com > ict > ex4_db > vo > EmpVO.java

```java
package com.ict.ex4_db.vo;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter @Setter @NoArgsConstructor @AllArgsConstructor
public class EmpVO {
    private String empno, ename, job, deptno;
}
```
## 1.3. Mapper 만들기
> src > main > resources > mapper > emp.xml

namespace는 <font color="#4bacc6">mapper interface를 가리킨다</font>.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ict.ex4_db.mapper.EmpMapper">

	<select id="all" resultType="com.ict.ex4_db.vo.EmpVO">
		SELECT * FROM emp
	</select>

    <!-- 총 사원들의 수 -->
    <select id="total_count" parameterType="Map" resultType="int" >
        SELECT COUNT(*) FROM emp
        <where>
            <if test="searchType != null and searchValue != null">
                <choose>
                    <when test="searchType == 0">
                        empno = #{searchValue}
                    </when>
                    <when test="searchType == 1">
                        ename LIKE concat('%',#{searchValue},'%')
                    </when>
                    <when test="searchType == 2">
                        UPPER(job) LIKE concat('%',UPPER(#{searchValue}),'%')
                    </when>
                </choose>
            </if>
        </where>
    </select>
	
	<select id="list" parameterType="Map" resultType="com.ict.ex4_db.vo.EmpVO">
		SELECT a.* FROM (
            SELECT @RN:=@RN+1 AS rownum, b.* FROM (
                SELECT * FROM emp 
                <where>
                    <if test="searchType != null and searchValue != null">
                        <choose>
                            <when test="searchType == 0">
                                empno = #{searchValue}
                            </when>
                            <when test="searchType == 1">
                                ename LIKE concat('%',#{searchValue},'%')
                            </when>
                            <when test="searchType == 2">
                                UPPER(job) LIKE concat('%',UPPER(#{searchValue}),'%')
                            </when>
                        </choose>
                    </if>
                </where>
            ) b, (SELECT @RN:=0) r
        ) a  
        WHERE rownum BETWEEN #{begin} AND #{end}  
	</select>
	
</mapper>
```
## 1.4. Mapper Interface 만들기
> src > main > java > com > ict > ex4_db > mapper > EmpMapper.java

```java
package com.ict.ex4_db.mapper;

public interface EmpMapper {
    
}
```
- `public class EmpMapper`를 `public interface EmpMapper`로 바꿔주자.
- 이제 이곳은 <font color="#548dd4">인터페이스이므로 추상메서드만 정의</font>한다. 이때 <font color="#548dd4">추상 메서드명</font>의 기준은 앞서 만든 mapper인 <font color="#548dd4">emp.xml의 id</font>이다.

<font color="#ccc1d9">추상 메서드 정의:</font>
```java
List<EmpVO> all();
int total_count(String searchType, String searchValue);
List<EmpVO> list(String searchType, String searchValue, String begin, String end);
```

이 인터페이스를 emp.xml이라고 생각해야 한다. 즉 EmpMapper.java는 메뉴판, emp.xml은 그 레시피와 같다.

[[#EmpMapper.java|EmpMapper.java 코드 전문 보러 가기]]
## 1.5. Mapper Service 만들기
> src > main > java > com > ict > ex4_db > service > EmpService.java

```java
package com.ict.ex4_db.service;

@Service
public class EmpService {
    
}
```
- 클래스가 맞지만 Service이므로 <font color="#548dd4">Service 어노테이션</font>을 붙여준다. <font color="#ffc000">DAO와 같은 역할</font>이다.

<font color="#ccc1d9">mapper 선언:</font>
```java
@Autowired
private EmpMapper mapper;
```

<font color="#ccc1d9">getList() 메서드 정의:</font>
```java
public EmpVO[] getList(String searchType, String searchValue, String begin, String end){
	EmpVO[] ar = null;
	List<EmpVO> list = mapper.list(searchType, searchValue, begin, end);
	if(list != null && list.size() > 0) {
		ar = new EmpVO[list.size()];
		list.toArray(ar);
	}
	return ar;
}
```

<font color="#ccc1d9">getTotalCount() 메서드 정의:</font>
```java
public int getTotalCount(String searchType, String searchValue){
	return mapper.total_count(searchType, searchValue);
}
```

[[#EmpService.java|EmpService.java 코드 전문 보러 가기]]
## 1.6. Config 만들기
> src > main > java > com > ict > ex4_db > config > DbConfig.java

<font color="#ccc1d9">설정 객체임을 지정하는 어노테이션:</font>
```java
@Configuration
@MapperScan(basePackages = "com.ict.ex4_db.mapper")
public class DbConfig {
    
}
```
- <font color="#f79646">Configuration 어노테이션</font>으로 설정 객체임을 지정한다.
- MapperScan 어노테이션으로 Mapper Interface를 찾도록 한다.
	- basePackages에 Mapper Interface가 위치한 경로를 지정한다.

<font color="#ccc1d9">이 클래스는:</font>
> 스프링 환경이 알아서 호출하며, 아래의 @Bean 어노테이션으로 인해 강제로 함수들을 한 번 호출한다. 이후 스프링 환경(Context)에 반환되는 객체들을 등록한다.

```java
@Bean
public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
	SqlSessionFactoryBean sqlSessionFactory = new SqlSessionFactoryBean();
	sqlSessionFactory.setDataSource(dataSource);
	PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
	sqlSessionFactory.setMapperLocations(resolver.getResources("classpath:mapper/**/*.xml"));
	return sqlSessionFactory.getObject();
}
```
- MyBatis에서 사용되는 <font color="#9bbb59">Session Factory를 생성하고 설정</font>하자.
- dataSource를 설정하면 MyBatis에서는 이 데이터 소스를 사용하여 DB와 연결한다.
	- <font color="#9bbb59">dataSource는 application.properties에서 이미 만들어졌다.</font>
- <font color="#4bacc6">XML 매퍼 파일들의 위치를 설정</font>한다.
- 이 메서드는 데이터베이스와의 상호작용을 위한 환경을 구성한다.

```java
@Bean
public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory factory) {
	return new SqlSessionTemplate(factory);
}
```
- SqlSessionTemplate를 생성한다.

[[#DbConfig.java|DbConfig.java 코드 전문 보러 가기]]
## 1.7. Controller 만들기
> src > main > java > com > ict > ex4_db > controller > EmpController.java

<font color="#ccc1d9">컨트롤러 지정:</font>
```java
@Controller
public class EmpController {
    
}
```

<font color="#ccc1d9">EmpService 멤버 변수 선언과 주입:</font>
```java
@Autowired
private EmpService e_Service;
```

<font color="#ccc1d9">list 메서드 생성:</font>
```java
@RequestMapping(value="/", method=RequestMethod.GET)
public ModelAndView list(){
	ModelAndView mv = new ModelAndView();
	EmpVO[] ar = e_Service.getList(null, null, "1", "10");
	mv.addObject("ar", ar);
	mv.setViewName("list");
	return mv;
}
```

[[#EmpController.java|EmpController.java 코드 전문 보러 가기]]
## 1.8. JSTL 라이브러리 추가
> pom.xml

```xml
<dependency>
	<groupId>jakarta.servlet</groupId>
	<artifactId>jakarta.servlet-api</artifactId>
	<version>6.0.0</version>
	<scope>provided</scope>
</dependency>

<dependency>
	<groupId>jakarta.servlet.jsp.jstl</groupId>
	<artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
	<version>3.0.0</version>
</dependency>

<dependency>
	<groupId>org.glassfish.web</groupId>
	<artifactId>jakarta.servlet.jsp.jstl</artifactId>
	<version>3.0.1</version>
</dependency>
```
## 1.9. View 만들기
> src > main > webapp > WEB-INF > jsp > list.jsp

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
    <header>
        <h2>사원 목록</h2>
    </header>
    <article>
        <ul>
            <c:forEach items="${ar}" var="vo">
                <li>${vo.empno}, ${vo.ename}, ${vo.job}, ${vo.deptno}</li>
            </c:forEach>
        </ul>
    </article>
</body>
</html>
```
# 2. DB 연결 예제 2

MySQL의 test_db.book_t 테이블 DB를 활용한다.
도서 목록 리스트를 출력하고, 검색 기능을 추가한다.:
- 도서 목록 리스트 컬럼은 번호, 도서명, 저자, 출판사
- 검색 카테고리는 도서명, 저자, 출판사
비동기 처리를 사용하지 않는다.
## 2.1. 새 프로젝트 생성
[[2024-01-17 2) Spring Boot#6.1. 새로운 프로젝트 생성하기|VSCODE에서 프로젝트 생성하기]] 참고:
- Project name: ex5_book
- Dependencies:
	- Spring Boot DevTools
	- Lombok
	- Spring Configuration Processor
	- Spring Web
	- MyBatis Framework
	+ MySQL Driver
## 2.2. application.properties 세팅
> src > main > resources > application.properties

```
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp

spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test_db?characterEncoding=UTF-8
spring.datasource.username=test_admin
spring.datasource.password=1111
spring.datasource.tomcat.max-active=30
spring.datasource.tomcat.max-idle=20
spring.datasource.tomcat.min-idle=10
```
## 2.3. VO 객체 만들기
> src > main > java > com > ict > ex5_book > vo > BookVO.java

```java
package com.ict.ex5_book.vo;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter @Setter @NoArgsConstructor @AllArgsConstructor
public class BookVO {
    private String subject, author, p_name;
}
```
## 2.4. Mapper 만들기
> src > main > resources > mapper > book.xml

## 2.5. Mapper Interface 만들기
> src > main > java > com > ict > ex5_book > mapper > EmpMapper.java
# A. 부록
## A.1. Servlet Container 서블릿 컨테이너

<font color="#9bbb59">HTTP 요청을 받아 웹페이지를 동적으로 생성</font>하는 역할을 한다.
대표적인 Servlet Container는 <font color="#4bacc6">Tomcat</font>
스프링 부트에는 Tomcat이 <font color="#f79646">내장되어 있다</font>.
스프링에서는 URL 요청을 완전히 막으므로 URI 요청 방식을 사용한다:
- JSP와 같은 Tomcat이 해석해야 하는 파일을 요청할 경우 무조건 자바를 거쳐간다.
- URL 자원 요청과 URI 식별자 요청 예:
	- URL : `http://naver.com/a.png`
	- URI : `http://naver.com/picture/a`
## A.2. JavaBean

특정한 정보를 가지고 있는 클래스를 표현하는 하나의 규칙
데이터를 표현하기 위한 목적

<font color="#ccc1d9">JavaBean의 규약:</font>
- 클래스는 반드시 패키지화 되어야 한다.
- 멤버변수를 Property라 한다.
- 멤버변수는 private로 지정, getter와 setter를 정의한다.

<font color="#ccc1d9">JavaBean의 형태:</font>
- 데이터 표현이 목적인 자바 클래스이므로 VO 클래스와 같은 형태
# 코드 전문
## EmpMapper.java
> src > main > java > com > ict > ex4_db > mapper > EmpMapper.java
```java
package com.ict.ex4_db.mapper;

import java.util.List;

import com.ict.ex4_db.vo.EmpVO;

public interface EmpMapper {
    List<EmpVO> all();
    int total_count(String searchType, String searchValue);
    List<EmpVO> list(String searchType, String searchValue, String begin, String end);
}
```
## EmpService.java
> src > main > java > com > ict > ex4_db > service > EmpService.java
```java
package com.ict.ex4_db.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.ict.ex4_db.mapper.EmpMapper;
import com.ict.ex4_db.vo.EmpVO;

@Service
public class EmpService {
    @Autowired
    private EmpMapper mapper;

    public EmpVO[] getList(String searchType, String searchValue, String begin, String end){
        EmpVO[] ar = null;
        List<EmpVO> list = mapper.list(searchType, searchValue, begin, end);
        if(list != null && list.size() > 0) {
            ar = new EmpVO[list.size()];
            list.toArray(ar);
        }
        return ar;
    }

    public int getTotalCount(String searchType, String searchValue){
        return mapper.total_count(searchType, searchValue);
    }
}
```
## DbConfig.java
> src > main > java > com > ict > ex4_db > config > DbConfig.java
```java
package com.ict.ex4_db.config;

import javax.sql.DataSource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;

@Configuration
@MapperScan(basePackages = "com.ict.ex4_db.mapper")
public class DbConfig {
    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean sqlSessionFactory = new SqlSessionFactoryBean();
        sqlSessionFactory.setDataSource(dataSource);
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        sqlSessionFactory.setMapperLocations(resolver.getResources("classpath:mapper/**/*.xml"));
        return sqlSessionFactory.getObject();
    }

    @Bean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory factory) {
        return new SqlSessionTemplate(factory);
    }
}
```
## EmpController.java
> src > main > java > com > ict > ex4_db > controller > EmpController.java
```java
package com.ict.ex4_db.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.servlet.ModelAndView;

import com.ict.ex4_db.service.EmpService;
import com.ict.ex4_db.vo.EmpVO;

@Controller
public class EmpController {
    @Autowired
    private EmpService e_Service;

    @RequestMapping(value="/", method=RequestMethod.GET)
    public ModelAndView list(){
        ModelAndView mv = new ModelAndView();
        EmpVO[] ar = e_Service.getList(null, null, "1", "10");
        mv.addObject("ar", ar);
        mv.setViewName("list");
        return mv;
    }
}
```