<font color="#fac08f">jQuery</font>는 <font color="#c3d69b">JavaScript를</font> 보다 <font color="#c3d69b">쉽게 다룰 수 있도록</font> 도와주는 라이브러리 중 하나이다. 주로 [[#DOM 조작]], 이벤트 처리, 애니메이션 등을 간편하게 다룰 수 있도록 한다.
<font color="#92cddc">AJAX</font><font color="#7f7f7f">(Asynchronous JavaScript and XML)</font>는 <font color="#d99694">웹 페이지를 리로드하지 않고도 서버와 비동기적으로 데이터를 교환할 수 있는 기술</font>이다. AJAX는 jQuery의 기능 중 하나이다.

# 1. jQuery의 사용 방법
## 1.1. jQuery 사용을 위한 스크립트 태그
```HTML
<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
```
- HTML, 보통 JSP에서 사용한다.

## 1.2. jQuery 선택자 `$()`
### 1.2.1. `$(document)`
- <font color="#c3d69b">전체 문서</font><font color="#7f7f7f">(document)</font>를 나타낸다.
- 문서 객체 모델<font color="#7f7f7f">(DOM)</font>에서 <font color="#c3d69b">HTML 문서 전체를 의미</font>하며, <font color="#92cddc">여기에 포함된 모든 요소에 대한 조작이 가능</font>하다.
- <font color="#d99694">예를 들어</font> 문서 내의 모든 `<p>` 태그를 숨기려면 다음과 같이 사용한다.
	```javascript
	$(document).ready(function(){
		$("p").hide();
	});
	```
#### 1.2.1.1. `ready(function(){})` METHOD #jQuery
- HTML의 `onload` 이벤트 속성과 같은 역할
- 문서가 로드되면 메서드 내부의 function을 실행한다.
- `$(function(){})`와 같이 축약해 사용한다.
	```javascript
	$(function(){
		$("p").hide();
	});
	```
	- 상단의 예제 코드를 위와 같이 축약해 사용 가능하다는 의미이다.

### 1.2.2. `$(window)`
- <font color="#c3d69b">현재 브라우저 창</font>을 나타낸다.
- <font color="#92cddc">브라우저 창에 관련된 이벤트</font><font color="#7f7f7f">(예 : 스크롤, 리사이즈)</font>를 처리하거나, 현재 브라우저 <font color="#92cddc">창의 크기 등</font>을 가져오는 데 사용된다.
- <font color="#d99694">예를 들어</font> 브라우저 창이 스크롤 될 때 어떤 동작을 수행하려면 다음과 같이 사용한다.
	```javascript
	$(window).scroll(function(){
		// 스크롤에 대한 이벤트
	});
	```

### 1.2.3. `$(this)`
- <font color="#c3d69b">현재 선택된 요소</font>를 나타낸다.
- <font color="#92cddc">이벤트 핸들러 내에서 해당 이벤트를 발생시킨 요소를 참조할 때</font> 사용된다.
- <font color="#d99694">예를 들어</font> 클릭 이벤트가 발생한 요소의 텍스트를 가져오려면 다음과 같이 사용한다.
	```javascript
	$("button").click(function(){
		var buttonText = $(this).text();
		alert("클릭된 버튼의 텍스트 : " + buttonText);
	});
	```

### 1.2.4. `$(selector)`
- <font color="#92cddc">CSS 선택자를 이용</font>해 원하는 HTML 요소를 선택한다.
- 예제 코드
	```javascript
	$(".myClass") // class가 myClass인 HTML 요소들 선택
	$("#myElementId") // id가 myElementId인 HTML 요소 선택
	$("tbody input:checkbox") // tbody 내 input 태그 중 type이 checkbox인 요소 선택
	```
- <font color="#d99694">해당하는 요소가 복수</font>라면 모두 선택되어 <font color="#d99694">배열로 반환</font>한다.
#### 1.2.4.1. 하나의 선택자로 여러 요소 선택하기
```javascript
let ar = $(".test, span, div");
```
- 클래스가 "test"인 요소, `<span>` 요소, `<div>` 요소를 모두 선택한다.
- 이들은 모두 `ar` 배열에 저장된다.
#### 1.2.4.2. 배열 내에서의 특정 요소 인덱스 구하기
```javascript
let idx = ar.index($("#t7"));
```
- `ar` 배열에서 id가 "t7"인 요소를 선택하며, 이 요소의 `ar` 배열 내에서의 인덱스를 `idx`에 저장한다.
## 1.3. jQuery의 메서드
### 1.3.1. 이벤트 관련
#### 1.3.1.1.(1) `bind(eventType [, eventData], handler)`
- `eventType`
	- 이벤트 종류를 지정하는 문자열
	- "click", "mouseover" 등
- `eventData` <font color="#7f7f7f">(option)</font>
	- 이벤트 핸들러에 전달되는 추가 데이터
- `handler`
	- 이벤트가 발생했을 때 실행되는 함수 또는 이벤트 핸들러
- 선택한 요소에 이벤트 핸들러를 연결한다. 이벤트가 발생하면 연결된 핸들러가 실행된다.
- 활용 예제
	```javascript
	$("button").bind("click", function(){
	    alert("버튼이 클릭되었습니다!");
	});
	```
#### 1.3.1.1.(2) `on(eventType, handler)`
- jQuery 3.0부터 `bind()` 대신 권장되는 메서드
- 사용 예제
	```javascript
	$("button").on("click", function(){
	    alert("버튼이 클릭되었습니다!");
	});
	```
#### 1.3.1.2. `click(handler)`
- 요소에 클릭 이벤트 핸들러를 연결한다.
- 활용 예제
	```javascript
	$("button").click(function(){
	    alert("버튼이 클릭되었습니다!");
	});
	```
#### 1.3.1.3. `each(callback)`
- 선택한 요소 집합에 대해 각각의 요소에 주어진 함수를 실행한다.
- 활용 예제
	```javascript
	$("li").each(function(){
	    console.log($(this).text());
	});
	```
	- 각 `<li>` 요소의 텍스트가 콘솔에 출력된다.
### 1.3.2. [[#3. DOM 조작|DOM 조작]] 및 속성 관련
#### 1.3.2.1. `css(propertyName)`
- 요소의 CSS 속성 값을 가져온다.
- 활용 예제
	```javascript
	$("div").css("color", "red");
	```
	- 모든 `<div>` 요소의 글자 색상이 빨간색으로 변경된다.
#### 1.3.2.2. `html()`
- 요소의 HTML 내용을 가져오거나 설정한다.
- 활용 예제
	```javascript
	$("p").html("<strong>강조된</strong> 텍스트");
	```
	- 모든 `<p>` 요소의 HTML 내용이 "<strong>강조된</strong> 텍스트"로 변경된다.
#### 1.3.2.3. `text()`
- 요소의 텍스트 내용을 가져오거나 설정한다.
- 활용 예제
	```javascript
	$("span").text("새로운 텍스트");
	```
	- 모든 `<span>` 요소의 텍스트가 "새로운 텍스트"로 변경된다.
#### 1.3.2.4. `eq(index)`
- 선택한 요소 집합에서 주어진 인덱스에 해당하는 요소를 선택한다.
	- 여기서 <font color="#92cddc">인덱스는 1부터 시작</font>이다.
- 활용 예제
	```javascript
	$("ul li").eq(2).css("background-color", "yellow");
	```
	- `<ul>` 안에서 두 번째 `<li>` 요소의 배경색이 노란색으로 변경된다.
#### 1.3.2.5. `index()`
- 선택한 요소의 부모 내에서의 인덱스를 가져온다.
- 활용 예제
	```javascript
	var index = $("li").index();
	console.log("인덱스: " + index);
	```
	- 첫 번째 `<li>` 요소의 부모 내에서의 인덱스가 콘솔에 출력된다.

> [!question] 두 번째 `<li>` 요소의 부모 내에서의 인덱스를 가져오고 싶다면?

#### 1.3.2.6. `prop(propertyName)`
- 요소의 속성 값을 가져오거나 설정한다.
- 활용 예제
	```javascript
	$("input[type='checkbox']").prop("checked", true);
	```
	- 해당 체크박스의 속성이 체크 상태로 변경된다.
#### 1.3.2.7. `find(selector)`
- 선택한 요소 하위에서 주어진 선택자에 일치하는 모든 요소를 찾는다.
- 활용 예제
	```javascript
	$("div.container").find("p").css("font-size", "20px");
	```
	- `<div class="container">` 안의 모든 `<p>` 요소의 글자 크기가 20px로 변경된다.
#### 1.3.2.8. `closest(selector)`
- 선택한 요소가 주어진 선택자와 일치하는 가장 가까운 부모를 찾는다.
- 활용 예제
	```javascript
	$("span").closest("div").css("border", "1px solid blue");
	```
	- `<span>` 요소가 속한 가장 가까운 `<div>` 요소의 테두리가 파란색으로 변경된다.
#### 1.3.2.9. `parent()`
- 선택한 요소의 부모 요소를 가져온다.
- 활용 예제
	```javascript
	$("p").parent().css("background-color", "lightgray");
	```
	- 각 `<p>` 요소의 부모 요소인 `<div>`의 배경색이 연한 회색으로 변경된다.
#### 1.3.2.10. `children()`
- 선택한 요소의 자식 요소를 가져온다.
- 활용 예제
	```javascript
	$("ul").children("li").css("list-style-type", "square");
	```
	- `<ul>`의 모든 자식 `<li>` 요소의 목록 스타일이 사각형으로 변경된다.
#### 1.3.2.11. `next()`
- 선택한 요소의 다음 형제 요소를 가져온다.
- 활용 예제
	```javascript
	$("h2").next().css("color", "green");
	```
	- `<h2>` 요소 다음의 형제 요소의 글자 색상이 녹색으로 변경된다.
#### 1.3.2.12. `addClass(className)`
- 선탠한 요소에서 클래스를 추가한다.
- 활용 예제
	```javascript
	$("div").addClass("highlight");
	```
	- 모든 `<div>` 요소에 "highlight" 클래스가 추가된다.
#### 1.3.2.13. `removeClass(className)`
- 선택한 요소에서 클래스를 제거한다.
- 활용 예제
	```javascript
	$("p").removeClass("italic");
	```
	- 모든 `<p>` 요소에서 "italic" 클래스가 제거된다.
#### 1.3.2.14. `val()`
- 입력 요소의 값을 가져오거나 설정한다.
- 활용 예제
	```javascript
	var inputValue = $("input").val();
	console.log("입력 값: " + inputValue);
	```
	- 입력 필드의 값이 콘솔에 출력된다.
#### 1.3.2.15. `hide()`
- 선택한 요소를 숨긴다.
- 활용 예제
	```javascript
	$("img").hide();
	```
	- 페이지의 모든 이미지가 숨겨진다.
#### 1.3.2.16. `show()`
- 선택한 요소를 보여준다.
- 활용 예제
	```javascript
	$("div.hidden").show();
	```
	- 숨겨진 `<div>`가 화면에 보여진다.
#### 1.3.2.17. `toggle()`
- 선택한 요소의 가시성을 토글<font color="#7f7f7f">(show/hide)</font>한다.
- 활용 예제
	```javascript
	$("button").click(function(){
	    $("p").toggle();
	});
	```
	- 버튼을 클릭할 때마다 `<p>` 요소의 가시성이 토글된다.
- 토글 메서드 없이 토글을 만드는 방법
	```javascript
	let check = true;
				
	$("#btn").click(function(){				
		if(check){				
			$("div").eq(1).children().addClass("cBlue");
			$(this).val("해제");
		} else {
			$("div").eq(1).children().removeClass("cBlue");
			$(this).val("적용");					
		}
		check = !check;
	});
	```
	- 클릭 이벤트가 발생할 때마다 불린형 변수인 check를 확인하여 "cBlue" 클래스를 추가하거나 삭제한다.
	- 메서드가 끝나기 전 check에 not 값을 주어 true는 false로, false는 true로 바뀌도록 한다.
#### 1.3.2.18. `is()`
- 선택한 요소가 주어진 선택자와 일치하는지 여부를 확인한다.
- 활용 예제
	```javascript
	if ($("input").is(":checked")) {
	    console.log("체크되었습니다.");
	}
	```
	- 체크박스가 체크되어 있으면 콘솔에 메시지가 출력된다.
- 삼항 연산자와 함께 사용할 수 있다.
	```javascript
	var result = $("#myElement").is(".highlight") ? "일치" : "불일치";
	```
	- id가 "myElement"인 요소가 "highlight" 클래스와 일치하는지 확인하고, 일치하면 `result` 변수에 "일치"를, 그렇지 않으면 "불일치"를 할당한다.

# 2. AJAX의 사용 방법
## 2.1. $.ajax() 메서드
```javascript
$.ajax({
	url: '서버URL',
	type: 'GET', // or 'POST'
	data: {key1: 'value1', key2: 'value2'},
	success: function(response){
		// 성공 시 처리
	},
	error: function(error){
		// 에러 시 처리
	}
});
```

url
- MVC 패턴을 사용하는 경우 `Controller?type=member`와 같이 설정한다.

`done()` 메서드를 이용하는 `$.ajax()` 메서드
```javascript
$.ajax({
	url: '서버URL',
	type: 'POST',
	data: 'type=' + value1 + '&key=' + value2
}).done(function(data){
	// 성공 시 처리
});
```

간편한 `$.ajax()` 메서드
```javascript
$.get('서버URL', {key1: 'value1', key2: 'value2'}, function(response){
	  // 성공 시 처리
});

$.post('서버URL', {key1: 'value1', key2: 'value2'}, function(response){
	   // 성공 시 처리
});
```

# 3. DOM 조작
## 3.1. DOM이란?
- 문서 객체 모델
- Document Object Model
- HTML 및 XML 문서의 프로그래밍 인터페이스를 나타낸다.
- 웹 페이지의 구조를 표현하고, 그 구조에 접근하고 조작할 수 있도록 하는 표준 인터페이스이다.

## 3.2. DOM 조작이란?
- 웹 페이지의 <font color="#92cddc">HTML 요소들을 동적으로 변경하거나 조작하는 것</font>을 의미한다.
- <font color="#fac08f">jQuery</font>는 이러한 DOM 조작을 쉽게 할 수 있도록 도와주는 기능을 제공한다.

## 3.3. jQuery를 사용하여 간단한 DOM 조작을 수행하는 예제
```javascript
// HTML 문서에서 id가 "myElement"인 요소를 선택하여 텍스트를 변경
$("#myElement").text("새로운 내용");

// HTML 문서에 새로운 요소를 추가
$("#container").append("<p>새로운 단락</p>");

// 이벤트 핸들러를 등록하여 버튼 클릭 시 특정 동작 수행
$("#myButton").click(function() {
  alert("버튼이 클릭되었습니다!");
});
```
- `$()`는 <font color="#92cddc">jQuery 선택자</font>로 HTML 요소를 선택하는 역할을 한다.
- `text()` 메서드는 선택한 요소의 <font color="#c3d69b">텍스트 내용을 변경</font>한다.
- `append()` 메서드는 선택한 요소에 새로운 <font color="#c3d69b">HTML 내용을 추가</font>한다.
- `click()` 메서드는 선택한 요소에 <font color="#c3d69b">클릭 이벤트 핸들러를 등록</font>한다.