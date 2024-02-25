- 비동기식 통신
	- 파일 보내기
	- 에디터 활용하기
- 게시판 완성
# 1. 비동기식 통신 - 에디터
- Spring Legacy Project
	- Project name: 0105_editor
	- Spring MVC Project
	- com.ict.editor
## 1.1. pom.xml
- `<dependencies>` 태그가 끝나기 전에 라이브러리를 추가하자.
- Maven 형식(?)이다.
- Spring JDBC - `${org.apringframework-version}`
- Spring Transaction - `${org.apringframework-version}`
- Commons DBCP - 1.4
- Commons Logging - 1.2
- Commons Pool - 1.6
- MyBatis - 3.5.14
- MyBatis Spring - 1.3.2
- <font color="#92cddc">Apache Commons FileUpload - 1.4</font>
- <font color="#92cddc">Apache Commons IO - 2.11.0</font>
	- 파일 업로드를 위한 라이브러리
	- [Apache](https://www.apache.org/) > Commons > FileUpload, IO<font color="#7f7f7f">(입출력)</font>
### 추가된 코드
```xml
	<!-- Test -->
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>4.7</version>
		<scope>test</scope>
	</dependency>        
	<!-- 여기서부터 -->
	<!-- Spring Environments -->
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
	<!-- 여기까지 -->
</dependencies>
```
## 1.2. write.jsp
> src > main > webapp > WEB-INF > views > JSP File

- style.css 파일을 따로 생성하고 `<style>`의 내용 삭제
- 에디터 삽입을 위한 라이브러리 추가 절차
	- [summernote](https://summernote.org/)
		- jQuery 필요
		- <font color="#7f7f7f">(CKEditor도 있음. 파일 업로드 약간 지랄맞음)</font>
	- Getting started
	- Download complied
	- UPZIP: `C:\My_Study\Spring_Study\util\summernote-0.8.18-dist`
	- summernote-lite.css
		- src > main > webapp > resources > css >
	- summernote-lite.js
		- src > main > webapp > resources > js >
### 1.2.1. summernote-lite.css에 폰트 추가하기
- `C:\My_Study\Spring_Study\util\summernote-0.8.18-dist`에 있는 "font" 폴더 복사
- `src > main > webapp > resources > css >`에 붙여넣기
- <font color="#d99694">summernote는 지들이 쓰는 폰트를 사용하므로 꼭 font 폴더를 css 파일과 같은 위치에 두자.</font>
### 1.2.2. CSS 연결
```HTML
<link rel="stylesheet" href="resources/css/style.css"/>
<link rel="stylesheet" href="resources/css/summernote-lite.css"/>
```
### 1.2.3. jQuery 작성
```HTML
<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
```
- jQuery 정의

```HTML
<script src="resources/js/summernote-lite.js"></script>
```
- summernote 정의
- jQuery가 먼저 정의되고, summernote가 정의되어야 한다.

```HTML
<td><textarea id="content" name="content" cols="50" rows="8" data-str="내용"></textarea></td>
```
- `<textarea name="content">`에 id 부여

```HTML
<script>
	$(function(){
		$("#content").summernote();
	});
</script>
```
- jQeury 스크립트 작성
- textarea에 에디터를 가져다 놓는다.

![[Pasted image 20240105112019.png|400]]
- 짜잔
### 지금까지 코드 전문
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>WRITE</title>
<link rel="stylesheet" href="resources/css/style.css"/>
<link rel="stylesheet" href="resources/css/summernote-lite.css"/>
</head>
<body>
	<div id="bbs">
	<form action="write" method="post" encType="multipart/form-data">
		<table summary="게시판 글쓰기">
			<caption>게시판 글쓰기</caption>
			<tbody>
				<tr>
					<th>제목:</th>
					<td><input type="text" name="title" size="45" data-str="제목"/></td>
				</tr>
				<tr>
					<th>이름:</th>
					<td><input type="text" name="writer" size="12" data-str="이름"/></td>
				</tr>
				<tr>
					<th>내용:</th>
					<td><textarea id="content" name="content" cols="50" rows="8" data-str="내용"></textarea></td>
				</tr>
				<tr>
					<th>첨부파일:</th>
					<td><input type="file" name="file"/></td>
				</tr>
				<tr>
					<td colspan="2">
						<input type="button" value="보내기"
						onclick="sendData()"/>
						<input type="button" value="다시"/>
						<input type="button" value="목록"/>
					</td>
				</tr>
			</tbody>
		</table>
	</form>
	</div>
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script src="resources/js/summernote-lite.js"></script>
	<script>
		$(function(){
			$("#content").summernote();
		});
		
		function sendData(){
			for(var i=0 ; i<document.forms[0].elements.length ; i++){
				if(document.forms[0].elements[i].value == ""){
					alert(document.forms[0].elements[i].dateset.str+
							"를 입력하세요");
					document.forms[0].elements[i].focus();
					return;
				}
			}
			// document.forms[0].action = "test.jsp";
			document.forms[0].submit();
		}
	</script>
</body>
</html>
```
### 1.2.4. 옵션 부여하기
```HTML
<script>
	$(function(){
		$("#content").summernote();
	});
</script>
```
- summernote()에 옵션을 부여한다.

```javascript
$("#content").summernote({
	height: 200,
	focus: true
});
```
- `focus: true`: 커서를 미리 가져다 놓는다. <font color="#7f7f7f">(default: false)</font>

```javascript
$("#content").summernote("lineHeight", .7);
```
- 옵션을 하나만 추가할 때는 위와 같이 작성한다.
- <font color="#d99694">가끔 옵션을 묶어서 적용하는게 안 될 때가 있다. 이럴 땐 이걸 사용하자.</font>
- `.7`은 `0.7`과 같은 의미이다.
### 1.2.5. 한글화
```javascript
$("#content").summernote({
	height: 200,
	focus: true,
	lang: "ko-KR"
});
```
- `lang: "ko-KR"`: 한글화 한다.
	- 이때 summernote-lite.js와 같은 위치에 있는 font 폴더의 summernote-ko-KR.js를 참조한다.
- `<script>` 태그를 추가하자.
	```HTML
	<script src="resources/js/lang/summernote-ko-KR.js"></script>
	```
- `C:\My_Study\Spring_Study\util\summernote-0.8.18-dist\lang`에 있는 summernote-ko-KR.js 파일을 `src\main\webapp\resources\js\lang`에 붙여넣는다.
### 1.2.6. 에디터에 이미지가 들어올 때
- <font color="#d99694">이미지가 들어올 때 호출되는 콜백 함수는 onImageUpload이다.</font>
- 이때 sendImage()라는 함수를 호출하자.
- sendImage() 함수가 호출되면 AJAX 비동기식 통신으로 서버에게 이미지 파일을 전달한다.
- 문서에 추가한 이미지 파일은 `"src\main\webapp\resources\editor_img"`에 저장한다.

```javascript
$("#content").summernote({
	height: 200,
	focus: true,
	lang: "ko-KR",
	callbacks:{
		onImageUpload: function(files, editor){
			for(let i = 0 ; i < files.length ; i++){
				sendImage(files[i], editor);
			}
		}
	}
});
```
- 이미지 업로드를 인지하는 로직을 summernote() 안에 작성한다.
- callbacks는 summernote 에디터의 이벤트에 대한 콜백 함수들을 설정하는 객체이다.
- `onImageUpload: function(files, editor){`<font color="#d99694">HERE</font>`}`
	- 이미지가 에디터에 수행될 때마다 수행하는 곳
	- 추가되어 들어온 이미지는 여러 개일 수 있다.
		- 따라서 이미지 파일들에 대한 정보를 files 배열에 담아야 한다.
		- 따라서 for 문을 돌리자. <font color="#7f7f7f">아직 sendImage()는 안 만들었다.</font>
- <font color="#fac08f">이미지가 업로드되면 해당 이미지 파일들에 대한 정보가 `files` 배열에 담겨 `sendImage` 함수를 호출하도록 되어 있다. `sendImage` 함수는 업로드된 이미지를 처리한다.</font>

```javascript
function sendImage(file, editor){
	let frm = new FormData();
	frm.append("s_file", file);
}
```
- sendImage() 함수를 만든다.
- <font color="#d99694">JavaScript에서 파일을 보낼 때는 반드시 FormData 객체로 보내야 한다.</font> #JavaScript #파일전송 
- 보내고자 하는 파일을 FormData 객체에 파라미터로 저장한다.
	- s_file이라는 이름으로 file을 저장한다.
	- console.log를 이용해 메시지를 띄워 보자. 이미지 첨부 시 콘솔에 "I'm ready!"가 떴다.

```javascript
$.ajax({
	url: "saveImg",
	data: frm,
	type: "POST",
	contentType: false,
	processData: false,
	cache: false,
	dataType: "json"
}).done(function(data){
	console.log(data);
});
```
- sendImage() 함수 수행문에 AJAX 비동기식 통신 추가
- file을 보내야 하므로 data는 FormData 객체인 frm으로 설정한다.
- POST로 전송하여야 FormData 객체를 보낼 수 있다.
- contentType, processData는 파일 첨부시 필요한 속성이다.
- dataType은 서버에서 보내오는 자원의 타입이다. 즉 `function(data)`의 data는 json 표기이다.

> [!tldr]
> saveImg를 POST 방식으로 호출한다.
> 이때 frm을 던져준다. 이 안에 이미지 파일은 s_file이라는 이름으로 담겨져서 간다.
#### 1.2.6.1. FormData
- JavaScript 객체
- HTML 폼의 데이터를 동적으로 생성하고 이를 서버로 전송할 수 있게 해주는 객체
- 주로 파일 업로드와 같은 복잡한 데이터를 서버로 전송할 때 활용
- 일반적으로 `XMLHttpRequest` 객체와 함께 사용되며, `fetch` API와도 통합하여 사용된다.
- 사용 예제
	```javascript
	// FormData 객체 생성
	var formData = new FormData();
	
	// 키-값 쌍 추가
	formData.append('username', 'john_doe');
	formData.append('email', 'john_doe@example.com');
	
	// 파일 추가 (input 태그에서 파일 선택 시)
	var fileInput = document.getElementById('fileInput');
	formData.append('profilePicture', fileInput.files[0]);
	
	// XMLHttpRequest를 사용하여 서버로 전송
	var xhr = new XMLHttpRequest();
	xhr.open('POST', '/submit-form', true);
	
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4 && xhr.status == 200) {
	    // 서버 응답 처리
	    console.log(xhr.responseText);
	  }
	};
	
	// FormData를 전송
	xhr.send(formData);
	```
### 코드 전문
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>WRITE</title>
<link rel="stylesheet" href="resources/css/style.css"/>
<link rel="stylesheet" href="resources/css/summernote-lite.css"/>
</head>
<body>
	<div id="bbs">
	<form action="write" method="post" encType="multipart/form-data">
		<table summary="게시판 글쓰기">
			<caption>게시판 글쓰기</caption>
			<tbody>
				<tr>
					<th>제목:</th>
					<td><input type="text" name="title" size="45" data-str="제목"/></td>
				</tr>
				<tr>
					<th>이름:</th>
					<td><input type="text" name="writer" size="12" data-str="이름"/></td>
				</tr>
				<tr>
					<th>내용:</th>
					<td><textarea id="content" name="content" cols="50" rows="8" data-str="내용"></textarea></td>
				</tr>
				<tr>
					<th>첨부파일:</th>
					<td><input type="file" name="file"/></td>
				</tr>
<!--
				<tr>
					<th>비밀번호:</th>
					<td><input type="password" name="pwd" size="12"/></td>
				</tr>
-->
				<tr>
					<td colspan="2">
						<input type="button" value="보내기"
						onclick="sendData()"/>
						<input type="button" value="다시"/>
						<input type="button" value="목록"/>
					</td>
				</tr>
			</tbody>
		</table>
	</form>
	</div>
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script src="resources/js/summernote-lite.js"></script>
	<script src="resources/js/lang/summernote-ko-KR.js"></script>
	<script>
		$(function(){
			$("#content").summernote({
				height: 200,
				focus: true,
				lang: "ko-KR",
				callbacks:{
					onImageUpload: function(files, editor){
						for(let i = 0 ; i < files.length ; i++){
							sendImage(files[i], editor);
						}
					}
				}
			});
			$("#content").summernote("lineHeight", .7);
		});
		
		function sendImage(file, editor){
			let frm = new FormData();
			frm.append("s_file", file);
			// console.log("I'm ready!");
			
			$.ajax({
				url: "saveImg",
				data: frm,
				type: "POST",
				contentType: false,
				processData: false,
				cache: false,
				dataType: "json"
			}).done(function(data){
				console.log(data);
			});
		}
		
		function sendData(){
			for(var i=0 ; i<document.forms[0].elements.length ; i++){
				if(document.forms[0].elements[i].value == ""){
					alert(document.forms[0].elements[i].dateset.str+
							"를 입력하세요");
					document.forms[0].elements[i].focus();
					return;
				}
			}

			// document.forms[0].action = "test.jsp";
			document.forms[0].submit();
		}
	</script>
</body>
</html>
```
## 1.3. BbsController.java
> src/main/java > com.ict.editor > Java Class

### 1.3.1. 테스트를 위한 list(), write() 메서드 생성
```java
@RequestMapping("/list")
public ModelAndView list() {
	ModelAndView mv = new ModelAndView();
	
	// 목록 저장 부분
	
	mv.setViewName("bbs/list");
	return mv;
}

@RequestMapping("/write")
public String write() {
	return "bbs/write";
}
```
- list()에서는 DB로부터 목록<font color="#7f7f7f">(`BbsVO[]`)</font>을 받아서 mv에 저장해야 한다.
### 1.3.2. 에디터에 이미지가 들어갈 때를 위한 멤버 변수
```java
private String editor_img = "/resources/editor_img";
```
- 에디터에 삽입된 이미지를 저장할 경로를 멤버변수로 만든다.
- 해당 경로를 절대경로화 해야한다.

```java
@Autowired
private ServletContext application;
```
- 절대경로를 만들기 위한 ServletContext를 Autowired로 연결하자.

### 1.3.3. 이미지를 받아 저장할 메서드 생성
```java
@RequestMapping(value="saveImg", method=RequestMethod.POST)
public String saveImg(MultipartFile ivo) {
	return "saveImg"
}
```
- MultipartFile이라는 자료형으로 받는다. 하지만 ImgVO로 받고 싶다면?
	- src/main/java > spring.vo > ImgVO.java를 생성한다.
	- 이때 ImgVO에는 전달받은 파라미터 이름과 동일한 변수를 선언해야 한다.
		```java
		private MultipartFile s_file;
		```
	- getter, setter 꼭 만들어야 한다.
	- 이 경우 넘어온 이미지를 ivo에서 가져와 일일이 지정을 해야 한다.

```java
@RequestMapping(value="saveImg", method=RequestMethod.POST)
	public String saveImg(ImgVO ivo) {
		MultipartFile f = ivo.getS_file();
		String fname = null;
		// 파일의 존재를 확인하는 수행문
		return "";
	}
```
- 파일의 존재 여부를 확인한다.
- 이전 중복된 파일이 있으면 뒤에 숫자를 붙여야 한다.
- exist로 비교하여 똑같은 것이 있다면 없을 때까지 반복을 반복해야 한다.

```java
if(f.getSize() > 0) {
	String realPath = application.getRealPath(editor_img);
	fname = f.getOriginalFilename();
}
```
- `f.getSize() > 0`이라면 <font color="#fac08f">파일이 첨부가 되었을 때</font>를 의미한다.
- `application.getRealPath()`로 <font color="#fac08f">절대경로</font>를 얻었다.
- `getOriginalFilename()`으로 <font color="#fac08f">파일명</font>을 얻었다.
- Spring Elements > Beans > servlet-context.xml
	- 스프링 환경에서 파일 첨부 처리를 위한 MultipartResolver를 추가하여야 한다. ^25cab8
	- `<annotation-driven/>` 아래에 다음 내용을 작성한다.
	```xml
	<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<beans:property name="defaultEncoding" value="utf-8"/>
		<beans:property name="maxUploadSize" value="-1"/>
	</beans:bean>
	```
	- maxUploadSize가 `-1`이면 사이즈를 지정하지 않겠다는 의미이다.

```java
if(f.getSize() > 0) {
	String realPath = application.getRealPath(editor_img);
	fname = f.getOriginalFilename();
	fname = FileRenameUtil.checkSameFileName(fname, realPath); // <<< HERE!
```
- [[#1.5. FileRenameUtil.java|FileRenameUtil]]을 만들고 나서 문장을 추가하자.
- 파일이 이미 존재할 수 있으므로 파일명 검증을 한다.

```java
try {
	f.transferTo(new File(realPath, fname));
} catch (Exception e) {
	e.printStackTrace();
}
```
- 스프링이 가지는 MultipartFile 객체 내부에 파일 업로드 기능이 있다. 이를 사용하자.

```java
@Autowired
private HttpServletRequest request;
```
- 현재 서버의 경로를 얻어낸다. <font color="#7f7f7f">(예 : `http://www.naver.com/test/pic1020.png`)</font>
	- request 객체가 필요하다.
	- annotation으로 Autowired를 지정 후 HttpServletRequest를 멤버 변수로 선언한다.

```java
String c_path = request.getContextPath();
```
- 생성한 request 객체를 이용해 ContextPath를 얻어냈다.
- 실행해서 확인을 하면 "/editor/resources/editor_img"와 같이 나온다.
	- 가장 앞의 "/editor"가 ContextPath이다.
	- local에서 실행하였으므로 더 앞 부분이 생략되었다.
### 코드 전문
```java
package com.ict.editor;

import java.io.File;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.servlet.ModelAndView;

import spring.vo.ImgVO;
import sprint.util.FileRenameUtil;

@Controller
public class BbsController {
	
	private String editor_img = "/resources/editor_img";
	
	@Autowired
	private ServletContext application;
	
	@Autowired
	private HttpServletRequest request;
	
	@RequestMapping("/list")
	public ModelAndView list() {
		ModelAndView mv = new ModelAndView();
		
		// 목록 저장
		
		mv.setViewName("bbs/list");
		return mv;
	}
	
	@RequestMapping("/write")
	public String write() {
		return "bbs/write";
	}
	
	@RequestMapping(value="saveImg", method=RequestMethod.POST)
	public String saveImg(ImgVO ivo) {
		MultipartFile f = ivo.getS_file();
		String fname = null;
		
		if(f.getSize() > 0) {
			String realPath = application.getRealPath(editor_img);
			fname = f.getOriginalFilename();
			fname = FileRenameUtil.checkSameFileName(fname, realPath);
			
			try {
				f.transferTo(new File(realPath, fname));
			} catch (Exception e) {
				e.printStackTrace();
			}
			
			String c_path = request.getContextPath();
			System.out.println(c_path + editor_img);
		}
		
		return "";
	}

}
```
## 1.4. style.css
> src > main > webapp > resources > css > CSS File

- write.jsp에 있던 `<style>` 태그 내 CSS 내용을 집어 넣는다.
- write.jsp의 `<style>` 태그는 삭제하고 아래의 태그를 `<head>` 태그 안에 넣자.
	```HTML
	<link rel="stylesheet" href="resources/css/style.css"/>
	```
### 코드 전문
```CSS
@charset "UTF-8";
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

#bbs table th {
    text-align:center;
    border:1px solid black;
    padding:4px 10px;
}

#bbs table td {
    text-align:left;
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
```

## 1.5. FileRenameUtil.java
> src/main/java > spring.util > Java Class

- 파일의 이름 변경을 위한 Util을 만든다.

```java
public static String checkSameFileName(String fileName, String path) {
	int period = fileName.lastIndexOf(".");			// test12.txt
	String f_name = fileName.substring(0, period);	// test12
	String suffix = fileName.substring(period);		// .txt
```
- 인자인 fileName에서 확장자를 뺀 파일명을 가려낸다.
- 확장자 앞의 "."의 위치를 알아낸다.
	- lastIndexOf()와 substring()을 이용한다.

```java
String saveFilePath = path + System.getProperty("file.separator") + fileName;
```
- 전체 경로를 얻어낸다. "절대경로 + 파일명"이 전체 경로이다.

```java
File f = new File(saveFilePath);
int idx = 1;
```
- 전체 경로를 가지고 File 객체를 생성한다.
	- 같은 파일이 있는지, 존재하는지 확인하기 위함이다.
- idx는 중복된 파일명이 있을 때마다 1씩 증가시키기 위함이다.
	- 이는 중복된 파일명이 없을 때까지 반복한다.

```java
	while(f != null && f.exists()) {
		StringBuffer sb = new StringBuffer();
		sb.append(f_name);
		sb.append(idx++);
		sb.append(suffix);
		fileName = sb.toString();
		
		saveFilePath = path + System.getProperty("file.separator") + fileName;
		f = new File(saveFilePath);
	}
	
	return fileName;
}
```
- 이름이 같은 파일이 존재하는 경우에 해당하는 while 문이다.
	- 이름이 같은 파일이 존재하므로 파일명을 변경해야 한다.
- StringBuffer는 문자열에 대한 편집이 이루어지기 때문에 사용한다.
	- 파일명을 붙이고, 뒤에 idx를 붙여준 후, 확장자<font color="#7f7f7f">(suffix)</font>를 붙인다.
- saveFilePath와 f에 저장 후 다시 반복문을 수행한다.
	- `System.getProperty("file.separator")`는 Java에서 파일 경로를 생성할 때 사용되는 파일 구분자<font color="#7f7f7f">(file separator)</font>를 반환하는 메서드이다. 즉 운영체제에 따라 다르게 정의된 파일 구분자<font color="#7f7f7f">(예 : "/")</font>를 반환한다.
### 코드 전문
```java
package sprint.util;

import java.io.File;

public class FileRenameUtil {
	
	public static String checkSameFileName(String fileName, String path) {
		int period = fileName.lastIndexOf(".");			// test12.txt
		String f_name = fileName.substring(0, period);	// test12
		String suffix = fileName.substring(period);		// .txt
		
		String saveFilePath = path + System.getProperty("file.separator") + fileName;
		
		File f = new File(saveFilePath);
		int idx = 1;
		
		while(f != null && f.exists()) {
			StringBuffer sb = new StringBuffer();
			sb.append(f_name);
			sb.append(idx++);
			sb.append(suffix);
			fileName = sb.toString();
			
			saveFilePath = path + System.getProperty("file.separator") + fileName;
			f = new File(saveFilePath);
		}
		
		return fileName;
	}

}
```
# 2. 비동기식 통신 - 에디터에 이미지가 보일 때까지
## 2.1. 센세 문서 원본
```
:: MVNRepository.com에 접속하여 jackson으로 검색한다. 
이때 

   4. Jackson1,778 usages
      org.codehaus.jackson » jackson-core-asl
를 선택하여 dependency를 복사하자!

<dependency>
 <groupId>org.codehaus.jackson</groupId>
 <artifactId>jackson-mapper-asl</artifactId>
 <version>1.9.13</version>
</dependency>  

<bean id="jsonHttpMessageConverter" class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"/>
<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
	<property name="messageConverters">
		<list>
			<ref bean="jsonHttpMessageConverter"/>
		</list>
	</property>
</bean>

-- 이용방법은 Method앞에 @responseBody를 붙여서 이용
     반환값은 객체형 또는 Map형태로 이용할 수 있다

Spring4.x부터는 변경되었다.

MVNRepository.com에서 "com.fasterxml.jackson.core"로 검색하면 , 나타나는
 Jackson Databind, Jackson Core, Jackson Annotations 이 3가지 라이브러리를 받아서 lib 또는 Maven으로 
등록한 후 

<bean id="jsonHttpMessageConverter" class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter" />
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
	<property name="messageConverters">
		<list>
			<ref bean="jsonHttpMessageConverter" />
		</list>
	</property>
</bean>
 -----------------------------------------------------------------------------------------

위와 같이 하게 되면 비동기식 통신에서 반환을 JSON으로 할 때마다 항상
	@RequestMapping(value="all.inc", method = RequestMethod.POST, 
			produces = "application/json;charset=utf-8")
위와 같이 명시해야 한다. 이것을 간편하게 해결하기 위해

Controller-servlet.xml에서 아래의 내용을 
<bean id="jsonHttpMessageConverter"
		class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter" />

------------------------------------------------------------------------------------------
다음과 같이 변경하면 더 이상 produces를 정의하지 않아도 된다.
              <bean id="jsonHttpMessageConverter"
		class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter" >
		<property name="supportedMediaTypes">
			<list>
				<value>text/html;charset=utf-8</value>
				<value>application/json;charset=utf-8</value>
			</list>
		</property>
	</bean>	

위의 첫번째 bean객체가 변경처리 되어야 한다.
```
## 2.2. pom.xml - 업데이트
```xml
<dependency>
	<groupId>org.codehaus.jackson</groupId>
	<artifactId>jackson-mapper-asl</artifactId>
	<version>1.9.13</version>
</dependency>
```
- 위 내용 추가
### 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ict</groupId>
	<artifactId>editor</artifactId>
	<name>0105_editor</name>
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
		
		<!-- Spring Environments -->
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
		
		<dependency>
		    <groupId>org.codehaus.jackson</groupId>
		    <artifactId>jackson-mapper-asl</artifactId>
		    <version>1.9.13</version>
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
## 2.3. servlet-context.xml - 업데이트
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
- Spring MVC의 Controller에서 <font color="#fac08f">JSON 데이터를 받거나 반환할 때 사용</font>되는 Message Converter를 설정한다.
	- Message Converter는 여러 가지 형식의 데이터를 HTTP 요청과 응답에서 변환하고 처리하는 HttpMessageConverter 인터페이스를 구현하는 클래스이다.
- <font color="#fac08f">JSON 형식의 데이터를 처리</font>하기 위한 MappingJacksonHttpMessageConverter Bean을 생성한다.
- 메시지 컨버터를 지정할 AnnotationMethodHandlerAdapter Bean을 설정한다.
	- messageConverters Property를 통해 <font color="#fac08f">사용할 메시지 컨버터</font>인 jsonHttpMessageConverter를 <font color="#fac08f">참조</font>하도록 한다.

### 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

	<!-- DispatcherServlet Context: defines this servlet's request-processing infrastructure -->
	
	<!-- Enables the Spring MVC @Controller programming model -->
	<annotation-driven />
	
	<!-- JSON 처리를 위한 Converter -->
	<beans:bean id="jsonHttpMessageConverter" class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"/>
	<beans:bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
		<beans:property name="messageConverters">
			<beans:list>
				<beans:ref bean="jsonHttpMessageConverter"/>
			</beans:list>
		</beans:property>
	</beans:bean>
	
	<!-- 스프링 환경에서 파일 첨부 처리를 위한 Resolver -->
	<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<beans:property name="defaultEncoding" value="utf-8"/>
		<beans:property name="maxUploadSize" value="-1"/>
	</beans:bean>

	<!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/resources/**" location="/resources/" />

	<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<beans:property name="prefix" value="/WEB-INF/views/" />
		<beans:property name="suffix" value=".jsp" />
	</beans:bean>
	
	<context:component-scan base-package="com.ict.editor" />

</beans:beans>
```
## 2.4. BbsController.java - 업데이트
```java
@RequestMapping(value="saveImg", method=RequestMethod.POST)
@ResponseBody
public Map<String, String> saveImg(ImgVO ivo) {
	Map<String, String> map = new HashMap<String, String>();
	MultipartFile f = ivo.getS_file();
	String fname = null;
	
	if(f.getSize() > 0) {
		String realPath = application.getRealPath(editor_img);
		fname = f.getOriginalFilename();
		fname = FileRenameUtil.checkSameFileName(fname, realPath);
		
		try {
			f.transferTo(new File(realPath, fname));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	String c_path = request.getContextPath();
	map.put("url", c_path + editor_img);
	map.put("fname", fname);
	
	return map;
}
```
- 반환형은 Map으로, 그런데 ResponseBody이다. 그러면 Map 구조를 JSON으로 바꾸어 보내준다.
- HashMap을 생성하고, url<font color="#7f7f7f">(경로)</font>와 fname<font color="#7f7f7f">(파일명)</font>을 map에 저장한다.
- return을 map으로 하면 JSON 표기법으로 바뀌어서 전달된다.
- 이제 에디터에서 이미지를 첨부하면 Console에 아래와 같은 메시지가 나온다.
	- `{fname: 'image1.png', url: '/editor/resources/editor_img'}`
	- src 뒤에 url + fname으로 입력하면 그 경로의 이미지가 나타난다.
### 코드 전문
```java
package com.ict.editor;

import java.io.File;
import java.util.HashMap;
import java.util.Map;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.servlet.ModelAndView;

import spring.vo.ImgVO;
import sprint.util.FileRenameUtil;

@Controller
public class BbsController {
	
	private String editor_img = "/resources/editor_img";
	
	@Autowired
	private ServletContext application;
	
	@Autowired
	private HttpServletRequest request;
	
	@RequestMapping("/list")
	public ModelAndView list() {
		ModelAndView mv = new ModelAndView();
		
		// 목록 저장
		
		mv.setViewName("bbs/list");
		return mv;
	}
	
	@RequestMapping("/write")
	public String write() {
		return "bbs/write";
	}
	
	@RequestMapping(value="saveImg", method=RequestMethod.POST)
	@ResponseBody
	public Map<String, String> saveImg(ImgVO ivo) {
		Map<String, String> map = new HashMap<String, String>();
		MultipartFile f = ivo.getS_file();
		String fname = null;
		
		if(f.getSize() > 0) {
			String realPath = application.getRealPath(editor_img);
			fname = f.getOriginalFilename();
			fname = FileRenameUtil.checkSameFileName(fname, realPath);
			
			try {
				f.transferTo(new File(realPath, fname));
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
		
		String c_path = request.getContextPath();
		map.put("url", c_path + editor_img);
		map.put("fname", fname);
		
		return map;
	}

}
```
## 2.5. write.jsp - 이미지가 보이도록 업데이트
```javascript
$.ajax({
	url: "saveImg",
	data: frm,
	type: "POST",
	contentType: false,
	processData: false,
	cache: false,
	dataType: "json"
}).done(function(data){
	$("#content").summernote("editor.insertImage", data.url + "/" + data.fname); // << HERE!
});
```
- 위와 같은 내용을 추가하면 에디터에 첨부한 이미지가 이미지로 나타난다.
### 코드 전문
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>WRITE</title>
<link rel="stylesheet" href="resources/css/style.css"/>
<link rel="stylesheet" href="resources/css/summernote-lite.css"/>
</head>
<body>
	<div id="bbs">
	<form action="write" method="post" encType="multipart/form-data">
		<table summary="게시판 글쓰기">
			<caption>게시판 글쓰기</caption>
			<tbody>
				<tr>
					<th>제목:</th>
					<td><input type="text" name="title" size="45" data-str="제목"/></td>
				</tr>
				<tr>
					<th>이름:</th>
					<td><input type="text" name="writer" size="12" data-str="이름"/></td>
				</tr>
				<tr>
					<th>내용:</th>
					<td><textarea id="content" name="content" cols="50" rows="8" data-str="내용"></textarea></td>
				</tr>
				<tr>
					<th>첨부파일:</th>
					<td><input type="file" name="file"/></td>
				</tr>
<!--
				<tr>
					<th>비밀번호:</th>
					<td><input type="password" name="pwd" size="12"/></td>
				</tr>
-->
				<tr>
					<td colspan="2">
						<input type="button" value="보내기"
						onclick="sendData()"/>
						<input type="button" value="다시"/>
						<input type="button" value="목록"/>
					</td>
				</tr>
			</tbody>
		</table>
	</form>
	</div>
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script src="resources/js/summernote-lite.js"></script>
	<script src="resources/js/lang/summernote-ko-KR.js"></script>
	<script>
		$(function(){
			$("#content").summernote({
				height: 200,
				focus: true,
				lang: "ko-KR",
				callbacks:{
					onImageUpload: function(files, editor){
						for(let i = 0 ; i < files.length ; i++){
							sendImage(files[i], editor);
						}
					}
				}
			});
			$("#content").summernote("lineHeight", .7);
		});
		
		function sendImage(file, editor){
			let frm = new FormData();
			frm.append("s_file", file);
			// console.log("I'm ready!");
			
			$.ajax({
				url: "saveImg",
				data: frm,
				type: "POST",
				contentType: false,
				processData: false,
				cache: false,
				dataType: "json"
			}).done(function(data){
				$("#content").summernote("editor.insertImage", data.url + "/" + data.fname);
			});
		}
		
		function sendData(){
			for(var i=0 ; i<document.forms[0].elements.length ; i++){
				if(document.forms[0].elements[i].value == ""){
					alert(document.forms[0].elements[i].dateset.str+
							"를 입력하세요");
					document.forms[0].elements[i].focus();
					return;
				}
			}

			// document.forms[0].action = "test.jsp";
			document.forms[0].submit();
		}
	</script>
</body>
</html>
```
## 2.6. 정리
### 2.6.1. 처리 로직
1. Client Request
2. `@Controller` -> BbsController
	- `@RequestMapping("/write")` -> write.jsp
	- `@RequestMapping(value="/saveImg", method=RequestMethod.POST)`
		- 파라미터를 ImgVO로 받는다.
		- 첨부파일은 자동으로 vo 안에 있는 s_file에 저장된다.
			- 이때 <font color="#fac08f">첨부파일에 대한 설정</font><font color="#7f7f7f">(MultipartFile Resolver)</font>을 한다.
		- DB 처리
### 2.6.2. 첨부 파일 설정은
- servlet-context.xml에 <font color="#fac08f">MultipartResolver</font> Beans를 추가했다.
### 2.6.3. 뷰 페이지에서 비동기식 통신을 할 때
- <font color="#d99694">JSON 표기법으로 응답하기 위한 설정</font>이 필요하다. [MVNRepository](https://mvnrepository.com/)
	- servlet-context.xml에 JSON 처리를 위한 [[#2.3. servlet-context.xml - 업데이트|Converter]] 추가
	- 스프링 버전 3.x일 때 필요 라이브러리
		- Jackson
	- 스프링 버전 4.x일 때 필요 라이브러리
		- Jackson Databind
		- Jackson Core
		- Jackson Annotations
- 스프링 Controller에서
	- 앞에 `@ResponseBody`를 지정한다.
	- 반환형을 `Map<String, String>`으로 지정
	- 반환할 때 JSON으로 변환된다.
		- 예 : `{"fname":"s4.png","url":"/editor/resources/editor_img"}`
# 3. 게시판 목록 가져오기
## 3.1. root-context.xml
> src > main > webapp > WEB-INF > spring >

```xml
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
```
- 위 내용이 들어가야 한다.
- 여기서 필요한 내용을 추가하거나 수정하자.
- <font color="#d99694">context:component-scan이 오류가 나는 이유</font>
	- 하단 Namespaces에서 context 체크하자.
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
## 3.2. BbsDAO.java
> src > main > java > mybatis > dao >

### 3.2.1. 기존 내용
```java
package mybatis.dao;

import java.util.HashMap;
import java.util.List;

import org.apache.ibatis.session.SqlSession;

import mybatis.vo.BbsVO;
import mybatis.vo.CommVO;
import mybatis.service.FactoryService;

public class BbsDAO {
	
	public static int getCount(String bname) {
		SqlSession ss = FactoryService.getFactory().openSession();
		int cnt = ss.selectOne("bbs.count", bname);
		return cnt;
	}
	
	public static BbsVO[] getList(String bname, int begin, int end) {
		BbsVO[] ar = null;
		
		SqlSession ss = FactoryService.getFactory().openSession();
		HashMap<String, String> map = new HashMap<String, String>();
		map.put("bname", bname);
		map.put("begin", String.valueOf(begin));
		map.put("end", String.valueOf(end));
		
		List<BbsVO> list = ss.selectList("bbs.list", map);
		ss.close();
		
		if(list != null && !list.isEmpty()) {
			ar = new BbsVO[list.size()];
			list.toArray(ar);
		}
		
		return ar;
	}
	
	public static int add(String subject, String writer, String content, String fname, String oname, String ip, String bname) {
		HashMap<String, String> map = new HashMap<String, String>();
		map.put("subject", subject);
		map.put("writer", writer);
		map.put("content", content);
		map.put("file_name", fname);
		map.put("ori_name", oname);
		map.put("ip", ip);
		map.put("bname", bname);
		
		SqlSession ss = FactoryService.getFactory().openSession();
		int cnt = ss.insert("bbs.add", map);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		
		ss.close();
		
		return cnt;
	}
	
	public static BbsVO getBbs(String b_idx) {
		SqlSession ss = FactoryService.getFactory().openSession();
		BbsVO bbsVO = ss.selectOne("bbs.getBbs", b_idx);
		ss.close();
		
		return bbsVO;
	}
	
	public static int addComm(CommVO vo) {
		SqlSession ss = FactoryService.getFactory().openSession();
		int cnt = ss.insert("bbs.addComm", vo);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		ss.close();
		
		return cnt;
	}
	
	public static int edit(String b_idx, String title, String content, String fname, String oname, String ip) {
		HashMap<String, String> map = new HashMap<String, String>();
		map.put("b_idx", b_idx);
		map.put("subject", title);
		map.put("content", content);
		if(fname != null) {
			map.put("file_name", fname);
			map.put("ori_name", oname);
		}
		map.put("ip", ip);
		
		SqlSession ss = FactoryService.getFactory().openSession();
		int cnt = ss.update("bbs.edit", map);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		
		ss.close();
		
		return cnt;
	}
	
	public static int del(String b_idx) {
		SqlSession ss = FactoryService.getFactory().openSession();
		int cnt = ss.update("bbs.del", b_idx);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		ss.close();
		
		return cnt;
	}
	
	public static int hit(String b_idx) {
		SqlSession ss = FactoryService.getFactory().openSession();
		int cnt = ss.update("bbs.hit", b_idx);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		ss.close();
		
		return cnt;
	}

}
```
### 3.2.2. 수정 내용
```java
package mybatis.dao;

import java.util.HashMap;
import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import mybatis.vo.BbsVO;
import mybatis.vo.CommVO;

@Component
public class BbsDAO {
	
	@Autowired
	private SqlSession ss;
	
	public int getCount(String bname) {
		int cnt = ss.selectOne("bbs.count", bname);
		return cnt;
	}
	
	public BbsVO[] getList(String bname, int begin, int end) {
		BbsVO[] ar = null;
		
		HashMap<String, String> map = new HashMap<String, String>();
		map.put("bname", bname);
		map.put("begin", String.valueOf(begin));
		map.put("end", String.valueOf(end));
		
		List<BbsVO> list = ss.selectList("bbs.list", map);
		
		if(list != null && !list.isEmpty()) {
			ar = new BbsVO[list.size()];
			list.toArray(ar);
		}
		
		return ar;
	}
	
	public int add(String subject, String writer, String content, String fname, String oname, String ip, String bname) {
		HashMap<String, String> map = new HashMap<String, String>();
		map.put("subject", subject);
		map.put("writer", writer);
		map.put("content", content);
		map.put("file_name", fname);
		map.put("ori_name", oname);
		map.put("ip", ip);
		map.put("bname", bname);
		
		int cnt = ss.insert("bbs.add", map);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		
		return cnt;
	}
	
	public BbsVO getBbs(String b_idx) {
		BbsVO bbsVO = ss.selectOne("bbs.getBbs", b_idx);
		
		return bbsVO;
	}
	
	public int addComm(CommVO vo) {
		int cnt = ss.insert("bbs.addComm", vo);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		
		return cnt;
	}
	
	public int edit(String b_idx, String title, String content, String fname, String oname, String ip) {
		HashMap<String, String> map = new HashMap<String, String>();
		map.put("b_idx", b_idx);
		map.put("subject", title);
		map.put("content", content);
		if(fname != null) {
			map.put("file_name", fname);
			map.put("ori_name", oname);
		}
		map.put("ip", ip);
		
		int cnt = ss.update("bbs.edit", map);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		
		return cnt;
	}
	
	public int del(String b_idx) {
		int cnt = ss.update("bbs.del", b_idx);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		
		return cnt;
	}
	
	public int hit(String b_idx) {
		int cnt = ss.update("bbs.hit", b_idx);
		if(cnt > 0) ss.commit();
		else ss.rollback();
		
		return cnt;
	}

}
```
## 3.3. Paging.java
> src/main/java > spring.util

### 코드 전문
```java
package spring.util;

public class Paging {
	
	int nowPage = 1;		// 현재 페이지의 값
	int numPerPage = 10;	// 한 페이지에 보여질 게시물의 수
	int totalRecord;		// 총 게시물의 수
	int pagePerBlock = 5;	// 한 블럭당 보여질 페이지 수
	int totalPage;			// 전체 페이지 수
	int begin;				// 현재 페이지에 보여질 게시물의 시작 행 번호
	int end;				// 현재 페이지에 보여질 게시물의 마지막 행 번호
	int startPage;			// 한 블럭에 보여질 시작 페이지
	int endPage;			// 한 블럭에 보여질 마지막 페이지
	
	public Paging() {}		// 기본 생성자
	
	public Paging(int numPerPage, int pagePerBlock) {
		this.numPerPage = numPerPage;
		this.pagePerBlock = pagePerBlock;
	}

	public int getNowPage() {
		return nowPage;
	}

	public void setNowPage(int nowPage) {		// 현재 페이지가 입력되는 부분
		this.nowPage = nowPage;
		
		if(nowPage > totalPage) nowPage = totalPage;
		
		begin = (nowPage - 1) * numPerPage + 1;
		end = nowPage * numPerPage;
		
		startPage = (int) ((nowPage - 1) / pagePerBlock) * pagePerBlock + 1;
		endPage = startPage + pagePerBlock - 1;

		if(endPage > totalPage) endPage = totalPage;
	}

	public int getNumPerPage() {
		return numPerPage;
	}

	public void setNumPerPage(int numPerPage) {
		this.numPerPage = numPerPage;
	}

	public int getTotalRecord() {
		return totalRecord;
	}

	public void setTotalRecord(int totalRecord) {
		this.totalRecord = totalRecord;
		
		totalPage = (int) Math.ceil((double) totalRecord / numPerPage);
	}

	public int getPagePerBlock() {
		return pagePerBlock;
	}

	public void setPagePerBlock(int pagePerBlock) {
		this.pagePerBlock = pagePerBlock;
	}

	public int getTotalPage() {
		return totalPage;
	}

	public void setTotalPage(int totalPage) {
		this.totalPage = totalPage;
	}

	public int getBegin() {
		return begin;
	}

	public void setBegin(int begin) {
		this.begin = begin;
	}

	public int getEnd() {
		return end;
	}

	public void setEnd(int end) {
		this.end = end;
	}

	public int getStartPage() {
		return startPage;
	}

	public void setStartPage(int startPage) {
		this.startPage = startPage;
	}

	public int getEndPage() {
		return endPage;
	}

	public void setEndPage(int endPage) {
		this.endPage = endPage;
	}

}
```
## 3.4. BbsController.java
> src/main/java > com.ict.editor >

### 3.4.1. list 메서드 업데이트
```java
@Autowired
private BbsDAO b_dao;

@RequestMapping("/list")
public ModelAndView list() {
	ModelAndView mv = new ModelAndView();
	
	Paging page = new Paging(3, 5);
	page.setTotalRecord(b_dao.getCount("BBS"));
	
	String cPage = request.getParameter("cPage");
	if(cPage == null) page.setNowPage(1);
	else {
		int nowPage = Integer.parseInt(cPage);
		page.setNowPage(nowPage);
	}
	
	BbsVO[] b_ar = b_dao.getList("BBS", page.getBegin(), page.getEnd());
	mv.addObject("b_ar", b_ar);
	mv.addObject("page", page);
	
	mv.setViewName("bbs/list");
	return mv;
}
```
### 코드 전문
```java
package com.ict.editor;

import java.io.File;
import java.util.HashMap;
import java.util.Map;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.BbsDAO;
import mybatis.vo.BbsVO;
import spring.util.FileRenameUtil;
import spring.util.Paging;
import spring.vo.ImgVO;

@Controller
public class BbsController {
	
	private String editor_img = "/resources/editor_img";
	
	@Autowired
	private ServletContext application;
	
	@Autowired
	private HttpServletRequest request;
	
	@Autowired
	private BbsDAO b_dao;
	
	@RequestMapping("/list")
	public ModelAndView list() {
		ModelAndView mv = new ModelAndView();
		
		Paging page = new Paging(3, 5);
		page.setTotalRecord(b_dao.getCount("BBS"));
		
		String cPage = request.getParameter("cPage");
		if(cPage == null) page.setNowPage(1);
		else {
			int nowPage = Integer.parseInt(cPage);
			page.setNowPage(nowPage);
		}
		
		BbsVO[] b_ar = b_dao.getList("BBS", page.getBegin(), page.getEnd());
		mv.addObject("b_ar", b_ar);
		mv.addObject("page", page);
		
		mv.setViewName("bbs/list");
		return mv;
	}
	
	@RequestMapping("/write")
	public String write() {
		return "bbs/write";
	}
	
	@RequestMapping(value="saveImg", method=RequestMethod.POST)
	@ResponseBody
	public Map<String, String> saveImg(ImgVO ivo) {
		Map<String, String> map = new HashMap<String, String>();
		MultipartFile f = ivo.getS_file();
		String fname = null;
		
		if(f.getSize() > 0) {
			String realPath = application.getRealPath(editor_img);
			fname = f.getOriginalFilename();
			fname = FileRenameUtil.checkSameFileName(fname, realPath);
			
			try {
				f.transferTo(new File(realPath, fname));
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
		
		String c_path = request.getContextPath();
		map.put("url", c_path + editor_img);
		map.put("fname", fname);
		
		return map;
	}
	
}
```
