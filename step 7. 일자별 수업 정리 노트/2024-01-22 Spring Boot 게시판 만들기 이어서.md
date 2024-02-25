# 1. 보기 기능 추가하기
> com.ict.bbs

## 1.1. Controller 메서드 추가
> src > main > java > com > ict > bbs > controller > BbsController.java

<font color="#4f81bd">내가 짠 코드:</font>
```java
@Autowired
private HttpSession session;

private ArrayList<BbsVO> r_list;

@RequestMapping("view")
public ModelAndView view(String b_idx, String cPage, String bname){
	ModelAndView mv = new ModelAndView();
	
	session = request.getSession(true);
	session.setMaxInactiveInterval(1800);
	Object obj = session.getAttribute("read_list");
	if(obj == null){
		r_list = new ArrayList<BbsVO>();
		session.setAttribute("read_list", r_list);
		System.out.println("test");
	} else {
		r_list = (ArrayList<BbsVO>) obj;
	}
	BbsVO bvo = b_Service.getBbs(b_idx);
	if(checkBbs(bvo)) {
		b_Service.hit(b_idx);
		r_list.add(bvo);
	}

	mv.addObject("bvo", bvo);
	mv.setViewName(bname + "/view");
	return mv;
}

public boolean checkBbs(BbsVO vo){
	boolean value = true;
	if(r_list != null){
		for(BbsVO bvo : r_list){
			if(bvo.getB_idx().equals(vo.getB_idx())){
				value = false;
				break;
			}
		}
	}
	return value;
}
```

<font color="#4f81bd">강사님이 짠 코드:</font>
```java
@RequestMapping("view")
public ModelAndView view(String b_idx, String cPage, String bname){
	ModelAndView mv = new ModelAndView();
	HttpSession session = request.getSession(true);
	Object obj = session.getAttribute("read_list");
	ArrayList<BbsVO> list = null;
	if(obj != null)
		list = (ArrayList<BbsVO>) obj;
	else
		list = new ArrayList<BbsVO>();
	
	BbsVO vo = b_Service.getBbs(b_idx);
	boolean chk = false;
	for(BbsVO bvo : list){
		if(bvo.getB_idx().equals(vo.getB_idx())){
			chk = true;
			break;
		}
	}
	if(!chk){
		int hit = Integer.parseInt(vo.getHit()) + 1;
		vo.setHit(String.valueOf(hit));
		b_Service.updateHit(vo);
	}
	
	mv.addObject("vo", vo);
	mv.setViewName(bname + "/view");
	return mv;
}
```
## 1.2. Mapper Service 메서드 추가
> src > main > java > com > ict > bbs > service > BbsService.java

```java
public int hit(String b_idx){
	return b_Mapper.hit(b_idx);
}
```
# 2. 파일 다운로드 기능 추가하기
## 2.1. Controller 메서드 추가
> src > main > java > com > ict > bbs > controller > BbsController.java

<font color="#4f81bd">파일 다운로드는 ResponseEntity를 반환해야 한다.</font>
```java
@RequestMapping("download")
public ResponseEntity<Resource> fileDownload(String cPage, String fname){
```
- org.springframework.core.io의 Resource를 선택하자.

<font color="#4f81bd">파일들이 위치하는 곳을 절대경로화 시킨다.</font>
```java
String realPath = application.getRealPath(upload_file + "/" + fname);
File f = new File(realPath);
```
- 파일들이 위치하는 곳은 upload_file 폴더이다.
- 직접 파일을 연결해야 하므로 파일 객체를 생성한다.

<font color="#4f81bd">파일이 존재하는지 유효성 검사를 한다.</font>
```java
if(f.exists()){
	byte[] buf = new byte[4069];
	int size = -1;
	BufferedInputStream bis = null;
	FileInputStream fis = null;
	BufferedOutputStream bos = null;
	ServletOutputStream sos = null;
	// 계속...
}
```
- <font color="#4bacc6">파일이 존재하면 파일을 읽어서 바로 다운로드 시킨다</font>.
- 스트림과 바이트 타입의 배열이 준비되어야 한다.
- 다운로드에 필요한 스트림은 BufferedInputStream, FileInputStream이다.
- 보내기 할 때 필요한 스트림은 BufferedOutputStream, ServletOutputStream이다.
	- <font color="#9bbb59">응답</font>을 하는 것이 <font color="#9bbb59">접속자의 컴퓨터로 다운로드</font>를 시키는 것이기 때문에 <font color="#9bbb59">response를 통해</font> 스트림을 생성한다.  response로부터 얻어지는 OutputStream이 <font color="#9bbb59">ServletOutputStream</font>이다.

<font color="#4f81bd">접속자 화면에 다운로드 창을 보여주자.</font>
#복붙코드 #contentType 
```java
response.setContentType("application/x-msdownload");
response.setHeader("Content-Disposition", "attachment;filename=" + new String(fname.getBytes(), "8859_1"));
```
- response에 contentType을 지정한다. application이며 파일 다운로드를 하는 타입이라는 의미이다.

<font color="#4f81bd">다운로드할 파일과 연결되는 스트림을 생성하자.</font>
```java
fis = new FileInputStream(f);
bis = new BufferedInputStream(fis);
sos = response.getOutputStream();
bos = new BufferedOutputStream(sos);
```
- f - fis - bis 연결을 통해 읽을 준비를 마쳤다.
- response에 out이라는 스트림이 존재한다. 하지만 다운로드를 시키기 위한 스트림을 새로 준비한다.

<font color="#4f81bd">준비된 스트림을 통해 읽기 후 바로 쓰기를 하여 다운로드시키자.</font>
```java
while((size = bis.read()) != -1){
	bos.write(buf, 0, size);
	bos.flush();
}
```
- 읽은 자원은 buf가 가지고 있으며, 읽은 바이트 수는 size가 기억한다. size가 -1이 되기 전까지 반복문을 수행한다.

```java
} catch (Exception e) {
	e.printStackTrace();
} finally {
	try {
		if(fis != null) fis.close();
		if(bis != null) bis.close();
		if(sos != null) sos.close();
		if(bos != null) bos.close();
	} catch (Exception e) {}
}
return null;
```
- 마지막으로 예외 처리와 스트림 종료를 추가하자.

[[#B.1. BbsController.java|BbsController.java 코드 전문 보기]]
## 2.2. View 수정
> src > main > webapp > WEB-INF > jsp > bbs > view.jsp

<font color="#4f81bd">첨부파일 부분의 태그를 확인하자.</font>
```HTML
<tr>
	<th>첨부파일:</th>
	<td>
	<c:if test="${bvo.file_name ne null}">
		<a href="javascript:down('${bvo.file_name}')">${bvo.file_name} </a>
	</c:if>
	</td>
</tr>
```
- 현재 첨부파일 링크는 down()이라는 함수를 호출하는 것을 볼 수 있다.

```HTML
<form name="frm" method="post">
	<input type="hidden" name="fname"/>
	<input type="hidden" name="bname" value="${param.bname}"/>
	<input type="hidden" name="b_idx" value="${bvo.b_idx}"/>
	<input type="hidden" name="cPage" value="${param.cPage}"/>
</form>
```

<font color="#4f81bd">다운로드 관련 스크립트를 수정하자.</font>
```JavaScript
function down(fname){
	location.href = "FileDownload?dir=upload_file&filename=" + fname;
}
```
- 위 코드를 수정하자.

```javascript
function down(fname){
	document.frm.fname.value = fname;
	document.frm.action = "download";
	document.frm.submit();
}
```
- 인자로 사용자가 클릭한 파일의 이름을 받으므로, 현재 문서<font color="#7f7f7f">(documenet)</font> 안에 있는 폼 객체<font color="#7f7f7f">(frm)</font>에 이름이 fname이라는 hidden 요소의 값<font color="#7f7f7f">(value)</font>으로 지정해주자.

[[#B.2. view.jsp|view.jsp 코드 전문 보기]]
# A. 부록
# B. 코드 전문
## B.1. BbsController.java
```java
package com.ict.bbs.controller;

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
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.servlet.ModelAndView;

import com.ict.bbs.service.BbsService;
import com.ict.bbs.util.FileRenameUtil;
import com.ict.bbs.util.Paging2;
import com.ict.bbs.vo.BbsVO;

import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletOutputStream;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;

@Controller
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
    public ModelAndView list(String bname, String cPage){
        ModelAndView mv = new ModelAndView();
        int nowPage = 1;

        if(bname == null) bname = "bbs";
        if(cPage != null) nowPage = Integer.parseInt(cPage);

        int totalRecord = b_Service.getCount(bname);
        Paging2 page = new Paging2(nowPage, totalRecord, 7, 5);
        String pageCode = page.getSb().toString();

        String begin = String.valueOf(page.getBegin());
        String end = String.valueOf(page.getEnd());
        BbsVO[] ar = b_Service.getList(bname, begin, end);

        mv.addObject("ar", ar);
        mv.addObject("page", page);
        mv.addObject("pageCode", pageCode);
        mv.addObject("totalRecord", totalRecord);
        mv.addObject("nowPage", page.getNowPage());
        mv.addObject("bname", bname);
        mv.addObject("blockList", page.getBlockList());
        mv.setViewName("bbs/list");

        return mv;
    }

    @RequestMapping("write")
    public String write(@RequestParam String bname){
        return bname + "/write";
    }

    @PostMapping("write")
    public ModelAndView write(BbsVO vo){
        ModelAndView mv = new ModelAndView();
        String c_type = request.getContentType();
        if(c_type.startsWith("multipart")){
            MultipartFile f = vo.getFile();
            if(f.getSize() > 0){
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
                mv.setViewName("redirect:/list?bname=" + vo.getBname());
            }
        }

        return mv;
    }

    @PostMapping("saveImg")
    @ResponseBody
    public Map<String, String> saveImg(MultipartFile s_file){
        Map<String, String> map = new HashMap<>();
        if(s_file.getSize() > 0){
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
    public ModelAndView view(String b_idx, String cPage, String bname){
        ModelAndView mv = new ModelAndView();
        
        session = request.getSession(true);
        session.setMaxInactiveInterval(1800);
        Object obj = session.getAttribute("read_list");
        if(obj == null){
            r_list = new ArrayList<BbsVO>();
            session.setAttribute("read_list", r_list);
            System.out.println("test");
        } else {
            r_list = (ArrayList<BbsVO>) obj;
        }
        BbsVO bvo = b_Service.getBbs(b_idx);
        if(checkBbs(bvo)) {
            b_Service.hit(b_idx);
            r_list.add(bvo);
        }

        mv.addObject("bvo", bvo);
        mv.setViewName(bname + "/view");
        return mv;
    }

    public boolean checkBbs(BbsVO vo){
        boolean value = true;
        if(r_list != null){
            for(BbsVO bvo : r_list){
                if(bvo.getB_idx().equals(vo.getB_idx())){
                    value = false;
                    break;
                }
            }
        }
        return value;
    }

    @RequestMapping("edit")
    public ModelAndView edit(String b_idx, String cPage, String bname){
        ModelAndView mv = new ModelAndView();
        BbsVO bvo = b_Service.getBbs(b_idx);
        mv.addObject("bvo", bvo);
        mv.setViewName(bname + "/edit");
        return mv;
    }

    @PostMapping("edit")
    public ModelAndView edit(BbsVO vo, String cPage){
        ModelAndView mv = new ModelAndView();
        String c_type = request.getContentType();
        if(c_type.startsWith("multipart")){
            MultipartFile f = vo.getFile();
            if(f.getSize() > 0){
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
        mv.setViewName("redirect:/view?b_idx=" + vo.getB_idx() + "&cPage=" + cPage + "&bname=" + vo.getBname());
        return mv;
    }

    @RequestMapping("download")
    public ResponseEntity<Resource> fileDownload(String cPage, String fname){
        String realPath = application.getRealPath(upload_file + "/" + fname);
        File f = new File(realPath);

        if(f.exists()){
            byte[] buf = new byte[4069];
            int size = -1;
            BufferedInputStream bis = null;
            FileInputStream fis = null;
            BufferedOutputStream bos = null;
            ServletOutputStream sos = null;

            try {
                response.setContentType("application/x-msdownload");
                response.setHeader("Content-Disposition", "attachment;filename=" + new String(fname.getBytes(), "8859_1"));
                fis = new FileInputStream(f);
                bis = new BufferedInputStream(fis);
                sos = response.getOutputStream();
                bos = new BufferedOutputStream(sos);

                while((size = bis.read()) != -1){
                    bos.write(buf, 0, size);
                    bos.flush();
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                try {
                    if(fis != null) fis.close();
                    if(bis != null) bis.close();
                    if(sos != null) sos.close();
                    if(bos != null) bos.close();
                } catch (Exception e) {}
            }
        }
        return null;
    }

}
```
## B.2. view.jsp
```HTML
<%@page import="com.ict.bbs.vo.CommVO"%>
<%@page import="com.ict.bbs.vo.BbsVO"%>
<%@page import="com.ict.bbs.util.Paging2"%>
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
		<form action="del" name="frm2" method="post">
			<label for="pwd">비밀번호</label>
			<input type="password" id="pwd" name="pwd" size="10"/>
			<br/><br/>
			
			<input type="hidden" name="cPage" value="${param.cPage}"/>
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
			document.frm.action = "edit";
			document.frm.method = "get";
			document.frm.submit();
		}
		
		function listBbs(){
			document.frm.action = "list";
			document.frm.submit();
		}
		
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
		
		function down(fname){
			document.frm.fname.value = fname;
			document.frm.action = "download";
			document.frm.submit();
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