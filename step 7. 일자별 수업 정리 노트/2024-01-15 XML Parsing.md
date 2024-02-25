- 웹에서 요청을 하면 HTML 또는 JSON으로 응답한다.
- Jackson Library는 Map을 JSON으로 바꾸는 작업인 서버 쪽에 해당한다. 클라이언트가 JSON을 받아 해석을 하는 라이브러리는 아니다.
- AJAX done 상황에서 우리는 JSON을 별다른 해석을 하지 않았다. 그래서 우리는 Map에 배열을 저장해서 JSON으로 보냈었다.
- 이번 시간에는 XML<font color="#7f7f7f">(</font>[[2. XML Extensible Markup Language|Extensible Markup Language]]<font color="#7f7f7f">)</font>로 반환을 받았을 때, 처리하는 방법을 알아보자.

# 1. XML Parsing
> Spring Legacy Project(Spring MVC Project) > 0115_JDOM > com.ict.ex

웹에서 요청시 <font color="#95b3d7">응답은 주로 XML 또는 JSON</font>이다.
지금까지 우리는 Ajax done 상황에서 JSON으로 응답을 받긴 했으나, 별다른 해석을 하지 않아도 원하는 동작을 취할 수 있었다.
이번 시간에는 XML<font color="#7f7f7f">(</font>[[2. XML Extensible Markup Language|Extensible Markup Language]]<font color="#7f7f7f">)</font>로 반환을 받았을 때, 처리하는 방법을 알아보자.
## 1.1. JDOM Library 추가하기
> [MVNRepository](https://mvnrepository.com/) > search: jdom > org.jdom >> jdom2 > select: 2.0.6
> -> 0115_JDOM > pom.xml

```xml
<dependency>
	<groupId>org.jdom</groupId>
	<artifactId>jdom2</artifactId>
	<version>2.0.6</version>
</dependency>
```

## 1.2. member XML에 임의 데이터 만들기
> src > main > webapp > resources > am > data > XML File : member.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
```
- 첫 화면

```xml
<members>
	<member>
		<name>마루치</name>
		<email>maruchi@korea.com</email>
		<phone>010-3957-2933</phone>
	</member>
	<member>
		<name>아라치</name>
		<email>arachi@korea.com</email>
		<phone>010-1039-1472</phone>
	</member>
</members>
```
- members 태그 안에 member 태그들을 생성한다.
- 각 member 태그 안에 name, email, phone 태그를 생성하여 정보들을 저장한다.
- VO 객체를 만든다면 이 파일이 기준이 되어야 한다.
## 1.3. MemberVO
> src/main/java > data.vo > Java Class : MemberVO.java

```java
package data.vo;

public class MemberVO {
	
	private String name, email, phone;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public String getPhone() {
		return phone;
	}

	public void setPhone(String phone) {
		this.phone = phone;
	}

}
```
- member.xml의 자원 이름과 같은 멤버 변수를 생성했다.
## 1.4. TestController
> src/main/java > com.ict.ex > Java Class : TestController.java

```java
@Autowired
private ServletContext application;
```
- <font color="#7f7f7f">(원래는)</font> 멤버 변수로 Open API Service, URL같은 경로가 멤버 변수로 선언되어 있어야 한다.
- 테스트를 위해 지금은 내부에 저장된 XML 문서에 접근을 하자. 따라서 <font color="#95b3d7">절대경로화 시킬 수 있는 ServletContext</font>를 선언하자.

```java
@RequestMapping("test")
public String test(Model model) throws Exception { 
	String realPath = application.getRealPath("/resources/am/data/member.xml");
	// 계속...
}
```
- 준비된 XML 문서의 절대경로를 얻어낸다.
- RequestMapping에 의해 이 메서드가 호출된다면 <font color="#95b3d7">Model 객체가 생성</font>된다.
	- Model은 <font color="#95b3d7">ModelAndView의 Interface</font>이다.
	- Model은 ModelAndView의 <font color="#95b3d7">상위 레벨</font>이다.
	- Model은 <font color="#95b3d7">최종적으로 Request에 저장</font>되도록 되어있다.
	- Request에 저장되는 Model 안에 배열같은 것들을 저장한다면? 결국은 Request에 저장되는 것이다(?)
- 이 메서드의 <font color="#c3d69b">반환형은 String</font>인데, 즉 반환값은 <font color="#c3d69b">JSP 경로</font>여야 한다.
	- 해당 JSP로 갔을 때 Request를 가져갈 수 있으니 <font color="#c3d69b">JSP에서 Model에 저장된 자원을 사용</font>할 수 있다.

<font color="#ddd9c3">이하: test() 메서드 내부</font>
```java
SAXBuilder builder = new SAXBuilder();
```
- XML 문서를 읽기 위한 객체를 생성하자.

```java
Document doc = builder.build(realPath);
```
- builder를 통하여 XML 문서를 읽어 <font color="#95b3d7">문서화</font><font color="#7f7f7f">(Document)</font>시키자.
	- Document는 <font color="#fac08f">org.jdom2</font>의 Document를 선택하자.
	- 문서화는 메모리 상에 올려놓는다는 의미이다.
	- <font color="#a5a5a5">builder의 build 메서드는 많은 Overloading을 가지고 있다.</font>
- XML 문서<font color="#7f7f7f">(member.xml)</font>의 Root Element는 members이다.

```java
Element root = doc.getRootElement();
```
- 메모리 상에 존재하는 XML 문서의 Root를 알아내자.
	- <font color="#fac08f">org.jdom2</font>의 Element를 선택하자.

```java
System.out.println("ROOT : " + root.getName());
```
- Print 문으로 출력해서 확인해 보자.
	- "test" 경로를 입력하면 Console에 "ROOT : members"가 출력되는 것을 볼 수 있다.

```java
List<Element> list = root.getChildren("member");
```
- Root의 자식 요소들 중 member 요소들을 가져와 보자.
	- getChildren의 Overloading 중 파라미터 cname은 Child name이다.
	- 태그 외부에 붙은 텍스트들은 text 메서드로 받아야 한다.

```java
MemberVO[] ar = null;
if(list != null && list.size() > 0) {
	ar = new MemberVO[list.size()];
	list.toArray(ar);
}
```
- list의 길이만큼 MemberVO 배열을 만들자.
- <font color="#d99694">주의! 이렇게 만들면 list와 ar의 자료형이 맞지 않는다.</font>

```java
MemberVO[] ar = null;
if(list != null && list.size() > 0) {
	ar = new MemberVO[list.size()];
	
	int i = 0;
	for(Element mem : list) {
		String name = mem.getChildText("name");
		String email = mem.getChildText("email");
		String phone = mem.getChildText("phone");
		System.out.println(name + " / " + email + " / " + phone);
		// 계속...
	}
}
```
- list 안에 있는 요소들만큼 반복하면서 Element를 MemberVO로 변환하여 ar에 저장해야 한다.
- 따라서 int 형 변수 i를 선언 배열 접근 시 필요한 index 값으로 활용하자.
- Element의 요소 값들을 얻어낸다. getChildText를 이용하자.
- `System.out.println(name + " / " + email + " / " + phone);`로 확인 해보자.
	```
	마루치 / maruchi@korea.com / 010-3957-2933
	아라치 / arachi@korea.com / 010-1039-1472
	```
	- 콘솔에 위와 같이 나온다면 성공!

```java
MemberVO mvo = new MemberVO();
mvo.setName(name);
mvo.setEmail(email);
mvo.setPhone(phone);

ar[i++] = mvo;
```
- 생성된 MemberVO를 배열 ar에 저장하자.

<font color="#c4bd97">if 문 탈출</font>
```java
model.addAttribute("ar", ar);

return "test";
```
- 생성한 배열을 Model에 저장하자.
### test() 메서드 전문
```java
@RequestMapping("test")
public String test(Model model) throws Exception { 
	String realPath = application.getRealPath("/resources/am/data/member.xml");
	SAXBuilder builder = new SAXBuilder();
	Document doc = builder.build(realPath);
	
	Element root = doc.getRootElement();
	
	List<Element> list = root.getChildren("member");
	MemberVO[] ar = null;
	if(list != null && list.size() > 0) {
		ar = new MemberVO[list.size()];
		
		int i = 0;
		for(Element mem : list) {
			String name = mem.getChildText("name");
			String email = mem.getChildText("email");
			String phone = mem.getChildText("phone");
			
			MemberVO mvo = new MemberVO();
			mvo.setName(name);
			mvo.setEmail(email);
			mvo.setPhone(phone);
			
			ar[i++] = mvo;
		}
	}
	model.addAttribute("ar", ar);
	
	return "test";
}
```
## 1.6. test JSP
> src > main > webapp > WEB-INF > views > JSP File

```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>mem_list</title>
<style>
	#t1{border-collapse: collapse; width: 400px;}
	#t1 caption{text-indent: -9999px; height: 0;}
	#t1 th{padding: 6px; background-color: #ddd; border: 1px solid black;}
	#t1 td{padding: 4px; border: 1px solid black;}
	#t1 .no-border{border: none;}
	#type, #value, #btn1{padding: 5px;}
	.w150{width: 150px;}
</style>
</head>
<body>
	<h1>회원 목록</h1>
	<hr/>
	<table id="t1">
		<caption>회원 목록 테이블</caption>
		<thead>
			<tr><td colspan="3" class="no-border">
				<select id="type">
					<option value="0">회원명</option>
					<option value="1">이메일</option>
					<option value="2">전화번호</option>
				</select>
				<input type="text" id="value" class="w150"/>
				<button type="button" id="btn1">검색</button>
			</td></tr>
			<tr><th>회원명</th><th>이메일</th><th>전화번호</th></tr>
		</thead>
		<tbody>
		<c:forEach items="${ar}" var="vo">
			<tr><td>${vo.name}</td><td>${vo.email}</td><td>${vo.phone}</td></tr>
		</c:forEach>
		</tbody>
	</table>
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script>
		$(function(){
			$("#t1 tbody tr").click(function(){
				viewData(this);
			});
			
			$("#btn1").click(function(){
				let type = $("#type").val();
				let value = $("#value").val();
				
				if(value.trim().length < 1){
					alert("검색어를 입력하세요.");
					$("#value").val("");
					$("#value").focus();
					return;
				}
				
				$.ajax({
					url: "edit",
					data: {"type":type, "value":value},
					type: "post",
					dataType: "json"
				}).done(function(data){
					let str = "";
					if(data.ar != null && data.ar.length > 0){
						for(let i = 0 ; i < data.ar.length ; i++){
							str += "<tr data-m_code='" + data.ar[i].m_code + "' onclick='viewData(this)'><td>";
							str += data.ar[i].m_id;
							str += "</td><td>";
							str += data.ar[i].m_name;
							str += "</td><td>";
							str += data.ar[i].m_pw;
							str += "</td></tr>";
						}
					}else{
						str = "<tr><td colspan='3'>검색 결과가 없습니다.</td></tr>";
					}
					$("#t1 tbody").html(str);
				});
			});
		});
		
		function viewData(tr){
			let tds = $(tr).children();
			alert(tds.eq(0).text() + " / " + tds.eq(1).text() + " / " + tds.eq(2).text());
		}
	</script>
</body>
</html>
```

![[Pasted image 20240115111348.png|500]]
- 위와 같이 출력된다.
# 2. 비동기 처리로 리스트 출력하기
## 2.1. test2 JSP
> src > main > webapp > WEB-INF > views > test.jsp

```javaScript
$.ajax({
	url: "test2",
	type: "post",
	dataType: "json"
}).done(function(){
	let str = "";
	if(data.ar != null && data.ar.length > 0){
		for(let i = 0 ; i < data.ar.length ; i++){
			str += "<tr onclick='viewData(this)'><td>";
			str += data.ar[i].name;
			str += "</td><td>";
			str += data.ar[i].email
			str += "</td><td>";
			str += data.ar[i].phone;
			str += "</td></tr>";
		}
	}else{
		str = "<tr><td colspan='3'>검색 결과가 없습니다.</td></tr>";
	}
	$("#t1 tbody").html(str);
});
```
- AJAX를 추가하자.
### test2 JSP 코드 전문
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>mem_list</title>
<style>
	#t1{border-collapse: collapse; width: 400px;}
	#t1 caption{text-indent: -9999px; height: 0;}
	#t1 th{padding: 6px; background-color: #ddd; border: 1px solid black;}
	#t1 td{padding: 4px; border: 1px solid black;}
	#t1 .no-border{border: none;}
	#type, #value, #btn1{padding: 5px;}
	.w150{width: 150px;}
</style>
</head>
<body>
	<h1>회원 목록</h1>
	<hr/>
	<table id="t1">
		<caption>회원 목록 테이블</caption>
		<thead>
			<tr><td colspan="3" class="no-border">
				<select id="type">
					<option value="0">회원명</option>
					<option value="1">이메일</option>
					<option value="2">전화번호</option>
				</select>
				<input type="text" id="value" class="w150"/>
				<button type="button" id="btn1">검색</button>
			</td></tr>
			<tr><th>회원명</th><th>이메일</th><th>전화번호</th></tr>
		</thead>
		<tbody>
		<c:forEach items="${ar}" var="vo">
			<tr><td>${vo.name}</td><td>${vo.email}</td><td>${vo.phone}</td></tr>
		</c:forEach>
		</tbody>
	</table>
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script>
		$(function(){
			$.ajax({
				url: "test2",
				type: "post",
				dataType: "json"
			}).done(function(){
				let str = "";
				if(data.ar != null && data.ar.length > 0){
					for(let i = 0 ; i < data.ar.length ; i++){
						str += "<tr onclick='viewData(this)'><td>";
						str += data.ar[i].name;
						str += "</td><td>";
						str += data.ar[i].email
						str += "</td><td>";
						str += data.ar[i].phone;
						str += "</td></tr>";
					}
				}else{
					str = "<tr><td colspan='3'>검색 결과가 없습니다.</td></tr>";
				}
				$("#t1 tbody").html(str);
			});
			
			$("#t1 tbody tr").click(function(){
				viewData(this);
			});
			
			$("#btn1").click(function(){
				let type = $("#type").val();
				let value = $("#value").val();
				
				if(value.trim().length < 1){
					alert("검색어를 입력하세요.");
					$("#value").val("");
					$("#value").focus();
					return;
				}
				
				$.ajax({
					url: "edit",
					data: {"type":type, "value":value},
					type: "post",
					dataType: "json"
				}).done(function(data){
					let str = "";
					if(data.ar != null && data.ar.length > 0){
						for(let i = 0 ; i < data.ar.length ; i++){
							str += "<tr data-m_code='" + data.ar[i].m_code + "' onclick='viewData(this)'><td>";
							str += data.ar[i].m_id;
							str += "</td><td>";
							str += data.ar[i].m_name;
							str += "</td><td>";
							str += data.ar[i].m_pw;
							str += "</td></tr>";
						}
					}else{
						str = "<tr><td colspan='3'>검색 결과가 없습니다.</td></tr>";
					}
					$("#t1 tbody").html(str);
				});
			});
		});
		
		function viewData(tr){
			let tds = $(tr).children();
			alert(tds.eq(0).text() + " / " + tds.eq(1).text() + " / " + tds.eq(2).text());
		}
	</script>
</body>
</html>
```
## 2.2. Controller - 메서드 추가
> src/main/java > com.ict.ex > TestController.java

```java
@RequestMapping("test2")
@ResponseBody
public Map<String, Object> test() throws Exception {
	Map<String, Object> map = new HashMap<String, Object>();
	String realPath = application.getRealPath("/resources/am/data/member.xml");
	SAXBuilder builder = new SAXBuilder();
	Document doc = builder.build(realPath);
	
	Element root = doc.getRootElement();
	
	List<Element> list = root.getChildren("member");
	MemberVO[] ar = null;
	if(list != null && list.size() > 0) {
		ar = new MemberVO[list.size()];
		
		int i = 0;
		for(Element mem : list) {
			String name = mem.getChildText("name");
			String email = mem.getChildText("email");
			String phone = mem.getChildText("phone");
			
			MemberVO mvo = new MemberVO();
			mvo.setName(name);
			mvo.setEmail(email);
			mvo.setPhone(phone);
			
			ar[i++] = mvo;
		}
	}
	
	map.put("ar", ar);
	
	return map;
}
```
- RequestMapping은 test2, ResponseBody를 설정한 test 메서드를 추가하자.

```java
@RequestMapping(value="test2", method=RequestMethod.GET)
public String test2() {
	return "test2";
}
```
- GET 방식인 경우의 메서드도 추가하자.
### Controller 코드 전문
```java
package com.ict.ex;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.servlet.ServletContext;

import org.jdom2.Document;
import org.jdom2.Element;
import org.jdom2.input.SAXBuilder;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.servlet.ModelAndView;

import data.vo.MemberVO;

@Controller
public class TestController {
	
	@Autowired
	private ServletContext application;
	
	@RequestMapping("test")
	public String test(Model model) throws Exception { 
		String realPath = application.getRealPath("/resources/am/data/member.xml");
		SAXBuilder builder = new SAXBuilder();
		Document doc = builder.build(realPath);
		
		Element root = doc.getRootElement();
		
		List<Element> list = root.getChildren("member");
		MemberVO[] ar = null;
		if(list != null && list.size() > 0) {
			ar = new MemberVO[list.size()];
			
			int i = 0;
			for(Element mem : list) {
				String name = mem.getChildText("name");
				String email = mem.getChildText("email");
				String phone = mem.getChildText("phone");
				
				MemberVO mvo = new MemberVO();
				mvo.setName(name);
				mvo.setEmail(email);
				mvo.setPhone(phone);
				
				ar[i++] = mvo;
			}
		}
		model.addAttribute("ar", ar);
		
		return "test";
	}
	
	@RequestMapping(value="test2", method=RequestMethod.GET)
	public String test2() {
		return "test2";
	}
	
	@RequestMapping("test2")
	@ResponseBody
	public Map<String, Object> test() throws Exception {
		Map<String, Object> map = new HashMap<String, Object>();
		String realPath = application.getRealPath("/resources/am/data/member.xml");
		SAXBuilder builder = new SAXBuilder();
		Document doc = builder.build(realPath);
		
		Element root = doc.getRootElement();
		
		List<Element> list = root.getChildren("member");
		MemberVO[] ar = null;
		if(list != null && list.size() > 0) {
			ar = new MemberVO[list.size()];
			
			int i = 0;
			for(Element mem : list) {
				String name = mem.getChildText("name");
				String email = mem.getChildText("email");
				String phone = mem.getChildText("phone");
				
				MemberVO mvo = new MemberVO();
				mvo.setName(name);
				mvo.setEmail(email);
				mvo.setPhone(phone);
				
				ar[i++] = mvo;
			}
		}
		
		map.put("ar", ar);
		
		return map;
	}

}
```
## 2.3. Project Object Model - Jackson Library 추가
> 0115_JDOM > pom.xml

```xml
<dependency>
	<groupId>org.codehaus.jackson</groupId>
	<artifactId>jackson-mapper-asl</artifactId>
	<version>1.9.13</version>
</dependency>
```
- JSON 처리를 위한 Jackson Library를 추가하자.
## 2.4. DispatcherServlet - Converter 추가
> Spring Elements > Beans > servlet-context.xml

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
- JSON 처리를 위한 MappingJacksonHttpMessageConverter를 추가하자.

# 3. 카카오 로그인
## 3.1. 카카오 개발자 센터 환경 설정
1. [카카오 개발자 센터](https://developers.kakao.com/) 로그인
2. 내 애플리케이션 > 애플리케이션 추가하기
	- 앱 이름: Login_ICTedu
	- 사업자명: ICT인재교육원
	- 카테고리: 교육
3. 요약 정보
	- 앱 키
		
		| 키 | 키값 |
		| :---: | :---: |
		| 네이티브 앱 키 | fb48da030a1547cc508c2f4420d5de71 |
		| REST API 키 | e789fe6fe29601ced8ba27c1ab2ac965 |
		| JavaScript 키 | 53c309554320c624008e72b0e6c5f5fc |
		| Admin 키 | 5676fb0c837e4e6c7bba0e661df1efeb |
		
	- 플랫폼
		- Web 플랫폼 등록 > <font color="#fac08f">사이트 도메인</font> : `http://localhost:8080`
		- 카카오 로그인 사용 시 Redirect URI 등록하러 가기
			- 활성화 설정 상태 ON
			- <font color="#fac08f">Redirect URI</font> 등록 : `http://localhost:8080/kakao/login`
4. 버튼 아이콘 다운로드
	- [카카오 개발자 센터](https://developers.kakao.com/) > 문서 > 카카오 로그인 > 디자인 가이드 > 
	- 카카오 로그인 버튼 리소스 다운로드 > 다운로드(.png)
	- 다운 받은 이미지를 다음<font color="#7f7f7f">(src/main/webapp/resources/img/)</font>에 저장하기
5. 내 애플리케이션 > 제품 설정 > 카카오 로그인 > 동의 항목
	- 닉네임과 프로필 사진의 설정을 필수 동의로 하자. 동의 목적은 "정보 수집"
## 3.2. 카카오 로그인의 움직임을 알아보자.
1. [카카오 개발자 센터](https://developers.kakao.com/) 로그인
2. 문서 > 카카오 로그인 > JavaScript
	![[Pasted image 20240115125607.png]]
## 3.3. home JSP - 로그인 화면 구현
> Spring Legacy Project(Spring MVC Project) > 0115_kakao > com.ict.kakao
> src > main > webapp > WEB-INF > views > home.jsp

```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page session="false" %>
<html>
<head>
	<meta charset="UTF-8">
	<title>Home</title>
</head>
<body>
	<h1>Hello world!</h1>
	<c:if test="${sessionScope.mvo eq null}">
		<form action="" method="post">
			ID:<input type="text" name="id"/><br/>
			PW:<input type="password" name="pw"/><br/>
			<input type="button" value="LOGIN"/>
		</form>
		<br/>
		<img src="resources/img/kakao_login.png"/>
	</c:if>
</body>
</html>
```
- 임의로 어떤 식으로 표현될지 만들어 보자.

![[Pasted image 20240115131529.png]]
- 이렇게 출력된다.

## 3.4. 카카오 개발자 센터 문서를 확인하자.
> [카카오 개발자 센터](https://developers.kakao.com/) > 카카오 로그인 > REST API

### 3.4.1. 인가 코드 받기 문서
1. 서비스 서버가 카카오 인증 서버로 [인가 코드 받기](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-code)를 요청합니다.
2. 카카오 인증 서버가 사용자에게 카카오계정 로그인을 통한 [인증](https://developers.kakao.com/docs/latest/ko/kakaologin/common#intro)을 요청합니다.
    - 클라이언트에 유효한 카카오계정 세션이 있거나, 카카오톡 인앱 브라우저에서의 요청인 경우 4단계로 넘어갑니다.
3. 사용자가 카카오계정으로 로그인합니다.
4. 카카오 인증 서버가 사용자에게 동의 화면을 출력하여 [인가](https://developers.kakao.com/docs/latest/ko/kakaologin/common#intro)를 위한 사용자 동의를 요청합니다.
    - 동의 화면은 서비스 애플리케이션(이하 앱)의 [동의항목 설정](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#consent-item)에 따라 구성됩니다.
5. 사용자가 필수 동의항목, 이 외 원하는 동의항목에 동의한 뒤 [동의하고 계속하기] 버튼을 누릅니다.
6. 카카오 인증 서버는 서비스 서버의 Redirect URI로 인가 코드를 전달합니다.

### 3.4.2. 인가 코드 받기
|메서드|URL|인증 방식|
|---|---|---|
|`GET`|`https://kauth.kakao.com/oauth/authorize`|-|

|권한|사전 설정|[카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/common)|[사용자 동의](https://developers.kakao.com/docs/latest/ko/kakaologin/common#authorization-consent-item)|
|---|---|---|---|
|-|[플랫폼 등록](https://developers.kakao.com/docs/latest/ko/getting-started/app#platform)  <br>[카카오 로그인 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-activate)  <br>[Redirect URI 등록](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-redirect-uri)  <br>[동의항목](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#consent-item)  <br>[OpenID Connect 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-oidc)(선택)  <br>[간편가입](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#simple-signup)(선택)|필요|필요:  <br>필수 동의항목|
### 3.4.3. 요청 쿼리 파라미터

|이름|타입|설명|필수|
|---|---|---|---|
|client_id|`String`|앱 REST API 키  <br>[내 애플리케이션] > [앱 키]에서 확인 가능|O|
|redirect_uri|`String`|인가 코드를 전달받을 서비스 서버의 URI  <br>[내 애플리케이션] > [카카오 로그인] > [Redirect URI]에서 등록|O|
|response_type|`String`|`code`로 고정|O|
### 3.4.4. 토큰 받기
|메서드|URL|인증 방식|
|---|---|---|
|`POST`|`https://kauth.kakao.com/oauth/token`|-|

|권한|사전 설정|[카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/common)|[사용자 동의](https://developers.kakao.com/docs/latest/ko/kakaologin/common#authorization-consent-item)|
|---|---|---|---|
|-|[플랫폼 등록](https://developers.kakao.com/docs/latest/ko/getting-started/app#platform)  <br>[카카오 로그인 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-activate)  <br>[Redirect URI 등록](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-redirect-uri)  <br>[동의항목](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#consent-item)  <br>[OpenID Connect 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-oidc)(선택)|필요|필요:  <br>필수 동의항목|
### 3.4.5. 토큰 받기 관련 요청 파라미터
<font color="#ddd9c3">헤더</font>

|이름|설명|필수|
|---|---|---|
|Content-type|`Content-type: application/x-www-form-urlencoded;charset=utf-8`  <br>요청 데이터 타입|O|
<font color="#ddd9c3">본문</font>

|이름|타입|설명|필수|
|---|---|---|---|
|grant_type|`String`|`authorization_code`로 고정|O|
|client_id|`String`|앱 REST API 키  <br>[내 애플리케이션] > [앱 키]에서 확인 가능|O|
|redirect_uri|`String`|인가 코드가 리다이렉트된 URI|O|
|code|`String`|인가 코드 받기 요청으로 얻은 인가 코드|O|
|client_secret|`String`|토큰 발급 시, 보안을 강화하기 위해 추가 확인하는 코드  <br>[내 애플리케이션] > [보안]에서 설정 가능  <br>ON 상태인 경우 필수 설정해야 함|X|
### 3.4.6. 사용자 정보 가져오기
|메서드|URL|인증 방식|
|---|---|---|
|`GET/POST`|`https://kapi.kakao.com/v2/user/me`|액세스 토큰  <br>서비스 앱 어드민 키|

|권한|사전 설정|[카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/common)|[사용자 동의](https://developers.kakao.com/docs/latest/ko/kakaologin/common#authorization-consent-item)|
|---|---|---|---|
|-|[플랫폼 등록](https://developers.kakao.com/docs/latest/ko/getting-started/app#platform)  <br>[카카오 로그인 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-activate)  <br>[동의항목](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#consent-item)|필요|필요:  <br>사용자 정보를 요청할 모든 동의항목|
### 3.4.7. 사용자 정보 가져오기 : 엑세스 토큰 방식
|이름|설명|필수|
|---|---|---|
|Authorization|`Authorization: Bearer ${ACCESS_TOKEN}`  <br>인증 방식, 액세스 토큰으로 인증 요청|O|
|Content-type|`Content-type: application/x-www-form-urlencoded;charset=utf-8`  <br>요청 데이터 타입|O|
## 3.5. home JSP - 인가 코드 받기
> src > main > webapp > WEB-INF > views > home.jsp

```HTML
<a href="https://kauth.kakao.com/oauth/authorize?client_id=e789fe6fe29601ced8ba27c1ab2ac965&redirect_uri=http://localhost:8080/kakao/login&response_type=code">
	<img src="resources/img/kakao_login.png"/>
</a>
```
- [[#3.4.1.1. 인가 코드 받기|인가 코드]]를 받을 URL과 [[#3.4.2. 요청 쿼리 파라미터|카카오 요청 쿼리 파라미터]]를 a 태그의 href에 추가하자.
- 해당 링크로 전송이 되면 검증을 거친 후 Controller로 오게 된다.
## 3.6. KakaoController - 카카오 서버와 통신
> src/main/java > com.ict.kakao > Java Class

```java
@Controller
public class KakaoController {
	
	@RequestMapping("/kakao/login")
	public ModelAndView login(String code) {
		ModelAndView mv = new ModelAndView();
		
	}

}
```
- 카카오 서버가 인자로 전달해준 인증코드가 code라는 변수로 넘어온다.
	- home.jsp의 a 링크로 요청한 인증 코드가 해당 URL에 파라미터로 전송되었으므로, 이 메서드를 생성과 동시에 변수로 받게 된다.
- 인증코드를 받아야 하므로 이를 위해 반환값 객체를 생성하자.

<font color="#c4bd97">login() 메서드</font>
```java
System.out.println(code);
return mv;
```
- 다음과 같이 작성하고 아래 [[#3.7. Server|server.xml의 수정 반영]] 후 버튼을 눌러 보자.

![[Pasted image 20240115150203.png|300]]
- 위와 같은 화면이 나타난다.
- 로그인을 할 경우 Console에 Code 값이 찍히는 것을 볼 수 있다.
	S7BwilZKiKhN5s_BYyYlPT_MMqQgMb_FJtSSCti4KdX6VO3_8mHnIQX6UjsKKiVSAAABjQu9g33dCc_9be4aqQ
### 3.6.1. 카카오 서버와 두 번째 통신
```java
String access_Token = "";
String refresh_Token = "";
```
- 받은 코드를 가지고 카카오 서버와 두 번째 통신을 하여 토큰을 받아야 한다.
- 카카오 서버에서 보내주 토근을 받을 수 있는 변수 access_Token과 refresh_Token을 선언하자.

```java
String reqURL = "https://kauth.kakao.com/oauth/token";
```
- 토큰을 받을 URL을 변수에 저장하자.

<font color="#ddd9c3">try-catch 구문의 try 구문</font>
```java
URL url = new URL(reqURL);
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("POST");
conn.setDoOutput(true);
```
- try-catch 구문을 만들자.
- try 구문 안에서 웹 상의 경로를 객체화 시키자.
	- java.net의 URL 객체를 생성한다.
- 웹 상의 경로와 <font color="#ff0000">(누구를)</font> 연결하자.
	- java.net의 HttpURLConnection 객체를 생성한다.
	- HttpURLConnection의 부모가 URLConnection이다. Add cast to "HttpURLConnection"을 선택하자.
- 토큰 요청은 <font color="#7f7f7f">문서에 나오길</font> POST 방식으로 요청을 해야 하므로 <font color="#fac08f">setDoOutput을 true로 지정</font>해야 한다.

```java
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
```
- 전달하고자 하는 파라미터들을 보낼 OutputStream을 만들자.

```java
StringBuffer sb = new StringBuffer();
sb.append("grant_type=authorization_code");
sb.append("&client_id=e789fe6fe29601ced8ba27c1ab2ac965");
sb.append("&redirect_uri=http://localhost:8080/kakao/login");
sb.append("&code=" + code);
```
- 파라미터 4개를 만들어 <font color="#95b3d7">BufferedWriter</font>를 통해 카카오 서버로 보내자.
- 파라미터 4개를 만들기 위해 <font color="#95b3d7">StringBuffer</font>로 먼저 만들자.

```java
bw.write(sb.toString());
bw.flush();
```
- 준비된 파라미터를 카카오 서버로 보내자.

```java
int res_code = conn.getResponseCode();
System.out.println("RES_CODE : " + res_code);
```
- <font color="#95b3d7">결과 코드가 200번</font>이면 요청에 성공한 것이다.
- 결과 코드를 확인하자.
### login() 메서드 현재까지 전문
```java
@RequestMapping("/kakao/login")
public ModelAndView login(String code) {
	ModelAndView mv = new ModelAndView();
	
	String access_Token = "";
	String refresh_Token = "";
	String reqURL = "https://kauth.kakao.com/oauth/token";
	
	try {
		URL url = new URL(reqURL);
		HttpURLConnection conn = (HttpURLConnection) url.openConnection();
		conn.setRequestMethod("POST");
		conn.setDoOutput(true);
		
		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
		
		StringBuffer sb = new StringBuffer();
		sb.append("grant_type=authorization_code");
		sb.append("&client_id=e789fe6fe29601ced8ba27c1ab2ac965");
		sb.append("&redirect_uri=http://localhost:8080/kakao/login");
		sb.append("&code=" + code);
		
		bw.write(sb.toString());
		bw.flush();
		
		int res_code = conn.getResponseCode();
		System.out.println("RES_CODE : " + res_code);
	} catch (Exception e) {
		e.printStackTrace();
	}
	
	return mv;
}
```
### 3.6.2. 카카오 서버와 세 번째 통신
```java
if(res_code == 200) {
	BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
	// 계속
}
```
- res_code가 200번인 경우 동작하도록 if 문을 만들자.
- 카카오 서버로 요청을 통해 얻은 <font color="#95b3d7">JSON 타입의 결과 메시지</font>를 읽어 오자.

```java
StringBuffer result = new StringBuffer();
String line = null;
```
- 한 줄씩 읽어 StringBuffer 객체에 적재하자.

```java
while((line = br.readLine()) != null) {
	result.append(line);
}
System.out.println(result.toString());
```
- 더 이상 읽을 내용이 없을 때까지 while 문을 수행한다.
- print 문으로 내용을 확인해 보자.
	```
	{
		"access_token":"TrJnE-596rx4_LAmIoI-ieqR2OpoNbt8578KPXUbAAABjQwRj2wWphHJzwXJqw",
		"token_type":"bearer",
		"refresh_token":"CGJmqSyvHStYshD8iEfdCaWlPMaE0E1LkR4KPXUbAAABjQwRj2kWphHJzwXJqw",
		"expires_in":21599,
		"scope":"profile_image profile_nickname",
		"refresh_token_expires_in":5183999
	}
	```
	- JSON 형태로 온 것을 확인할 수 있다.
	- Parsing을 하자.

```java
JSONParser pars = new JSONParser();
```
- <font color="#95b3d7">access_token</font>과 <font color="#95b3d7">refresh_token</font>을 <font color="#95b3d7">JSON Parsing 처리로</font> 잡아내어 <font color="#95b3d7">카카오 API 서버에 요청</font>한 후 <font color="#95b3d7">ModelAndView에 저장</font>하여 result.jsp에서 <font color="#95b3d7">결과를 표현</font>할 수 있도록 하자.
- 이를 위해 [[#3.8. JSON Parsing Library 추가하기|JSON Parsing Library 추가]]를 하자. 이 라이브러리 외에도 JSON Parsing 관련 라이브러리는 무수히 많다.
- ---
- org.json.simple.parser의 JSONParser 객체를 생성하자.
	- JSON 표현식의 값이 하나의 문자열로 되어 있는 것을 JSON 객체로 변환해주는 라이브러리이다.

```java
Object obj = pars.parse(result.toString());
```
- `result.toString()`을 JSON 객체화 하자.
- 아직 객체화 했을 뿐, JSON 객체화는 아니다.

```java
JSONObject json = (JSONObject) obj;
```
- Object 객체를 <font color="#95b3d7">JSONObject로 변환</font>하자.
- <font color="#95b3d7">Add cast to 'JSONObject'</font>를 선택하자.

```java
access_Token = (String) json.get("access_token");
refresh_Token = (String) json.get("refresh_token");
```
- access_Token, refresh_Token에 값을 할당해 주자.
- String으로 캐스팅 해야 한다.

```java
System.out.println(access_Token);
System.out.println(refresh_Token);
```
- 값이 정확하게 들어왔는지 확인 해보자.
	```
	WojrfYDukuexeszi7_QxkbHwmMJTYh0vmK4KKcleAAABjQwkyaYWphHJzwXJqw
	X3ImsHLKkGZhRf2iSfZoK6siRf8hwNCKUSQKKcleAAABjQwkyaMWphHJzwXJqw
	```
	- 값이 정확하게 들어왔다!

```java
String apiURL = "https://kapi.kakao.com/v2/user/me";
String header = "Bearer " + access_Token;
```
- 받은 토큰을 가지고 <font color="#95b3d7">사용자 정보 요청</font>을 하자. 마지막 세 번째 호출이다.
- <font color="#d99694">Bearer 뒤에 띄어쓰기</font> 존나 중요하다.

```java
URL url2 = new URL(apiURL);
```
- <font color="#95b3d7">Java 객체</font>에서 특정 <font color="#95b3d7">웹상의 경로를 호출</font>하기 위해서는 <font color="#95b3d7">먼저 URL 객체를 생성</font>해야 한다.

```java
HttpURLConnection conn2 = (HttpURLConnection) url2.openConnection();
conn2.setRequestMethod("POST");
conn2.setDoOutput(true);
```
- 연결 객체인 HttpURLConnection 객체를 생성한다.

```java
conn2.setRequestProperty("Authorization", header);
```
- 카카오 개발자 센터 문서 상에는 헤더를 지정하도록 되어 있다.
- 헤더를 지정하자.

```java
res_code = conn2.getResponseCode();
System.out.println("RES_CODE : " + res_code + " / HttpURLConnection : " + HttpURLConnection.HTTP_OK);
```
- ResponseCode를 확인하자.
- res_code와 HttpURLConnection.HTTP_OK와 비교해서 확인해 보자.
	- <font color="#95b3d7">정상적으로 연결</font>되었다면 <font color="#95b3d7">둘 다 200번</font>이 떠야 한다.

```java
if(res_code == HttpURLConnection.HTTP_OK) {
	
}
```
- 요청에 성공한 경우에 해당하는 if 문을 다음과 같이 쓰자.

```java
BufferedReader brdm = new BufferedReader(new InputStreamReader(conn2.getInputStream()));
String str = null;
StringBuffer res = new StringBuffer();
```
- 요청에 성공한 경우 카카오 서버측에서 사용자의 정보를 보냈다.
- 이것을 읽어서 필요한 정보들을 선별하자.
- String str과 StringBuffer로 정보들을 받을 준비를 하자.

```java
while((str = brdm.readLine()) != null) {
	res.append(str);
	// 계속
}
```
- while 문을 이용해 StringBuffer에 정보들을 추가하자.

```java
while((str = brdm.readLine()) != null) {
	res.append(str);
}
System.out.println("RES : " + res.toString());
```
- 카카오 서버에서 전달되는 모든 값들이 res에 누적되어있다.
- print 문으로 확인해 보자.
	```
	RES : {
		"id":3287078213,
		"connected_at":"2024-01-15T07:05:39Z",
		"properties":{
			"nickname":"주영",
			"profile_image":"http://k.kakaocdn.net/dn/DuYwZ/btrX30use8N/
				8rw7wRR2CKcU1owuV37CBk/img_640x640.jpg",
			"thumbnail_image":"http://k.kakaocdn.net/dn/DuYwZ/btrX30use8N/
				8rw7wRR2CKcU1owuV37CBk/img_110x110.jpg"},
			"kakao_account":{
				"profile_nickname_needs_agreement":false,
				"profile_image_needs_agreement":false,
				"profile":{"nickname":"주영",
				"thumbnail_image_url":"http://k.kakaocdn.net/dn/DuYwZ/
					btrX30use8N/8rw7wRR2CKcU1owuV37CBk/img_110x110.jpg",
				"profile_image_url":"http://k.kakaocdn.net/dn/DuYwZ/btrX30use8N/
					8rw7wRR2CKcU1owuV37CBk/img_640x640.jpg",
				"is_default_image":false
			}
		}
	}
	```

```java
obj = pars.parse(res.toString());
json = (JSONObject) obj;
```
- 받은 값을 JSON 객체로 변환하자.
- 위에서 이미 JSONObject 객체를 생성했으므로 그대로 사용하자.

```java
JSONObject props = (JSONObject) json.get("properties");
```
- 원하는 정보가 JSON 객체 안에 properties라는 이름으로 저장되어 있는 상태이며, 이 역시 JSON 객체이므로, JSONObject를 하나 더 얻어내야 한다.

```java
String nickName = (String) props.get("nickname");
String p_img = (String) props.get("profile_image");
String thum_img = (String) props.get("thumbnail_image");
```
- properties 안의 nickname과 profile_image, thumbnail_image 값을 얻어내자.
- 사용자의 정보들을 얻어내었다.
- 보통 이들을 <font color="#95b3d7">VO 객체에 저장</font>해둔다. [[#3.9. MemberVO|>> VO 객체 만들러 가기]]

```java
MemberVO mvo = new MemberVO();
mvo.setNickName(nickName);
mvo.setP_img(p_img);
mvo.setThum_img(thum_img);
```
- VO 객체에 정보를 담자.

HttpSession 연결하기
```java
@Autowired
private HttpSession session;
```
- 잠시 메서드 밖으로 나가 HttpSession 멤버 변수를 선언하고 Autowired로 연결하자.

```java
session.setAttribute("mvo", mvo);
```
- HttpSession 연결 후 mvo라는 이름으로 VO 객체를 저장하자.

```java
mv.setViewName("result");
```
- 넘어갈 JSP를 지정했다. [[#3.10. result JSP|>> JSP 만들러 가기]]
### KakaoController 코드 전문
```java
package com.ict.kakao;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;
import java.net.URL;

import javax.servlet.http.HttpSession;

import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import mybatis.vo.MemberVO;

@Controller
public class KakaoController {
	
	@Autowired
	private HttpSession session;
	
	@RequestMapping("/kakao/login")
	public ModelAndView login(String code) {
		ModelAndView mv = new ModelAndView();
		
		String access_Token = "";
		String refresh_Token = "";
		String reqURL = "https://kauth.kakao.com/oauth/token";
		
		try {
			URL url = new URL(reqURL);
			HttpURLConnection conn = (HttpURLConnection) url.openConnection();
			conn.setRequestMethod("POST");
			conn.setDoOutput(true);
			
			BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
			
			StringBuffer sb = new StringBuffer();
			sb.append("grant_type=authorization_code");
			sb.append("&client_id=e789fe6fe29601ced8ba27c1ab2ac965");
			sb.append("&redirect_uri=http://localhost:8080/kakao/login");
			sb.append("&code=" + code);
			
			bw.write(sb.toString());
			bw.flush();
			
			int res_code = conn.getResponseCode();
			
			if(res_code == 200) {
				BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
				StringBuffer result = new StringBuffer();
				String line = null;
				
				while((line = br.readLine()) != null) {
					result.append(line);
				}
				
				JSONParser pars = new JSONParser();
				Object obj = pars.parse(result.toString());
				JSONObject json = (JSONObject) obj;
				
				access_Token = (String) json.get("access_token");
				refresh_Token = (String) json.get("refresh_token");
				
				String apiURL = "https://kapi.kakao.com/v2/user/me";
				String header = "bearer " + access_Token;
				
				URL url2 = new URL(apiURL);
				HttpURLConnection conn2 = (HttpURLConnection) url2.openConnection();
				conn2.setRequestMethod("POST");
				conn2.setDoOutput(true);
				
				conn2.setRequestProperty("Authorization", header);
				
				res_code = conn2.getResponseCode();
				if(res_code == HttpURLConnection.HTTP_OK) {
					BufferedReader brdm = new BufferedReader(new InputStreamReader(conn2.getInputStream()));
					String str = null;
					StringBuffer res = new StringBuffer();
					
					while((str = brdm.readLine()) != null) {
						res.append(str);
					}
					
					obj = pars.parse(res.toString());
					json = (JSONObject) obj;
					
					JSONObject props = (JSONObject) json.get("properties");
					String nickName = (String) props.get("nickname");
					String p_img = (String) props.get("profile_image");
					String thum_img = (String) props.get("thumbnail_image");
					
					MemberVO mvo = new MemberVO();
					mvo.setNickName(nickName);
					mvo.setP_img(p_img);
					mvo.setThum_img(thum_img);
					
					session.setAttribute("mvo", mvo);
					mv.setViewName("result");
				}
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
		
		return mv;
	}

}
```
## 3.7. Server
> Servers > Tomcat v9.0 Server at locatlhost-config > server.xml

```xml
<Context docBase="0115_kakao" path="/" reloadable="true" source="org.eclipse.jst.jee.server:0115_kakao"/>
```
- 위와 같이 path를 수정하자.
## 3.8. JSON Parsing Library 추가하기
> [MVNRepository](https://MVNRepository.com) > search: JSON.simple > 1.1.1 > Maven
> -> 0115_kakao > pom.xml

```xml
<dependency>
    <groupId>com.googlecode.json-simple</groupId>
    <artifactId>json-simple</artifactId>
    <version>1.1.1</version>
</dependency>
```
## 3.9. MemberVO - 객체 생성
> src/main/java > mybatis.vo > Java Class

```java
package mybatis.vo;

public class MemberVO {
	
	private String nickName, p_img, thum_img;
	private String email, phone, addr;
	
	public String getNickName() {
		return nickName;
	}
	public void setNickName(String nickName) {
		this.nickName = nickName;
	}
	public String getP_img() {
		return p_img;
	}
	public void setP_img(String p_img) {
		this.p_img = p_img;
	}
	public String getThum_img() {
		return thum_img;
	}
	public void setThum_img(String thum_img) {
		this.thum_img = thum_img;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getPhone() {
		return phone;
	}
	public void setPhone(String phone) {
		this.phone = phone;
	}
	public String getAddr() {
		return addr;
	}
	public void setAddr(String addr) {
		this.addr = addr;
	}

}
```
- nickName, p_img, thum_img 외에도 필요한 항목들을 만들 수 있다.
## 3.10. result JSP - 결과 확인
> src > main > webapp > WEB-INF > views > JSP File

```html
<div>
	<header>
		<h1>회원정보</h1>
	</header>
	<article>
		<h2>${sessionScope.mvo.nickName}</h2>
		<img src="${mvo.p_img}"/>
		<a href="/">HOME</a>
	</article>
</div>
```
## 3.11. 결과 확인
![[Pasted image 20240115181605.png|200]]
- 로그인하면 result.jsp로 이동하며, 위와 같이 출력된다.