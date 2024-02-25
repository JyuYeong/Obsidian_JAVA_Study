# 1. 파라미터 전달 복습
## `document.forms[0].submit();`
- 현재 문서의 첫 번째 폼을 제출한다.
- `submit()`은 함수이다.
- 폼 태그의 action 속성에 지정한 URL 매핑 경로에, method 속성에 지정한 방식으로 제출하게 된다.
## `doPost()` 안에 `doGet()`이 있는 이유
- post 방식이든 get 방식이든 구현의 방식이 같을 때 한 곳에서 처리하기 위함이다.
- 두 함수는 tomcat이 호출한다.
## Tomcat
- 모든 요청과 응답을 스레드로 처리한다.
### WAS, Tomcat, Servlet의 역할과 차이
- WAS<font color="#7f7f7f">(Web Application Server)</font>
	- 웹 애플리케이션을 실행하기 위한 <font color="#fac08f">환경을 제공</font>하는 소프트웨어
	- 클라이언트의 <font color="#92cddc">요청을 받아들이고</font>, 동적인 콘텐츠를 생성 후, 클라이언트에게 <font color="#92cddc">전달하는 역할</font>을 수행한다.
	- 보통 웹 서버와 연결되어 정적인 콘텐츠를 처리하고, 동적인 콘텐츠를 처리하기 위한 여러 서비스를 제공한다.
		- Servlet 컨테이너
		- JSP(JavaServerPages) 컨테이너
		- 데이터베이스 연동
		- 트랜잭션 관리
	- 대표적인 WAS : Apache Tomcat, IBM WebSphere, Oracle WebLogic
- Tomcat<font color="#7f7f7f">(Apache Tomcat)</font>
	- WAS의 한 종류
	- Java Servlet과 JavaServer Pages 기술을 구현하는데 사용되는 오픈 소스 소프트웨어
	- 경량화 되어 있고, 주로 개발 및 테스트 목적으로 사용한다.
	- <font color="#fac08f">Servlet 및 JSP 컨테이너 역할</font>
	- 정적인 웹 페이지와 동적인 웹 애플리케이션을 실행할 수 있다.
- Servlet
	- <font color="#92cddc">Java 언어로 작성된 서버측의 프로그램</font>
	- 동적인 웹 페이지를 생성하고, 클라이언트 요청에 대한 동적인 처리를 수행하는 데 사용된다.
	- 클라이언트의 HTTP 요청을 처리하고, 데이터베이스와의 상호작용, 세션 관리, 사용자의 입력 검증 등의 작업을 수행할 수 있다.
	- 주로 웹 애플리케이션의 백엔드 로직을 담당
	- Tomcat과 같은 Servlet 컨테이너에서 실행된다.
## goGet() 내 응답 수행 관련 반복문 부분 다른 풀이
```java
out.println("<ol>");
for(int i = 0 ; i < name.length ; i++){
	if(name[i].trim().length() > 0){
		out.println("<li>" + name[i] + "</li>");
	}
}
out.println("</ol>");
out.close();
```
- `<ol>`, `<li>` 태그를 이용하여 넘버링을 할 수 있다.

# 2. 연습문제
## 결과물
![[Pasted image 20231205130417.png|300]]
![[Pasted image 20231205130452.png|150]]
## 강사님 풀이
```HTML
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<div id="wrap">
		<header>
			<h1>가입 폼</h1>
		</header>
		<article>
			<form action="ex2" method="post">
				<table>
					<caption>가입테이블</caption>
					<colgroup>
						<col width="70px"/>
						<col width="*"/>
					</colgroup>
					<tbody>
						<tr>
							<td><label for="s_id">ID:</label></td>
							<td>
								<input type="text" id="s_id"
									name="u_id" data-msg="아이디"/>
							</td>
						</tr>
						<tr>
							<td><label for="s_pw">PW:</label></td>
							<td>
								<input type="password" id="s_pw"
									name="u_pw" data-msg="비밀번호"/>
							</td>
						</tr>
						<tr>
							<td><label for="s_name">NAME:</label></td>
							<td>
								<input type="text" id="s_name"
									name="u_name" data-msg="이름"/>
							</td>
						</tr>
						<tr>
							<td><label for="s_email">EMAIL:</label></td>
							<td>
								<input type="text" id="s_email"
									name="u_email"/>
								<label for="s_email2">@</label>
								<input type="text" id="s_email2"
									name="u_email"/>
								<select id="sel_email">
									<option>직접입력</option>
									<option value="gmail.com">구글</option>
									<option value="daum.net">다음</option>
									<option value="naver.com">네이버</option>
								</select>
							</td>
						</tr>
						<tr>
							<td><label for="s_hobby">HOBBY:</label></td>
							<td>
								<input type="checkbox" id="s_hobby"
									name="u_hobby" value="독서"/>
								<label for="s_hobby">독서</label>
								&nbsp;&nbsp;
								<input type="checkbox" id="s_hobby2"
									name="u_hobby" value="음악감상"/>
								<label for="s_hobby2">음악감상</label>
								&nbsp;&nbsp;
								<input type="checkbox" id="s_hobby3"
									name="u_hobby" value="영화감상"/>
								<label for="s_hobby3">영화감상</label>
								&nbsp;&nbsp;
								<input type="checkbox" id="s_hobby4"
									name="u_hobby" value="게임"/>
								<label for="s_hobby4">게임</label>
								&nbsp;&nbsp;
								<input type="checkbox" id="s_hobby5"
									name="u_hobby" value="볼링"/>
								<label for="s_hobby5">볼링</label>
								&nbsp;&nbsp;
							</td>
						</tr>
						<tr>
							<td colspan="2">
								<button type="button" 
									id="btn">보내기</button>
							</td>
						</tr>
					</tbody>
				</table>
			</form>
		</article>
	</div>
	
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script>
		$(function(){
			
			$("#btn").click(function(){
				// 보내기 버튼을 클릭할 때...
				let ar = document.forms[0].elements;
				
				for(let i=0; i<ar.length-1;i++){
					if(i >= 3)
						break;
					if(ar[i].value.trim().length < 1){
						// 입력하지 않은 경우
						alert(ar[i].dataset.msg+"을 입력하세요");
						ar[i].value = "";//청소
						ar[i].focus();
						return;
					}
				}//for의 끝
				document.forms[0].submit();
			});
			
			$("#sel_email").change(function(){
				// 이메일 선택을 변경할 때...
				let thisVal = $(this).val();
				let thisText = $("#sel_email option:selected").text();
				
				//console.log(thisVal+"/"+thisText);
				$("#s_email2").val(thisVal);
			});
		});	
	</script>
</body>
</html>
```

```java
package am;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class Ex01_test
 */
@WebServlet("/ex2")
public class Ex01_test extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public Ex01_test() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setContentType("text/html;charset=utf-8");
		request.setCharacterEncoding("utf-8");
		String u_id = request.getParameter("u_id");
		String u_pw = request.getParameter("u_pw");
		String u_name = request.getParameter("u_name");
		String[] u_mail = request.getParameterValues("u_mail");
		String[] u_hobby = request.getParameterValues("u_hobby");
		PrintWriter out = response.getWriter();
		
		out.println("<h2>받은 값</h2>");
		out.println("<hr/>");
		out.println("<p>아이디 : " + u_id + "</p>");
		out.println("<p>비밀번호 : " + u_pw + "</p>");
		out.println("<p>이름 : " + u_name + "</p>");
		out.println("<p>이메일 : " + u_mail[0] + "@" + u_mail[1] + "</p>");
		out.println("<ul>");
		for(int i = 0 ; i < u_hobby.length ; i++){
			out.println("<li>" + u_hobby[i] + "</li>");
		}
		out.println("</ul>");
		out.close();
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doGet();
	}

}
```

# 3. DB 연동
## DB 연동 세팅
1. MyBatis, MySQL Connector jar 파일의 설정은 "src/main/webapp/WEB-INF/lib"에 저장한다.
2. VO 객체 생성
3. mapper 생성<font color="#7f7f7f">(SQL 문장)</font>
4. 환경 설정 파일 config.xml
	- Servlet에서 이 파일을 로드한다.
	- Servlet에서 factory를 만든 후 SQL session을 만들어 낸다.
## DB 연동 실습
- jar 파일 배치
	- mybatis의 활용을 위해 mybatis jar-file과 mysql-connector jar-file 파일을 "webapp/WEB-INF/lib"에 저장해야 한다.
- Class 생성
	- vo.EmpVO 생성
	- MySQL에서 가져오려는 컬럼과 동일한 이름의 멤버 변수 선언
	- Getters and Setters 생성
- mapper 생성
	- MyBatis 참고
- config 생성
	- MyBatis 참고
	- environments default = ""
	- environment id = ""
	- property-driver : com.mysql.cj.jdbc.Driver
	- property-url : jdbc:mysql://localhost:3306/test_db
	- property-username : test_admin
	- property-password : 1111
- Servlet 생성
	- 환경 설정 파일을 연결하는 스트림을 생성한다.
	- doGet() 함수 안에 작성
		```java
		Reader r = Resources.getResourceAsReader("pm/config/config.xml");
		SqlSessionFactory factory = new SqlSessionFactoryuilder().build(r);
		r.close();
		SqlSessin ss = factory.openSession();
		List<EmpVO> list = ss.selectList("emp.all");
		StringBuffer sb = new StringBuffer();
		
		sb.append("<ol>");
		for(EmpVO vo : list) {
			sb.append("<li>");
			sb.append(vo.getEmpno());
			sb.append(" / ");
			sb.append(vo.getEname());
			sb.append(" / ");
			sb.append(vo.getJob());
			sb.append(" / ");
			sb.append(vo.getDeptno());
			sb.append("</li>");
		}
		sb.append("</ol>");
		```
	- 이어서 기존의 나머지 내용 작성
		```java
		response.setContentType("text/html;charset=utf-8");
		PrintWriter out = response.getWriter();
		out.println("<h1>사원목록</h1>");
		out.println(sb.toString());
		out.close();
		```
		- `sb.toString()`
			- sb에 저장한 내용들을 모두 출력한다.
## DB 연동 실습 2 - 검색
- HTML
- Servlet
	- doGet() 함수 안에 작성
		```java
		response.setContentType("text/html;charset=utf-8");
		String deptno = request.getParameter("deptno");
		
		Reader r = Resources.getResourceAsReader("pm/config/config.xml");
		SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(r);
		r.close();
		SqlSession ss = factory.openSession();
		
		List<EmpVO> list = ss.selectList("emp.search_dept", deptno);
		
		StringBuffer sb = new StringBuffer();
		
		sb.append("<ol>");
		for(EmpVO vo : list) {
			sb.append("<li>");
			sb.append(vo.getEmpno());
			sb.append(" / ");
			sb.append(vo.getEname());
			sb.append(" / ");
			sb.append(vo.getJob());
			sb.append(" / ");
			sb.append(vo.getDeptno());
			sb.append("</li>");
		}
		sb.append("</ol>");
		
		PrintWriter out = response.getWriter();
		out.println("<h1>부서 검색 결과</h1>");
		out.println(sb.toString());
		out.close();
		```
- mapper xml
	- select 태그 추가
		```xml
		<select id="search_dept" parameterType="String" resultType="pm.vo.EmpVO">
		select * from emp where deptno = #{deptno}
		</select>
		```

# 연습문제
## HTML - ex04_searchForm.html
```HTML
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<div id="wrap">
		<header>
			<h1>동적 쿼리</h1>
		</header>
		<article>
			<form action="search" method="post">
				<select id="searchType" name="searchType">
					<option value="0">사번</option>
					<option value="1">이름</option>
					<option value="2">직종</option>
					<option value="3">부서</option>
				</select>
				<input type="text" id="searchValue" name="searchValue"/>
				<button type="button" id="btn" onclick="sendData()">검색</button>
			</form>
		</article>
	</div>

	<script>
		function sendData(){
			let value = document.getElementById("searchValue").value;
			if(value.trim().length < 1){
				alert("검색할 내용을 입력하세요.");
				document.getElementById("searchValue").value = "";
				document.getElementById("searchValue").focus();
				return;
			}
			document.forms[0].submit();
		}
	</script>
</body>
</html>
```
## Servlet - Ex3_Search.java
```java
package pm;

import java.io.IOException;
import java.io.PrintWriter;
import java.io.Reader;
import java.util.HashMap;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import pm.vo.EmpVO;

/**
 * Servlet implementation class Ex3_Search
 */
@WebServlet("/search")
public class Ex3_Search extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public Ex3_Search() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setContentType("text/html;charset=utf-8");
		request.setCharacterEncoding("utf-8");
		
		String type = request.getParameter("searchType");
		String value = request.getParameter("searchValue");
		
		HashMap<String, String> map = new HashMap<>();
		map.put("searchType", type);
		map.put("searchValue", value);
		
		Reader r = Resources.getResourceAsReader("pm/config/config.xml");
		SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(r);
		r.close();
		
		SqlSession ss = factory.openSession();
		List<EmpVO> list = ss.selectList("emp.search", map);
		StringBuffer sb = new StringBuffer();
		
		sb.append("<ol>");
		for(EmpVO vo : list) {
			sb.append("<li>");
			sb.append(vo.getEmpno());
			sb.append(" / ");
			sb.append(vo.getEname());
			sb.append(" / ");
			sb.append(vo.getJob());
			sb.append(" / ");
			sb.append(vo.getDeptno());
			sb.append("</li>");
		}
		sb.append("</ol>");
		
		PrintWriter out = response.getWriter();
		out.println("<h1>부서 검색 결과</h1>");
		out.println(sb.toString());
		out.close();
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}
```
## mapper - emp.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="emp">
  <select id="all" resultType="pm.vo.EmpVO">
    select * from emp
  </select>
  <select id="search_dept" parameterType="String" resultType="pm.vo.EmpVO">
    select * from emp where deptno = #{deptno}
  </select>
  <select id="search_all" parameterType="pm.vo.SearchIndexVO" resultType="pm.vo.EmpVO">
    select * from emp where #{kind} like concat('%', #{item}, '%')
  </select>
  <select id="search" parameterType="Map" resultType="pm.vo.EmpVO">
  	select * from emp
  	<trim prefix="where">
  		<choose>
  			<when test="searchType != null and searchType == 0">
  				empno = #{searchValue}
  			</when>
  			<when test="searchType != null and searchType == 1">
  				ename like concat('%', #{searchValue}, '%')
  			</when>
  			<when test="searchType != null and searchType == 2">
  				job like concat('%', #{searchValue}, '%')
  			</when>
  			<when test="searchType != null and searchType == 3">
  				deptno = #{searchValue}
  			</when>
  		</choose>
  	</trim>
  </select>
</mapper>
```
## 유효성 검사
```HTML
<script>
	function sendData(){
		let value = document.getElementById("searchValue").value;
		if(value.trim().length < 1){
			alert("검색할 내용을 입력하세요.");
			document.getElementById("searchValue").value = "";
			document.getElementById("searchValue").focus();
			return;
		}
		document.forms[0].submit();
	}
</script>
```
- if 문 : value의 좌우 공백 제거 후의 길이가 1보다 작다면 수행한다.
	- alert : 경고문 출력
	- 해당 value를 공백으로 변경
	- 해당 value에 커서 놓기
	- function 종료