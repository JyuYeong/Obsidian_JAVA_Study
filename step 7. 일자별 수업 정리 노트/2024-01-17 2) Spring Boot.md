정보처리기사, SQLD
# 1. 환경 설정
## 1.1. Visual Studio Code
[유용한 단축키](https://inpa.tistory.com/entry/VS-Code-%E2%8F%B1%EF%B8%8F-%EC%9C%A0%EC%9A%A9%ED%95%9C-%EB%8B%A8%EC%B6%95%ED%82%A4-%EC%A0%95%EB%A6%AC)

Extensions 설치:
- Extension Pack for Java
- Spring Boot Extension Pack
- Lombok Annotations Support for VS Code

[[#1.2. Spring Boot Project 생성]] 후:
1. File > Open Folder
	- My_Study/SpringBoot_Study/work/ex1 선택
2. SideBar > Spring Boot Dashboard
	- src > main > java/com/ict/ex1 > Ex1Application.java를 직접 실행해도 되지만
	- 대시보드를 보면 APPS에 실행 가능한 파일이 보인다.
	- <font color="#d99694">STS나 Eclipse가 실행중이었다면 반드시 종료하고 실행하자.</font>
## 1.2. Spring Boot Project 생성
1. [spring initializr](https://start.spring.io) 접속
2. 설정
	- Project : Maven
	- Language : Java
	- Spring Boot : 3.2.1
	- Project Metadata
		- Group : com.ict
		- Artifact : ex1
		- Name : ex1
		- Description : Demo project for Spring Boot
		- Package name : com.ict.ex1
		- Packaging : Jar
		- Java : 17
	- Dependencies > ADD DEPENDENCIES
		- Spring Boot DevTools
		- Lombok
		- Spring Configuration Processor
		- Spring Web
		- <font color="#7f7f7f">MySQL Driver</font>
		- <font color="#7f7f7f">MyBatis Framework</font>
3. Generate
4. ZIP file move : My_Study/SpringBoot_Study/work/
5. UPZIP : My_Study/SpringBoot_Study/work/ex1/
# 2. Spring Boot
- Tomcat이 내장되어 있다.
- 참고 링크 [스프링과 스프링부트](https://www.codestates.com/blog/content/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8)
- POJO<font color="#7f7f7f">(Plaing Old Java Object)</font> Programming
# 3. Spring Boot 시작
## 3.1. Controller
> src > main > java > com > ict > ex1 > TestController.java

```java
package com.ict.ex1;

public class TestController {

}
```
- Java 클래스 열린 화면

```java
@RestController
public class TestController {

    @GetMapping("/test")
    public String test(){
        return "Hello, World!";
    }

}
```
- RestController는 <font color="#95b3d7">ResponseBody와 Controller가 합쳐진</font> 어노테이션이다.
	- Controller와 RestController가 궁금하면 [여기](https://mangkyu.tistory.com/49)를 참고하자.
- RequestMapping이 아닌 <font color="#95b3d7">GetMapping 또는 PostMapping</font>을 써야 한다.
- 저장하고 `localhost:8080/test`로 들어가 확인해 보자.

### 3.1.1. PathVariable annotation
```java
@GetMapping("test/{var}")
public String varTest(String v1){
	return v1;
}
```
- String 타입의 파라미터를 그대로 반환하는 varTest() 메서드를 만들자.
- GetMapping에 "test/{var}"를 사용하면 "test/list"와 같이 입력했을 때, list를 변수 var로 인식하며, 경로로 인식한다.

```java
@GetMapping("test/{var}")
public String varTest(@PathVariable("var") String v1){
	return v1;
}
```
- 파라미터 앞에 PathVariable 어노테이션을 추가하면 파라미터 v1에 변수 var가 대입된다.
- 따라서 `localhost:8080/test/list`라고 입력하면 해당 경로에 list가 출력된다.

```java
@GetMapping("test/{var}")
public String varTest(@PathVariable String var){
	return var;
}
```
- 메서드의 파라미터 이름이 PathVariable과 같다면 이렇게 입력해도 위와 똑같이 구동된다.
### 3.1.2. RequestParam annotation
```java
@GetMapping("req1")
public String getReq(String name, String email){
	return name + " / " + email;
}
```
- `localhost:8080/req1?name=Tom&email=tom@korea.com`을 입력하면 `localhost:8080/req1`에 리턴값이 출력된다.

```java
@GetMapping("req1")
public String getReq(@RequestParam String name, @RequestParam String email){
	return name + " / " + email;
}
```
- RequestParam 어노테이션을 붙이면 반드시 이 파라미터가 있어야 한다.
	- RequestParam을 안 썼을 때는 해당 파라미터의 값은 null이다.
	- RequestParam을 적용한 파라미터가 값을 전달받지 못한 경우 오류 페이지가 뜬다.
### 3.1.3. Value annotation
> src > main > resources > application.properties

```
global.guestId=A00001
global.siteName=ict.com
```
- 위와 같이 입력하자.

TestController로 돌아와서
```java
@Value("${global.guestId}")
private String guestId;
```
- 멤버 변수를 추가하자.
- Value 어노테이션을 적용하고, EL을 이용해 global.guestId를 입력하면 application.properties에 입력한 guestId의 값을 자동으로 연결된다.

```java
@GetMapping("req2")
public String getReq2(){
	return guestId;
}
```
- 메서드를 새로 하나 만들고 확인해 보자.

```java
@Value("${global.siteName}")
private String siteName;
```
- 멤버 변수를 하나 더 만들어 보자.

```java
@GetMapping("req2")
public String getReq2(){
	return guestId + " / " + siteName;
}
```
- 메서드를 다음과 같이 수정하고 확인해 보자.
## 3.2. lombok 알아보기

getter와 setter, Constructor를 자동으로 생성해 보자.
### 3.2.1. getter와 setter

VO 클래스를 만들어 보자.
> src > main > java > test > vo > TestVO.java

```java
package com.ict.ex1.test.vo;

public class TestVO {

}
```
- TestVO.java 파일을 생성했다.

```java
@Data
public class TestVO {
	private String id;
	private int value;
}
```
- lombok의 Data 어노테이션을 추가하였다.

TestController로 이동하자.
> src > main > java > com > ict > ex1 > TestController.java

```java
@GetMapping("req3")
public String getReq3(){
	TestVO tvo = new TestVO();
	tvo.setId(guestId);
	return tvo.getId() + " / " + siteName + "^^";
}
```
- TestVO 객체를 생성했더니 해당 멤버변수의 getter, setter를 사용할 수 있다.
- VO 클래스에 <font color="#95b3d7">Data 어노테이션</font>을 추가하면 <font color="#95b3d7">자동으로 getter, setter가 등록</font>됨을 알 수 있다.

새롭게 VO 클래스를 만들어 보자.
> src > main > java > com > icct > ex1 > test > vo > DataVO.java

```java
@Getter
@Setter
public class DataVO {
    private String ename, job, hiredate;
}
```
- 이번에는 Data 어노테이션이 아닌 Getter와 Setter 어노테이션을 사용하였다.
- 롬복은 <font color="#95b3d7">Getter와 Setter를 사용하는 것을 권장</font>한다.

TestController로 이동하자.
> src > main > java > com > ict > ex1 > TestController.java

```java
@GetMapping("req4")
public String getReq4(){
	DataVO dvo = new DataVO();
	dvo.setEname("홍길동");
	dvo.setJob("개발");
	dvo.setHiredate("2024-01-17");
	return dvo.getEname() + " / " + dvo.getJob() + " / " + dvo.getHiredate();
}
```
- `localhost:8080/req4`에서 확인 해보자.
### 3.2.2. Constructor

VO 클래스 어노테이션 추가
> src > main > java > com > ict > ex1 > test > vo > DataVO.java

```java
@Getter
@Setter
@AllArgsConstructor
public class DataVO {
    private String ename, job, hiredate;
}
```
- <font color="#95b3d7">AllArgsConstructor 어노테이션</font>을 추가하자.

TestController에서 확인
> src > main > java > com > ict > ex1 > TestController.java

```java
@GetMapping("req4")
public String getReq4(){
	DataVO dvo = new DataVO("홍길동", "개발", null);
	return dvo.getEname() + " / " + dvo.getJob() + " / " + dvo.getHiredate();
}
```
- VO 클래스에 AllArgsConstructor 어노테이션으로 인해 <font color="#95b3d7">모든 멤버 변수를 인자로 넣는 생성자를 생성</font>하였으므로, 이와 같이 사용이 가능하다.
- <font color="#95b3d7">NoArgsConstructor 어노테이션</font>은 인자가 필요 없는 <font color="#95b3d7">기본 생성자가 자동으로</font> 만들어진다.
# 4. VSCode에서 Spring Boot Project 만들기

Visual Studio Code에서 새로운 Spring Boot Project 만들기 절차:
1. Ctrl+Shift+P -> Spring Initializr: Create a Maven Project...
2. 3.2.1
3. Java
4. com.ict
5. ex2
6. jar
7. 17
8. dependencies
	- Spring Boot DevTools
	- Lombok
	- Spring Configuration Processor
	- Spring Web
9. 프로젝트 폴더 지정 : My_Study/SpringBoot_Study/work/

# 5. 연습문제
> ex2

새로운 프로젝트에서 DataVO와 DataController를 만들고, URL에 "t1"을 입력하였을 때, "환영합니다" 메시지가 페이지에 출력되도록 하자.

## 5.1. VO 객체 만들기
> src > main > java > data > vo > DataVO.java

```java
package data.vo;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@AllArgsConstructor
public class DataVO {
    private String msg;
}
```

## 5.2. application.properties 활용하기
> src > main > resources > application.properties

```
str=Hello
```
- <font color="#d99694">한글은 출력 시 깨짐 주의</font>

## 5.3. Controller 만들기
> src > main > java > com > ict > ex2 > DataController.java

```java
package com.ict.ex2;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import data.vo.DataVO;

@RestController
public class DataController {
    @Value("${str}")
    private String msg;

    @GetMapping("/t1")
    public String test(){
        DataVO vo = new DataVO(msg);
        return vo.getMsg();
    }
}
```
# 6. Spring Boot에서 MVC 사용해보기
## 6.1. 새로운 프로젝트 생성하기

Visual Studio Code에서 새로운 Spring Boot Project 만들기 절차:
1. Ctrl+Shift+P -> Spring Initializr: Create a Maven Project...
2. 3.2.1
3. Java
4. com.ict
5. ex3
6. jar
7. 17
8. dependencies
	- Spring Boot DevTools
	- Lombok
	- Spring Configuration Processor
	- Spring Web
9. 프로젝트 폴더 지정 : My_Study/SpringBoot_Study/work/
## 6.2. View 만들기
> src > main > webapp > WEB-INF > views > test.jsp

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
    <h1>${msg}</h1>
</body>
</html>
```
- 이 JSP로 msg가 와야 한다.

## 6.3. View를 JSP로 하겠다는 명시적인 설정
> src > main > resources > application.properties

```
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```
- Spring Legacy Project의 DispatcherServlet인 servlet-context.xml에서 ViewResolver 설정과 같은 설정이다.

servlet-context.xml의 ViewResolver 설정:
```xml
<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<beans:property name="prefix" value="/WEB-INF/views/" />
	<beans:property name="suffix" value=".jsp" />
</beans:bean>
```

## 6.4. dependency 추가
> pom.xml

```xml
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
	<scope>provided</scope>
</dependency>
```

## 6.5. Controller 생성
> src > main > java > com > ict > ex3 > TestController.java

```java
@Controller
public class TestController {
    @RequestMapping("test")
    public ModelAndView test() {
        ModelAndView mv = new ModelAndView();
        mv.addObject("msg", "go for it!");
        mv.setViewName("test");
        return mv;
    }
}
```
- 기존 Spring Legacy Project와 같이 Controller, RequestMapping 어노테이션을 사용하자.