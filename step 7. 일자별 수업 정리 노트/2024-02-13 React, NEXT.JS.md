[[2024-02-08 React, NEXT.JS]] 이어서 진행

# 1. 예제 1 : 아이템 상세 보기 페이지 만들기
## 1.1. page.js : 비동기 처리 추가
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
- 이전 작업 내용
- id 값은 그레이브<font color="#7f7f7f">(백틱, Backtick, Grave accent, backquote)</font>로 둘러싸야 한다.

```js
const [item, setItem] = useState({});
const api_uri = `/api/v1/products/${id}.json`;
```
- item이라는 인자를 넘기기 위해 변수를 선언하자.
	- useState를 꼭 import 하자.
- 위에서 받은 id를 가지고 제품 정보를 받기 위해 다시 비동기식 통신을 수행하자.
- api_uri 값도 그레이브로 둘러싸야 한다.

<font color="#548dd4">비동기 처리를 위한 함수 생성:</font>
```js
function getItem(){
	axios.get(api_uri).then((res) => {});
}
```
- 비동기 처리를 위해 함수를 생성하자.
- axios를 꼭 import 하자.

```js
setItem(res.data);
```
- 위의 중괄호 내부에 입력하면 9번 행의 item에 저장한다.

<font color="#548dd4">한 번만 호출되도록 하는 함수 생성:</font>
```js
useEffect(() => {});
```
- useEffect를 꼭 import 하자.
- 익명 함수를 생성한다.

```js
if(id && id > 0) getItem();
```
- 만약 id라는 변수가 비어있지 않고, 0보다 클 때만 수행하자.

<font color="#548dd4">return 문 수정:</font>
```js
<p>
	<Item item={item}/>
</p>
```
- Item을 꼭 import 하자.

[[#예제 1 page.js|> page.js 코드 전문 보기]]
## 1.2. item.js : 아이템 상세 페이지 구현
> src > component > Item.js

```js
const {name, image_link, price, category, product_type, description} = item;
```
- 인자로 넘어온 item의 속성과 받을 변수명들을 일치시켜야 한다.

<font color="#548dd4">return 문 안을 작성하자:</font>
```js
<>
	<div>
		<div>
			<img src={image_link} alt={name}/>
		</div>
	</div>
</>
```

[[#예제 1 Item.js|> Item.js 코드 전문 보기]]

# 2. 예제 2 : 꾸미기
## 2.1. Item.module.css
> public > css > Item.module.css

Item.js에게만 적용할 CSS를 만들자.
- 파일 이름에 module을 붙이면 다른 파일에는 적용 할 수 없다.

[[#예제 2 Item.module.css|> Item.module.css 코드 전문 보기]]
## 2.2. Item.js에 CSS 적용하기
> src > component > Item.js

```js
import styles from '/public/css/Item.module.css';
```
- CSS 파일을 import 하자.

```js
<>
	<div className={styles.wrap}>
		<div>
			<img src={image_link} alt={name}/>
		</div>
	</div>
</>
```
- return 문의 첫 번째 div에 클래스 속성을 부여하자.

[[#예제 2 Item.js|> Item.js 코드 전문 보기]]
## 2.3. Item.js에 추가 내용을 적용해 보자.
> src > component > Item.js

```js
<div>
	<strong>{name}</strong>
	<strong>${price}</strong>
	<span>
		
	</span>
</div>
```
- return 문 안에 새로운 div를 생성하고 name과 price를 출력하도록 한다.

```js
{category ? `${category}/` : ''}
```
- span 태그 안에 category에 값이 있으면 출력하고, 없으면 공백을 출력하도록 한다.
- 삼항연산자를 활용하자.
	- `조건식 ? 참 : 거짓;`
- 참고로 js 파일에서 주석은
	```js
	{/* 이렇게 작성한다.*/}
	```

```js
{product_type}
```
- span 태그 안에 product_type도 추가한다.

[[#예제 2 Item.js|> Item.js 코드 전문 보기]]
## 2.4. CSS 추가
> public > css > Item.module.css

[[#예제 2 Item.module.css|> Item.module.css 코드 전문 보기]]
## 2.5. Item.js에 버튼 추가하기
> src > component > Item.js

```js
<Button variant='contained' color='error'>구매하기</Button>
```
- variant의 contained 속성은 내부에 색상이 채워진 버튼을 의미한다.

```js
<div className={styles.disWrap}>
	<h3>Description</h3>
	<div style={{paddingBottom: 20, fontSize: 19}}>
		{description}
	</div>
</div>
```
- 설명 부분 추가

<font color="#548dd4">결과:</font>
![[Pasted image 20240213103521.png]]
# 3. 예제 3 : 네비게이션 수정
## 3.1. Nav.js 추가
> src > component > Nav.js

```js
const router = useRouter();
```
- next/router의 useRouter이다.

```js
function gogo(idx){
	if(idx === 0) router.push("pages/product");
}
```
- 함수 생성

```js
<Grid item key={item} xs={12} sm onClick={() => gogo(index)}>
	<Box bgcolor='primary.light' p={2}>{item}</Box>
</Grid>
```
- Grid에 클릭 이벤트와 index를 추가하자.

```js
function gogo(idx){
	switch(idx){
		case 0:
			router.push("/pages/product");
			break;
		case 1:
			router.push("/pages/color");
			break;
		case 2:
			router.push("/pages/skin");
			break;
		case 3:
			router.push("/pages/about");
			break;
	}
}
```
- 함수 수정

[[#예제 3 Nav.js|> Nav.js 코드 전문 보기]]
## 3.2. page.js 추가 생성
> src > app > pages > product > page.js

```js
export default function Page(){
    return (
        <div style={{width: '80%', margin: 'auto', padding: '20px'}}>
            <h2>product/page</h2>
        </div>
    );
}
```

[[#예제 3 page.js|> page.js 코드 전문 보기]]
## 3.3. color/page.js
> src > app > pages > color > page.js

```js
export default function Page(){
    return (
        <div style={{width: '80%', margin: 'auto', padding: '20px'}}>
            <h2>product/Color Cosmetics</h2>
        </div>
    );
}
```
## 3.3. skin/page.js
> src > app > pages > skin > page.js

```js
export default function Page(){
    return (
        <div style={{width: '80%', margin: 'auto', padding: '20px'}}>
            <h2>product/Skin Cosmetics</h2>
        </div>
    );
}
```
## 3.3. about/page.js
> src > app > pages > about > page.js

```js
export default function Page(){
    return (
        <div style={{width: '80%', margin: 'auto', padding: '20px'}}>
            <h2>product/About</h2>
        </div>
    );
}
```
# 4. 예제 4 : 각 페이지에 내용 채우기
> My_Study > SpringBoot_Study > work에 새로운 프로젝트를 만들어 스프링부트를 사용하고 싶다.

[[2024-01-17 2) Spring Boot#4. VSCode에서 Spring Boot Project 만들기]] 참고
- 대신 WAR 파일 선택
- My_Study/SpringBoot_Study/work/next에 새로운 프로젝트 생성
- My_Study/SpringBoot_Study/work/ex4_db/src/main/java/com/ict/ex4_db의 폴더들 복사
- My_Study/SpringBoot_Study/work/next/src/main/java/com/ict/next에 붙여넣기
- My_Study/SpringBoot_Study/work/ex4_db/src/main/resources/mapper 폴더 복사
- My_Study/SpringBoot_Study/work/next/src/main/resources에 붙여넣기
- 각 파일의 package의 경로<font color="#7f7f7f">(com.ict.ex4_db)</font> 변경<font color="#7f7f7f">(com.ict.next)</font>
- src/main/resources/application.properties에 내용 추가
	```
	### DB information ###
	spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
	spring.datasource.url=jdbc:mysql://localhost:3306/test_db?characterEncoding=UTF-8
	spring.datasource.username=test_admin
	spring.datasource.password=1111
	spring.datasource.tomcat.max-active=30
	spring.datasource.tomcat.max-idle=20
	spring.datasource.tomcat.min-idle=10
	#######################
	```
## 4.1. EmpController
> src > main > java > com > ict > next > controller > EmpController.java

`@Controller` 어노테이션 삭제
- NEXT에서는 이미 페이지가 열려 있으므로 필요한 자료만 넘겨주면 된다.
- `@RestController`로 다시 작성하자.

`@RequestMapping("/emp")`
- 추가하면 emp로 시작하는 경로는 모두 이 곳으로 오게 된다.

```java
@RequestMapping(value = "/list", method = RequestMethod.GET)
public Map<String, Object> list(String cPage) {
	Map<String, Object> map = new HashMap<>();
	EmpVO[] ar = e_Service.getList(null, null, "1", "10");
	map.put("list", ar);
	return map;
}
```
- list 함수를 위와 같이 수정한다.
- 기존에 반환형이 ModelAndView였던 것을 Map으로 변경하였다.

서버를 실행하고 localhost:8080/emp/list로 들어가면
```
{"list":[{"empno":"1000","ename":"대한민국","job":"DEVELOPER","deptno":"50"},{"empno":"7369","ename":"SMITH","job":"CLERK","deptno":"20"},{"empno":"7499","ename":"ALLEN","job":"SALESMAN","deptno":"30"},{"empno":"7521","ename":"WARD","job":"SALESMAN","deptno":"30"},{"empno":"7566","ename":"JONES","job":"MANAGER","deptno":"20"},{"empno":"7654","ename":"MARTIN","job":"SALESMAN","deptno":"30"},{"empno":"7698","ename":"BLAKE","job":"MANAGER","deptno":"30"},{"empno":"7782","ename":"CLARK","job":"MANAGER","deptno":"10"},{"empno":"7788","ename":"SCOTT","job":"ANALYST","deptno":"20"},{"empno":"7839","ename":"KING","job":"PRESIDENT","deptno":"10"}]}
```
위와 같이 출력되는 것을 볼 수 있다.
## 4.2. Postman 설치

1. [Postman](postman.com/downloads) 다운로드 페이지 접속
2. Windows 64-bit 다운로드
3. 회원 가입(구글)
4. My Workspace > New > HTTP
5. 우측 화면 GET : `http://localhost:8080/emp/list` > Send
6. 결과 화면
   ![[Pasted image 20240213122056.png]]
	- cPage는 직접 입력해본 것
## 4.3. next_ex1_re/next.config.mjs 수정
> next_ex1_re > next.config.mjs

```js
return[
	{
		source: "/api/:path*",
		destination: "http://makeup-api.herokuapp.com/api/:path*"
	},
	{
		source: ":path*",
		destination: "http://localhost:8080/:path*"
	}
]
```
- source를 추가하였다.
## 4.4. EmpList.js 생성
> next_ex1_re > src > component > EmpList.js

```js
<TableContainer component={Paper}>
	<Table sx={{ minWidth: 650 }} aria-label="simple table">
		<TableHead>
		<TableRow>
			<TableCell>번호</TableCell>
			<TableCell align="right">사번</TableCell>
			<TableCell align="right">이름</TableCell>
			<TableCell align="right">직종</TableCell>
			<TableCell align="right">부서코드</TableCell>
		</TableRow>
		</TableHead>
		<TableBody>
		{list.map((row) => (
			<TableRow
			key={row.name}
			sx={{ '&:last-child td, &:last-child th': { border: 0 } }}
>
			<TableCell component="th" scope="row">
				
			</TableCell>
			<TableCell align="right">{row.empno}</TableCell>
			<TableCell align="right">{row.ename}</TableCell>
			<TableCell align="right">{row.job}</TableCell>
			<TableCell align="right">{row.deptno}</TableCell>
			</TableRow>
		))}
		</TableBody>
	</Table>
</TableContainer>
```
- MUI의 [Basic Table](https://mui.com/material-ui/react-table/#basic-table)을 이용하자.

[[#예제 4 EmpList.js|> EmpList.js 코드 전문 보기]]
## 4.5. about/page.js에서 EmpList를 받자
> src > app > pages > about > page.js

<font color="#548dd4">스프링부트를 받을 준비를 하자:</font>
```js
const api_uri = "/emp/list";
```
- 스프링부트의 경로

```js
const [list, setList] = useState([]);
```
- 스프링부트에서 전달하는 JSON을 저장할 곳
- 배열이 저장되어야 하므로 대괄호를 넣는다.

```js
const [currentpage, setCurrentpage] = useState(1);
```
- 스프링부트로 전달할 파라미터
- 초기값을 1로 지정하였다. 임의로 지정하였다.

```js
"use client"
```
- useState를 사용하므로 페이지 상단에 use client를 지정하자.

```js
function callData(){
	axios.get().then();
}
```
- 비동기 처리를 위한 함수를 생성한다.

```js
api_uri,
{params: {cPage: currentpage}}
```
- get 안에 api_uri 값과 파라미터를 넣었다.

```js
).then(jsonData => {
	setList(jsonData.data.list);
});
```
- then 안에 익명 함수 생성

```js
useEffect(() => {}, []);
```
- useEffect로 위에서 만든 callData 함수를 호출하자.

```js
useEffect(() => {
	callData();
}, []);
```
- callData를 한 번 호출하도록 하였다.

```js
return (
	<div style={{width: '80%', margin: 'auto', padding: '20px'}}>
		<EmpList list={list}/>
	</div>
);
```
- return 문 수정

<font color="#548dd4">결과 화면:</font>
![[Pasted image 20240213130441.png]]

[[#예제 4 about/page.js|> page.js 코드 전문 보기]]
# 5. 예제 5 : 댓글처럼 보기?
## 5.1. 새 Spring Boot Project

- next_bbs
- WAR
- ---
- `C:\My_Study\SpringBoot_Study\work\bbs\src\main\java\com\ict\bbs`의 폴더 모두 복사
	- `C:\My_Study\SpringBoot_Study\work\next_bbs\src\main\java\com\ict\bbs`에 붙여넣기
- `C:\My_Study\SpringBoot_Study\work\bbs\src\main\resources`의 mapper 폴더와 application.properties 파일 복사
	- `C:\My_Study\SpringBoot_Study\work\next_bbs\src\main\resources`에 붙여넣기

## 5.2. BbsController 수정
> src > main > java > com > ict > next_bbs > controller > BbsController.java

[[#예제 5 BbsController.java|> BbsController.java 코드 전문 보기]]
## 5.3. 새 NEXT Project
```
npx create-next-app@latest next_bbx
npm install @mui/material @emotion/react @emotion/styled
npm i axios
npm run dev
```
## 5.4. layout.js 수정
> src > app > layout.js

```js
<html lang="en">
  <body className={inter.className}>
	<div style={{width: '80%', margin: 'auto', paddingTop: '20px'}}>
		{children}
	</div>
  </body>
</html>
```
- `{children}`을 div로 감쌌다.
## 5.5. next.config.mjs 내용 추가
> next_bbx > next.config.mjs

```js
const nextConfig = {
    reactStrictMode: true,
    swcMinify: true,
    async rewrites(){
        return [
            {
                source: "/:path*",
                destination: "http://localhost:8080/:path*"
            }
        ];
    }
};
```
- nextConfig의 내용을 위와 같이 채우자.
## 5.6. list 페이지 만들기
> src > app > list > page.js

```js
"use client"
import { useState } from "react";

export default function List(){
    const [list, setList] = useState([]);
    const api_uri = `/bbs/list`;
    return (
        <div>
		
        </div>
    );
}
```
- useState를 사용하므로 use client를 선언한다.

```js
// useState 변수 새로 생성
const [currentPage, setCurrentPage] = useState(1);

// axios 완성
function getList(){
	axios.get(
		api_uri,
		{params: {cPage: currentPage}}
	).then(json => {
		setList(json.data.ar);
	})
}
```
- 서버와 비동기 통신을 해야 한다.

```js
useEffect(() => {
	getList();
}, []);
```
- useEffect 생성

```js
<div>
	<div>
		<header>
			<h1>게시팔</h1>
			<p><button type="button">글쓰기</button></p>
		</header>
		<hr/>
		<TableContainer component={Paper}>
			<Table sx={{ minWidth: 650 }} aria-label="simple table">
				<TableHead>
				<TableRow>
					<TableCell>번호</TableCell>
					<TableCell align="right">제목</TableCell>
					<TableCell align="right">글쓴이</TableCell>
					<TableCell align="right">등록일</TableCell>
					<TableCell align="right">조회수</TableCell>
				</TableRow>
				</TableHead>
				<TableBody>
					{list.map((row, index) => (
						<TableRow
							key={row.b_idx}
							sx={{ '&:last-child td, &:last-child th': { border: 0 } }}
						<TableCell component="th" scope="row">
							{index}
						</TableCell>
						<TableCell align="right">{row.subject}</TableCell>
						<TableCell align="right">{row.writer}</TableCell>
						<TableCell align="right">{row.write_date}</TableCell>
						<TableCell align="right">{row.hit}</TableCell>
						</TableRow>
					))}
				</TableBody>
			</Table>
		</TableContainer>
	</div>
</div>
```
- return 문을 위와 같이 작성한다.

[[#예제 5 list/page.js|> list/page.js 코드 전문 보기]]
# A. 부록
## 부록 1 : 오류 해결 : npm run dev 시 Ready 구문이 안 뜬다면?

- 서버를 중지한 후
- 프로젝트 폴더의 `.next` 폴더를 삭제
- 다시 시도해 보자.
# B. 코드 전문
## 예제 1 : page.js
> src > app > views > `[id]` > page.js

```js
"use client"
import Item from "@/component/Item";
import axios from "axios";
import { useRouter } from "next/navigation";
import { useEffect, useState } from "react";

export default function Page({params}){
    const router = useRouter();
    const id = `${params.id}`;

    const [item, setItem] = useState({});
    const api_uri = `/api/v1/products/${id}.json`;

    function getItem(){
        axios.get(api_uri).then((res) => {
            setItem(res.data);
        });
    }

    useEffect(() => {
        if(id && id > 0) getItem();
    });

    return (
        <p>
            <Item item={item}/>
        </p>
    );
}
```
## 예제 1 : Item.js
> src > component > Item.js

```js
export default function Item({item}){
    const {name, image_link, price, category, product_type, description} = item;

    return (
        <>
            <div>
                <div>
                    <img src={image_link} alt={name}/>
                </div>
            </div>
        </>
    );
}
```
## 예제 2 : Item.module.css
> public > css > Item.module.css

```CSS
.wrap {
    display: flex;
    width: 80%;
    margin: 0 auto;
    padding: 40px 0;
    border-bottom: 1px solid #ccc;
}

.disWrap {
    width: 80%;
    margin: 0 auto;
}

.img_item {
    flex: 200px 0 0;
}

.img_item img {
    display: block;
}

.info_item {
    flex: 1 0 0;
}

.tit_item {
    display: block;
    font-size: 24px;
    margin-top: 20px;
}

.num_price {
    display: block;
    font-size: 34px;
    margin-top: 20px;
    color: #00bcd4;
}

.txt_info {
    display: block;
    font-size: 24px;
    margin: 20px 0 40px;
}
```
## 예제 2 : Item.js
> src > component > Item.js

```js
import { Button } from '@mui/material';
import styles from '/public/css/Item.module.css';

export default function Item({item}){
    const {name, image_link, price, category, product_type, description} = item;

    return (
        <>
            <div className={styles.wrap}>
                <div className={styles.img_item}>
                    <img src={image_link} alt={name}/>
                </div>
                <div className={styles.info_item}>
                    <strong className={styles.tit_item}>{name}</strong>
                    <strong className={styles.num_price}>${price}</strong>
                    <span className={styles.txt_info}>
                        {category ? /* `${category}/` */ : ''}
                        {product_type}
                    </span>
                    <Button variant='contained' color='error'>구매하기</Button>
                </div>
            </div>
            <div className={styles.disWrap}>
                <h3>Description</h3>
                <div style={{paddingBottom: 20, fontSize: 19}}>
                    {description}
                </div>
            </div>
        </>
    );
}
```
- 주석 처리 표현 삭제 후 사용
## 예제 3 : Nav.js
> src > component > Nav.js

```js
"use client"
import { Box, Grid } from "@mui/material";
import { useRouter } from "next/navigation";
import React, { useState } from "react";

export default function Nav(){
    const navItem = ['Products', 'Color Cosmetics', 'Skin Cosmetics', 'About'];
    const [anchorEl, setAnchorEl] = useState(null);
    const open = Boolean(anchorEl);

    const router = useRouter();

    function gogo(idx){
        switch(idx){
            case 0:
                router.push("/pages/product");
                break;
            case 1:
                router.push("/pages/color");
                break;
            case 2:
                router.push("/pages/skin");
                break;
            case 3:
                router.push("/pages/about");
                break;
        }
    }

    function handleClick(event){
        setAnchorEl(event.currentTarget);
    }
    
    return (
        <nav id="header">
            <Grid container my={2}>
                {navItem.map((item, index) => (
                    <Grid item key={item} xs={12} sm onClick={() => gogo(index)}>
                        <Box bgcolor='primary.light' p={2}>{item}</Box>
                    </Grid>
                ))}
            </Grid>
        </nav>
    );
}
```
## 예제 3 : page.js
> src > app > pages >  product > page.js

```js
export default function Page(){
    return (
        <div style={{width: '80%', margin: 'auto', padding: '20px'}}>
            <h2>product/page</h2>
        </div>
    );
}
```
## 예제 4 : EmpController.java
> src > main > java > com > ict > next > controller > EmpController.java

```java
package com.ict.next.controller;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import com.ict.next.service.EmpService;
import com.ict.next.vo.EmpVO;

@RestController
@RequestMapping("/emp")
public class EmpController {
    @Autowired
    private EmpService e_Service;

    @RequestMapping(value = "/list", method = RequestMethod.GET)
    public Map<String, Object> list(String cPage) {
        Map<String, Object> map = new HashMap<>();
        EmpVO[] ar = e_Service.getList(null, null, "1", "10");
        map.put("list", ar);
        return map;
    }
}
```
## 예제 4 : EmpList.js
> src > component > EmpList.js

```js
import { Paper, Table, TableBody, TableCell, TableContainer, TableHead, TableRow } from "@mui/material";

export default function EmpList({list, cp}){
    return (
        <TableContainer component={Paper}>
            <Table sx={{ minWidth: 650 }} aria-label="simple table">
                <TableHead>
                <TableRow>
                    <TableCell>번호</TableCell>
                    <TableCell align="right">사번</TableCell>
                    <TableCell align="right">이름</TableCell>
                    <TableCell align="right">직종</TableCell>
                    <TableCell align="right">부서코드</TableCell>
                </TableRow>
                </TableHead>
                <TableBody>
                    {list.map((row, index) => (
                        <TableRow
                            key={row.empno}
                            sx={{ '&:last-child td, &:last-child th': { border: 0 } }}
                        >
                            <TableCell component="th" scope="row">
                                {index}
                            </TableCell>
                            <TableCell align="right">{row.empno}</TableCell>
                            <TableCell align="right">{row.ename}</TableCell>
                            <TableCell align="right">{row.job}</TableCell>
                            <TableCell align="right">{row.deptno}</TableCell>
                        </TableRow>
                    ))}
                </TableBody>
            </Table>
        </TableContainer>
    );
}
```
## 예제 4 : about/page.js
> src > app > pages > about > page.js

```js
"use client"
import EmpList from "@/component/EmpList";
import axios from "axios";
import { useEffect, useState } from "react";

export default function Page(){
    const api_uri = "/emp/list";
    const [list, setList] = useState([]);
    const [currentpage, setCurrentpage] = useState(1);

    function callData(){
        axios.get(
            api_uri,
            {params: {cPage: currentpage}}
        ).then(jsonData => {
            setList(jsonData.data.list);
        });
    }

    useEffect(() => {
        callData();
    }, []);

    return (
        <div style={{width: '80%', margin: 'auto', padding: '20px'}}>
            <EmpList list={list} cp={currentpage}/>
        </div>
    );
}
```
## 예제 5 : BbsController.java
> src > main > java > com > ict > next_bbs > controller > BbsController.java

```java
package com.ict.next_bbs.controller;

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.io.Resource;
import org.springframework.core.io.ResourceLoader;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import com.ict.next_bbs.service.BbsService;
import com.ict.next_bbs.util.FileRenameUtil;
import com.ict.next_bbs.util.Paging2;
import com.ict.next_bbs.vo.BbsVO;

import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletOutputStream;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;

@RestController
@RequestMapping("/bbs")
public class BbsController {
    @Autowired
    private BbsService b_Service;

    @Autowired
    private ServletContext application;

    @Autowired
    private HttpServletRequest request;

    @Autowired
    private HttpServletResponse response;

    @Autowired
    private HttpSession session;

    @Autowired
    private ResourceLoader resourceLoader;

    private String editor_img = "/editor_img";
    private String upload_file = "/upload_file";
    private ArrayList<BbsVO> r_list;

    @RequestMapping("list")
    public Map<String, Object> list(String bname, String cPage) {
        Map<String, Object> map = new HashMap<>();
        int nowPage = 1;

        if (bname == null)
            bname = "bbs";
        if (cPage != null)
            nowPage = Integer.parseInt(cPage);

        int totalRecord = b_Service.getCount(bname);
        Paging2 page = new Paging2(nowPage, totalRecord, 7, 5);
        String pageCode = page.getSb().toString();

        String begin = String.valueOf(page.getBegin());
        String end = String.valueOf(page.getEnd());
        BbsVO[] ar = b_Service.getList(bname, begin, end);

        map.put("ar", ar);
        map.put("page", page);
        map.put("pageCode", pageCode);
        map.put("totalRecord", totalRecord);
        map.put("nowPage", page.getNowPage());
        map.put("bname", bname);
        map.put("blockList", page.getBlockList());

        return map;
    }

    @RequestMapping("write")
    public String write(@RequestParam String bname) {
        return bname + "/write";
    }

    @PostMapping("write")
    public Map<String, Object> write(BbsVO vo) {
        Map<String, Object> map = new HashMap<>();
        String c_type = request.getContentType();
        if (c_type.startsWith("multipart")) {
            MultipartFile f = vo.getFile();
            if (f.getSize() > 0) {
                String realPath = application.getRealPath(upload_file);
                String oname = f.getOriginalFilename();
                vo.setOri_name(oname);
                String fname = FileRenameUtil.checkSameFileName(oname, realPath);
                try {
                    f.transferTo(new File(realPath, fname));
                    vo.setFile_name(fname);
                } catch (Exception e) {
                    e.printStackTrace();
                }
                vo.setIp(request.getRemoteAddr());
                b_Service.addBbs(vo);
            }
        }
        map.put("vo", vo);

        return map;
    }

    @PostMapping("saveImg")
    @ResponseBody
    public Map<String, String> saveImg(MultipartFile s_file) {
        Map<String, String> map = new HashMap<>();
        if (s_file.getSize() > 0) {
            String realPath = application.getRealPath(editor_img);
            String oname = s_file.getOriginalFilename();
            String fname = FileRenameUtil.checkSameFileName(oname, realPath);
            try {
                s_file.transferTo(new File(realPath, fname));
            } catch (Exception e) {
                e.printStackTrace();
            }
            String path = request.getContextPath();
            map.put("url", path + editor_img);
            map.put("fname", fname);
        }

        return map;
    }

    @RequestMapping("view")
    public Map<String, Object> view(String b_idx, String cPage, String bname) {
        Map<String, Object> map = new HashMap<>();

        session = request.getSession(true);
        session.setMaxInactiveInterval(1800);
        Object obj = session.getAttribute("read_list");
        if (obj == null) {
            r_list = new ArrayList<BbsVO>();
            session.setAttribute("read_list", r_list);
            System.out.println("test");
        } else {
            r_list = (ArrayList<BbsVO>) obj;
        }
        BbsVO bvo = b_Service.getBbs(b_idx);
        if (checkBbs(bvo)) {
            b_Service.hit(b_idx);
            r_list.add(bvo);
        }

        map.put("bvo", bvo);
        return map;
    }

    public boolean checkBbs(BbsVO vo) {
        boolean value = true;
        if (r_list != null) {
            for (BbsVO bvo : r_list) {
                if (bvo.getB_idx().equals(vo.getB_idx())) {
                    value = false;
                    break;
                }
            }
        }
        return value;
    }

    @RequestMapping("edit")
    public Map<String, Object> edit(String b_idx, String cPage, String bname) {
        Map<String, Object> map = new HashMap<>();
        BbsVO bvo = b_Service.getBbs(b_idx);
        map.put("bvo", bvo);
        return map;
    }

    @PostMapping("edit")
    public Map<String, Object> edit(BbsVO vo, String cPage) {
        Map<String, Object> map = new HashMap<>();
        String c_type = request.getContentType();
        if (c_type.startsWith("multipart")) {
            MultipartFile f = vo.getFile();
            if (f.getSize() > 0) {
                String realPath = application.getRealPath(upload_file);
                String oname = f.getOriginalFilename();
                vo.setOri_name(oname);
                String fname = FileRenameUtil.checkSameFileName(oname, realPath);
                try {
                    f.transferTo(new File(realPath, fname));
                    vo.setFile_name(fname);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
        vo.setIp(request.getRemoteAddr());
        b_Service.editBbs(vo);
        return map;
    }

    @RequestMapping("download")
    public ResponseEntity<Resource> fileDownload(String cPage, String fname) {
        String realPath = application.getRealPath(upload_file + "/" + fname);
        File f = new File(realPath);

        if (f.exists()) {
            byte[] buf = new byte[4069];
            int size = -1;
            BufferedInputStream bis = null;
            FileInputStream fis = null;
            BufferedOutputStream bos = null;
            ServletOutputStream sos = null;

            try {
                response.setContentType("application/x-msdownload");
                response.setHeader("Content-Disposition",
                        "attachment;filename=" + new String(fname.getBytes(), "8859_1"));
                fis = new FileInputStream(f);
                bis = new BufferedInputStream(fis);
                sos = response.getOutputStream();
                bos = new BufferedOutputStream(sos);

                while ((size = bis.read()) != -1) {
                    bos.write(buf, 0, size);
                    bos.flush();
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                try {
                    if (fis != null)
                        fis.close();
                    if (bis != null)
                        bis.close();
                    if (sos != null)
                        sos.close();
                    if (bos != null)
                        bos.close();
                } catch (Exception e) {
                }
            }
        }
        return null;
    }

}
```
## 예제 5 : list/page.js
> src > app > list > page.js

```js
"use client"
import { useEffect, useState } from "react";
import Table from '@mui/material/Table';
import TableBody from '@mui/material/TableBody';
import TableCell from '@mui/material/TableCell';
import TableContainer from '@mui/material/TableContainer';
import TableHead from '@mui/material/TableHead';
import TableRow from '@mui/material/TableRow';
import Paper from '@mui/material/Paper';
import axios from "axios";

export default function List(){
    const [list, setList] = useState([]);
    const [currentPage, setCurrentPage] = useState(1);
    const api_uri = `/bbs/list`;

    function getList(){
        axios.get(
            api_uri,
            {params: {cPage: currentPage}}
        ).then(json => {
            setList(json.data.ar);
        })
    }

    useEffect(() => {
        getList();
    }, []);

    return (
        <div>
            <div>
                <header>
                    <h1>게시팔</h1>
                    <p><button type="button">글쓰기</button></p>
                </header>
                <hr/>
                <TableContainer component={Paper}>
                    <Table sx={{ minWidth: 650 }} aria-label="simple table">
                        <TableHead>
                        <TableRow>
                            <TableCell>번호</TableCell>
                            <TableCell align="right">제목</TableCell>
                            <TableCell align="right">글쓴이</TableCell>
                            <TableCell align="right">등록일</TableCell>
                            <TableCell align="right">조회수</TableCell>
                        </TableRow>
                        </TableHead>
                        <TableBody>
                            {list.map((row, index) => (
                                <TableRow
                                    key={row.b_idx}
                                    sx={{ '&:last-child td, &:last-child th': { border: 0 } }}
                                >
                                <TableCell component="th" scope="row">
                                    {index}
                                </TableCell>
                                <TableCell align="right">{row.subject}</TableCell>
                                <TableCell align="right">{row.writer}</TableCell>
                                <TableCell align="right">{row.write_date}</TableCell>
                                <TableCell align="right">{row.hit}</TableCell>
                                </TableRow>
                            ))}
                        </TableBody>
                    </Table>
                </TableContainer>
            </div>
        </div>
    );
}
```