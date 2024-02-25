# select 태그의 option index를 선택하는 방법
- `selectedIndex`
- 예제
	```HTML
	<form action="" method="post">
		<label for="m_car">차종</label>
		<select id="m_car" name="car" onchange="ex(this)">
			<option>::선택하세요::</option>
			<option value="G">제네시스</option>
			<option value="A">아우디R8</option>
			<option value="B">벤틀리</option>
			<option value="P">포르쉐</option>
		</select>
	</form>
	```
	```javascript
	if(obj.selectedIndex == 0) return;
	console.log(obj.selectedIndex + " / " + obj.value);
	```

# 검색한 요소의 자식 요소들을 배열로 받는 방법
- `children`
- 예제
	```javascript
	let ar = table.children;
	let tbody = ar[2];
	tbody.innerHTML += str;
	```
	- 원하는 자식 요소를 index로 선택한다.
	- 만약 세 번째 자식을 선택한다면 index 값은 2이다.

# 태그(요소)를 만들어 주는 함수
- `createElement("[tag]")`
- 예제
	```javascript
	let tr = document.createElement("tr"); // <tr></td> 생성!
	```
	- <font color="#d99694">주의!</font> 쌍따옴표 꼭 사용할 것

# 자식 태그 추가
- `appendChild()`

# 이벤트
- `onload` : 로드하면
- `onclick` : 클릭하면
- `onchange` : value가 바뀌면
- `onmouseover` : 마우스가 올라오면
- `onmouseout` : 마우스가 이탈하면

# 스타일 적용
- `style`
- 예제
	```javascript
	td2.style.textAlign = "center";
	```
	- CSS에서 `text-align`이지만 스크립트 상에서는 대쉬(`-`)를 지우고 연결되는 앞 문자를 대문자로 바꾼다.

# onclick에 함수를 지정하려면
```javascript
btn.onclick = function() {

}
```

# 행 index 선택과 삭제
```javascript
tr.onmouseover = function() {
	table.rowIdx = this.rowIndex; // this는 tr을 의미한다.
}
```

```javascript
table.deleteRow(table.rowIdx);
```
- `table.rowIdx`가 반환하는 index 값에 해당하는 행을 삭제한다.

# 폼(form) 예제
## form 내부의 input에게 id가 없어도 form 내부에 있다면 찾아갈 수 있다?
- 현재 문서에서 첫 번째 form 안에 name이 bYear인 요소의 value를 가져온다.
	- `document.forms[0]`
		- 현재 문서의 form들 중 첫 번째 form 선택
		- `document.forms[0].bYear.value;`와 같이 쓸 수 있다.

# 정규표현식
- replace(a, b)
	- a의 정규표현식으로 찾은 문자열을 b로 교체한다.
- `replace(/^\s+|\s+|\s+$/g, "")`는 모든 공백을 제거한다.
	- `/^` : 문자열의 시작
	- `\s` : 공백 한 칸(`" "`)
	- `+` : 1회 이상 반복되는 경우
	- `|` : OR
	- `%/` : 문자열의 끝
	- `g` : 앞의 내용을 찾기