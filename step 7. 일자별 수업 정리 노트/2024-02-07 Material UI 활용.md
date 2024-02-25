# 1. React 프로젝트 생성 및 실행하기
VS Code Terminal:
1. `npm init react-app ex3`
2. `npm install react-router-dom@6`

# 2. Material UI 사용하기
1. [Material UI](https://mui.com) 접속
2. Discover the Core libraries > Material UI > Get started > Installation 참고
3. VS Code Terminal
	- `npm install @mui/material @emotion/react @emotion/styled`
	- 프로젝트 생성 시 항상 설치할 것

> [!caution] 현재 프로젝트 경로인지 잘 확인하여 진행할 것

# 3. 예제 1 : Material UI Grid
## 3.1. Header.js
> src > pages > Header.js

[[#예제 1 Header.js|> Header.js 코드 전문 보기]]
## 3.2. Nav.js
> src > pages > Nav.js

[[#예제 1 Nav.js|> Nav.js 코드 전문 보기]]
## 3.3. Content.js
> src > pages > Content.js

<font color="#548dd4">MUI 버튼 디자인 사용하기:</font>
```js
<div className="btn_area">
	<Button variant="contained">버튼1</Button>&nbsp;&nbsp;
	<Button variant="outlined">버튼2</Button>&nbsp;&nbsp;
	<Button variant="contained">버튼3</Button>&nbsp;&nbsp;
</div>
```
- Material의 Button을 import 한다.
- variant를 이용하여 버튼 모양을 달리 할 수 있다.

```js
<Button variant="contained" color="inherit">버튼3</Button>&nbsp;&nbsp;
<Button variant="contained" color="secondary">버튼4</Button>&nbsp;&nbsp;
<Button variant="contained" color="success">버튼5</Button>&nbsp;&nbsp;
<Button variant="contained" color="error">버튼5</Button>&nbsp;&nbsp;
<Button variant="contained" color="info">버튼6</Button>&nbsp;&nbsp;
<Button variant="contained" color="warning">버튼7</Button>&nbsp;&nbsp;
```
- 버튼의 색상은 위와 같이 줄 수 있다.
- `#colorCode`로 주는 것은 불가능하다.

[[#예제 1 Content.js|> Content.js 코드 전문 보기]]
## 3.4. App.js
> src > App.js

```js
<Container fixed>
	<Header/>
	<Nav/>
	<Content/>
</Container>
```
- App()의 return 문 안에 다음과 같이 자리를 잡는다.

```js
<Container fixed>
	<Header/>
	<Grid container>
		<Grid xs="3">
			<Nav/>
		</Grid>
		<Grid xs="9">
			<Content/>
		</Grid>
	</Grid>
</Container>
```
- Grid를 이용해 Nav와 Content를 감싸고, 이들을 container 속성의 Grid로 감싼다.
- Grid xs는 총 12개의 가상 컬럼을 가지고 있다. 이 비율을 나눠서 설정해야 한다.

[[#예제 1 App.js|> App.js 코드 전문 보기]]
## 3.5. 결과 화면
![[Pasted image 20240207153705.png]]
# 4. 예제 2 : Material UI Grid
## 4.1. Left.js
> src > pages > Left.js

```js
const styles = {
	backgroundColor: "orange",
	width: "100%",
	height: "100vh",
	margin: 0,
	padding: 0
}
```
- vh는 viewport + height

[[#예제 2 Left.js|> Left.js 코드 전문 보기]]
## 4.2. Right.js
> src > pages > Right.js

```js
const navItems = ["Home", "Products", "Content", "Board", "About"];
```
- 메뉴에 들어갈 아이템들이다.
- return 문의 div 안에 이 메뉴들이 들어갈 박스를 만들어야 한다.

```js
<div style={style}>
	<Box sx={{display : {xs : 'none', sm : 'block'}}}>
		{navItems.map()}
	</Box>
</div>
```
- navItems는 배열이므로 반복문, 즉 map 메서드를 사용한다.
- MUI에서는 Box에 스타일을 입힐 때 sx 속성을 사용한다.
- `sm : 'block'`은 sm일 때, display를 block으로 지정하겠다는 의미이다.

```js
{navItems.map((item) => (
	<Button key={item} sx={{
		color : '#000', 
		fontSize : '16px', 
		fontWeight : 'bold'
	}}>
		{item}
	</Button>
))}
```
- 반복문을 돌릴 때에는 반드시 key를 넣어주어야 오류가 나지 않는다.
- 이제 navItems 배열에 따라 버튼이 생성되고, 화면 크기에 따라 버튼의 표시 여부가 결정된다. 예를 들어 <font color="#9bbb59">extra-small 화면 크기에서는 아무 버튼도 표시되지 않고, small 이상의 크기에서는 버튼이 표시</font>된다.
## 4.3. App.js
> src > App.js

```js
<Container fixed>
	<div className='App'>
		<grid container>
			<Grid item={true} xs={2}>
				<Left/>
			</Grid>
			<Grid item={true} xs={10}>
				<Right/>
			</Grid>
		</Grid>
	</div>
</Container>
```
## 4.4. 결과 화면
![[Pasted image 20240207155645.png]]
# 5. 예제 3 : Material UI Grid
<font color="#f79646">프로젝트 생성 시 MUI 설치를 잊지 말자!</font>
## 5.1. Ex1_Box.js
> src > pages > Ex1_Box.js

<font color="#548dd4">export default 구문을 생략하기:</font>
```js
import React from "react";

function Ex1_Box() {
	return (
	
	);
}
export default Ex1_Box;
```
- 위의 식에서 `export default Ex1_Box;` 부분을 아래와 같이 코드를 수정해 생략할 수 있다.

```js
export default function Ex1_Box() {
	return (
	
	);
}
```

<font color="#548dd4">내부에서 스타일 입히기:</font>
```js
<div style = {{padding: '20px'}}>
	<Box sx={{
		backgroundColor: 'primary.main',
		color: 'white',
		height: '100px',
		width: '100px',
		padding: '15px',
		'&:hover': {backgroundColor: 'primary.light'}
	}}>BOX테스트</Box>
	<Box sx={{
		backgroundColor: 'success.light',
		color: 'white',
		height: '100px',
		width: '100px',
		padding: '15px',
		'&:hover': {backgroundColor: 'success.main'}
	}}></Box>
	<Box sx={{
		backgroundColor: 'primary.main',
		color: 'white',
		height: '100px',
		width: '100px',
		padding: '15px',
		'&:hover': {backgroundColor: 'primary.light'}
	}}>BOX테스트3</Box>
</div>
```

<font color="#548dd4">Grid 이용하기:</font>
```js
<Grid container mt={5}>
	<Grid xs={3}>
		<Box bgcolor='primary.light' p={2}>Item1</Box>
	</Grid>
	<Grid xs={9}>
		<Box bgcolor='primary.main' p={2}>Item2</Box>
	</Grid>
</Grid>
```
- Grid container의 mt는 margin-top을 의미한다.
- Grid를 사용하면 어느 디바이스든 지정해둔 비율로 보여준다. #반응형
- Grid의 xs 속성값의 합이 12를 넘어가면 자동으로 다음 줄에 자리를 잡는다.
- 하나의 Grid가 한 줄을 다 차지하고 싶다면 xs 속성값을 12로 설정하자.

[[#예제 4 Ex1_Box.js|> Ex_Box.js 코드 전문 보기]]
# 6. 예제 4 : Material UI 반응형 구조
## 6.1. Ex1_Box.js : 추가
> src > pages > Ex1_Box.js

메뉴와 같이 하나의 행으로 있다가 화면이 좁아지면 컬럼형으로 자리잡는 구조를 만들어 보자.

---

박스 크기 : xs<font color="#7f7f7f">(extra-small)</font> < sm<font color="#7f7f7f">(small)</font> < md<font color="#7f7f7f">(medium)</font> < lg<font color="#7f7f7f">(large)</font> < xl<font color="#7f7f7f">(extra-large)</font>

|    크기     |   해상도    |       설명       |
|:-----------:|:-----------:|:----------------:|
| extra-small | 576px 미만  | 휴대폰 세로 방향 |
|    small    | 576px 이상  | 휴대폰 가로 방향 |
|   medium    | 768px 이상  |      태블릿      |
|    large    | 992px 이상  |     데스크탑     |
| extra-large | 1200px 이상 | 데스크탑(와이드) |

---

```js
<Grid container mt={5}>
	<Grid item xs={12} sm={true}>
		<Box bgcolor='primary.light' p={2}>Item1</Box>
	</Grid>
	<Grid item xs={12} sm={true}>
		<Box bgcolor='primary.light' p={2}>Item2</Box>
	</Grid>
	<Grid item xs={12} sm={true}>
		<Box bgcolor='primary.light' p={2}>Item3</Box>
	</Grid>
	<Grid item xs={12} sm={true}>
		<Box bgcolor='primary.light' p={2}>Item4</Box>
	</Grid>
	<Grid item xs={12} sm={true}>
		<Box bgcolor='primary.light' p={2}>Item5</Box>
	</Grid>
</Grid>
```
- return 문 Grid 태그 아래에 위 태그를 추가한다.
- sm에는 원래 true가 default이므로 sm만 써도 된다.

[[#예제 4 Ex1_Box.js|> Ex1_Box.js 코드 전문 보기]]
# A. 부록
# B. 코드 전문
## 예제 1 : Header.js
> src > pages > Header.js
```js
import React from "react";

function Header(){
    return(
        <header>
            <h1>WELCOME</h1>
        </header>
    );
}
export default Header;
```
## 예제 1 : Nav.js
> src > pages > Nav.js
```js
import React from "react";

function Nav(){
    return(
        <nav>
            <ol>
                <li>메뉴1</li>
                <li>메뉴2</li>
                <li>메뉴3</li>
            </ol>
        </nav>
    );
}
export default Nav;
```
## 예제 1 : Content.js
> src > pages > Content.js
```js
import { Button } from "@mui/material";
import React from "react";

function Content(){
    return(
        <article>
            <section>
            64년 만의 아시아축구연맹(AFC) 아시안컵 우승 도전에 실패한 
            한국 축구대표팀의 위르겐 클린스만 감독은 사퇴 의사가 없다고 
            딱 잘라 말했다.
            클린스만 감독이 지휘하는 한국 축구 대표팀은 7일(한국시간) 
            카타르 알라이얀의 아흐마드 빈 알리 스타디움에서 
            열린 2023 AFC 카타르 아시안컵 준결승전에서 
            요르단에 0-2로 완패해 탈락했다.
            </section>
            <section>
            클린스만 감독은 경기 후 기자회견에서 '해임에 대한 이야기가 나올 텐데 
            계속 감독직을 수행할 것이냐'는 질의에 "난 어떤 조치도 생각하고 
            있는 게 없다"고 밝혔다.
            그러면서 "팀과 한국으로 돌아가 이번 대회를 분석하고, 
            대한축구협회와 어떤 게 좋았고, 좋지 않았는지를 논의해보려 한다"며 
            사퇴 의사가 없음을 분명히 했다.
            </section>
            <section>
            클린스만 감독은 다음 목표로 2026 국제축구연맹(FIFA) 북중미 월드컵을 제시했다. 
            그는 "2년 반 동안 북중미 월드컵을 목표로 팀이 더 발전해야 한다. 
            매우 어려운 예선도 치러야 한다"며 "우리 앞에 쌓인 과제가 많다"고 말했다.
            </section>
            <section>
            대회 결과에 책임질 의사가 있냐는 물음에는 "감독으로서 이렇게 
            원했던 목표를 이루지 못하면 당연히 책임을 져야 한다"고 답했다.
            클린스만 감독이 언급한 '책임지는 행위'는 사퇴가 아닌 
            '분석과 발전'이었다. 클린스만 감독은 "목표를 이루지 못했기 때문에 
            더 많이 분석할 필요가 있다. 대회의 모든 경기를 돌아볼 필요가 있다"고 
            밝혔다.
            </section>
            <div className="btn_area">
                <Button variant="contained">버튼1</Button>&nbsp;&nbsp;
                <Button variant="outlined">버튼2</Button>&nbsp;&nbsp;
                <Button variant="contained" color="inherit">버튼3</Button>&nbsp;&nbsp;
                <Button variant="contained" color="secondary">버튼4</Button>&nbsp;&nbsp;
                <Button variant="contained" color="success">버튼5</Button>&nbsp;&nbsp;
                <Button variant="contained" color="error">버튼5</Button>&nbsp;&nbsp;
                <Button variant="contained" color="info">버튼6</Button>&nbsp;&nbsp;
                <Button variant="contained" color="warning">버튼7</Button>&nbsp;&nbsp;
            </div>
        </article>
    );
}
export default Content;
```
## 예제 1 : App.js
> src > App.js
```js
import './App.css';
import Header from './pages/Header';
import Nav from './pages/Nav';
import Content from './pages/Content';
import { Container, Grid } from '@mui/material';

function App() {
  return (
    <Container fixed>
      <Header />
      <Grid container>
        <Grid xs="2">
          <Nav />
        </Grid>
        <Grid xs="10">
          <Content />
        </Grid>
      </Grid>
    </Container>
  );
}

export default App;
```
## 예제 2 : Left.js
> src > pages > Left.js
```js
import React from "react";

const styles = {
	backgroundColor: "orange",
	width: "100%",
	height: "100vh",
	margin: 0,
	padding: 0
}

function Left() {
	return (
		<div style={styles}>
			<h1 style={{margin : 0, padding : 0}}>WELCOME</h1>
		</div>
	);
}
export default Left;
```
## 예제 2 : Right.js
> src > pages > Right.js
```js
import React from "react";

const styles = {
	backgroundColor: "orange",
	width: "100%",
	height: "100vh",
	margin: 0,
	padding: 0,
	paddingTop: "20px"
}

function Right() {
	const navItems = ["Home", "Products", "Content", "Board", "About"];
	
	return (
		<div style={style}>
			<Box sx={{display : {xs : 'none', sm : 'block'}}}>
				{navItems.map((item) => (
					<Button key={item} sx={{
						color : '#000', 
						fontSize : '16px', 
						fontWeight : 'bold'
					}}>
						{item}
					</Button>
				))}
			</Box>
		</div>
	);
}
export default Right;
```
## 예제 4 : Ex1_Box.js
> src > page > Ex1_Box.js
```js
import { Box, Stack, Divider, Grid } from "@mui/material";
import React from "react";

export default function Ex1_Box() {
	return (
		<div style={{ padding: '20px' }}>
			<Stack direction="row" spacing={2}>
				<Box sx={{
					border: '1px solid',
					backgroundColor: 'primary.main',
					color: 'white',
					height: '100px',
					width: '100px',
					padding: '15px',
					'&:hover': {backgroundColor: 'primary.light'}
				}}>BOX테스트1</Box>
				
				<Divider orientation="vertical" flexItem/>
				
				<Box sx={{
					backgroundColor: 'success.light',
					color: 'white',
					height: '100px',
					width: '100px',
					padding: '15px',
					'&:hover': {backgroundColor: 'success.main'}
				}}></Box>
				
				<Divider orientation="vertical" flexItem/>
				
				<Box sx={{
					backgroundColor: 'primary.main',
					color: 'white',
					height: '100px',
					width: '100px',
					padding: '15px',
					'&:hover': {backgroundColor: 'primary.light'}
				}}>BOX테스트3</Box>
			</Stack>
			
			<Grid container mt={5}>
				<Grid xs={3}>
					<Box bgcolor='primary.light' p={2}>Item1</Box>
				</Grid>
				<Grid xs={9}>
					<Box bgcolor='primary.main' p={2}>Item2</Box>
				</Grid>
				
				<Grid xs={6}>
					<Box bgcolor='success.main' p={2}>Item1</Box>
				</Grid>
				<Grid xs={3}>
					<Box bgcolor='success.light' p={2}>Item2</Box>
				</Grid>
				<Grid xs={3}>
					<Box bgcolor='success.light' p={2}>Item1</Box>
				</Grid>
				
				<Grid xs={12}>
					<Box bgcolor='error.main' p={2}>Item1</Box>
				</Grid>
			</Grid>
			
			<Grid container mt={5}>
				<Grid item xs={12} sm={true}>
					<Box bgcolor='primary.light' p={2}>Item1</Box>
				</Grid>
				<Grid item xs={12} sm={true}>
					<Box bgcolor='primary.light' p={2}>Item2</Box>
				</Grid>
				<Grid item xs={12} sm={true}>
					<Box bgcolor='primary.light' p={2}>Item3</Box>
				</Grid>
				<Grid item xs={12} sm={true}>
					<Box bgcolor='primary.light' p={2}>Item4</Box>
				</Grid>
				<Grid item xs={12} sm={true}>
					<Box bgcolor='primary.light' p={2}>Item5</Box>
				</Grid>
			</Grid>
		</div>
	);
}
```