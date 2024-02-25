# 1. 새 프로젝트 생성

VS Code: Ctrl + Shift + P
- version : 3.2.2.
- Artifact : secure
- dependencies :
	- Spring Boot DevTools
	- Lombok
	- Spring Configuration Processor
	- Spring Web
	- MyBatis Framework
	- MySQL Driver
	- <font color="#9bbb59">Spring Security</font>

## 1.1. dependencies 추가 : tomcat-jasper
> pom.xml

```xml
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
	<scope>provided</scope>
</dependency>

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

## 1.2. application.properties 내용 추가
> src > main > resources > application.properties

```
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp

spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test_db?characterEncoding=UTF-8
spring.datasource.username=test_admin
spring.datasource.password=1111
spring.datasource.tomcat.max-active=30
spring.datasource.tomcat.max-idle=20
spring.datasource.tomcat.min-idle=10
```

## 1.3. MySQL Workbench - member 테이블 설정

- test_db.member의 m_pw의 Datatype을 VARCHAR(100)으로 설정

## 1.4. JSP 세팅 : index, login, reg

## 1.5. DbConfig 세팅
> src > main > java > com >  ict > secure > config > DbConfig.java

```java
@Configuration
@MapperScan(basePackages = "com.ict.secure.mapper")
public class DbConfig {
	
	@Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean factory = new SqlSessionFactoryBean();
		
        // 인자로 넘어온 DataSource를 비어 있던 FactoryBean에 적용하자.
        factory.setDataSource(dataSource);
		
        // mapper들을 인지를 시키자.
        PathMatchingResourcePatternResolver resolver = 
	        new PathMatchingResourcePatternResolver();
        factory.setMapperLocations(resolver.getResources(
                "classpath:mapper/**/*.xml"
		));
		
        return factory.getObject();
    }
}
```
- Bean 어노테이션을 이용해 SqlSessionFactory를 Context에 등록한다.

```java
@Bean
public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sessionFactory) {
	return new SqlSessionTemplate(sessionFactory);
}
```

[[#예제 1 DbConfig.java|> DbConfig 코드 전문 보기]]
## 1.6. VO 객체 만들기
> src > main > java > com > ict > secure > vo > MemVO.java

[[#예제 1 MemVO.java|> MemVO 코드 전문 보기]]
## 1.7. mapper 만들기
> src > main > resources > mapper > mem.xml

> [!caution] 로그인에는 기본키만 전달한다.
> 비밀번호는 이미 암호화가 되어 SQL 문에서 비교할 수 없다.

```xml
<select id="login" parameterType="String" resultType="com.ict.secure.vo.MemVO">
	SELECT * FROM member
	WHERE m_id = #{m_id}
</select>
```

[[#예제 1 mem.xml|> mapper 코드 전문 보기]]
## 1.8. Mapper 만들기
> src > main > java > com > ict > secure > mapper > MemberMapper.java

[[#예제 1 MemberMapper.java|> Mapper 코드 전문 보기]]
## 1.9. Service 만들기
> src > main > java > com > ict > secure > service > MemberService.java

```java
public int regMember(MemVO vo) {
        // reg.jsp에서 전달되는 m_id, m_pw, m_name이 vo에 채워진다.
        // 여기서 비밀번호를 암호화 시킨다.
    }
```
- 회원 가입을 위한 함수를 만든다.

```java
MemVO mvo = new MemVO();
mvo.setM_id(vo.getM_id());
mvo.setM_name(vo.getM_name());
```
- 우선 [[#1.10. SecurityJavaConfig 만들기|SecurityJavaConfig.java]]를 먼저 만들자.
- 그리고 비밀번호를 암호화 시키도록 하자.

```java
@Autowired
private PasswordEncoder passwordEncoder;
```
- SecurityJavaConfig에 Bean으로 등록한 PasswordEncoder를 Autowired 어노테이션으로 연결하자.

```java
mvo.setM_pw(passwordEncoder.encode(vo.getM_pw()));
```
- 새로 생성한 MemVO 객체에 비밀번호를 등록하였다.
	- 새로운 MemVO 객체를 생성할 필요 없이, 기존의 VO 객체에 다시 넣어도 된다.
	- 만약 그렇게 한다면 위의 mvo에 새로 id와 name을 넣는 작업은 할 필요 없다.
	```java
	public int regMember(MemVO vo) {
		vo.setM_pw(passwordEncoder.encode(vo.getM_pw()));
		return m_Mapper.reg(vo);
	}
	```
- 암호화를 시킬 때 <font color="#7f7f7f">(원래)</font> sort라는 값이 필요하다.

```java
public MemVO login(MemVO mvo) {
	MemVO vo = m_Mapper.login(mvo.getM_id());
}
```
- 로그인을 위한 함수를 만들자.
- DB에서 m_id를 이용해 MemVO 객체를 얻어낸다.

```java
public MemVO login(MemVO mvo) {
	MemVO vo = m_Mapper.login(mvo.getM_id());
	
	if (vo != null) {
		if (passwordEncoder.matches(mvo.getM_pw(), vo.getM_pw())) {
			return vo;
		}
	}
	
	return null;
}
```
- MemVO가 얻은 m_pw는 암호화되어 있으므로 복호화 해야 한다.
- PasswordEncoder의 matches 메서드로 입력한 비밀번호와 DB에 저장된 비밀번호를 비교한다.

[[#예제 1 MemberService.java|> Service 코드 전문 보기]]
## 1.10. SecurityJavaConfig 만들기
> src > main > java > com > ict > secure > config > SecurityJavaConfig.java

```java
@Configuration
@EnableWebSecurity
public class SecurityJavaConfig {
    
}
```
- Configuration과 EnableWebSecurity 어노테이션을 추가한다.

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
	http.csrf((csrfConfig) -> csrfConfig.disable())
			.headers(
					(headerConfig) -> 
						headerConfig.frameOptions(frameOptionsConfig -> 
							frameOptionsConfig.disable()))
			.authorizeHttpRequests((authorizeHttpRequests) -> authorizeHttpRequests
					.requestMatchers("/", "/reg", "/login", "/reqLogin").permitAll()
					.anyRequest().authenticated());
	return http.build();
}
```
- 위의 코드를 집어 넣자.
- <span style="background:rgba(240, 200, 0, 0.2)">우선 `"/"` 대신 `"/**"`을 넣어서 동작하자. 해당 이슈 추후 처리</span>
	- 이건 root 하위를 모조리 허용을 한다는 의미이다!

```java
@Bean
public PasswordEncoder passwordEncoder() {
	return new BCryptPasswordEncoder();
}
```
- 암호화된 비밀번호를 반환하는 PasswordEncoder를 추가한다.

> [!todo] 파란 줄이 생기는 이유 찾아보자.

[[#예제 1 SecurityJavaConfig.java|> SecurityJavaConfig 코드 전문 보기]]
## 1.11. MemberController 만들기
> src > main > java > com > ict > secure > controller > MemberController.java

```java
@PostMapping("/reqLogin")
public ModelAndView login(MemVO vo) {
	ModelAndView mv = new ModelAndView();
	MemVO mvo = memService.login(vo);
	if (mvo != null) {
		session.setAttribute("mvo", vo);
	}
	mv.setViewName("redirect:/");

	return mv;
}
```
- 로그인 로직을 처리하였다.

[[#예제 1 MemberController.java|> MemberController 코드 전문 보기]]
# A. 부록
## 2024-02-16 SNS 로그인 안 되던 문제
[[2024-02-16 NEXT에서 SNS 로그인을 할 때]]에서 로그인이 안 되던 문제는
카카오에서 새로운 애플리케이션을 만들어서 사용하면 문제없이 동작한다.
# B. 코드 전문
## 예제 1 : index.jsp
> src > main > webapp > WEB-INF > jsp > index.jsp

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>     
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>

</head>
<body>
	<div id="bbs">
        <c:if test="${sessionScope.mvo ne null}">
            <a href="/logout">로그아웃</a>
        </c:if>
        <c:if test="${sessionScope.mvo eq null}">
            <a href="/login">로그인</a>
            <a href="/reg">회원등록</a>
        </c:if>
	</div>

    
</body>
</html>
```
## 예제 1 : login.jsp
> src > main > webapp > WEB-INF > jsp > login.jsp

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style type="text/css">
	#bbs table {
	    width:580px;
	    margin-left:10px;
	    border:1px solid black;
	    border-collapse:collapse;
	    font-size:14px;
	    
	}
	
	#bbs table caption {
	    font-size:20px;
	    font-weight:bold;
	    margin-bottom:10px;
	}
	
	#bbs table th,#bbs table td {
	    text-align:center;
	    border:1px solid black;
	    padding:4px 10px;
	}
	
	.no {width:15%}
	.subject {width:30%}
	.writer {width:20%}
	.reg {width:20%}
	.hit {width:15%}
	.title{background:lightsteelblue}
	
	.odd {background:silver}
	
	/* paging */
	
	table tfoot ol.paging {
	    list-style:none;
	}
	
	table tfoot ol.paging li {
	    float:left;
	    margin-right:8px;
	}
	
	table tfoot ol.paging li a {
	    display:block;
	    padding:3px 7px;
	    border:1px solid #00B3DC;
	    color:#2f313e;
	    font-weight:bold;
	    text-decoration: none
	}
	
	table tfoot ol.paging li a:hover {
	    background:#00B3DC;
	    color:white;
	    font-weight:bold;
	    text-decoration: none
	}
	
	.disable {
	    padding:3px 7px;
	    border:1px solid silver;
	    color:silver;
	}
	
	.now {
	   padding:3px 7px;
	    border:1px solid #ff4aa5;
	    background:#ff4aa5;
	    color:white;
	    font-weight:bold;
	}
	
	.empty{
		height: 50px;
		color: #ababab;
	}
</style>
</head>
<body>
	<div id="bbs">
    <form action="/reqLogin" method="post">
		<table summary="로그인">
			<caption>로그인</caption>			
			<tbody>
                <tr>
                    <th>아이디</th>
                    <td><input type="text" id="m_id" name="m_id"/></td>
                </tr>
                <tr>
                    <th>비밀번호</th>
                    <td><input type="password" id="m_pw" name="m_pw"/></td>
                </tr>
                <tr>
                    <td colspan="2">
                        <button type="button" onclick="sendData(this.form)">로그인</button>
                    </td>
                </tr>
			</tbody>
		</table>
    </form>
	</div>

    <script>

        function sendData(ff){
            //let id = $("#m_id").val();
            //let id = document.getElementById("m_id").value;
            let id = ff.m_id.value;
            if(id.trim().length < 1){            
                alert("아이디를 입력하세요");
                //document.getElementById("m_id").focus();// 커서 놓기
                ff.m_id.focus();
                return;
            }

            ff.submit();
        }
    </script>
</body>
</html>
```
## 예제 1 : reg.jsp
> src > main > webapp > WEB-INF > jsp > reg.jsp

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style type="text/css">
	#bbs table {
	    width:580px;
	    margin-left:10px;
	    border:1px solid black;
	    border-collapse:collapse;
	    font-size:14px;
	    
	}
	
	#bbs table caption {
	    font-size:20px;
	    font-weight:bold;
	    margin-bottom:10px;
	}
	
	#bbs table th,#bbs table td {
	    text-align:center;
	    border:1px solid black;
	    padding:4px 10px;
	}
	
	.no {width:15%}
	.subject {width:30%}
	.writer {width:20%}
	.reg {width:20%}
	.hit {width:15%}
	.title{background:lightsteelblue}
	
	.odd {background:silver}
	
	/* paging */
	
	table tfoot ol.paging {
	    list-style:none;
	}
	
	table tfoot ol.paging li {
	    float:left;
	    margin-right:8px;
	}
	
	table tfoot ol.paging li a {
	    display:block;
	    padding:3px 7px;
	    border:1px solid #00B3DC;
	    color:#2f313e;
	    font-weight:bold;
	    text-decoration: none
	}
	
	table tfoot ol.paging li a:hover {
	    background:#00B3DC;
	    color:white;
	    font-weight:bold;
	    text-decoration: none
	}
	
	.disable {
	    padding:3px 7px;
	    border:1px solid silver;
	    color:silver;
	}
	
	.now {
	   padding:3px 7px;
	    border:1px solid #ff4aa5;
	    background:#ff4aa5;
	    color:white;
	    font-weight:bold;
	}
	
	.empty{
		height: 50px;
		color: #ababab;
	}
</style>
</head>
<body>
	<div id="bbs">
    <form action="reg" method="post">
		<table summary="회원가입">
			<caption>회원가입</caption>			
			<tbody>
                <tr>
                    <th>아이디</th>
                    <td><input type="text" id="m_id" name="m_id"/></td>
                </tr>
                <tr>
                    <th>비밀번호</th>
                    <td><input type="password" id="m_pw" name="m_pw"/></td>
                </tr>
                <tr>
                    <th>이름</th>
                    <td><input type="text" id="m_name" name="m_name"/></td>
                </tr>
                <tr>
                    <td colspan="2">
                        <button type="button" onclick="sendData(this.form)">등록</button>
                    </td>
                </tr>
			</tbody>
		</table>
    </form>
	</div>

    <script>

        function sendData(ff){
            //let id = $("#m_id").val();
            //let id = document.getElementById("m_id").value;
            let id = ff.m_id.value;
            if(id.trim().length < 1){            
                alert("아이디를 입력하세요");
                //document.getElementById("m_id").focus();// 커서 놓기
                ff.m_id.focus();
                return;
            }

            ff.submit();
        }
    </script>
</body>
</html>
```
## 예제 1 : DbConfig.java
> src > main > java > com >  ict > secure > config > DbConfig.java

```java
package com.ict.secure.config;

import javax.sql.DataSource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;

@Configuration
@MapperScan(basePackages = "com.ict.secure.mapper")
public class DbConfig {

    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean factory = new SqlSessionFactoryBean();

        // 인자로 넘어온 DataSource를 비어 있던 FactoryBean에 적용하자.
        factory.setDataSource(dataSource);

        // mapper들을 인지를 시키자.
        PathMatchingResourcePatternResolver resolver = 
	        new PathMatchingResourcePatternResolver();
        factory.setMapperLocations(resolver.getResources(
                "classpath:mapper/**/*.xml"));

        return factory.getObject();
    }

    @Bean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sessionFactory) {
        return new SqlSessionTemplate(sessionFactory);
    }
}
```
## 예제 1 : MemVO.java
> src > main > java > com > ict > secure > vo > MemVO.java

```java
package com.ict.secure.vo;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class MemVO {
    private String m_code, m_id, m_pw, m_name;
}
```
## 예제 1 : mem.xml
> src > main > resources > mapper > mem.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ict.secure.mapper.MemberMapper">
    <!-- 회원가입 기능 -->
    <insert id="reg" parameterType="com.ict.secure.vo.MemVO">
        INSERT INTO member (m_id, m_pw, m_name)
        VALUES (#{m_id}, #{m_pw}, #{m_name})
    </insert>

    <!-- 로그인 기능 -->
    <select id="login" parameterType="String" resultType="com.ict.secure.vo.MemVO">
        SELECT * FROM member
        WHERE m_id = #{m_id}
    </select>
</mapper>
```
## 예제 1 : MemberMapper.java
> src > main > java > com > ict > secure > mapper > MemberMapper.java

```java
package com.ict.secure.mapper;

import org.apache.ibatis.annotations.Mapper;

import com.ict.secure.vo.MemVO;

@Mapper
public interface MemberMapper {

    int reg(MemVO vo);

    MemVO login(String m_id);

}
```
## 예제 1 : MemberService.java
> src > main > java > com > ict > secure > service > MemberService.java

```java
package com.ict.secure.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;

import com.ict.secure.mapper.MemberMapper;
import com.ict.secure.vo.MemVO;

@Service
public class MemberService {

    @Autowired
    private MemberMapper m_Mapper;

    @Autowired
    private PasswordEncoder passwordEncoder;

    // 회원 가입
    public int regMember(MemVO vo) {
        // reg.jsp에서 전달되는 m_id, m_pw, m_name이 vo에 채워진다.

        MemVO mvo = new MemVO();
        mvo.setM_id(vo.getM_id());
        mvo.setM_name(vo.getM_name());

        // 비밀번호 암호화 시작
        mvo.setM_pw(passwordEncoder.encode(vo.getM_pw()));

        return m_Mapper.reg(mvo);
    }

    // 로그인
    public MemVO login(MemVO mvo) {
        MemVO vo = m_Mapper.login(mvo.getM_id());

        if (vo != null) {
            if (passwordEncoder.matches(mvo.getM_pw(), vo.getM_pw())) {
                return vo;
            }
        }

        return null;
    }

}
```
## 예제 1 : SecurityJavaConfig.java

```java
package com.ict.secure.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
public class SecurityJavaConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.csrf((csrfConfig) -> csrfConfig.disable())
                .headers(
                        (headerConfig) -> 
	                        headerConfig.frameOptions(frameOptionsConfig -> 
		                        frameOptionsConfig.disable()))
                .authorizeHttpRequests((authorizeHttpRequests) -> 
	                authorizeHttpRequests
                        .requestMatchers("/**", "/reg", "/login", "/reqLogin").permitAll()
                        .anyRequest().authenticated());
        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```
## 예제 1 : MemberController.java
> src > main > java > com > ict > secure > controller > MemberController.java

```java
package com.ict.secure.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import com.ict.secure.service.MemberService;
import com.ict.secure.vo.MemVO;

import jakarta.servlet.http.HttpSession;

@Controller
public class MemberController {

    @Autowired
    private MemberService memService;

    @Autowired
    private HttpSession session;

    @RequestMapping("/") // 홈
    public String home() {
        return "index";
    }

    @RequestMapping("/reg") // 회원 등록 페이지 입장
    public String reg() {
        return "reg";
    }

    @PostMapping("/reg") // 회원 등록 시
    public String reg(MemVO mvo) {
        int cnt = memService.regMember(mvo); // 회원 등록

        // 필요한 나머지 동작

        return "login";
    }

    @RequestMapping("/login") // 로그인 페이지 입장
    public String login() {
        return "login";
    }

    @PostMapping("/reqLogin")
    public ModelAndView login(MemVO vo) {
        ModelAndView mv = new ModelAndView();
        MemVO mvo = memService.login(vo);
        if (mvo != null) {
            session.setAttribute("mvo", vo);
        }
        mv.setViewName("redirect:/");

        return mv;
    }

}
```