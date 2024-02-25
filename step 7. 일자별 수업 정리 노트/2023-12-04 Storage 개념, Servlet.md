# file API

```HTML
<body>
	<div id="wrap">
		<header>
			<h1>HTML5 File API</h1>
		</header>
		<section id="enc">
			<input type="file" id="s_file"/>
			<select id="ss">
				<option>UTF-8</option>
				<option>8859_1</option>
			</select>
			<button type="button" id="read_btn" onclick="readFile()">읽기</button>
			<div>
				<span id="fileName">File name</span>
				<span id="fileSize">File size</span>
			</div>
			<textarea id="content" readonly></textarea>
		</section>
	</div>
</body>
```
## 사용자가 선택한 파일 가져오기
```javascript
let file = document.getElementById("s_file").files; // 파일 가져오기
let file = document.getElementById("s_file").files[0]; // 여러 개 중 첫 번째
```
- input tag
	- type=file
		- 파일 선택기
		- 옵션에 따라 여러 개의 파일 선택이 가능하다.
			- 따라서 파일이 하나만 선택되어도 배열로 반환한다.

## 파일 내용 불러오기
```javascript
let reader = new FileReader();
```
- 선택된 파일로부터 자원을 읽기 위해 스트림을 생성한다.

```javascript
reader.onload = function(){};
```
- reader로부터 자원 읽기를 수행하고 그것이 끝나면(이벤트) 특정 함수를 수행하도록 지정한다.

```javascript
let ta = document.getElementById("content");
ta.innerText = reader.result;
```
- 현재 문서에서 id가 content인 요소를 검색한다.
- 읽기 결과를 해당 요소에 표현한다.

## reader 오류 감지 이벤트 (예외 처리)
```javascript
reader.onerror = function(err){
	console.log(err.target.error.code);
}
```

## 드롭다운에서 각 옵션 관리하기
### 옵션값 얻기
```javascript
let ss = document.getElementById("ss");
let encoding = ss.options[ss.selectedIndex].value; // 아래와 같다.
let encoding = ss.value;
```
### 읽어야 할 파일 지정하기
```javascript
reader.readAsText(file, encoding);
```

## 스크립트 코드 전문
```javascript
function readFile() {
	let file = document.getElementById("s_file").files[0];
	document.getElementById("fileName").innerText = file.name;
	document.getElementById("fileSize").innerText = "(" + file.size + ")";
	
	let reader = new FileReader();
	reader.onload = function(){
		let ta = document.getElementById("content");
		ta.innerHTML = reader.result;
	};
	
	reader.onerror = function(err){
		console.log(err.target.error.code);
	}
	
	let ss = document.getElementById("ss");
	let encoding = ss.value;
	reader.readAsText(file, encoding);
}
```

# local storage
## 최종 결과물
![[Pasted image 20231204120745.png|200]]
- 새 창을 열어서 확인해도 저장된 정보가 유지된다.
- clear storage를 클릭해도 전체 창에 대해 적용된다.
- 브라우저가 달라지면 적용되지 않는다.
## HTML 부분
```HTML
<body>
	<div id="content">
		<header>
			<h1>Local Storage</h1>
		</header>
		<div id="form">
			<form id="travelForm">
				<table class="form">
					<tbody>
						<tr>
							<td class="label">Traveler</td>
							<td class="label">
								<input type="text" name="traveler" id="traveler"/>
							</td>
						</tr>
						<tr>
							<td class="label">Destination</td>
							<td class="label">
								<input type="text" name="destination" id="destination"/>
							</td>
						</tr>
						<tr>
							<td class="label">Transportation</td>
							<td class="label">
								<input type="text" name="transportation" id="transportation"/>
							</td>
						</tr>
					</tbody>
					<tfoot>
						<tr>
							<td colspan="2" class="button">
								<button type="button" onclick="addData()">Save</button>
								<button type="button" onclick="clearStorage()">Clear Storage</button>
							</td>
						</tr>
					</tfoot>
				</table>
			</form>
		</div>
		<div id="results">
		
		</div>
	</div>
	
</body>
```

## javaScript 부분
### 참고 사항
- window 객체
	- 브라우저의 최상위 객체, 전역 객체라고 부른다.
	- 창을 제어하는 다양한 메서드를 제공한다.
### 코드 설명
```javascript
let db = getStorage();
let result;
let res;
```
- window가 가지고 있는 localStorage 저장소를 받는 변수를 생성한다.
	- `getStorage()`라는 함수에서 반환할 것.
- 결과값을 받을 변수 선언
- id=results인 div 요소를 받을 변수 선언

```javascript
window.onload = function(){
	res = document.getElementById("results");
	init();
}
```
- 현재 창이 열리고 로드될 때 실행되는 함수 생성
- 현재 문서 안에 `id="results"`인 요소 검색
- `init()` 함수 실행

```javascript
function init(){
	result = "";
			
	for(let i = 0 ; i < db.length ; i++){
		let key = db.key(i);
		let value = db.getItem(i);
		result += key + " : " + value + "<br/>";
	}
}
```
- 저장소에 저장된 값이 있을 때 `id="results"`인 div에 표현하는 함수이다.
- `for()`는 저장소에 저장된 key와 value를 얻어 result에 적재한다.
	- 저장된 값들은 key와 value가 함께 저장된다. `key()`, `getItem()`
- `id="results"`인 div에 result를 HTML로 표현한다.

```javascript
function getStorage(){
	try{
		if(window.localStorage) return window.localStorage;
	}catch(e){
		return undefined;
	}
}
```
- window.localStorage는 불린형이다.
	- 사용할 수 있는 local storage가 있으면 true, 없으면 false.
- undefined는 자바의 null과 같다. 즉 `return undefined;`는 "값이 없음"을 반환한다는 의미이다.

```javascript
function addData(){
	let v1 = document.getElementById("traveler").value;
	let v2 = document.getElementById("destination").value;
	let v3 = document.getElementById("transportation").value;
	
	db.setItem("transportation", v3);
	db.setItem("destination", v2);
	db.setItem("traveler", v1);
	
	init();
}
```
- `v1`, `v2`, `v3`
	- 사용자가 입력한 traveler, destination, transportation 정보를 가져온다.
- 가져온 값들에 대한 유효성 검사를 해야 한다. <font color="#7f7f7f">(여기선 패스)</font>
- `db.setItem`
	- 키와 함께 저장소에 저장한다.

```javascript
function clearStorage(){
	db.clear();
}
```
- 변수 db에 저장된 내용을 삭제한다.

# Session Storage
- 코드의 내용은 local storage와 `window.localStorage`, `window.sessionStorage`만 다르다.
- session storage는 새 창으로 열었을 때 기존 창에서 저장한 내용이 보이지 않는다. 즉 session에서만 저장된다.

# JSP Servlet 세팅과 기초
## 세팅
- 폴더 생성
	- My_Study > JSP_Study(생성) > work(생성)
	- My_Study > JSP_Study(생성) > util(생성)
- Eclipse 세팅
	- File > Switch Workspace > Other...> Add an exisint or create a new workspace
	- JSP_Study > work 폴더로 설정 > Launch
	- Window > Preferences > Web > JSP Files > Encoding : ISO 10646/Unicode(UTF-8)
		- HTML Files, CSS Files 동일 작업 수행
	- (Browser 세팅 후)
		- Server > Runtime Environments
		- Add... > Apache > Apache Tomcat v9.0 > Next
			- (받은 파일이 없다면) Download and Install
			- Browse... > apache-tomcat-9.0.83 폴더 선택 > Finish
	- Apply and Close
- Browser 세팅
	- [APACHE](https://apache.org) 접속 > APACHE PROJECT LIST > Tomcat 선택
	- Download > Tomcat 9 > 9.0.83 > Binary Distributions > Core > zip 선택
	- 다운로드 위치 > JSP_Study > 압축 해제
## 실행
- Ctrl+N > Dynamic Web project
	- Project name
	- Target runtime : Apache Tomcat v9.0 확인
	- Dynamic web module version : 4.0 확인
- Ctrl+N > Servlet
	- Java Resources > src > main > java 폴더에 만들어져야 함.
	- java package, class name 세팅
	- superclass : javax.servlet.http.HttpServlet 확인
- Next > URL mappings 확인
	- URL : 웹 경로
- Next
	- Constructors from superclass 체크 확인
	- Inherited abstract methods 체크 확인
	- doGet, doPost 체크 확인
		- 처음만 init, destroy, service 알아보려고 한 것..
- Finish
- 초기 화면
	```java
	package am;
	
	import java.io.IOException;
	import javax.servlet.ServletConfig;
	import javax.servlet.ServletException;
	import javax.servlet.annotation.WebServlet;
	import javax.servlet.http.HttpServlet;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	
	/**
	 * Servlet implementation class Ex1_Servlet
	 */
	@WebServlet("/Ex1_Servlet")
	public class Ex1_Servlet extends HttpServlet {
		private static final long serialVersionUID = 1L;
	       
	    /**
	     * @see HttpServlet#HttpServlet()
	     */
	    public Ex1_Servlet() {
	        super();
	        // TODO Auto-generated constructor stub
	    }
	
		/**
		 * @see Servlet#init(ServletConfig)
		 */
		public void init(ServletConfig config) throws ServletException {
			// TODO Auto-generated constructor stub
		}
	
		/**
		 * @see Servlet#destroy()
		 */
		public void destroy() {
			// TODO Auto-generated method stub
		}
	
		/**
		 * @see HttpServlet#service(HttpServletRequest request, HttpServletResponse response)
		 */
		protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			// TODO Auto-generated method stub
		}
	
		/**
		 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
		 */
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			// TODO Auto-generated method stub
			response.getWriter().append("Served at: ").append(request.getContextPath());
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
## 기초
- Run on Server(Alt+Shift+X, R) > Tomcat v9.0 선택 > 우측으로 프로젝트가 추가되어 있는지 확인 > Finish
- web 서버 환경
	![[JSP01.png|400]]
- 생명주기
	- init -> service -> doGet or doPost -> service -> ... -> destroy
		![[JSP02.png|400]]
	- init는 서버를 호출하는 첫 번째 호출자에 의해 1회만 수행한다.
		- 두 번째부터는 다른 호출자가 요청하더라도 service부터 수행한다.
	- destroy는 서버를 종료하는 경우에만 1회 수행한다.
- 파라미터 전달
## service 함수
- request 요청
	- `request.getMethod()`
		- `System.out.println("service() : " + request.getMethod());`
			- 실행 시 `service() : GET`이라고 출력된다.
				- 요청 방식이 GET이라는 의미이다.
				- 요청 방식에 따라 해당하는 메서드를 호출해야 한다.
- response 응답

# Servlet
## doGet
### 응답을 위한 한글 처리하기
```java
response.setContentType("text/html;charset=utf-8");
```
- <font color="#d99694">주의!</font> `text/html;`에서 마지막은 세미콜론임.
- 한글을 입력할 수 있게 됨.
- 응답을 위한 스트림을 얻기 전에 해야 한다.
![[2023-12-04 Storage 개념, Servlet#^3c7bb6]]
### 응답하기 위한 스트림 생성
```java
PrintWriter out = response.getWriter();
```
### 응답하기
```java
out.println("<1>Hello Servlet</h1>");
out.println("<h2>서블릿 예제입니다.</h2>");
```
### 스트림 닫기
```java
out.close();
```
### 브라우저 확인
- URL에 `http://localhost:8080/Ex_1204/Ex2_Servlet`와 같이 나온다면 GET 방식이다.
## 값을 주면서 응답 요청하기?
- 응답을 위한 한글 처리하기
- 응답하기 위한 스트림 생성
- 파라미터 받기
- 응답하기
- 스트림 닫기
### parameter 받기
- 요청<font color="#7f7f7f">(request)</font>한 곳으로부터 인자 받기
	- msg라는 이름<font color="#7f7f7f">(name)</font>으로 전달되는 인자
- <font color="#d99694">파라미터는 기본적으로 문자열로 넘어온다.</font>
	- 따라서 자료형을 String으로 선언하자.

```java
String msg = request.getParameter("msg");
```
### 결과
- `null입니다.`가 출력된다.
- URL : `http://localhost:8080/Ex_1204/Ex3_Servlet` 뒤에
	- `?msg=Test`를 입력하면 화면이 `Test입니다.`로 바뀐다.
### 물음표(`?`) 뒤는 파라미터이다.
- GET 방식이다.
- 한글 입력 시 깨진다. 추가 설정 필요


# 로그인 페이지 만들기
## HTML
```HTML
<body>
	<div id="wrap">
		<header>
			<h1>파라미터 전달 연습</h1>
		</header>
		<article>
			<form action="" method="post">
				<table>
					<caption>로그인 테이블</caption>
					<colgroup>
						<col width="50px"/>
						<col width="*"/>
					</colgroup>
					<tbody>
						<tr>
							<td><label for="s_id">ID:</label></td>
							<td><input type="text" id="s_id" name="u_id"/></td>
						</tr>
						<tr>
							<td><label for="s_pw">PW:</label></td>
							<td><input type="password" id="s_pw" name="u_pw"/></td>
						</tr>
						<tr>
							<td colspan="2">
								<button type="button" id="login_btn">로그인</button>
							</td>
						</tr>
					</tbody>
				</table>
			</form>
		</article>
	</div>
</body>
```
## javaScript + JQuery
```javascript
$(function(){
	$("#login_btn").bind("click", function(){
		document.forms[0].submit();
	});
});
```
- 사용자가 아이디와 비밀번호를 올바르게 입력하였는지 확인하는 유효성 검사가 <font color="#7f7f7f">(원래는)</font> 필요하다.
- 현재 문서에 있는 첫 번째 폼을 action에 지정된 서버 URL로 보내는 문장이다.
- 현재까지 작성 후 run on server 동작 시 임의로 id, pw를 채운 후 버튼을 누르면 다시 로드되며 입력된 내용이 지워진다.
- <font color="#fac08f">HTML에서 form 태그의 action 값과 servlet의 `@WebServlet("/여기")`가 동일해야 한다.</font>
	- `@WebServlet()` : 해당 survlet을 호출하는 웹 상의 경로<font color="#7f7f7f">(URL)</font>를 지정하는 곳이다.

## Survlet
```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	response.setContentType("text/html;charset=utf-8");
	PrintWriter out = response.getWriter();
	String id = request.getParameter("u_id");
	String pw = request.getParameter("u_pw");
	out.println("<h2>받은 ID : " + id + "</h2>");
	out.println("<h2>받은 PW : " + pw + "</h2>");
}
```
![[2023-12-04 Storage 개념, Servlet#응답을 위한 한글 처리하기]]![[2023-12-04 Storage 개념, Servlet#응답하기 위한 스트림 생성]]
![[2023-12-04 Storage 개념, Servlet#parameter 받기]]

![[2023-12-04 Storage 개념, Servlet#응답하기]]
![[2023-12-04 Storage 개념, Servlet#스트림 닫기]]
- 주의! 입력한 ID, PW, Name 등에 한글을 입력하면 깨진다. ^3c7bb6
	- response에 대한 한글처리를 했을 뿐 request에 대한 한글처리를 안 했기 때문이다.
	- 아래를 추가하자.
		```java
		request.setCharacterEncoding("utf-8");
		```

## 유효성 검사
### 잘 못한 예
```javascript
$(function(){
	$("#login_btn").bind("click", function(){
		let id = $("#s_id").val();
		if(id.trim().length < 1){
			alert("아이디를 입력하세요.");
			$("#s_id").val(""); // 청소
			$("#s_id").focus(); // 커서 놓기
			return;
		}
		let pw = $("#s_pw").val();
		if(pw.trim().length < 1){
			alert("비밀번호를 입력하세요.");
			$("#s_pw").val(""); // 청소
			$("#s_pw").focus(); // 커서 놓기
			return;
		}
		document.forms[0].submit();
	});
});
```
- 모든 상황에 대해 모두 입력해야 한다.
### 잘한 예
```javascript
$(function(){
	$("#login_btn").bind("click", function(){
		let ar = document.forms[0].elements;
		for(let i = 0 ; i < ar.length - 1 ; i++){
			if(ar[i].value.trim().length < 1){
				alert(ar[i].id + "를 입력하세요.");
				$(ar[i]).val("");
				ar[i].focus();
				return;
			}
		}
		document.forms[0].submit();
	});
});
```
- 폼 객체 하위 요소들을 모두 가져온다. (`let ar`)
	- table은 해당 사항이 없다.
	- input, button, select, ... 이런 것만 해당함.
- 반복문을 사용하자.
	- 예제에서 마지막 요소는 버튼이므로 (필요 없으니까) `ar.length - 1`
- 정상적으로 동작하지만 `s_id를 입력하세요.`와 같이 출력되는 문제가 있다.
### 데이터셋 dataset
```HTML
<tbody>
	<tr>
		<td><label for="s_id">ID:</label></td>
		<td><input type="text" id="s_id" name="u_id" data-str="아이디"/></td>
	</tr>
	<tr>
		<td><label for="s_pw">PW:</label></td>
		<td><input type="password" id="s_pw" name="u_pw" data-str="비밀번호"/></td>
	</tr>
```
- `data-str`을 입력하자.
	- 스크립트 태그에서 `let str = ar[i].dataset.str;`을 입력한다.
		- HTML5에서 확장된 속성이다.
	- `data-`는 `dataset`을 의미한다.
- 이제 `아이디를 입력하세요.`와 같은 문구를 출력할 수 있다.
# 체크박스
- 전달되는 파라미터 중 이름이 같은 것이 <font color="#fac08f">여러 개 있을 때는 배열로 전달</font>된다.
	- 한 개만 받더라도 배열로 전달된다.
	- servlet에서 배열을 받을 때에는
		- `String[] hobby = request.getParameterValues("hobby")`
		- `getParameter`가 아닌 `getParameterValues`를 사용한다.