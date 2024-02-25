# window.onload
```javascript
window.onload = function() {
	console.log("초기화!");
}
```
- 위와 같이 작성하는 것을 익명 함수라고 한다.
- 아래와 같은 의미이다.
```HTML
<body onload="init()"></body>
```
```javascript
function init() {
	console.log("초기화!");
}
```

# JQuery 설치 및 세팅
- [JQuery](https://jquery.com) 접속
- Download > Past Releases(가장 아래) > jQuery CDN
- jQuery 3.x > minified
- copy to clipboard
- 작업 중인 HTML 파일 > script 태그 바로 위에 붙여 넣기!

# JQuery 사용 예제
- 태그 이름으로 요소 검색
	- `getElementsByTagName()`