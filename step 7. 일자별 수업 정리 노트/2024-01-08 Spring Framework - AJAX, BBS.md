# 1. Intro
> Spring STS 환경에서 게시판 만들기
## 1.1. 목적별 필요 라이브러리
- Spring Core
	- spring-beans
	- spring-context
	- spring-core
	- spring-expression
	- commons-logging
- Spring DB와 MyBatis 활용
	- commons-dbcp
	- commons-pool
	- mybatis
	- mybatis-spring
	- spring-aop
	- spring-jdbc
	- spring-tx
- Spring MVC 활용
	- spring-web
	- spring-webmvc
- Spring JSTL 활용
	- jstl
	- standard
- 파일 업로드
	- commons-fileupload
	- commons-io
---
- STS 라이브러리<font color="#7f7f7f">(pom.xml)</font> ^a208eb
	- spring-jdbc : ${org.springframework-version}
	- spring-tx : ${org.springframework-version}
	- commons-logging : 1.2
	- commons-dbcp : 1.4
	- commons-pool : 1.6
	- mybatis : 3.5.14
	- mybatis-spring : 1.3.2
	- commons-fileupload : 1.4
	- commons-io : 2.11.0
---
- Spring Core는 몰라도 상관 없는 영역 - 알아서 들어가 있음.
- STS에서는 필요한 라이브러리를 pom.xml에서 dependency를 이용해 추가한다.
	- MVNRepository.com에서 Maven dependency를 복사하여 pom.xml에 붙여 넣는다.
## 1.2. 오늘의 목표
- `@ResponseBody`로 JSON 표기법으로 응답을 위한 설정하기
	- jackson-mapper-asl 라이브러리가 필요하다. <font color="#7f7f7f">Spring 3.x 기준</font>
		- Spring 4.x 기준 Jackson Databind, Jackson Core, Jackson Annotations 필요
	- Spring Controller 메서드 앞에 `@ResponseBody` 지정
		- 반환형은 `Map<String, String>`으로 지정한다.
		- 이제 반환할 때 Map이 JSON으로 변환된다.
## 1.3. 설정 몇 가지
- `C:\My_Study\JSP_Study\work\Ex_1220_MVC_BBS\src\main\java\mybatis` 폴더를 복사하여 `C:\My_Study\Spring_Study\work\0108_STS_Bbs\src\main\java`에 붙여넣자.
	- mybatis.config, mybatis.service는 삭제하자.
	- mybatis.mapper는 `src/main/resources`로 이동하자.
- `0105_editor > src/main/java > spring.util`을 오늘 프로젝트의 같은 경로에 복사하자.
- `C:\My_Study\Spring_Study\work\0108_STS_Bbs\src\main\webapp`의 list.jsp, write.jsp를 복사하여 `0108_STS_Bbs > src > main > webapp > WEB-INF > views > bbs`에 저장하자.
# 2. 게시판 만들기 - 목록 보기
> Spring Legacy Project > 0108_STS_Bbs > Spring MVC Project > com.ict.bbs
## 2.1. pom.xml - 라이브러리 추가하기
> 0108_STS_Bbs
> [[#^a208eb|STS 라이브러리]]를 설치하자. 

```xml
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
	<groupId>commons-logging</groupId>
	<artifactId>commons-logging</artifactId>
	<version>1.2</version>
</dependency>
<dependency>
	<groupId>commons-dbcp</groupId>
	<artifactId>commons-dbcp</artifactId>
	<version>1.4</version>
</dependency>
<dependency>
	<groupId>commons-pool</groupId>
	<artifactId>commons-pool</artifactId>
	<version>1.6</version>
</dependency>
<dependency>
	<groupId>commons-io</groupId>
	<artifactId>commons-io</artifactId>
	<version>2.11.0</version>
</dependency>
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.4</version>
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
```
### 2.1.1. 나머지 라이브러리 추가하기
JSTL 사용을 위한 라이브러리를 등록하자.
`C:\My_Study\JSP_Study\work\Ex_1220_MVC_BBS\src\main\webapp\WEB-INF\lib`에서
- jstl.jar
- standard.jar
## 2.2. list.jsp
> src > main > webapp > WEB-INF > views > bbs >
### 2.2.1. 가져온 코드
```HTML
<%@page import="bbs.util.Paging"%>
<%@page import="mybatis.vo.BbsVO"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>LIST</title>
<style type="text/css">
#bbs table {
	width: 580px;
	margin-left: 10px;
	border: 1px solid black;
	border-collapse: collapse;
	font-size: 14px;
}

#bbs table caption {
	font-size: 20px;
	font-weight: bold;
	margin-bottom: 10px;
}

#bbs table th, #bbs table td {
	text-align: center;
	border: 1px solid black;
	padding: 4px 10px;
}

.no {
	width: 15%
}

.subject {
	width: 30%
}

.writer {
	width: 20%
}

.reg {
	width: 20%
}

.hit {
	width: 15%
}

.title {
	background: lightsteelblue
}

.odd {
	background: silver
}

/* paging */
table tfoot ol.paging {
	list-style: none;
}

table tfoot ol.paging li {
	float: left;
	margin-right: 8px;
}

table tfoot ol.paging li a {
	display: block;
	padding: 3px 7px;
	border: 1px solid #00B3DC;
	color: #2f313e;
	font-weight: bold;
	text-decoration: none;
}

table tfoot ol.paging li a:hover {
	background: #00B3DC;
	color: white;
	font-weight: bold;
}

.disable {
	padding: 3px 7px;
	border: 1px solid silver;
	color: silver;
}

.now {
	padding: 3px 7px;
	border: 1px solid #ff4aa5;
	background: #ff4aa5;
	color: white;
	font-weight: bold;
}
</style>
</head>
<body>
	<div id="bbs">
		<table summary="게시판 목록">
			<caption>게시판 목록</caption>
			<thead>
				<tr class="title">
					<th class="no">번호</th>
					<th class="subject">제목</th>
					<th class="writer">글쓴이</th>
					<th class="reg">날짜</th>
					<th class="hit">조회수</th>
				</tr>
			</thead>
			<tfoot>
				<tr>
					<td colspan="4">
						<ol class="paging">
							<c:if test="${requestScope.page.startPage < requestScope.page.pagePerBlock}">
								<li class="disable">&lt;</li>
							</c:if>
							<c:if test="${requestScope.page.startPage >= requestScope.page.pagePerBlock}">
								<li><a href="Controller?type=bbsList&cPage=${page.startPage - page.pagePerBlock}">&lt;</a></li>
							</c:if>
							<c:forEach begin="${page.startPage}" end="${page.endPage}" varStatus="vs">
								<c:if test="${vs.index eq page.nowPage}">
									<li class="now">${vs.index}</li>
								</c:if>
								<c:if test="${vs.index ne page.nowPage}">
									<li><a href="Controller?type=bbsList&cPage=${vs.index}">${vs.index}</a></li>
								</c:if>
							</c:forEach>
							<c:if test="${page.endPage < page.totalPage}">
								<li><a href="Controller?type=bbsList&cPage=${page.startPage + page.pagePerBlock}">&gt;</a></li>
							</c:if>
							<c:if test="${page.endPage >= page.totalPage}">
								<li class="disable">&gt;</li>
							</c:if>
						</ol>
					</td>
					<td><input type="button" value="글쓰기"
						onclick="javascript:location.href='Controller?type=write'" /></td>
				</tr>
			</tfoot>
			<tbody>
				<c:forEach var="vo" items="${requestScope.b_ar}" varStatus="vs">
					<tr>
						<td>${page.totalRecord - ((page.nowPage - 1) * page.numPerPage + vs.index)}</td>
						<td style="text-align: left">
							<a href="Controller?type=view&b_idx=${vo.b_idx}&cPage=${page.nowPage}">
								${vo.subject}
								<c:if test="${vo.c_list.size() > 0}">
									(${vo.c_list.size()})
								</c:if>
							</a>
						</td>
						<td>${vo.writer}</td>
						<td>${vo.write_date}</td>
						<td>${vo.hit}</td>
					</tr>
				</c:forEach>
			</tbody>
		</table>
	</div>
</body>
</html>
```
### 2.2.2. 코드 수정
```HTML
<%@page import="bbs.util.Paging"%>
```
- bbs를 spring으로 수정하자.

```HTML
<c:forEach var="vo" items="${requestScope.b_ar}" varStatus="vs">
```
- requestScope.b_ar을 requestScope.ar로 수정하자.
## 2.3. BbsDAO.java
> src/main/java > mybatis.dao > 

- 해야할 것은 [[2024-01-05 Spring Framework - AJAX#3.2. BbsDAO.java|지난 시간]]에 한 것을 참고하자.
	- `@Component`를 부여하는 이유
		- Spring Framework에서 Bean으로 등록하고자 하는 클래스에 붙일 수 있는 Annotation
		- Data Access Object는 데이터베이스와의 상호 작용을 담당하는 클래스이다.
		- <font color="#fac08f">해당 클래스가 Spring Container에 Bean으로 자동 등록 및 관리된다.</font>
			- Bean은 Spring IoC<font color="#7f7f7f">(Inversion of Control)</font> Container에 의해 생성, 관리되며 다른 Bean과의 DI<font color="#7f7f7f">(의존성 주입)</font>가 가능해진다.
			- 만약 `@Component` Annotation이 <font color="#92cddc">없다면 해당 클래스는 Bean으로 등록되지 않으므로</font> Spring IoC Container에서 관리되지 않아, <font color="#92cddc">BbsDAO를 직접 생성하고 관리해야</font> 하며, DI를 받을 수 없게 된다.
			- 일반적으로 Spring에서는 <font color="#fac08f">Component Scan을 통해 Bean을 자동으로 등록</font>하고 DI를 처리하기 때문에 사용하는 것이 편리하다.
		- DAO가 다른 서비스나 컴포넌트와 상호작용해야 할 때, Spring은 자동으로 필요한 의존성을 주입해준다.
		- AOP를 적용하여 logging, transaction 등을 분리하여 모듈화할 수 있다.
	- `@Autowired`를 붙이는 이유
		- Spring Container가 SqlSessionTemplate 타입의 Bean을 찾아서 자동으로 ss 변수에 주입한다.
		- 따라서 개발자가 직접 SqlSessionTemplate 객체를 생성 및 관리할 필요가 없다.
		- SqlSessionTemplate는 일반적으로 MyBatis 설정 파일에서 설정되고, Spring Container에 의해 Bean으로 등록된다.
### 2.3.1. 가져온 코드
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
### 2.3.2. 수정한 코드
```java
package mybatis.dao;

import java.util.HashMap;
import java.util.List;

import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import mybatis.vo.BbsVO;
import mybatis.vo.CommVO;

@Component
public class BbsDAO {
	
	@Autowired
	private SqlSessionTemplate ss;
	
	public int getCount(String bname) {
		
		return ss.selectOne("bbs.count", bname);
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
		
		return ss.insert("bbs.add", map);
	}
	
	public BbsVO getBbs(String b_idx) {
		
		return ss.selectOne("bbs.getBbs", b_idx);
	}
	
	public int addComm(CommVO vo) {
		
		return ss.insert("bbs.addComm", vo);
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
		
		return ss.update("bbs.edit", map);
	}
	
	public int del(String b_idx) {
		
		return ss.update("bbs.del", b_idx);
	}
	
	public int hit(String b_idx) {
		
		return ss.update("bbs.hit", b_idx);
	}

}
```
## 2.4. FileRenameUtil.java
> src/main/java > spring.util > 

### 2.4.1. 가져온 코드
```java
package spring.util;

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
## 2.5. Paging.java
> src/main/java > spring.util >

### 2.5.1. 가져온 코드
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
## 2.6. BbsController.java
> src/main/java > com.ict.bbs > Java Class

### 2.6.1. 코드 작성
```java
@Controller
public class BbsController {
	
	@Autowired
	private BbsDAO b_dao;

}
```
- Controller Annotation을 지정하고, BbsDAO가 들어와야 하므로 Autowired Annotation을 지정해준다.

```java
int nowPage;
int rowTotal;
int blockList = 7;
int blockPage = 5;
```
- blockList: 한 페이지당 표현될 게시물
- blockPage: 한 블럭당 표현될 페이지

#### 2.6.1.1. list 함수
```java
public ModelAndView list(String cPage, String bname) {
		ModelAndView mv = new ModelAndView();
		
		mv.setViewName(bname + "/list");
		return mv;
	}
```
- 목록을 얻어내고자 하는 함수를 만든다.
- 반환형을 ModelAndView로 지정해주어야 한다.
	- 게시물들을 ModelAndView에 저장하고 가야하기 때문이다.
- 파라미터 cPage, bname을 String 형으로 받는다.
	- bname을 이용하여 view name을 지정하려고 한다.

```java
if(cPage == null) nowPage = 1;
else nowPage = Integer.parseInt(cPage);
```
- 인자로 넘어온 파라미터는 현재 페이지를 가리키는 값이다.
- 이 현재 페이지 값이 null이면 첫 페이지로 지정해주자.

```java
if(bname == null) bname = "bbs";
```
- 만약 bname이 null이면 "bbs"로 지정해주자.

```java
rowTotal = b_dao.getCount(bname);
```
- bname으로 총 레코드 수를 구할 수 있다.

```java
Paging page = new Paging(blockList, blockPage);
page.setTotalRecord(rowTotal);
page.setNowPage(nowPage);
```
- 페이징 처리를 위한 객체를 생성하자.
- 총 레코드 수를 page에 지정하자.
- 현재 페이지를 page에 지정하자.
	- 이때 page의 멤버 변수 begin, end, startPage, endPage가 구해진다.

```java
int begin = page.getBegin();
int end = page.getEnd();
BbsVO[] ar = b_dao.getList(bname, begin, end);
```
- view page에서 표현할 게시물들을 가져오기 위해 begin과 end를 얻어내자.
- BbsVO 배열에 BbsDAO의 getList 함수의 반환값을 저장한다.

```java
mv.addObject("ar", ar);
mv.addObject("page", page);
mv.addObject("nowPage", nowPage);
mv.addObject("bname", bname);
```
- ModelAndView에 ar과 page, nowPage, bname을 저장한다.

```java
@RequestMapping("/list")
public ModelAndView list(String cPage, String bname) {
```
- RequestMapping Annotation을 지정하자.

### 2.6.2. 완성된 코드 전문
```java
package com.ict.bbs;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.BbsDAO;
import mybatis.vo.BbsVO;
import spring.util.Paging;

@Controller
public class BbsController {
	
	@Autowired
	private BbsDAO b_dao;
	
	int nowPage;
	int rowTotal;
	int blockList = 7;
	int blockPage = 5;
	
	@RequestMapping("/list")
	public ModelAndView list(String cPage, String bname) {
		ModelAndView mv = new ModelAndView();
		
		if(cPage == null) nowPage = 1;
		else nowPage = Integer.parseInt(cPage);
		
		if(bname == null) bname = "bbs";
		
		rowTotal = b_dao.getCount(bname);
		
		Paging page = new Paging(blockList, blockPage);
		page.setTotalRecord(rowTotal);
		page.setNowPage(nowPage);
		
		int begin = page.getBegin();
		int end = page.getEnd();
		BbsVO[] ar = b_dao.getList(bname, begin, end);
		
		mv.addObject("ar", ar);
		mv.addObject("page", page);
		mv.addObject("nowPage", nowPage);
		mv.addObject("bname", bname);
		mv.setViewName(bname + "/list");
		
		return mv;
	}

}
```
## 2.7. root-context.xml
> src > main > webapp > WEB-INF > spring >

### 2.7.1. 코드 작성
```xml
<bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	<property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql://localhost:3306/test_db"/>
	<property name="username" value="test_admin"/>
	<property name="password" value="1111"/>
	<property name="maxActive" value="30"/>
	<property name="maxIdle" value="20"/>
	<property name="minIdle" value="10"/>
</bean>
```
- DB에 연결하기 위해 DataSource를 Bean 객체로 등록하자.
- maxActive: 동시에 사용할 수 있는 최대 커넥션 수
- maxIdle, minIdle: 커넥션 풀에 반납할 때 최대/최소로 유지하는 커넥션 수

```xml
<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="ds"/>
	<property name="mapperLocations" value="classpath:mybatis/mapper/**/*.xml"/>
</bean>
```
- 앞서 생성된 DataSource를 현재 객체의 dataSource에 저장한다.
- mapper의 위치를 지정하여 mapperLocations에 저장한다.
	- classPath: "src/main/resources"를 의미한다.
	- `/**/`: 그 하위 폴더까지 포함한다는 의미이다.

```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg ref="factory"/>
</bean>
```
- SqlSessionTemplage Bean을 생성하고 이의 매개변수로 factory Bean을 주입하였다.

```xml
<context:component-scan base-package="mybatis.dao"/>
```
- 필요한 DAO들은 모두 mybatis.dao 패키지에 둘 예정이다.
- 이곳에 있는 `@Component`들을 모두 생성하여 Context에 저장하자.
### 2.7.2. 완성된 코드 전문
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
		<property name="maxActive" value="30"/>
		<property name="maxIdle" value="20"/>
		<property name="minIdle" value="10"/>
	</bean>
	<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="ds"/>
		<property name="mapperLocations" value="classpath:mybatis/mapper/**/*.xml"/>
	</bean>
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg ref="factory"/>
	</bean>
	<context:component-scan base-package="mybatis.dao"/>
		
</beans>
```
## 2.8. 실행 화면
> localhost:8080/bbs/list

![[Pasted image 20240108122758.png|400]]
- 페이징을 위해 bname을 ModelAndView에 저장하여 가지고 다녀야 한다.
# 3. 게시판 만들기 - 쓰기
## 3.1. list.jsp
> src > main > webapp > WEB-INF > views >
### 3.1.1. 글쓰기의 onclick 이벤트 수정
```HTML
<input type="button" value="글쓰기" onclick="javascript:location.href='Controller?type=write?bname=bbs'" />
```
- `Controller?type=write`를 `write?bname=bbs`로 수정하자.

## 3.2. write.jsp
> src > main > webapp > WEB-INF > views >
### 3.2.1. 가져온 코드
```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>WRITE</title>
<style type="text/css">
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
	
		
</style>
<script type="text/javascript">
	function sendData(){
		for(var i=0 ; i<document.forms[0].elements.length ; i++){
			if(i >= 3) break;
			if(document.forms[0].elements[i].value == ""){
				alert(document.forms[0].elements[i].dataset.str + "를 입력하세요");
				document.forms[0].elements[i].focus();
				return;//수행 중단
			}
		}

//		document.forms[0].action = "test.jsp";
		document.forms[0].submit();
	}
</script>
</head>
<body>
	<div id="bbs">
	<form action="Controller?type=write" method="post" encType="multipart/form-data">
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
					<td><textarea name="content" cols="50" rows="8" data-str="내용"></textarea></td>
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
</body>
</html>
```
## 3.3. BbsController.java
> src/main/java > com.ict.bbs > 

### 3.3.1. 코드 추가
```java
@RequestMapping("/write")
public String write() {
	return "bbs/write";
}
```
- 위와 같이 작성하면 write.jsp 화면으로 이동할 수 있다.
- 다만 유지보수 부분에서는 좋지 않다.

```java
@RequestMapping("/write")
public String write(String bname) {
	return bname + "/write";
}
```
- 위와 같이 작성하면 유지보수가 더 수월해 진다.
### 3.3.2. 수정한 코드 전문
```java
package com.ict.bbs;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.BbsDAO;
import mybatis.vo.BbsVO;
import spring.util.Paging;

@Controller
public class BbsController {
	
	@Autowired
	private BbsDAO b_dao;
	
	int nowPage;
	int rowTotal;
	int blockList = 7;
	int blockPage = 5;
	
	@RequestMapping("/list")
	public ModelAndView list(String cPage, String bname) {
		ModelAndView mv = new ModelAndView();
		
		if(cPage == null) nowPage = 1;
		else nowPage = Integer.parseInt(cPage);
		
		if(bname == null) bname = "bbs";
		
		rowTotal = b_dao.getCount(bname);
		
		Paging page = new Paging(blockList, blockPage);
		page.setTotalRecord(rowTotal);
		page.setNowPage(nowPage);
		
		int begin = page.getBegin();
		int end = page.getEnd();
		BbsVO[] ar = b_dao.getList(bname, begin, end);
		
		mv.addObject("ar", ar);
		mv.addObject("page", page);
		mv.addObject("nowPage", nowPage);
		mv.addObject("bname", bname);
		mv.setViewName(bname + "/list");
		
		return mv;
	}
	
	@RequestMapping("/write")
	public String write(String bname) {
		return bname + "/write";
	}

}
```
## 3.4. write.jsp - summernote editor 추가
아래 경로에 summernote의 파일들을 가져다 놓자.
- src > main > webapp > resources > css >
	- style.css
	- summernote-lite.css
	- font
- src > main > webapp > resources > js
	- summernote-lite.js
	- lang
		- summernote-ko-KR.js

```HTML
<link rel="stylesheet" href="resources/css/style.css"/>
<link rel="stylesheet" href="resources/css/summernote-lite.css"/>
```
- 기존의 `<style>` 태그를 삭제하고 위와 같이 `<link>` 태그를 등록한다.

```HTML
<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
<script src="resources/js/summernote-lite.js"></script>
<script src="resources/js/lang/summernote-ko-KR.js"></script>
```
- jQuery와 summernote를 위한 `<script>` 태그를 등록한다.

```javascript
$(function(){
	$("#content").summernote({
		lang: "ko-KR"		
	});
});
```
- summernote를 등록한다.
## 3.5. 실행 화면
> localhost:8080/bbs/write?bname=bbs

![[Pasted image 20240108143109.png|400]]
- bname의 값이 있어야하므로 bbs의 값을 같이 보내자.
# 4. 게시판 만들기 - 쓰기 제출
## 4.1. write.jsp
### 4.1.1. 수정 사항
```HTML
<form action="write" method="post" encType="multipart/form-data">
```
- form 태그의 action을 write로 수정한다. <font color="#7f7f7f">Controller 필요 없다.</font>

```HTML
<input type="hidden" name="bname" value="bbs"/>
```
- table 태그 밑에 위의 내용을 추가하자.
- form 태그의 값을 제출할 때 bname이라는 값이 같이 넘어가게 된다.
### 4.1.2. editor를 사용하면
```javascript
function sendData(){
	for(var i=0 ; i<document.forms[0].elements.length ; i++){
		if(i >= 3) break;
		if(document.forms[0].elements[i].value == ""){
			alert(document.forms[0].elements[i].dataset.str + "를 입력하세요");
			document.forms[0].elements[i].focus();
			return;
		}
	}
	document.forms[0].submit();
}
```
- 유효성 검사를 실행할 때 위와 같은 반복문을 사용하면 안 된다.
	- editor의 각 버튼들 또한 input 태그이기 때문이다.

```javascript
function sendData(){
	if(document.forms[0].title.value.trim().length == 0){
		alert("제목을 입력하세요.");
		document.forms[0].title.focus();
		return;
	}
	if(document.forms[0].writer.value.trim().length == 0){
		alert("작성자를 입력하세요.");
		document.forms[0].writer.focus();
		return;
	}

	document.forms[0].submit();
}
```
- 따라서 위와 같이 작성하면 되겠다.
## 4.2. BbsController.java
> src/main/java > com.ict.bbs >

### 4.2.1. 코드 추가
```java
@Autowired
private HttpServletRequest request;
```
- Autowired Annotation으로 HttpServletRequest를 지정하자.

```java
@Autowired
private ServletContext application;

private String upload_file = "/resources/upload_file";
private String editor_img = "/resources/editor_img";
```
- src > main > webapp > resources > upload_file을 업로드 폴더로 지정한다.
- 이를 위해 ServletContext를 Autowired Annotation으로 연결한다.
- 첨부파일의 저장 위치를 담은 변수를 생성한다.
- summernote 에디터에서 추가한 이미지를 저장할 위치를 담은 변수를 생성한다.

```java
@RequestMapping(value="/write", method=RequestMethod.POST)
public String write() {
	
	return "/write";
}
```
- 글을 쓰고 저장 버튼을 눌렀을 때 이 위치로 제목, 글쓴이, 내용을 받을 수 있어야 한다.
---
- 받아야 할 파라미터는 subject, writer, content, file, bname이다.
	- 이 중 <font color="#fac08f">MultipartFile이 존재한다면</font> [[2024-01-05 Spring Framework - AJAX#^25cab8|servletMultipartResolver를 정의]]해야 한다.
- BbsVO 파일을 확인하자.
	- String bname, MultipartFile file이 있는지 확인하고, 없으면 추가하자.
- write.jsp의 form 내의 name과 BbsVO의 멤버 변수 이름이 일치하는지 확인하자.
	- <font color="#92cddc">BbsVO의 subject와 동일하게 write.jsp의 title을 subject로 바꿨다.</font>
	- sendData 함수의 title도 subject로 바꾸자.

```java
@RequestMapping(value="/write", method=RequestMethod.POST)
public String write(BbsVO vo) {
	System.out.println(
				vo.getWriter() + " / "
				+ vo.getContent() + " / "
				+ vo.getFile().getOriginalFilename()
			);		
	
	return vo.getBname() + "/write";
}
```
- Spring Framework의 `@RequestMapping` Annotation에서 `method=RequestMethod.POST`로 설정된 메서드는 HTTP POST 요청을 처리하는 역할을 한다. 이 메서드는 form 데이터를 서버로 전송할 때 <font color="#fac08f">자동으로 form 데이터를 해당 메서드의 매개변수로 매핑</font>한다.
- BbsVO vo는 write 메서드의 매개변수로 전달되는 객체인데, Spring MVC는 <font color="#92cddc">요청을 분석</font>하고, 요청의 내용을 기반으로 <font color="#92cddc">BbsVO 클래스의 인스턴스를 생성</font>하고, 그 인스턴스에 요청된 데이터를 <font color="#92cddc">자동으로 바인딩</font>한다.
### 4.2.2. 제출된 값을 파라미터로 가져온 로직
- 글쓰기 form은
	- action = "write"
	- method = "POST"
	- encType = "multipart/form-data"
- 으로 이루어져 있고, 이를 submit 하면
- `@RequestMapping(value="/write", method=RequestMethod.POST)`이 걸린 곳으로 온다.
- 매핑된 write 함수의 파라미터로 BbsVO이므로 자동으로 BbsVO 객체를 생성해버린다.
- 넘어오는 파라미터들을 BbsVO 객체의 멤버변수와 비교해 자동 매핑한다.
- 따라서 write 함수 내에서는 BbsVO 객체를 이용해 값을 가져올 수 있다.
### 4.2.3. 파일이 첨부가 되어 있다면
```java
@RequestMapping(value="/write", method=RequestMethod.POST)
public String write(BbsVO vo) {
	String c_type = request.getContentType();
	if(c_type.startsWith("multipart")) {
		MultipartFile f = vo.getFile();
		if(f != null && f.getSize() > 0) {
			
		}
	}
	
	return vo.getBname() + "/write";
}
```
- 첨부 파일이 있는 경우 encType은 multipart로 시작된다.
	- 따라서 request.getContentType을 이용해 encType을 확인한다.
- 파일 첨부가 됐는지 확인해야 한다.
	- 파일이 첨부된 상태라면 f가 null이 아니고 size는 0보다 크다.
#### 4.2.3.1. 첨부된 파일의 이름이 같다면 `if(f != null && f.getSize() > 0)`
```java
String fname = null;
if(f != null && f.getSize() > 0) {
	String realPath = application.getRealPath(upload_file);
	fname = f.getOriginalFilename();
	vo.setOri_name(fname);
	fname = FileRenameUtil.checkSameFileName(fname, realPath);
}
```
- 이미 같은 파일명이 존재할 경우 파일명을 변경한다.
- setOri_name에 원래의 파일명을 저장한다.

```java
try {
	f.transferTo(new File(realPath, fname));
	vo.setFile_name(fname);
} catch (Exception e) {
	e.printStackTrace();
}
```
- transferTo를 이용해 파일 업로드를 한다.
- setFile_name에 업로드된 파일명을 저장한다.

```java
vo.setIp(request.getRemoteAddr());
```
- if 문을 탈출하면 ip를 저장한다.
#### 4.2.3.2. DB에 저장되도록 하자.
```java
b_dao.add(vo);
```
- DAO의 add 함수를 호출한다.

```java
public ModelAndView write(BbsVO vo) {
```
- write 함수의 반환형을 ModelAndView로 수정하자.

```java
ModelAndView mv = new ModelAndView();
mv.setViewName("redirect:/list?bname=" + vo.getBname());

return mv;
```
- 원글을 저장한 후 목록화면으로 이동한다.
- "redirect:/list"는 list를 호출할 때 forward가 아닌 redirect로 호출한다. #redirect
- bname도 함께 보내자.
### 4.2.4. 수정한 코드 전문
```java
package com.ict.bbs;

import java.io.File;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.servlet.ModelAndView;

import mybatis.dao.BbsDAO;
import mybatis.vo.BbsVO;
import spring.util.FileRenameUtil;
import spring.util.Paging;

@Controller
public class BbsController {
	
	private String upload_file = "/resources/upload_file";
	private String editor_img = "/resources/editor_img";
	
	@Autowired
	private BbsDAO b_dao;
	
	@Autowired
	private HttpServletRequest request;
	
	@Autowired
	private ServletContext application;
	
	int nowPage;
	int rowTotal;
	int blockList = 7;
	int blockPage = 5;
	
	@RequestMapping("/list")
	public ModelAndView list(String cPage, String bname) {
		ModelAndView mv = new ModelAndView();
		
		if(cPage == null) nowPage = 1;
		else nowPage = Integer.parseInt(cPage);
		
		if(bname == null) bname = "bbs";
		
		rowTotal = b_dao.getCount(bname);
		
		Paging page = new Paging(blockList, blockPage);
		page.setTotalRecord(rowTotal);
		page.setNowPage(nowPage);
		
		int begin = page.getBegin();
		int end = page.getEnd();
		BbsVO[] ar = b_dao.getList(bname, begin, end);
		
		mv.addObject("ar", ar);
		mv.addObject("page", page);
		mv.addObject("nowPage", nowPage);
		mv.addObject("bname", bname);
		mv.setViewName(bname + "/list");
		
		return mv;
	}
	
	@RequestMapping("/write")
	public String write(String bname) {
		return bname + "/write";
	}
	
	@RequestMapping(value="/write", method=RequestMethod.POST)
	public ModelAndView write(BbsVO vo) {
		String c_type = request.getContentType();
		if(c_type.startsWith("multipart")) {
			MultipartFile f = vo.getFile();
			String fname = null;
			if(f != null && f.getSize() > 0) {
				String realPath = application.getRealPath(upload_file);
				fname = f.getOriginalFilename();
				vo.setOri_name(fname);
				fname = FileRenameUtil.checkSameFileName(fname, realPath);
				
				try {
					f.transferTo(new File(realPath, fname));
					vo.setFile_name(fname);
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
			vo.setIp(request.getRemoteAddr());
			b_dao.add(vo);
		}
		ModelAndView mv = new ModelAndView();
		mv.setViewName("redirect:/list?bname=" + vo.getBname());
		
		return mv;
	}

}
```
## 4.3. bbs.xml
> src > main > resources > mybatis > mapper >
### 4.3.1. 코드 수정
```xml
<insert id="add" parameterType="mybatis.vo.BbsVO">
	INSERT INTO bbs_t(subject, writer, content, file_name, ori_name, write_date, ip, hit, bname, status)
	VALUES(#{subject}, #{writer}, #{content}, #{file_name}, #{ori_name}, NOW(), #{ip}, 0, #{bname}, 0)
</insert>
```
- parameterType을 mybatis.vo.BbsVO로 수정한다.
## 4.4. BbsDAO.java
> src/main/java > mybatis.dao >
### 4.4.1. 코드 수정
```java
public int add(BbsVO vo) {
	
	return ss.insert("bbs.add", vo);
}
```
- 다음과 같이 짧게 수정하자.
## 4.5. 실행 화면
![[Pasted image 20240108161745.png]]
- DB에 제대로 저장되는 것을 볼 수 있다.
# 5. 게시판 만들기 - 에디터에 이미지 첨부
- 에디터에서 이미지를 추가하였을 때 소스 보기에서
	```
	<p style="line-height: 0.7;">테스트예용 테슷흐</p><p style="line-height: 0.7;"><img src="/bbs/resources/editor_img/image2.png" style="width: 481.328px;"><br></p>
	```
	- 위와 같이 나타나면 된다.
## 5.1. pom.xml - jackson 라이브러리 추가
> 0108_STS_Bbs >
### 5.1.1. jackson 라이브러리 추가
```xml
<dependency>
	<groupId>org.codehaus.jackson</groupId>
	<artifactId>jackson-mapper-asl</artifactId>
	<version>1.9.13</version>
</dependency>
```
### 5.1.2. 수정된 코드 전문
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ict</groupId>
	<artifactId>bbs</artifactId>
	<name>0108_STS_Bbs</name>
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
		
		<!-- 추가 라이브러리 -->
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
	    	<groupId>commons-logging</groupId>
		    <artifactId>commons-logging</artifactId>
		    <version>1.2</version>
		</dependency>
		<dependency>
	    	<groupId>commons-dbcp</groupId>
		    <artifactId>commons-dbcp</artifactId>
		    <version>1.4</version>
		</dependency>
		<dependency>
	    	<groupId>commons-pool</groupId>
		    <artifactId>commons-pool</artifactId>
		    <version>1.6</version>
		</dependency>
		<dependency>
	    	<groupId>commons-io</groupId>
		    <artifactId>commons-io</artifactId>
		    <version>2.11.0</version>
		</dependency>
		<dependency>
	    	<groupId>commons-fileupload</groupId>
		    <artifactId>commons-fileupload</artifactId>
		    <version>1.4</version>
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
## 5.2. servlet-context.xml - Bean 추가
> src > main > webapp > WEB-INF > spring > appServlet > 
### 5.2.1. Bean 추가
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
- MultipartResolver도 같이 등록해야 하지만, 앞서 파일 첨부 부분에서 이미 등록을 하였다.
### 5.2.2. 수정된 코드 전문
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
	
	<beans:bean id="jsonHttpMessageConverter" class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"/>
	<beans:bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
		<beans:property name="messageConverters">
			<beans:list>
				<beans:ref bean="jsonHttpMessageConverter"/>
			</beans:list>
		</beans:property>
	</beans:bean>
	
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
	
	<context:component-scan base-package="com.ict.bbs" />
</beans:beans>
```
## 5.3. BbsController.java - saveImg 함수 추가
> src/main/java > com.ict.bbs >
### 5.3.1. 내가 한 saveImg 함수 추가
```java
@RequestMapping(value="/saveImg", method=RequestMethod.POST)
@ResponseBody
public Map<String, String> saveImg(ImgVO ivo){
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
	map.put("fname",  fname);
	
	return map;
}
```
### 5.3.2. 센세가 한 saveImg 함수 추가
```java
@RequestMapping(value="/saveImg", method=RequestMethod.POST)
@ResponseBody
public Map<String, String> saveImg(MultipartFile s_file){
	Map<String, String> map = new HashMap<String, String>();
	
	String fname = null;
	if(s_file != null && s_file.getSize() > 0) {
		String realPath = application.getRealPath(editor_img);
		fname = s_file.getOriginalFilename();
		fname = FileRenameUtil.checkSameFileName(fname, realPath);
		
		try {
			s_file.transferTo(new File(realPath, fname));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	String c_path = request.getContextPath();
	map.put("url", c_path + editor_img);
	map.put("fname",  fname);
	
	return map;
}
```
- `@RequestMapping(value="/saveImg", method=RequestMethod.POST)`을 추가한다. ^ac1777
	- `@RequesetMapping("/saveImg")`는 기본적으로 모든 HTTP 메서드<font color="#7f7f7f">(GET, POST, PUT, DELETE 등)</font>에 대해 매핑된다.
	- 이 경우 <font color="#fac08f">POST 요청에 대해서만</font> 해당 메서드가 호출된다.
- `@ResponseBody`을 추가한다.
	- 이는 해당 메서드가 반환하는 값이 <font color="#92cddc">HTTP Response의 본문</font><font color="#7f7f7f">(body)</font><font color="#92cddc">에 직접 포함되어야 함</font>을 나타낸다.
	- saveImg 메서드는 Map 객체를 반환하므로 이 Map 객체는 JSON 형식으로 변환되어 HTTP Response의 본문에 포함된다. 클라이언트는 서버로부터 이 Response를 받아와서 JSON 형식의 데이터를 사용할 수 있게 된다.
	- 만일 `@ResponseBody`가 <font color="#c3d69b">없다면</font> Spring MVC는 해당 메서드의 <font color="#c3d69b">return을 View로 처리하려고 시도</font>한다.
- `if(s_file != null && s_file.getSize() > 0)`에서
	- 파일이 잘 들어온 경우 업로드할 위치<font color="#7f7f7f">(editor_img)</font>를 절대경로화 한다.
	- getOriginalFilename으로 파일명을 얻는다.
	- checkSameFileName으로 파일명이 동일한 경우 파일명을 변경한다.
	- transferTo로 파일을 업로드한다.
- request.getContextPath로 서버의 경로를 얻어낸다.
- url에 서버 경로와 editor_img 폴더의 경로를 붙여 저장한다.
- return한 map은 JSON으로 변환되어 반환된다.
### 5.3.3. 수정된 코드 전문
```java
package com.ict.bbs;

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
	
	private String upload_file = "/resources/upload_file";
	private String editor_img = "/resources/editor_img";
	
	@Autowired
	private BbsDAO b_dao;
	
	@Autowired
	private HttpServletRequest request;
	
	@Autowired
	private ServletContext application;
	
	int nowPage;
	int rowTotal;
	int blockList = 7;
	int blockPage = 5;
	
	@RequestMapping("/list")
	public ModelAndView list(String cPage, String bname) {
		ModelAndView mv = new ModelAndView();
		
		if(cPage == null) nowPage = 1;
		else nowPage = Integer.parseInt(cPage);
		
		if(bname == null) bname = "bbs";
		
		rowTotal = b_dao.getCount(bname);
		
		Paging page = new Paging(blockList, blockPage);
		page.setTotalRecord(rowTotal);
		page.setNowPage(nowPage);
		
		int begin = page.getBegin();
		int end = page.getEnd();
		BbsVO[] ar = b_dao.getList(bname, begin, end);
		
		mv.addObject("ar", ar);
		mv.addObject("page", page);
		mv.addObject("nowPage", nowPage);
		mv.addObject("bname", bname);
		mv.setViewName(bname + "/list");
		
		return mv;
	}
	
	@RequestMapping("/write")
	public String write(String bname) {
		return bname + "/write";
	}
	
	@RequestMapping(value="/write", method=RequestMethod.POST)
	public ModelAndView write(BbsVO vo) {
		String c_type = request.getContentType();
		if(c_type.startsWith("multipart")) {
			MultipartFile f = vo.getFile();
			String fname = null;
			if(f != null && f.getSize() > 0) {
				String realPath = application.getRealPath(upload_file);
				fname = f.getOriginalFilename();
				vo.setOri_name(fname);
				fname = FileRenameUtil.checkSameFileName(fname, realPath);
				
				try {
					f.transferTo(new File(realPath, fname));
					vo.setFile_name(fname);
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
			vo.setIp(request.getRemoteAddr());
			b_dao.add(vo);
		}
		ModelAndView mv = new ModelAndView();
		mv.setViewName("redirect:/list?bname=" + vo.getBname());
		
		return mv;
	}
	
	@RequestMapping(value="/saveImg", method=RequestMethod.POST)
	@ResponseBody
	public Map<String, String> saveImg(MultipartFile s_file){
		Map<String, String> map = new HashMap<String, String>();
		
		String fname = null;
		if(s_file != null && s_file.getSize() > 0) {
			String realPath = application.getRealPath(editor_img);
			fname = s_file.getOriginalFilename();
			fname = FileRenameUtil.checkSameFileName(fname, realPath);
			
			try {
				s_file.transferTo(new File(realPath, fname));
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
		
		String c_path = request.getContextPath();
		map.put("url", c_path + editor_img);
		map.put("fname",  fname);
		
		return map;
	}

}
```
## 5.4. write.jsp - summernote 함수 수정과 추가
> src > main > webapp > WEB-INF > views > bbs >
### 5.4.1. summernote 함수 수정
```javascript
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
```
- callbacks 속성을 추가한다.
	- onImageUpload를 이용해 이미지를 첨부할 때 sendImage가 동작하도록 한다.
### 5.4.2. sendImage 함수 추가
```javascript
function sendImage(file, editor){
	let frm = new FormData();
	frm.append("s_file", file);
	
	$.ajax({
		url: "saveImg",
		type: "POST",
		data: frm,
		contentType: false,
		processData: false,
		cache: false,
		dataType: "json"
	}).done(function(data){
		$("#content").summernote("editor.insertImage", data.url + "/" + data.fname);
	});
	
}
```
- JavaScript로 비동기식 통신을 할 때 파일 전송은 FormData 객체를 활용해야 한다.
	- form 태그의 action에서 "multipart/form-data"의 form-data와 같다.
- append를 이용해 서버로 보내고자 하는 파일을 FormData에 저장한다.
	- 앞의 s_file을 기억했다가 Spring의 파라미터와 같은 이름으로 똑같이 지정해야 한다.
- AJAX를 이용해 비동기식 통신을 한다.
	- contentType : 서버로 전송되는 데이터의 유형을 지정한다.
		- false : jQeury가 자동으로 올바른 contentType을 설정하도록 한다.
	- processData : 데이터를 전송하기 전에 jQuery가 데이터를 처리할지 여부
		- false : jQuery가 데이터를 자동으로 변환하지 않도록 한다.
	- cache : 브라우저에게 결과를 캐시할지 여부
		- false : 캐싱을 비활성화. 요청 결과가 항상 서버에서 새로 가져오도록 강제한다.
### 5.4.3. 수정된 코드 전문
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
					<td><input type="text" name="subject" size="45" data-str="제목"/></td>
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
						<input type="button" value="보내기"	onclick="sendData()"/>
						<input type="button" value="다시"/>
						<input type="button" value="목록"/>
					</td>
				</tr>
			</tbody>
		</table>
		<input type="hidden" name="bname" value="bbs"/>
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
			
			$.ajax({
				url: "saveImg",
				type: "POST",
				data: frm,
				contentType: false,
				processData: false,
				cache: false,
				dataType: "json"
			}).done(function(data){
				$("#content").summernote("editor.insertImage", data.url + "/" + data.fname);
			});
			
		}
		
		function sendData(){
			if(document.forms[0].subject.value.trim().length == 0){
				alert("제목을 입력하세요.");
				document.forms[0].title.focus();
				return;
			}
			if(document.forms[0].writer.value.trim().length == 0){
				alert("작성자를 입력하세요.");
				document.forms[0].writer.focus();
				return;
			}
	
			document.forms[0].submit();
		}
	</script>
</body>
</html>
```
# 6. 참고
## 6.1. Servlet? #Servlet 
- <font color="#7f7f7f">Java 언어를 기반으로 하는 Web Application에서</font> 동적인 웹 페이지를 생성<font color="#7f7f7f">하고, </font>클라이언트의 요청을 처리<font color="#7f7f7f">하는 Java</font> 클래스<font color="#7f7f7f">이다.</font>
	- Client가 서버로 요청을 보내면, Servlet은 해당 요청을 받아 처리한다.
	- 동적인 응답 생성, 데이터 처리, 세션 관리, HTTP 요청 및 응답을 처리할 수 있다.
	- Client가 Servlet에 대한 요청을 전송하면 Web Container<font color="#7f7f7f">(예 : Apache Tomcat)</font>는 해당 Servlet을 찾아서 로드하고 인스턴스를 생성한다.
### 6.1.1. 식당의 주방, 서빙, 직원
- Client<font color="#7f7f7f">(손님)</font>
	- Client는 주문을 하고 음식을 기다린다.
- Servlet<font color="#7f7f7f">(웨이터)</font>
	- 손님이 주문을 하면, Servlet은 그 요청을 받아들이고, 요청에 따라 응답을 생성한다.
- Server<font color="#7f7f7f">(주방)</font>
	- 웨이터가 요청을 받으면 Server는 그 요청을 처리하고 필요한 데이터를 가져오거나 연산을 수행한다.
- HTTP Response<font color="#7f7f7f">(완성된 음식)</font>
	- 웨이터가 요청을 처리하고 주방에서 필요한 작업을 완료하면, HTTP Response가 손님에게 돌아가게 된다.
## 6.2. ServletContext? #ServletContext
- <font color="#7f7f7f">Java Web Application에서 사용되는</font> Interface
- <font color="#7f7f7f">Web Application 전체에 대한 정보를 제공하고</font> Servlet 간에 데이터를 공유<font color="#7f7f7f">하는 데 사용한다.</font>
- <font color="#7f7f7f">이 Interface는</font> 모든 Servlet이나 JSP에서 접근 가능<font color="#7f7f7f">하다.</font>
### 6.2.1. ServletContext의 주요 기능(메서드)
- web application 환경 정보 제공
	- getServerInfo()<font color="#7f7f7f">: 현재 실행 중인 server의 정보를 얻을 수 있다.</font>
	- getServletContextName()<font color="#7f7f7f">: web application의 이름을 얻을 수 있다.</font>
- 리소스 로딩 및 접근
	- <font color="#fac08f">getRealPath()</font><font color="#7f7f7f">: web application 내의</font> 실제 파일 경로<font color="#7f7f7f">를 얻을 수 있다.</font>
	- getResource(), getResourceAsStream()<font color="#7f7f7f">: classPath나 web application의 상대 경로를 기반으로 리소스를 얻을 수 있다.</font>
- Servlet 간 데이터 공유
	- setAttribute(), getAttribute(), removeAttribute()<font color="#7f7f7f">: ServletContext를 통해 Servlet 간에 데이터를 공유할 수 있다.</font>
- 세션 관리
	- getSessionCookieConfig()<font color="#7f7f7f">: 세션 쿠키의 설정을 얻을 수 있다.</font>
	- getSession(), getNamedDispatcher()<font color="#7f7f7f">: 세션 객체를 생성하거나 요청에 따른 ServletDispatcher를 얻을 수 있다.</font>
- 기타 웹 애플리케이션 수준 설정
	- getInitParameter(), getInitParameterNames()<font color="#7f7f7f">: web.xml에서 설정한 초기화 매개변수에 접근할 수 있다.</font>
	- log(), log(Exception, String)<font color="#7f7f7f">: logging을 통해 로그 메시지를 출력할 수 있다.</font>
### 6.2.2. Dispatcher? #Dispatcher
- <font color="#7f7f7f">Web Application에서</font> Client의 요청을 적절한 Servlet이나 JSP로 전달하는 역할<font color="#7f7f7f">을 하는 객체 또는 인터페이스를 의미한다.</font>
- 전달 받은 Servlet이나 Resource가 그 응답을 Client에게 반환한다.
#### 6.2.2.1. RequestDispatcher? #RequestDispatcher
- Servlet Container 내에서 Servlet이나 JSP로 요청을 전달하는 데 사용된다.
- ServletRequest의 getRequestDispatcher() 메서드를 통해 얻을 수 있다.
- 주로 Servlet에서 다른 Servlet이나 JSP로 forward 할 때 사용한다.
- 예제 코드
	```java
	RequestDispatcher dispatcher = request.getRequestDispatcher("/path/to/servlet");
	dispatcher.forward(request, response);
	```
#### 6.2.2.2. FilterDispatcher? #FileterDispatcher
- Filter에서 처리된 후, 다음에 실행될 Servlet이나 Resource를 지정하는 데 사용된다.
- FilterChain의 doFilter() 메서드를 통해 지정할 수 있다.
- Filter는 요청이나 응답을 수정하거나 가로챌 때 사용된다.
- 예제 코드
	```java
	filterChain.doFilter(request, response);
	RequestDispatcher dispatcher = request.getRequestDispatcher("/path/to/resource");
	dispatcher.forward(request, response);
	```
#### 6.2.2.3. forward? #forward 
- Client에게 새로운 URL로의 요청이 발생하지 않고, 서버에서의 내부 전이를 의미한다.
## 6.3. Annotation? #Annotation
- Java 프로그래밍 언어의 <font color="#95b3d7">메타데이터를 표현하는 방식</font> 중 하나이다.
	- 메타데이터는 데이터에 대한 데이터로, 소스 코드에 추가 정보를 부여하여 컴파일러, 개발 도구, 런타임 환경 등에 부가적인 정보를 제공한다.
- `@` 기호로 시작하며, <font color="#95b3d7">주로 클래스, 메서드, 필드 등에 적용</font>된다.
- 개발자가 직접 사용할 수도 있으며, 일부는 Framework나 Library에서 관리된다.
	- `@RequestMapping`과 같은 annotation들은 Spring이 관리한다.
	- Framework는 reflection을 통해 annotaion 정보를 읽고 해석하여 적절한 동작을 수행한다.
	- 사용자 정의 annotation도 만들 수 있다.
### 6.3.1. @Autowired? #Autowired
- <font color="#c3d69b">의존성 주입</font><font color="#7f7f7f">(Dependency Injection)</font><font color="#c3d69b">을 자동으로 수행</font>하는 데 사용된다.
- `@Autowired`는 Spring Container에서 Bean으로 등록되어야 한다.
	- 즉 <font color="#95b3d7">해당 클래스나 메서드가</font> `@Component`, `@Service`, `@Repository`, `@Controller` 등과 같은 <font color="#95b3d7">Spring Bean으로 등록되어 있어야</font> 한다.
	- 해당 annotation이 부여된 필드, 생성자, 메서드가 호출되는 시점에 Spring Container는 해당 타입의 Bean을 검색해 해당 위치에 주입한다.
### 6.3.2. @Controller? #Controller
- <font color="#c3d69b">해당 클래스가 Controller 역할을 수행한다고 지정된다.</font>
- <font color="#938953">Spring Container에 Bean으로 등록</font>되므로 `@Autowired`를 사용하여 DI를 받을 수 있게 된다.
- 해당 annotation이 부여된 클래스 내의 <font color="#92cddc">메서드들은 HTTP 요청을 처리하기 위한 메서드로 간주</font>되어 `@RequestMapping`을 사용하여 특정 URL과 연결되고, 해당 URL로 들어오는 요청에 대한 처리를 담당한다.
- 일반적으로 메서드들은 결과로 View의 이름을 반환한다. 이 이름은 View Resolver에 의해 실제 View로 매팽되어 Client에게 보여진다.
### 6.3.3. @RequestMapping? #RequestMapping
- Controller 메서드에 특정 URL을 매핑하는 데 사용된다.
- `@RequestMapping("/list")`는 기본적으로 모든 HTTP 메서드<font color="#7f7f7f">(GET, POST, PUT, DELETE 등)</font>에 대해 매핑된다.
- `@RequesteMapping(value="/list", method=RequestMethod.POST)`는 POST 요청에 대해서만 해당 메서드가 호출된다.
	- 이 경우 form 데이터를 서버로 전송할 때 <font color="#fac08f">자동으로 form 데이터를 해당 메서드의 매개변수로 매핑</font>한다.
### 6.3.4. @ResponseBody? #ResponseBody
- 이는 해당 메서드가 반환하는 값이 <font color="#92cddc">HTTP Response의 본문</font><font color="#7f7f7f">(body)</font><font color="#92cddc">에 직접 포함되어야 함</font>을 나타낸다.
- 예를 들어, saveImg 메서드는 Map 객체를 반환하므로 이 Map 객체는 JSON 형식으로 변환되어 HTTP Response의 본문에 포함된다. 클라이언트는 서버로부터 이 Response를 받아와서 JSON 형식의 데이터를 사용할 수 있게 된다.
- 만일 `@ResponseBody`가 <font color="#c3d69b">없다면</font> Spring MVC는 해당 메서드의 <font color="#c3d69b">return을 View로 처리하려고 시도</font>한다.

# 7. 요약
[[2024-01-05 ~ 2024-01-11 Spring Framework MVC BBS]]
## 7.1. pom.xml은
- dependencies 태그 안에 dependency 태그로 필요한 라이브러리를 추가한다.
## 7.2. servlet-context.xml은
- 아래의 bean을 추가했다.
	- multipartResolver
	- jsonHttpMessageConverter
	- AnnotationMethodHandlerAdaptor