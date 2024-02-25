# 1. 페이징 활성화
> Spring Legacy Project > 0108_STS_Bbs
## 1.1. list.jsp
### 1.1.1. href 링크 수정
#### 1.1.1.1. 수정 전 코드
```HTML
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
		<td><input type="button" value="글쓰기" onclick="javascript:location.href='write?bname=bbs'" /></td>
	</tr>
</tfoot>
```
- "Controller?type=bbsList&cPage="를 "list?cPage="로 수정하자.
#### 1.1.1.2. 수정한 코드
```HTML
<tfoot>
	<tr>
		<td colspan="4">
			<ol class="paging">
				<c:if test="${requestScope.page.startPage < requestScope.page.pagePerBlock}">
					<li class="disable">&lt;</li>
				</c:if>
				<c:if test="${requestScope.page.startPage >= requestScope.page.pagePerBlock}">
					<li><a href="list?cPage=${page.startPage - page.pagePerBlock}">&lt;</a></li>
				</c:if>
				<c:forEach begin="${page.startPage}" end="${page.endPage}" varStatus="vs">
					<c:if test="${vs.index eq page.nowPage}">
						<li class="now">${vs.index}</li>
					</c:if>
					<c:if test="${vs.index ne page.nowPage}">
						<li><a href="list?cPage=${vs.index}">${vs.index}</a></li>
					</c:if>
				</c:forEach>
				<c:if test="${page.endPage < page.totalPage}">
					<li><a href="list?cPage=${page.startPage + page.pagePerBlock}">&gt;</a></li>
				</c:if>
				<c:if test="${page.endPage >= page.totalPage}">
					<li class="disable">&gt;</li>
				</c:if>
			</ol>
		</td>
		<td><input type="button" value="글쓰기" onclick="javascript:location.href='write?bname=bbs'" /></td>
	</tr>
</tfoot>
```
### 1.1.2. li 태그를 Paging2에 넣기
- JSP에서 매번 li 태그를 넣으며 구성할 필요가 없도록 하자.
- Paging2.java를 만들고, 아래 부분을 삭제하자.

```java
<ol class='paging'>
	<c:if test="${requestScope.page.startPage < requestScope.page.pagePerBlock}">
		<li class='disable'>&lt;</li>
	</c:if>
	<c:if test="${requestScope.page.startPage >= requestScope.page.pagePerBlock}">
		<li><a href='list?cPage=${page.startPage - page.pagePerBlock}'>&lt;</a></li>
	</c:if>
	<c:forEach begin="${page.startPage}" end="${page.endPage}" varStatus="vs">
		<c:if test="${vs.index eq page.nowPage}">
			<li class="now">${vs.index}</li>
		</c:if>
		<c:if test="${vs.index ne page.nowPage}">
			<li><a href="list?cPage=${vs.index}">${vs.index}</a></li>
		</c:if>
	</c:forEach>
	<c:if test="${page.endPage < page.totalPage}">
		<li><a href="list?cPage=${page.startPage + page.pagePerBlock}">&gt;</a></li>
	</c:if>
	<c:if test="${page.endPage >= page.totalPage}">
		<li class="disable">&gt;</li>
	</c:if>
</ol>
```
- 삭제
### 1.1.3. Paging2에 맞도록 수정하기
- BbsController의 ModelAndView를 수정 후 totalRecord, nowPage, numPerPage 앞에 달린 `page.`을 삭제하자.
- 위에서 삭제한 곳에 `${pageCode}`를 삽입하자.
### 1.1.4. 수정된 코드 전문
```HTML
<%@page import="spring.util.Paging"%>
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
						${pageCode }
					</td>
					<td><input type="button" value="글쓰기" onclick="javascript:location.href='write?bname=bbs'" /></td>
				</tr>
			</tfoot>
			<tbody>
				<c:forEach var="vo" items="${requestScope.ar}" varStatus="vs">
					<tr>
						<td>${totalRecord - ((nowPage - 1) * numPerPage + vs.index)}</td>
						<td style="text-align: left">
							<a href="Controller?type=view&b_idx=${vo.b_idx}&cPage=${nowPage}">
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

## 1.2. Paging2.java
> src/main/java > spring.util > Java Class

- 개선된 페이징 기법을 위해 새로운 파일 생성
### 1.2.1. 코드 작성
```java
private int nowPage, rowTotal, blockList, blockPage, totalPage, startPage, endPage, begin, end;
private boolean isPrePage;
private boolean isNextPage;
```
- nowPage: 현재 페이지
- rowTotal: 총 게시물 수
- blockList: 한 페이지에 표현할 게시물 수
- blockPage: 한 블럭에 보여질 페이지 수
- totalPage: 총 페이지 수
- startPage: <font color="#7f7f7f">(페이징 단에서)</font> 시작 페이지
- endPage: <font color="#7f7f7f">(페이징 단에서)</font> 끝 페이지
- begin: 현재 페이지의 시작 게시물의 행 번호
- end: 현재 페이지의 마지막 게시물의 행 번호
- isPrePage: 이전 기능 가능 여부 (true일 때 활성화)
- isNextPage: 다음 기능 가능 여부 (true일 때 활성)

```java
private StringBuffer sb;
```
- sb: JSP와 같은 View page에서 표현할 Paging HTML을 생성하여 저장할 곳

```java
public Paging2(int nowPage, int rowTotal, int blockList, int blockPage) {
	this.nowPage = nowPage;
	this.rowTotal = rowTotal;
	this.blockList = blockList;
	this.blockPage = blockPage;
```
- 생성자를 통해 모든 멤버 변수를 초기화 할 수 있도록 한다.
- 이를 위해 생성자에서 4개의 인자를 받도록 한다.

```java
isPrePage = false;
isNextPage = false;
```
- 이전 기능과 다음 기능을 초기화 시킨다.

```java
totalPage = (int) Math.ceil((double) rowTotal / blockList);
```
- 전체 게시물의 수를 통해 전체 페이지 값을 구한다.
- Math.ceil()의 인자는 double 형이어야 하므로 double로 형 변환을 하고, totalPage는 int 형이므로 계산 결과는 int 형으로 형 변환 한다.

```java
if(nowPage > totalPage) nowPage = totalPage;
```
- 현재 페이지가 전체 페이지 수보다 큰 값을 가질 수 없도록 현재 페이지의 최댓값이 전체 페이지의 수가 되도록 하자.

```java
startPage = (int)((nowPage - 1) / blockPage) * blockPage + 1;
endPage = startPage + blockPage - 1;
if(endPage > totalPage) endPage = totalPage;
```
- 현재 블럭의 시작 페이지 값과 마지막 페이지 값을 구한다.
- int 형으로 형 변환은 `(nowPage - 1) / blockPage`에만 해당한다.
	- `nowPage - 1`이 `blockPage`보다 크지 않는 이상 `(nowPage - 1) / blockpage`는 항상 0이다.
	- `nowPage - 1`이 5 이상 10 미만이라면 `(nowPage - 1) / blockPage`는 항상 1이다.
- endPage에서 totalPage = 9, startPage = 6, blockPage = 5일 때, endPage = 10이므로, endPage > totalPage가 된다.
	- 따라서 endPage > totalPage가 될 때는 endPage = totalPage가 되도록 수정한다.

```java
begin = (nowPage - 1) * blockList + 1;
end = nowPage * blockList;
```
- 현재 페이지에서 보여질 게시물의 시작<font color="#7f7f7f">(begin)</font>과 끝<font color="#7f7f7f">(end)</font> 행 번호를 구한다.
- begin에서
	- blockList = 5, nowPage = 1인 경우, begin = 1
	- blockList = 5, nowPage = 2인 경우, begin = 6
	- 각 페이지의 가장 위에 놓일 게시물의 번호는 1, 6, 11, ...
- end에서 최종 게시물 보다 많은 수를 요청한다고 해서 오류는 발생하지 않는다.
	- SQL 문에서 범위를 벗어난 결과는 무시당한다.
	- rowTotal = 28, blockList = 5이고, cPage가 1, 2, 3, ...으로 커질 때, end는 5, 10, 15, ...로 커진다. 이때 end가 25가 나와도 상관이 없다는 의미이다.

```java
if(startPage > 1) isPrePage = true;
if(endPage < totalPage) isNextPage = true;
```
- 이전 기능 가능 여부 확인하기
- 다음 기능 가능 여부 확인하기

```java
sb = new StringBuffer("<ol class='paging'>");
```
- 페이징 기법에 사용할 HTML을 작성하여 StringBuffer에 저장해야 한다.
- <font color="#fac08f">쌍따옴표 안에 또 쌍따옴표를 사용하면 문자열이 끊어짐에 주의하자. 내부에선 홑따옴표!</font>

```java
if(isPrePage) {
	
}else sb.append("<li class='disable'>&lt;</li>");
```
- 이전 기능 가능 여부가 true일 때, 동작을 위한 if 문을 생성한다.
- 이전 기능 가능 여부가 false일 때, 비활성화되므로 그 때 사용할 HTML을 적용한다.

이전 기능 가능 여부가 true일 때의 if 문
```java
sb.append("<li><a href='list?cPage=");
sb.append(nowPage - blockPage);
sb.append("'>&lt;</a></li>");
```
- isPrePage = true일 때, StringBuffer를 이용해 사용할 HTML을 완성한다.
	- `<li><a href='list?cPage=${page.startPage - page.pagePerBlock}'>&lt;</a></li>`
	- 위의 HTML을 구현한 것!

```java
for(int i = startPage ; i <= endPage ; i++) {
	if(i == nowPage) {
		sb.append("<li class='now'>");
		sb.append(i);
		sb.append("</li>");
	} else {
		sb.append("<li><a href='list?cPage='");
		sb.append(i);
		sb.append("'>");
		sb.append(i);
		sb.append("</a></li>");
	}
}
```
- 페이지 번호를 출력하는 반복문을 생성한다.
- 이때 i가 현재 페이지와 동일하면 a 태그를 지정하지 않아야 한다.

```java
if(isNextPage) {
	sb.append("<li><a href='list?cPage=");
	sb.append(nowPage + blockPage);
	sb.append("'>&gt;</a></li>");
}else sb.append("<li class='disable'>&gt;</li>");
```
- isNextPage에 대해 StringBuffer를 이용해 사용할 HTML을 완성하자.

```java
sb.append("</ol>");
```
- ol 태그를 닫아주자.
### 1.2.2. 작성한 코드 전문
```java
package spring.util;

public class Paging2 {
	
	private int nowPage, rowTotal, blockList, blockPage, totalPage, startPage, endPage, begin, end;
	private boolean isPrePage;
	private boolean isNextPage;
	
	private StringBuffer sb;
	
	public Paging2(int nowPage, int rowTotal, int blockList, int blockPage) {
		this.nowPage = nowPage;
		this.rowTotal = rowTotal;
		this.blockList = blockList;
		this.blockPage = blockPage;
		
		isPrePage = false;
		isNextPage = false;
		
		totalPage = (int) Math.ceil((double) rowTotal / blockList);
		
		if(nowPage > totalPage) nowPage = totalPage;
		
		startPage = (int)((nowPage - 1) / blockPage) * blockPage + 1;
		endPage = startPage + blockPage - 1;
		if(endPage > totalPage) endPage = totalPage;
		
		begin = (nowPage - 1) * blockList + 1;
		end = nowPage * blockList;
		
		if(startPage > 1) isPrePage = true;
		if(endPage < totalPage) isNextPage = true;
		
		sb = new StringBuffer("<ol class='paging'>");
		
		if(isPrePage) {
			sb.append("<li><a href='list?cPage=");
			sb.append(nowPage - blockPage);
			sb.append("'>&lt;</a></li>");
		}else sb.append("<li class='disable'>&lt;</li>");
		
		for(int i = startPage ; i <= endPage ; i++) {
			if(i == nowPage) {
				sb.append("<li class='now'>");
				sb.append(i);
				sb.append("</li>");
			} else {
				sb.append("<li><a href='list?cPage=");
				sb.append(i);
				sb.append("'>");
				sb.append(i);
				sb.append("</a></li>");
			}
		}
		
		if(isNextPage) {
			sb.append("<li><a href='list?cPage=");
			sb.append(nowPage + blockPage);
			sb.append("'>&gt;</a></li>");
		}else sb.append("<li class='disable'>&gt;</li>");
		
		sb.append("</ol>");
	}

	public int getNowPage() {
		return nowPage;
	}

	public void setNowPage(int nowPage) {
		this.nowPage = nowPage;
	}

	public int getRowTotal() {
		return rowTotal;
	}

	public void setRowTotal(int rowTotal) {
		this.rowTotal = rowTotal;
	}

	public int getBlockList() {
		return blockList;
	}

	public void setBlockList(int blockList) {
		this.blockList = blockList;
	}

	public int getBlockPage() {
		return blockPage;
	}

	public void setBlockPage(int blockPage) {
		this.blockPage = blockPage;
	}

	public int getTotalPage() {
		return totalPage;
	}

	public void setTotalPage(int totalPage) {
		this.totalPage = totalPage;
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

	public boolean isPrePage() {
		return isPrePage;
	}

	public void setPrePage(boolean isPrePage) {
		this.isPrePage = isPrePage;
	}

	public boolean isNextPage() {
		return isNextPage;
	}

	public void setNextPage(boolean isNextPage) {
		this.isNextPage = isNextPage;
	}

	public StringBuffer getSb() {
		return sb;
	}

	public void setSb(StringBuffer sb) {
		this.sb = sb;
	}

}
```
## 1.3. BbsController.java
### 1.3.1. Paging2를 넣자.
```java
Paging page = new Paging(blockList, blockPage);
page.setTotalRecord(rowTotal);
page.setNowPage(nowPage);
```
- 이 부분 삭제

```java
Paging2 page = new Paging2(nowPage, rowTotal, blockList, blockPage);
```
- 그 자리에 이거 추가

```java
mv.addObject("ar", ar);
mv.addObject("page", page);
mv.addObject("nowPage", nowPage);
mv.addObject("bname", bname);
// 위를 아래로 수정 ----------------
mv.addObject("ar", ar);
mv.addObject("totalRecord", rowTotal);
mv.addObject("nowPage", nowPage);
mv.addObject("numPerPage", blockList);
mv.addObject("bname", bname);
mv.addObject("pageCode", page.getSb().toString());
```
- ModelAndView의 addObject를 다음과 같이 수정하자.
- 기존의 list.jsp에서 변수명을 유지하기 위해 구분하여 저장하자.
- pageCode를 사용하면 StringBuffer에 담긴 내용들을 list.jsp에 넣을 수 있다.
### 1.3.2. 수정한 코드 전문
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
import spring.util.Paging2;

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
		
		Paging2 page = new Paging2(nowPage, rowTotal, blockList, blockPage);
		
		int begin = page.getBegin();
		int end = page.getEnd();
		BbsVO[] ar = b_dao.getList(bname, begin, end);
		
		mv.addObject("ar", ar);
		mv.addObject("totalRecord", rowTotal);
		mv.addObject("nowPage", nowPage);
		mv.addObject("numPerPage", blockList);
		mv.addObject("bname", bname);
		mv.addObject("pageCode", page.getSb().toString());
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
# 2. 보기 기능 수정
## 2.1. list.jsp
```HTML
<a href="Controller?type=view&b_idx=${vo.b_idx}&cPage=${nowPage}">
<!-- 수정 -->
<a href="view?b_idx=${vo.b_idx}&cPage=${nowPage}&bname=${bname}">
```
- href를 수정한다.

## 2.2. BbsController.java
```java
@RequestMapping("/view")
public ModelAndView view(String b_idx, String cPage, String bname) {
	ModelAndView mv = new ModelAndView();
	
	mv.setViewName(bname + "/view");
	return mv;
}
```
- list.jsp에서 보내온 파라미터 b_idx, cPage를 받아야 한다.

```java
BbsVO bvo = b_dao.getBbs(b_idx);
		
mv.addObject("bvo", bvo);
```
- 인자로 받은 b_idx를 가지고 게시물을 검색해야 한다.
- ModelAndView에 그 정보를 저장해야 한다.
### 2.2.1. 컨트롤러 메서드가 받은 파라미터
#forward
```java
@RequestMapping("/view")
public ModelAndView view(String b_idx, String cPage, String bname) {
	ModelAndView mv = new ModelAndView();
	
	BbsVO bvo = b_dao.getBbs(b_idx);
	
	mv.addObject("bvo", bvo);
	mv.setViewName(bname + "/view");
	return mv;
}
```
- view 메서드가 받은 파라미터 b_idx, cPage, bname은 view.jsp로 forward 된다.
- view.jsp에서는 EL을 통해 `${param.b_idx}`, `${param.cPage}`, `${param.bname}`으로 사용할 수 있다.
## 2.3. write.jsp
### 2.3.1. 코드 수정
- 수정된 코드 분석하기
- 목록 버튼을 누르면 목록으로 돌아갈 수 있도록 하였다.

```java

```
### 2.3.2. 작성한 코드 전문
```HTML
<%@page import="mybatis.vo.CommVO"%>
<%@page import="mybatis.vo.BbsVO"%>
<%@page import="spring.util.Paging"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>VIEW</title>
<link rel="stylesheet" href="//code.jquery.com/ui/1.13.2/themes/base/jquery-ui.css">
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

#bbs table th {
	text-align: center;
	border: 1px solid black;
	padding: 4px 10px;
}

#bbs table td {
	text-align: left;
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

.hide {
	display: none;
}

#myWin {
	display: none;
}
.list_bg {
	background: rgb(249, 250, 255);
	padding: 10px;
}
.list_item {
	background: #fff;
	border-radius: 10px;
	padding: 20px;
	margin-bottom: 5px;
}
</style>

</head>
<body>
	<div id="bbs">
		<form method="post">
			<table summary="게시판 보기">
				<caption>게시판 보기</caption>
				<tbody>
					<tr>
						<th>제목:</th>
						<td>${bvo.subject}</td>
					</tr>

					<tr>
						<th>첨부파일:</th>
						<td>
						<c:if test="${bvo.file_name ne null}">
							<a href="javascript:down('${bvo.file_name}')">${bvo.file_name} </a>
						</c:if>
						</td>
					</tr>

					<tr>
						<th>이름:</th>
						<td>${bvo.writer}</td>
					</tr>
					<tr>
						<th>내용:</th>
						<td>${bvo.content}</td>
					</tr>
					<tr>
						<td colspan="2">
							<input type="button" value="수정" onclick="edit()"/> 
							<input type="button" value="삭제" onclick="delBbs()"/> 
							<input type="button" value="목록" onclick="listBbs()"/>
						</td>
					</tr>
				</tbody>
			</table>
		</form>
		<button type="button" id="a_btn">댓글 쓰기</button>
		<div id="myWin" title="댓글 쓰기">
			<form method="post" action="Controller">
				<input type="hidden" name="type" value="answer"/>
				<input type="text" name="writer" placeholder="이름 입력"/><br/>
				<textarea rows="4" cols="35" name="content" placeholder="내용 입력"></textarea><br/>
				<input type="password" name="pwd" placeholder="비밀번호 입력" /><br/>
				<input type="hidden" name="b_idx" value="${bvo.b_idx}"/>
				<input type="submit" value="저장하기"/>
			</form>
		</div>
		댓글들
		<hr/>
		<c:forEach var="cvo" items="${bvo.c_list}">
			<div>
				이름:${cvo.writer} &nbsp;&nbsp; 
				날짜:${cvo.write_date}<br/> 
				내용:${cvo.content}
			</div>
		</c:forEach>
	</div>
	
	<form name="frm" method="post">
		<input type="hidden" name="fname"/>
		<input type="hidden" name="bname" value="${param.bname}"/>
		<input type="hidden" name="b_idx" value="${bvo.b_idx}"/>
		<input type="hidden" name="cPage" value="${param.cPage}"/>
	</form>
	
	<div id="del_dialog" title="삭제" class="hide">
		<form action="Controller" method="post">
			<label for="pwd">비밀번호</label>
			<input type="password" id="pwd" name="pwd" size="10"/>
			<br/><br/>
			
			<input type="hidden" name="cPage" value="${param.cPage}"/>
			<input type="hidden" name="type" value="del"/>
			<input type="hidden" name="b_idx" value="${bvo.b_idx}"/>
			<input type="button" value="삭제" onclick="del(this.form)"/>
		</form>
	</div>
	
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script src="https://code.jquery.com/ui/1.13.2/jquery-ui.js"></script>
	<script>
		$(function() {
			$("#a_btn").click(function() {
				$("#myWin").dialog({
					width:500,
					height:250
				});
			});
		});
		
		function edit() {
			document.frm.type.value = "edit";
			document.frm.submit();
		}
		
		function listBbs(){
			document.frm.action = "list";
			document.frm.submit();
		}
		
		function delBbs(){
			let res = confirm("삭제하시겠습니까?");
			//console.log(res);
			if(res){
				//삭제를 원한 경우!
				$("#del_dialog").dialog();// 삭제시 대화창 보여주기
			}
		}
		
		function del(frm){
			//비밀번호를 입력했는지 유효성 검사!
			let pw = $("#pwd").val().trim();
			if(pw.length < 1){
				alert("비밀번호를 입력하세요");
				//공백만 입력했을 때를 생각해서
				// 입력란을 청소해 주자!
				$("#pwd").val("");
				$("#pwd").focus();
				return;
			}
			frm.submit();// 결국 Controller를 거쳐서 DelAction으로 간다.
		}
		
		function down(fname){
			//인자로 사용자가 클릭한 파일명을 받는다.
			//이것을 현재문서 안에 있는 frm이라는 폼객체에 이름이 fname이라는
			// hidden요소의 값(value)으로 지정해 준다.
			document.frm.fname.value = fname;
			document.frm.type.value = "down";
			document.frm.submit(); // Controller?type=down&fname=logo.png
			
		}
	</script>
</body>
</html>
<c:if test="${bvo.file_name eq null}">
	<script>
		locatin.href="list"
	</script>
</c:if>
```
# 3. 수정 기능
## 3.1. edit.jsp
### 3.1.1. 내가 작성한 코드
```HTML
<%@page import="mybatis.vo.BbsVO"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>WRITE</title>
<link rel="stylesheet" href="resources/css/summernote-lite.css"/>
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
</head>
<body>
	<div id="bbs">
	<form action="edit" name="frm" method="post" encType="multipart/form-data">
		<table summary="게시판 수정하기">
			<caption>게시판 수정하기</caption>
			<tbody>
				<tr>
					<th>제목:</th>
					<td>
						<input type="text" name="subject" value="${bvo.subject}" data-str="제목"/>
						<input type="hidden" name="cPage" value="${param.cPage}"/>
						<input type="hidden" name="bname" value="${param.bname}"/>
					</td>
				</tr>
				<tr>
					<th>이름:</th>
					<td><input type="text" name="writer" disabled value="${bvo.writer}" data-str="이름"/></td>
				</tr>
				<tr>
					<th>내용:</th>
					<td><textarea id="content" name="content" cols="50" rows="8" data-str="내용">${bvo.content}</textarea></td>
				</tr>
				<tr>
					<th>첨부파일:</th>
					<td>
						<input type="file" name="file" data-str="첨부 파일"/>
						<c:if test="${bvo.file_name ne null}">
							(${bvo.file_name}) 파일이 첨부된 상태
						</c:if>
					</td>
				</tr>
<!--
				<tr>
					<th>비밀번호:</th>
					<td><input type="password" name="pwd" size="12"/></td>
				</tr>
-->
				<tr>
					<td colspan="2">
						<input type="button" value="저장" onclick="sendData()"/>
						<input type="button" value="다시"/>
						<input type="button" value="목록" onclick="listBbs()"/>
					</td>
				</tr>
			</tbody>
		</table>
		
		<input type="hidden" name="b_idx" value="${bvo.b_idx}"/>
	</form>
	</div>
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script src="resources/js/summernote-lite.js"></script>
	<script src="resources/js/lang/summernote-ko-KR.js"></script>
	<script>
		$(function(){
			$("#content").summernote({
				lang: "ko-KR",
				height: 200,
				focus: true,
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
			document.frm.action = "saveEdit";
			document.frm.submit();
		}
		
		function listBbs(){
			document.frm.action = "list";
			document.frm.submit();
		}
	</script>
</body>
</html>
<c:if test="$bvo.file_name eq null}">
	<script>
		location.href = "list"
	</script>
</c:if>
```
## 3.2. BbsController.java
### 3.2.1. 내가 수정한 코드
```java
@RequestMapping("/edit")
public ModelAndView edit(String b_idx, String bname) {
	ModelAndView mv = new ModelAndView();
	BbsVO bvo = b_dao.getBbs(b_idx);
	
	mv.addObject("bvo", bvo);
	mv.setViewName(bname + "/edit");
	return mv;
}

@RequestMapping("/saveEdit")
public ModelAndView saveEdit(BbsVO vo, String cPage){
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
		b_dao.edit(vo.getB_idx(), vo.getSubject(), vo.getContent(), vo.getFile_name(), vo.getOri_name(), vo.getIp());
	}
	ModelAndView mv = new ModelAndView();
	mv.setViewName("redirect:/view?b_idx=" + vo.getB_idx() + "&cPage=" + cPage + "&bname=" + vo.getBname());
	
	return mv;
}
```
### 3.2.2. 센세가 작성한 코드
```java
@RequestMapping("edit")
publid ModelAndView edit(BbsVO vo, String cPage) {
	ModelAndView mv = new ModelAndView();
	String c_type = request.getContentType();
	
	if(c_type.startsWith("application")){
		BbsVO vo2 = b_dao.getBbs(vo.getB_idx());
		mv.addObjet("vo", vo2);
		mv.setViewName(vo.getBname() + "/edit");
	} else if(c_type.startsWith("multipart")){
		multipartFile f = vo.getFile();
		
		if(f != null && f.getSize() > 0){
			String realPath = application.getRealPath(upload_file);
			String fname = f.getOriginalFilename();
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
		b_dao.edit(vo);
		mv.setViewName("redirect:/edit?b_idx=" + vo.getB_idx() + "&bname=" + vo.getBname() + "&cPage=" + cPage);
	}
	return mv;
}
```
- request.getContentType()을 통해 #request #contentType
	- GET 방식으로 호출되었다면 c_type = null
	- POST 방식으로 호출되었다면 c_type = application...
	- POST 방식이면서 encType이 multipart/form-data이면 c_type = multipart...
- 인자로 받은 b_idx로 게시물 검색 후 ModelAndView에 저장한다.
## 3.3. bbs.xml
```xml
<update id="edit" parameterType="mybatis.vo.BbsVO">
	UPDATE bbs_t
	<trim prefix="SET" suffixOverrides=",">
	subject = #{subject}, 
	content = #{content},
		<if test="file_name != null">
			file_name = #{file_name},
			ori_name = #{ori_name}
		</if>
	ip = #{ip}
	</trim> 
	WHERE b_idx = #{b_idx}
</update>
```
- parameterType을 mybatis.vo.BbsVO로 수정
## 3.4. BbsDAO.java
```java
public int edit(BbsVO vo) {
			
	return ss.update("bbs.edit", vo);
}
```
- Map으로 받은 것을 BbsVO로 수정
# 4. 삭제 기능
## 4.1. BbsController.java
### 4.1.1. 내가 만든 코드
```java
@RequestMapping("/del")
public ModelAndView del(String b_idx, String bname, String cPage) {
	ModelAndView mv = new ModelAndView();
	int cnt = b_dao.del(b_idx);
	mv.setViewName("redirect:/list?bname=" + bname + "&cPage=" + cPage);
	
	return mv;
}
```
## 4.2. view.jsp
### 4.2.1. 내가 수정한 코드
```HTML
<input type="button" value="삭제" onclick="delBbs()"/> 
```
- 삭제 버튼에 onclick 이벤트로 delBbs() 함수가 걸려있다.

```HTML
<form name="frm" method="post">
	<input type="hidden" name="fname"/>
	<input type="hidden" name="bname" value="${param.bname}"/>
	<input type="hidden" name="b_idx" value="${bvo.b_idx}"/>
	<input type="hidden" name="cPage" value="${param.cPage}"/>
</form>
```
- name = frm인 form 태그에 bname, b_idx, cPage 정보가 담겨있다.

```HTML
<div id="del_dialog" title="삭제" class="hide">
	<form action="del" name="frm2" method="post">
		<label for="pwd">비밀번호</label>
		<input type="password" id="pwd" name="pwd" size="10"/>
		<br/><br/>
		
		<input type="hidden" name="cPage" value="${param.cPage}"/>
		<input type="hidden" name="b_idx" value="${bvo.b_idx}"/>
		<input type="button" value="삭제" onclick="del(this.form)"/>
	</form>
</div>
```
- id = del_dialog인 div 태그에 name = frm2인 form 태그가 있다.

```JavaScript
function delBbs(){
	let res = confirm("삭제하시겠습니까?");
	if(res){
		$("#del_dialog").dialog();
	}
}

function del(frm){
	let pw = $("#pwd").val().trim();
	if(pw.length < 1){
		alert("비밀번호를 입력하세요");
		$("#pwd").val("");
		$("#pwd").focus();
		return;
	}
	document.frm.action = "del";
	document.frm.submit();
}
```
# 5. 페이징 수정
## 5.1. Paging2.java
```java
if(nowPage > totalPage) {
	nowPage = totalPage;
	this.nowPage = nowPage; // HERE!
}
```
- if 문에 수행문 추가
## 5.2. BbsController.java
```java
mv.addObject("nowPage", page.getNowPage());
```
- nowPage를 page.getNowPage()로 수정
# 참고
- Paging 관련 [mui](https://mui.com)에서 BasicPagination 참고