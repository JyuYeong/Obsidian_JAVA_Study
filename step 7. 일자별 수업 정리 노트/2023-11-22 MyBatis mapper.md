# association 태그의 property와 javaType
- property가 가리키는 변수는 javaType이 가리키는 객체의 멤버 변수와 매핑할 이름이다.
- property 요소는 연관된 객체 내에서 해당 속성을 식별하는 데 사용된다.
- javaType 요소는 해당 태그가 생성할 연관된 객체의 자바 클래스를 지정하는 것이다.

# association 태그의 요소
```XML
<association property="loc" javaType="mybatis.vo.LocationsVO" select="locations.getLocations" column="loccode"/>
```
- property
	- 연관된 객체에 매핑할 속성의 이름을 지정한다.
	- 이 경우 연관된 객체의 속성 이름은 "loc"이다.
- javaType
	- 해당 태그가 생성할 연관된 객체의 자바 클래스를 지정한다.
	- 이 경우 연관된 객체의 자바 클래스는 "mybatis.vo.LocationsVO"이다.
- select
	- 연관된 객체를 조회하기 위해 사용할 SQL 문장을 지정한다.
- column
	- 연관된 객체와 매핑할 데이터베이스의 컬럼<font color="#7f7f7f">(열)</font>을 지정한다.

# collection 태그의 요소
```XML
<collection property="e_list" ofType="mybatis.vo.EmpVO" select="emp.search_deptno" column="deptno"/>
```
- property
	- 연관된 컬렉션 객체에 매핑할 속성의 이름을 지정한다.
- ofType
	- 해당 태그가 생성할 연관된 컬렉션의 요소 타입을 지정한다.
- select
	- 연관된 컬렉션을 조회하기 위해 사용할 SQL 문장을 지정한다.
- column
	- 연관된 컬렉션과 매핑할 데이터베이스의 컬럼(열)을 지정한다.

# 웹표준
- 어떠한 운영체제나 브라우저를 사용하더라도 웹 페이지가 동일하게 보이고 정상적으로 동일한 동작을 하게 하는 것.
- HTML
	- Hyper Text Markup Language
	- 웹 페이지의 구조와 내용을 정의하는 언어
	- 웹 페이지의 제목, 단락, 링크, 이미지 등을 표시하고 구성
- CSS
	- Cascading Style Sheets
	- 웹 페이지의 스타일과 레이아웃을 지정하는 언어
	- HTML로 작성된 요소들의 디자인을 꾸밀 수 있다.
	- 배경색, 글꼴, 크기, 위치 등을 설정하여 웹 페이지를 보다 시각적으로 꾸밀 수 있다.
- JavaScript
	- 웹 페이지의 동적인 기능을 구현하는 언어
	- 사용자와의 상호작용이 가능하고, 데이터를 동적으로 변경하거나 웹 페이지를 다양한 방식으로 제어할 수 있다.

# 이클립스 설정
- switch workspace
- new Dynamic Web Project 선택
- Target runtime : 서버
- 인코딩 설정
	- preferences > web > html files > encoding > ISO 10646/Unicode(UTF-8)
	- CSS Files, JSP Files 동일 적용

# HTML
- 주석(`<!-- -->`)에 중요한 내용 작성 금지, 소스 코드에 다 보인다.
## `<head>`
- 창 상단의 타이틀
- 창의 가장 상단에 이름, 탭 이름을 나타낸다.
- `<style></style>`
	- `article{width:400px;}` : `<article>` 영역의 폭이 400px로 설정된다.
	- `article{border:1px solid red;}` : `<article>` 경계를 빨간색 선으로 표현한다.
## `<body>`
- 메인 화면
- `<br/>` : 줄 바꿈
	- break의 줄임말
	- 줄 바꿈을 실행할 위치에 작성한다.
	- empty element이다. 열고 닫기를 동시에!
- `<h1></h1>` : 제목
	- h1, h2, h3, h4, h5, h6 순서로 하위 제목이 된다.
	- 알아서 줄바꿈이 되는 block-level이다.
		- block-level 요소는 일반적으로 새로운 줄에서 시작하며, 가로 폭 전체를 차지하고 수직으로 쌓이는 특징을 가지고 있다.
		- block-level 요소는 `<div>`, `<p>`, `<h1>` ~ `<h6>`, `<ul>`, `<ol>`, `<li>`, `<table>`, `<form>` 등이 있다.
		- block-level 요소는 다른 블록 레벨 요소나 inline-level 요소를 포함할 수 있으며, 일반적으로 너비(width), 높이(height), 여백(margin) 등의 스타일 속성을 지정할 우 싱고, CSS를 사용하여 레이아웃을 조정할 수 있다.
- `<pre></pre>` : 텍스트를 원본 형식 그대로 표시
	- block-level
	- 사전 형식<font color="#7f7f7f">(preformatted)</font>의 텍스트를 표시하기 위해 사용
	- 텍스트는 원본 형식 그대로 표시되며, 공백과 줄바꿈을 그대로 보존한다.
	- inline-level 요소를 사용할 수 있다.
- `<div></div>` : 하나의 영역을 구별
	- block-level
	- 구획을 나누거나 특정 요소들을 그룹화하는 데 사용된다.
	- 아무런 의미를 가지지 않는 컨테이너 요소로, 주로 CSS 스타일링이나 JavaScript를 적용하기 위해 사용된다.
- `<acticle></article>`
	- 독립적인, 완전한 하나의 콘텐츠 조각을 나타낸다.
	- 뉴스 기사, 블로그 게시물, 포럼 게시물 등과 같이 독립적인 정보를 표현
	- 해당 콘텐츠가 자체적으로 완결된 주제이며, 다른 콘텐츠와의 관계 없이 독립적ㅇ로 읽을 수 잇는 의미 단위로 간주된다.
- `<section></section>`
	- 문서의 섹션을 정의하는 데 사용된다.
	- 문서를 구획화하여 논리적인 구조를 만들기 위해 사용된다.
- `<xmp></xmp>`
	- HTML5에서는 폐기된 태그이다. 이전 버전에서는 텍스트를 원본 그대로 표시하기 위해 사용되었다. 하지만 사용은 가능하다.
	- 순수하게 텍스트만 가져와서 inline-level 요소, 즉 볼드와 같은 요소를 사용할 수 없다.
	- `<pre>` 태그가 그 기능을 대신한다.
- `<b></b>` : 볼드
- `<p></p>` : 단락
	- `<p/>`의 경우 줄바꿈으로 사용할 수 있다. 다만 새로운 문단을 시작하는 것으로 인식하여 한 줄을 비운다. 이 경우 여러 번 연속으로 사용한다고 해서 break 처럼 모두 적용되진 않는다.
- `<header>` : `<article>` 내에 사용하는 제목 태그
	- 내부에 `<h1>` ~ `<h6>`을 따로 지정하여 사용한다.

# HTML 태그 재정리
## 문서 구조 태그
- `<html>` : HTML 문서의 시작
	- `<head>` : 문서의 헤더를 정의
		- `<meta>` : 메타데이터를 정의
		- `<title>` : 문서의 제목을 정의
	- `<body>` : 문서의 본문을 정의
## 섹션 및 구획 태그
- `<body>` 태그 내에 정의한다.
---
- `<header>` : 문서나 섹션의 헤더를 정의
- `<footer>` : 문서나 섹션의 푸터를 정의
- `<nav>` : 내비게이션 링크를 그룹화
	- Java에서는 JMenu에 해당하는 메뉴이다.
- `<article>` : 독립적인 컨텐츠를 정의
- `<section>` : 문서의 섹션을 정의
## 목록과 표 태그
- `<ul>` : 순서가 없는 목록을 정의, `un-ordered list`
	- `<li>` : 목록 항목을 정의, `list`
- `<ol>` : 순서가 있는 목록을 정의, `ordered list`
	- `start="4"` : 4부터 시작한다.
	- `<li>` : 목록 항목을 정의
- `<dl>` : 설명 목록을 정의, `description list`
	- `<dt>` : 용어를 정의, `definition title`
	- `<dd>` : 설명을 정의, `definition description`
	![[Pasted image 20231122160334.png|300]]
- `<table>` : 표를 정의
	- `<thead>` : 제목 행, 생략 가능
		- `<tr>`
			- `<th>` : `table head`
	- `<tbody>` : 생략 불가
		- `<tr>` : 표의 행을 정의, `table row`
		- `<td>` : 표의 셀<font color="#7f7f7f">(열)</font>을 정의
			- `rowspan=[병합할 행 개수]`
				- 3개를 병합한다면 병합될 3개의 `<td>`는 삭제하여야 한다.
	- `<tfoot>` : 생략 가능
		- `<tr>`
			- `<td>`
				- `colspan=[병합할 열 개수]`
					- 5개를 병합한다면 병합될 4개의 `<td>`는 안 만들어도 된다.
## 텍스트 포맷팅과 링크 태그
- `<p>` : 단락을 정의
	- `<p/>` : 단락 종료, 한 줄 띄우기
- `<h1>`, `<h2>`, ..., `<h6>` : 제목을 정의
- `<a>` : 하이퍼링크를 정의
	- `href` : hyper reference
- `<em>` : 텍스트를 강조 - 이탤릭체
- `<strong>` : 텍스트를 강조 - 볼드체
- `<b>` : 텍스트를 강조 - 볼드체, 단순히 굵게 표시한다.
	- 최근 웹 표준에서는 시맨틱한 마크업을 선호하므로 `<em>`, `<strong>`을 더 권장한다.
- `<i>` : 텍스트를 강조 - 이탤릭, 단순히 기울인다. `<b>`와 동일
- `<s>` : 취소선
- `<font>`
	- `size = 1` : 글꼴 크기
	- `face="궁서"` : 글씨체
		- 궁서, 바탕, 굴림, ...
- `<sub>`, `<sup>` : subscript<font color="#7f7f7f">(아래첨자)</font>, superscript<font color="#7f7f7f">(위첨자)</font>
- `<hr/>` : 구분선
- `<img>`
	- `alt` : 대체 텍스트
	- `src` : 이미지 경로
	- `width`, `height` : 폭, 너비<font color="#7f7f7f">(px)</font>
		- 둘 중 하나만 써도 그 비율에 맞춰서 크기가 적용된다.
## 엔터티(Entity)
![[2. XML Extensible Markup Language#내장 엔터티]]
## 색상과 레이아웃
- `<head>`
	- `<style>`
		- `article{}` parameter
			- `width:400px;`
				- `<article>` 영역의 폭이 400px로 설정된다.
			- `border:1px solid red;`
				- `<article>` 경계를 빨간색 선으로 표현한다.
- `<body>`
	- `bgcolor` : `<body>`의 배경 색상 설정
		- `"blue"`
		- `"#f00"` : RGB 한 자리씩
		- `"#0000ff"` : RGB 두 자리씩
		- 색상 팔레트로 검색해보자.
	- `text` : `<body>`의 텍스트 색상 설정
		- `bgcolor`와 동일하다.
	- `background`
		- `"./images/back1.png"`
			- `"./"`은 현재 위치<font color="#7f7f7f">(webapp)</font>이다.
		- 기본적으로 바둑판식 배열로 들어간다.
	- `style="border: [border-width] [border-style] [border-color];"`
		- CSS 속성이다.
		- `border` : 테두리 지정
			- `width` : 테두리 두께, `1px`, `2px`, `3px`, ...
			- `style` : 테두리 종류, `solid`, `dashed`, `dotted`, ...
			- `color` : 테두리 색상, `red`, `#00ff00`, `rgba(255, 0, 0, 0.5)`, ...