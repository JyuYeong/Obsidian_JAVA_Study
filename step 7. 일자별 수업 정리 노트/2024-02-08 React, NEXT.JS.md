<font color="#4f81bd">새 프로젝트 생성:</font>
```
npm init react-app ex5
npm install @mui/material @emotion/react @emotion/styled
```
- init은 work 폴더에서, install은 ex5 폴더에서 실행하자.
# 1. 예제 1 : React, Material UI로 카드 만들기
## 1.1. Ex1_Card.js : 예제용 파일 생성
> src > pages > Ex1_Card.js

<font color="#4f81bd">return 문:</font>
```js
<Stack spacing={2} direction="row">
	<Box width="300px">
		<Card>
			
		</Card>
	</Box>
</Stack>
```
- 아래로 정렬되어야 하므로 direction 속성값은 row로 설정한다.

<font color="#4f81bd">Card 태그 내부:</font>
```js
<CardMedia 
	component="img" 
	height="140" 
	image="https://source.unsplash.com/random"
	alt="unsplash image"/>
```
- CardMedia의 component 속성값으로 img를 주면 이미지를 나타낸다.

```js
<CardContent>
	<Typography variant="h5" component="div">
	Card Test
	</Typography>
</CardContent>
```
- 내용을 넣기 위해 CardContent 태그로 둘러싸자.
## 1.2. App.js : return 문 수정
> src > App.js

```js
return (
	<div className="App">
	  <Ex1_Card/>
	</div>
);
```
- return 문 내부를 위와 같이 바꾸고 실행해 보자.

<font color="#4f81bd">결과:</font>
![[Pasted image 20240208100424.png]]
## 1.3. Ex1_Card.js : 본문 추가, 배열
> src > pages > Ex1_Card.js

[Material UI](https://mui.com)에 접속하여 Card Component를 참고하자.

<font color="#4f81bd">CardContent 안에서:</font>
```js
<Typography variant="body2" color="text.secondary">
Lizards are a widespread group of squamate reptiles, with over 6,000
species, ranging across all continents except Antarctica
</Typography>
```
- 본문 내용을 추가해 보자.

```js
<CardActions>
	<Button size="small">SHARE</Button>
	<Button size="small">LEARN MORE</Button>
</CardActions>
```
- CardActions 태그 안에서 만들고 싶은 버튼을 만들면 된다.

<font color="#4f81bd">결과:</font>
![[Pasted image 20240208101223.png]]

<font color="#4f81bd">Stack 태그의 direction 속성값을:</font>
- row로 하면 카드들이 옆으로 나란히
- column(default)으로 하면 아래로 나란히 배열된다.

<font color="#4f81bd">Box 태그의 width 속성으로 카드의 크기를 지정할 수도 있지만:</font>
- Card 태그의 sx 속성으로도 지정할 수도 있다.
	- 예 : `sx={{maxWidth: 345}}`
## 1.4. Ex1_Card.js : 카드 헤더 추가하기
> src > pages > Ex1_Card.js

```js
<CardHeader avatar={}/>
```
- CardContent 안에 CardHeader를 추가하자.

```js
<CardHeader avatar={
	<Avatar sx={{bgcolor:red[500]}}>JY</Avatar>
}/>
```
- Avatar를 추가해 보자.

<font color="#4f81bd">결과:</font>
![[Pasted image 20240208102143.png]]

<font color="#4f81bd">Icon Material 설치:</font>
```
npm install @mui/icons-material
```
- 서버가 실행 중이면 정지 후 설치를 진행하자.

<font color="#4f81bd">MoreVertIcon(더보기) 사용:</font>
```js
action={
	<IconButton aria-label="settings">
		<MoreVertIcon/>
	</IconButton>
}
```

<font color="#4f81bd">타이틀 달기:</font>
```js
title="Unsplash random image"
subheader="February 8, 2024"
```

<font color="#4f81bd">결과:</font>
![[Pasted image 20240208104754.png]]

<font color="#4f81bd">버튼을 아이콘으로 변경해 보자:</font>
```js
<CardActions>
	<IconButton>
		<ShareIcon/>
	</IconButton>
	<IconButton>
		<FavoriteIcon/>
	</IconButton>
</CardActions>
```
- CardActions 안의 버튼 태그를 삭제하고 위와 같이 입력해 보자.
- 자동 import가 안 되면 직접 입력하자.

결과:
![[Pasted image 20240208105616.png]]

[[#예제 1 Ex1_Card.js|> Ex1_Card.js 코드 전문 보기]]
# 2. NEXT.JS 처음 사용
## 2.1. NEXT.JS 사용 방법
1. [NEXT.JS](https://nextjs.org) 접속
2. Get Started > Installation

<font color="#4f81bd">Automatic Installation:</font>
```
npx create-next-app@latest
```
- 실행 중인 서버를 중지하고, 터미널에 위 명령어 + 프로젝트명을 입력한다.
- 이번 예제는 프로젝트명을 `next_ex1`으로 하자.

```
npm install -g npm@latest
```
- 만약 ENOENT 에러가 발생한다면 위 명령어를 먼저 입력하자.

```
create-next-app@14.1.0
Ok to proceed? (y)
```
- 위 문구가 뜨면 엔터를 눌러 수락하자.
- 이번 수업에서는 아래와 같이 선택하자.
	- TypeScript: NO
	- ESLint: YES
	- Tailwind CSS: NO
	- `src/` directory: YES
	- App Router: YES
	- import alias: NO

```
npm run dev
```
- 실행 명령어
- 현재 위치가 next_ex1인지 확인 하고 실행하자.
- 제대로 실행 되었다면 `localhost:3000` 경로로 들어가 확인하자.
	![[Pasted image 20240208111034.png]]
	이러면 제대로 나온 것
## 2.2. NEXT.JS 설명
Router 또는 서버 사이드 렌더링(SSR)이 자동화 되어 있는 React 플랫폼이다.
## 2.3. React의 App.js와 같은 곳
> src > app > page.js

```js
import Image from "next/image";
import styles from "./page.module.css";

export default function Home() {
  return (
    <main className={styles.main}>
      
    </main>
  );
}
```
- return 문 안의 내용을 삭제하자.

```js
<h1>한국ICT인재개발원 강남센터</h1>
```
- main 태그 사이에 이렇게 넣어보면 잘 나오는 것을 확인할 수 있다.
	![[Pasted image 20240208112327.png|300]]

[[#NEXT.JS 처음 사용 page.js|> page.js 코드 전문 보기]]
# 3. 예제 2 : NEXT.JS 사용하기
## 3.1. page.js : Route Test
> src > app > page.js

```js
function Page(){
    return(
        <div>
            <h2>Route Test</h2>
        </div>
    );
}
export default Page;
```
- 저장 후 URL에 `localhost:3000/pages/test`를 입력하면 자동으로 test 폴더 안의 page.js를 출력한다.
	![[Pasted image 20240208113852.png]]
- ---
- 이때 같은 폴더 내의 layout.js를 참조한다. 만약 없다면 src/app의 layout.js를 참조한다.
## 3.2. 레이아웃 잡기? 세팅

<font color="#4f81bd">MUI 설치:</font>
```
npm install @mui/material @emotion/react @emotion/styled
```

<font color="#4f81bd">logo.png 이미지 저장:</font>
- public > images > logo.png
## 3.3. 푸터 만들기
> src > component > Footer.js

```js
export default function Footer(){
    return (
        <div style={{width:'80%', margin:'auto', padding:'20px', textAlign:'center'}}>
            Copyright &copy; 2024 Korea ICT
        </div>
    );
}
```
- 실무에서는 태그에 직접 스타일을 적지 않는다. ~~유지보수 어려워 욕먹음~~
## 3.4. 메뉴 만들기
> src > component > Nav.js

```js
const navItem = ['Products', 'Color Cosmetics', 'Skin Cosmetics', 'About'];
const [anchorEl, setAnchorEl] = useState(null);
const open = Boolean(anchorEl);
```
- 변수 생성

```js
function handleClick(event){
	setAnchorEl(event.currentTarget);
}
```
- 현재 이벤트를 발생시킨 객체를 anchorEl에 저장되도록 한다.

```js
return (
	<nav id="header">
		<Grid container my={2}>
			{navItem.map((item) => (
				<Grid item key={item} xs={12} sm>
					<Box bgcolor='primary.light' p={2}>{item}</Box>
				</Grid>
			))}
		</Grid>
	</nav>
);
```
- return 문 작성
## 3.5. 헤더 만들기
> src > component > Header.js

```js
import Image from "next/image";
import Nav from "./Nav";

export default function Header(){
    return (
        <div>
            <Image src='/images/logo.png' alt='logo' width={300} height={70}
                style={{paddingTop:'20px', paddingLeft:'20px'}}/>
            <Nav/>
        </div>
    );
}
```
## 3.6. 레이아웃 수정
> src > app > layout.js

```js
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <Header/>
        {children}
        <Footer/>
      </body>
    </html>
  );
}
```

<font color="#4f81bd">결과:</font>
![[Pasted image 20240208123828.png|300]]
# 4. 예제 3 : 오픈 API 활용해 보기?
## 4.1. 비동기 처리를 위한 axios 설치
<font color="#4f81bd">터미널:</font>
```
npm i axios
```
- axios를 설치하자.

<font color="#4f81bd">프록시 설정:</font>
> next.config.mjs
```js
const nextConfig = {};
```
- nextConfig 변수를 선언하고 중괄호에 아래를 추가하자.

```js
reactStrictMode: true,
swcMinify: true,

async rewrites(){
	return[
		{
			source: "/api/:path*",
			destination: "https://makeup-api.herokuapp.com/api/:path*"
		}
	]
}
```
- 경로에 대한 설정이다.
- 이 설정을 통해 뒤에만 입력하면 된다?
## 4.1. page.js 수정
> src > app > page.js

```js
const api_uri = "/api/v1/products.json?brand=dior";
```
- API 데이터 소스(JSON) 경로를 저장한다.
- 위의 설정을 통해 `/api`부터 경로를 지정하면 된다.

```js
"use client"
```
- use client 선언을 해야 한다.

<font color="#4f81bd">호출을 위한 함수 선언:</font>
```js
function getData(){
	axios.get(api_uri).then(
		data => {console.log(data);}
	);
}
```
- axios를 이용해 비동기 처리를 하자.
- ajax에서 done인 것이, axios에서는 then이다.
- 이제 호출에 성공했을 때, 서버측에서 보내오는 JSON 자원이 data로 들어온다.

<font color="#4f81bd">최초 한 번만 호출하기 위해 사용되는 객체 useEffect:</font>
```js
useEffect(() => {
	getData();
}, []);
```
- 하나의 이벤트 같은 녀석이다.
- 첫 번째 인자로 함수, 두 번째 인자로 배열이 들어간다.
- 함수 영역은 딱 한 번만 호출해준다.
# 5. 예제 4 : 받은 JSON 데이터 뿌리기
## 5.1. 데이터를 뿌릴 컴포넌트를 만들자.
> src > component > ItemList.js

```js
export default function ItemList({list}){}
```
- `{list}`로 인자를 받는다.
	- 반드시 중괄호, 이름은 list여야 한다.

```js
<div>
	<Grid container>
		{list.map((item => (
			<Grid item xs={3} key={item.id}>
				<img src={item.image_link} alt={item.name}/>
			</Grid>
		)))}
	</Grid>
</div>
```
- return 문 안을 다음과 같이 작성하자.
## 5.2. page.js : ItemList 넣기
> src > app > page.js

```js
const [list, setList] = useState([]);
```
- useState를 이용해 list 변수를 선언한다.
- useState 괄호 안에 대괄호를 넣으면 빈 배열이 하나 들어간다.

<font color="#4f81bd">axios.get의 then 부분을 수정하자:</font>
```js
data => {
	setList(data.data);
}
```
- JSON에서 우리가 필요한 데이터는 data 안의 data에 들어 있다.
- 이때 해당 파일의 상단에는 `"use client"`가 선언되어 있어야 한다.

<font color="#4f81bd">return 문에 ItemList를 list 인자와 함께 호출하자:</font>
```js
<main className={styles.main}>
  <ItemList list={list}/>
</main>
```

<font color="#4f81bd">짜잔:</font>
![[Pasted image 20240208145310.png|400]]
## 5.3. 조금 더 꾸미기
> src > component > ItemList.js

```js
<Grid item xs={3} key={item.id}>
	<img src={item.image_link} alt={item.name}/>
	<strong>{item.name}</strong><br/>
	<span>{item.category}</span>
</Grid>
```
- Grid 안을 위와 같이 수정하자.
# 예제 5 : 특정 파일에게만 적용하는 CSS
## 5.1. CSS 파일 생성
> public > css > ItemList.module.css

<font color="#4f81bd">ItemList.js 파일에만 적용되는 CSS 파일이다.:</font>
```CSS
.wrap {
    padding-bottom: 20px;
    text-align: center;
}

.img_item {
    display: block;
    margin: 0 auto;
}

.title_item {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
    width: 160px;
    margin: 10px auto;
}

.txt_info {
    display: block;
    margin-bottom: 10px;
    color: #999;
    text-align: center;
}

.num_price {
    font-size: 17px;
    color: #00bcd4;
    font-weight: bold;
    text-align: center;
}
```
## 5.2. ItemList에 CSS를 적용하자.
> src > component > ItemList.js

```js
import styles from "/public/css/ItemList.module.css";
```
- 사용을 위해 임포트 하자.

```js
<Grid item xs={3} key={item.id}>
	<img src={item.image_link} alt={item.name} className={styles.img_item}/><br/>
	<p><strong className={styles.title_item}>{item.name}</strong></p>
	<p><span className={styles.txt_info}>{item.category} &nbsp; {item.product_type}</span></p>
	<p className={styles.num_price}><strong>${item.price}</strong></p>
</Grid>
```
- Grid에 CSS 적용을 위한 클래스를 부여하자.
# 6. 예제 5 : 슬라이스
## 6.1. page.js 수정
> src > app > page.js

```js
<main className={styles.container}>
	<h2>베스트 상품</h2>
	<Divider/>
	<ItemList list={list}/>
</main>
```
- main의 클래스를 main에서 container로 바꾸었다.
- ItemList에 있던 h2와 Divider를 page.js에 가져와 붙이자.

```js
<main className={styles.container}>
	<h2>베스트 상품</h2>
	<Divider/>
	<ItemList list={list.slice(0, 9)}/>
</main>
```
- list에 slice를 이용하여 원하는 수만큼 끊어서 볼 수 있다.
- 0, 9이면 처음부터 9개를 가져온다.

```js
<h2>신상품</h2>
<Divider/>
<ItemList list={list.slice(9)}/>
```
- 이렇게 하면 9번째 부터 끝까지 출력한다.

```js
<main className={styles.container}>
  <div style={{width:'80%', margin:'auto', paddingTop:'20px'}}>
	<h2>베스트 상품</h2>
	<Divider/>
	<ItemList list={list.slice(0, 9)}/>
  </div>
  <div style={{width:'80%', margin:'auto', paddingTop:'20px'}}>
	<h2>신상품</h2>
	<Divider/>
	<ItemList list={list.slice(9)}/>
  </div>
</main>
```
- div로 둘러싸고 스타일을 입혔다.
# 7. 예제 6 : 
## 7.1. 뷰
> src > app > views > `[id]` > page.js
> 이때 `[id]`는 가변적인 이름이다.

```js
import { useRouter } from "next/navigation";

export default function Page({params}){
    const router = useRouter();
    const id = `${params.id}`;
}
```
- Router는 next/navigation의 Router이다.
	- 이때 컴포넌트 이름은 꼭 대문자로 시작하여야 한다. Page 처럼.
	- 커스텀 HOOK의 이름은 "use"로 시작하여야 한다.
- `${params.id}`를 쓸 때 꼭 주변을 backtick으로 감싸주어야 한다.

```js
return (
	<p>
		Post: {id}
	</p>
);
```
- return 문을 위와 같이 작성한다.

[[#예제 7 page.js|> page.js 코드 전문 보기]]
## 7.2. ItemList 내용 추가
> src > component > ItemList.js

```js
<Grid item xs={3} key={item.id}>
	<Link href='/views/[id]' as={'/views/' + item.id}>
		<div className={styles.wrap}>
			<img src={item.image_link} alt={item.name} className={styles.img_item}/><br/>
			<p><strong className={styles.title_item}>{item.name}</strong></p>
			<p><span className={styles.txt_info}>{item.category} &nbsp; {item.product_type}</span></p>
			<p className={styles.num_price}><strong>${item.price}</strong></p>
		</div>
	</Link>
</Grid>
```

[[#예제 7 ItemList.js|> ItemList.js 코드 전문 보기]]
# A. 부록
## 이미지를 랜덤하게 뿌리는 사이트
- [랜덤이미지뽑기](https://images.unsplash.com)접속
# B. 코드 전문
## 예제 1 : Ex1_Card.js
> src > pages > Ex1_Card.js
```js
import { Avatar, Box, Button, Card, CardActions, CardContent, CardHeader, CardMedia, IconButton, Stack, Typography } from "@mui/material";
import { red } from "@mui/material/colors";
import MoreVertIcon from '@mui/icons-material/MoreVert';
import FavoriteIcon from '@mui/icons-material/Favorite';
import ShareIcon from '@mui/icons-material/Share';
import React from "react";

export default function Ex1_Card(){
    return (
        <Stack spacing={2} direction="row">
            <Box>
                <Card sx={{maxWidth: 345}}>
                    <CardMedia 
                        component="img" 
                        height="140" 
                        image="https://source.unsplash.com/random"
                        alt="unsplash image"/>
                    <CardContent>
                        <CardHeader avatar={
                            <Avatar sx={{bgcolor:red[500]}}>JY</Avatar>
                        }
                        action={
                            <IconButton aria-label="settings">
                                <MoreVertIcon/>
                            </IconButton>
                        }
                        title="Unsplash random image"
                        subheader="February 8, 2024"
                        />
                        <Typography variant="h5" component="div">
                        Card Test
                        </Typography>
                        <Typography variant="body2" color="text.secondary">
                        Lizards are a widespread group of squamate reptiles, with over 6,000
                        species, ranging across all continents except Antarctica
                        </Typography>
                        <CardActions>
                            <Button size="small">SHARE</Button>
                            <Button size="small">LEARN MORE</Button>
                        </CardActions>
                    </CardContent>
                </Card>
            </Box>
            <Box width="300px">
                <Card>
                    <CardMedia 
                        component="img" 
                        height="140" 
                        image="https://source.unsplash.com/random"
                        alt="unsplash image"/>
                    <CardContent>
                        <Typography variant="h5" component="div">
                        Card Test
                        </Typography>
                        <Typography variant="body2" color="text.secondary">
                        Lizards are a widespread group of squamate reptiles, with over 6,000
                        species, ranging across all continents except Antarctica
                        </Typography>
                        <CardActions>
                            <IconButton>
                                <ShareIcon/>
                            </IconButton>
                            <IconButton>
                                <FavoriteIcon/>
                            </IconButton>
                        </CardActions>
                    </CardContent>
                </Card>
            </Box>
        </Stack>
    );
}
```
## NEXT.JS 처음 사용 : page.js
> src > app > page.js
```js
import styles from "./page.module.css";

export default function Home() {
  return (
    <main className={styles.main}>
      <h1>한국ICT인재개발원 강남센터</h1>
    </main>
  );
}
```
## 예제 2 : page.js
> src > app > page.js
```js
function Page(){
    return(
        <div>
            <h2>Route Test</h2>
        </div>
    );
}
export default Page;
```
## 예제 2 : Footer.js
> src > component > Footer.js
```js
export default function Footer(){
    return (
        <div style={{width:'80%', margin:'auto', padding:'20px', textAlign:'center'}}>
            Copyright &copy; 2024 Korea ICT
        </div>
    );
}
```
## 예제 2 : Nav.js
> src > component > Nav.js
```js
"use client"
import { Box, Grid } from "@mui/material";
import React, { useState } from "react";

export default function Nav(){
    const navItem = ['Products', 'Color Cosmetics', 'Skin Cosmetics', 'About'];
    const [anchorEl, setAnchorEl] = useState(null);
    const open = Boolean(anchorEl);

    function handleClick(event){
        setAnchorEl(event.currentTarget);
    }
    
    return (
        <nav id="header">
            <Grid container my={2}>
                {navItem.map((item) => (
                    <Grid item key={item} xs={12} sm>
                        <Box bgcolor='primary.light' p={2}>{item}</Box>
                    </Grid>
                ))}
            </Grid>
        </nav>
    );
}
```
## 예제 3 : next.config.mjs
> next.config.mjs
```js
/** @type {import('next').NextConfig} */
const nextConfig = {
    reactStrictMode: true,
    swcMinify: true,

    async rewrites(){
        return[
            {
                soure: "/api/:path*",
                destination: "http://makeup-api.herokuapp.com/api/:path*"
            }
        ]
    }
};

export default nextConfig;
```
## 예제 3 : page.js
> src > app > page.js
```js
"use client"
import Image from "next/image";
import styles from "./page.module.css";
import { useEffect } from "react";
import axios from "axios";

export default function Home() {
  const api_uri = "/api/v1/products.json?brand=dior";

  function getData(){
    axios.get(api_uri).then(
      data => {console.log(data);}
    );
  }

  useEffect(() => {
    getData();
  }, []);

  return (
    <main className={styles.main}>
      <h2>Route Test</h2>
    </main>
  );
}
```
## 예제 4 : ItemList.js
> src > component > ItemList.js
```js
import { Divider, Grid } from "@mui/material";

export default function ItemList({list}){
    return (
        <div>
            <h2>베스트 상품</h2>
            <Divider/>
            <Grid container>
                {list.map((item => (
                    <Grid item xs={3} key={item.id}>
                        <img src={item.image_link} alt={item.name}/><br/>
                        <strong>{item.name}</strong><br/>
                        <span>{item.category} &nbsp; {item.product_type}</span>
                    </Grid>
                )))}
            </Grid>
        </div>
    );
}
```
## 예제 4 : page.js
> src > app > page.js
```js
"use client"
import axios from "axios";
import { useEffect, useState } from "react";
import styles from "./page.module.css";
import ItemList from "@/component/ItemList";

export default function Home() {
  const api_uri = "/api/v1/products.json?brand=maybelline";
  const [list, setList] = useState([]);

  function getData(){
    axios.get(api_uri).then(
      data => {
        setList(data.data);
      }
    );
  }

  useEffect(() => {
    getData();
  }, []);

  return (
    <main className={styles.main}>
      <ItemList list={list}/>
    </main>
  );
}
```
## 예제 7 : page.js
> src > app > views > `[id]` > page.js
```js
"use client"
import { useRouter } from "next/navigation";

export default function Page({params}){
    const router = useRouter();
    const id = `${params.id}`;

    return (
        <p>
            Post: {id}
        </p>
    );
}
```
## 예제 7 : ItemList.js
> src > component > ItemList.js
```js
import { Grid } from "@mui/material";
import styles from "/public/css/ItemList.module.css";
import Link from "next/link";

export default function ItemList({list}){
    return (
        <div>
            <Grid container>
                {list.map((item => (
                    <Grid item xs={3} key={item.id}>
                        <Link href='/views/[id]' as={'/views/' + item.id}>
                            <div className={styles.wrap}>
                                <img src={item.image_link} alt={item.name} className={styles.img_item}/><br/>
                                <p><strong className={styles.title_item}>{item.name}</strong></p>
                                <p><span className={styles.txt_info}>{item.category} &nbsp; {item.product_type}</span></p>
                                <p className={styles.num_price}><strong>${item.price}</strong></p>
                            </div>
                        </Link>
                    </Grid>
                )))}
            </Grid>
        </div>
    );
}
```