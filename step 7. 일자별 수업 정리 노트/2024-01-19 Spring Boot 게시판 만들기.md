# 1. Spring Boot 게시판 만들기
## 1.1. 프로젝트 생성하기
[[2024-01-17 2) Spring Boot#4. VSCode에서 Spring Boot Project 만들기|VS Code에서 프로젝트 생성하기]] 참고:
- Artifact: bbs
- Dependencies:
	- Spring Boot DevTools
	- Lombok
	- Spring Configuration Processor
	- Spring Web
	+ MyBatis Framework
	+ MySQL Driver
## 1.2. 환경 설정

<font color="#4f81bd">Dependency 추가:</font>
> pom.xml

```xml
<dependency>
	<groupId>jakarta.servlet</groupId>
	<artifactId>jakarta.servlet-api</artifactId>
	<version>6.0.0</version>
	<scope>provided</scope>
</dependency>
<dependency>
	<groupId>jakarta.servlet.jsp.jstl</groupId>
	<artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
	<version>3.0.0</version>
</dependency>
<dependency>
	<groupId>org.glassfish.web</groupId>
	<artifactId>jakarta.servlet.jsp.jstl</artifactId>
	<version>3.0.1</version>
</dependency>
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
	<scope>provided</scope>
</dependency>
```

이 Dependency들은 Java Server Pages, 즉 <font color="#f79646">JSP를 사용하기 위해 추가</font>한 것이다.
- jakarta.servlet-api
	- Servlet 기술을 사용하기 위한 필수적인 API를 포함하는 라이브러리
- jakarta.servlet.jsp.jstl-api
	- JSTL에 대한 API를 제공하는 라이브러리
- jakarta.servlet.jsp.jstl
	- JSTL 구현체 라이브러리
- tomcat-embed-jasper
	- Embedded Tomcat에서 JSP를 처리하기 위한 라이브러리

<font color="#4f81bd">application.properties 작성:</font>
> src > main > resources > application.properties

```
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test_db?characterEncoding=UTF-8
spring.datasource.username=test_admin
spring.datasource.password=1111
spring.datasource.tomcat.max-active=30
spring.datasource.tomcat.max-idle=20
spring.datasource.tomcat.min-idle=10
```

<font color="#4f81bd">DbConfig.java 작성:</font>
> src > main > java > com > ict > bbs > config > DbConfig.java

```java
sqlSessionFactory.setDataSource(dataSource);
```
- 비어 있던 factory에 DB 정보 객체를 넣어 인식시킨다.

```java
PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
```
- SQL 문장인 mapper들(bbs.xml, mem.xml, ...) 인식시킨다.

[[#B.3. DbConfig.java|DbConfig.java 코드 전문 보기]]
## 1.3. VO 객체 생성

<font color="#4f81bd">BbsVO 코드 전문:</font>
> src > main > java > com > ict > bbs > vo > BbsVO.java

```java
package com.ict.bbs.vo;

import java.util.List;

import org.springframework.web.multipart.MultipartFile;

import lombok.Getter;
import lombok.Setter;

@Getter @Setter
public class BbsVO {
	private String b_idx, subject, writer, content, file_name, ori_name, write_date, ip, hit, bname, status;
	private List<CommVO> c_list;
	private MultipartFile file;
}
```

<font color="#4f81bd">CommVO 코드 전문:</font>
> src > main > java > com > ict > bbs > vo > CommVO.java

```java
package com.ict.bbs.vo;

import lombok.Getter;
import lombok.Setter;

@Getter @Setter
public class CommVO {
	private String c_idx, writer, content, write_date, ip, b_idx, status;
}
```
## 1.4. Mapper 생성
> src > main > resources > mapper > bbs.xml

[[#B.1. bbs.xml|bbs.xml 코드 전문 보기]]
## 1.5. Mapper Interface 생성
> src > main > java > com > ict > bbs > mapper > BbsMapper.java

연동된 <font color="#f79646">Mapper</font><font color="#7f7f7f">(bbs.xml)</font>에 있는 <font color="#f79646">id와 이름이 같은 추상 메서드</font>를 정의해야 한다.
추상 메서드 선언 시 자동으로 public으로 선언되므로 public은 굳이 안 써도 된다.

<font color="#4f81bd">BbsMapper Interface 코드 전문:</font>
```java
package com.ict.bbs.mapper;

import com.ict.bbs.vo.BbsVO;
import com.ict.bbs.vo.CommVO;

public interface BbsMapper {
    int count(String bname);
	List<BbsVO> list(String bname, String begin, String end);
    List<CommVO> commList(String b_idx);
    int add(BbsVO vo);
    BbsVO getBbs(String b_idx);
    int addComm(CommVO vo);
    int edit(BbsVO vo);
    int del(String b_idx);
    int hit(String b_idx);
}
```
## 1.6. Mapper Service 생성
> src > main > java > com > ict > bbs > service > BbsService.java

전부 만든 것은 아님. 나머지는 알아서 채우자.

[[#B.2. BbsService.java|BbsService.java 코드 전문 보기]]
## 1.7. Controller 생성
> src > main > java > com > ict > bbs > controller > BbsController.java

<font color="#4f81bd">Paging 객체 생성과 Paging에 필요한 HTML 코드 받기:</font>
```java
Paging2 page = new Paging2(nowPage, totalRecord, 7, 5);
String pageCode = page.getSb().toString();
```

<font color="#4f81bd">View에서 표현할 목록 가져오기:</font>
```java
String begin = String.valueOf(page.getBegin());
String end = String.valueOf(page.getEnd());
BbsVO[] ar = b_Service.getList(bname, begin, end);
```
- Mapper는 String, Paging 객체는 int 타입이므로 Paging 객체의 값을 String 타입으로 변환하자.

<font color="#4f81bd">ModelAndView에 View에 필요한 정보 저장하기:</font>
```java
mv.addObject("ar", ar);
mv.addObject("page", page);
mv.addObject("pageCode", pageCode);
mv.addObject("totalRecord", totalRecord);
mv.addObject("nowPage", nowPage);
mv.addObject("blockList", page.getBlockList());
```
- totalRecord는 게시물의 번호 출력을 위하여 저장한다.

[[#B.4. BbsController.java|BbsController.java 코드 전문 보기]]
## 1.8. Paging 객체 생성
> src > main > java > com > ict > bbs > util > Paging2.java

[[#B.5. Paging2.java|Paging2.java 코드 전문 보기]]
## 1.9. FileRenameUtil 객체 생성
> src > main > java > com > ict > bbs > util > FileRenameUtil.java

[[#B.6. FileRenameUtil.java|FileRenameUtil.java 코드 전문 보기]]
## 1.10. View 구현: 테스트
> src > main > webapp > WEB-INF > jsp > bbs > list.jsp

[[#B.7. list.jsp|list.jsp 코드 전문 보기]]
# 2. Spring Boot 게시판 글쓰기
## 2.1. Controller
> src > main > java > com > ict > bbs > controller > BbsController.java

<font color="#4f81bd">글쓰기 페이지로 forward 하기 위한 메서드:</font>
```java
@RequestMapping("write")
public String write(@RequestParam String bname){
	return bname + "/write";
}
```

<font color="#4f81bd">비동기 처리로 에디터에 올라간 이미지 저장하기 위한 메서드:</font>
```java
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
		// 계속...
	}

	return map;
}
```
- 들어온 파일이 있다면 MultipartFile의 size는 0보다 크다. 비어 있는지 확인하는 것보다 이 방법이 정확하다.
- 전달된 파일이 저장될 위치를 절대경로화 하여야 한다. ServletContext를 이용하자.
- 전달된 파일명이 이미 저장된 파일명과 같다면 파일명을 변경해야 한다. 아까 추가한 FileRenameUtil 객체를 이용하자.
- MultipartFile 객체의 transferTo 메서드를 이용해 파일 업로드를 완료하자.

```java
String path = request.getContextPath();
map.put("url", path + editor_img);
map.put("fname", fname);
```
- 업로드된 파일의 경로를 반환하기 위해 현재 서버의 URL을 알아내야 한다. HttpServletRequest의 getContextPath 메서드를 이용하자.
- JSON으로 반환하기 위해 Map 구조에 저장하자.

<font color="#9bbb59">Spring Boot에서는 JSON 처리를 위한 Jackson 라이브러리의 추가 등의 설정을 할 필요가 없다.</font>
에디터에 이미지를 첨부하고 브라우저의 <font color="#92cddc">개발자 모드 > Network > saveImg > Response</font>를 확인하면 무엇을 응답 받았는지 확인할 수 있다.

<font color="#95b3d7">내가 짠 write 메서드:</font>
```java
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
```

<font color="#95b3d7">강사님이 작성한 write 메서드:</font>
```java
@GetMapping("write")
public String write(@RequestParam String bname){
	return bname + "/write";
}

@PostMapping("write")
public ModelAndView write(BbsVO vo) {
	ModelAndView mv = new ModelAndView();
	MultipartFile mf = vo.getFile();
	if(mf.getSize() > 0){
		String realPath = application.getRealPath(upload_file);
		String oname = mf.getOriginalFilename();
		String fname = FileRenameUtil.checkSameFileName(oname, realPath);
		try{
			mf.transferTo(new File(realPath, fname));
		} catch (Exception e) {
			e.printStackTrace();
		}
		vo.setFile_name(fname);
		vo.setOri_name(oname);
	}
	vo.setIp(request.getRemoteAddr());
	// BbsVO에 bname은 저장된 상태이다.
	int cnt = b_Service.addBbs(vo);
	mv.setViewName("redirect:/list");
	return mv;
}
```

<font color="#f79646">redirect도 forward</font>이다. 따라서 write 메서드의 인자인 <font color="#4bacc6">BbsVO 객체에 담긴</font> bname 등의 값을 <font color="#4bacc6">list.jsp에서 사용할 수 있다.</font>
## 2.2. View 구현: 글쓰기
> src > main > webapp > WEB-INF > jsp > bbs > write.jsp

<font color="#95b3d7">내가 수정한 페이지:</font>
[[#B.8. write.jsp|write.jsp 코드 전문 보기]]
# A. 부록
# B. 코드 전문
## B.1. bbs.xml
> src > main > resources > mapper > bbs.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ict.bbs.mapper.BbsMapper">
	<resultMap type="com.ict.bbs.vo.BbsVO" id="map1">
		<id property="b_idx" column="b_idx"/>
		<collection property="c_list" ofType="com.ict.bbs.vo.CommVO" select="commList" column="b_idx"/>
	</resultMap>
	<select id="count" parameterType="String" resultType="int">
		SELECT COUNT(*) FROM bbs_t
		WHERE status = 0 AND bname = #{bname}
	</select>
	<select id="list" parameterType="Map" resultMap="map1">
		SELECT * 
		FROM (
			SELECT @RN:=@RN+1 AS rnum, a.*
			FROM (
				SELECT * FROM bbs_t
				WHERE status=0 AND bname=#{bname}
				ORDER BY b_idx DESC
		        ) a, 
		        (SELECT @RN:=0) b
		    ) c
		WHERE c.rnum BETWEEN #{begin} AND #{end}
	</select>
	<select id="commList" parameterType="String" resultType="com.ict.bbs.vo.CommVO">
		SELECT * FROM comment_t
		WHERE b_idx = #{idx} AND status = 0
	</select>
	<insert id="add" parameterType="com.ict.bbs.vo.BbsVO">
		INSERT INTO bbs_t(subject, writer, content, file_name, ori_name, write_date, ip, hit, bname, status)
		VALUES(#{subject}, #{writer}, #{content}, #{file_name}, #{ori_name}, NOW(), #{ip}, 0, #{bname}, 0)
	</insert>
	<select id="getBbs" parameterType="String" resultMap="map1">
		SELECT * FROM bbs_t
		WHERE b_idx = #{num}
	</select>
	<insert id="addComm" parameterType="com.ict.bbs.vo.CommVO">
		INSERT INTO comment_t(writer, content, pwd, ip, write_date, b_idx, status)
		VALUES(#{writer}, #{content}, #{pwd}, #{ip}, NOW(), #{b_idx}, 0)
	</insert>
	<update id="edit" parameterType="com.ict.bbs.vo.BbsVO">
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
	<update id="del" parameterType="String">
		UPDATE bbs_t
		SET status = 1
		WHERE b_idx = #{b_idx}
	</update>
	<update id="hit" parameterType="String">
		UPDATE bbs_t
		SET hit = hit + 1
		WHERE b_idx = #{b_idx}
	</update>
</mapper>
```
## B.2. BbsService.java
> src > main > java > com > ict > bbs > service > BbsService.java

```java
package com.ict.bbs.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.ict.bbs.mapper.BbsMapper;
import com.ict.bbs.vo.BbsVO;
import com.ict.bbs.vo.CommVO;

@Service
public class BbsService {
    @Autowired
    private BbsMapper b_Mapper;

    public int getCount(String bname){
        return b_Mapper.count(bname);
    }

    public BbsVO[] getList(String bname, String begin, String end){
        BbsVO[] ar = null;
        List<BbsVO> list = b_Mapper.list(bname, begin, end);
        if(list != null && list.size() > 0){
            ar = new BbsVO[list.size()];
            list.toArray(ar);
        }
        return ar;
    }

    public BbsVO getBbs(String b_idx){
        return b_Mapper.getBbs(b_idx);
    }

    public int addBbs(BbsVO vo){
        return b_Mapper.add(vo);
    }

    public int addComm(CommVO vo){
        return b_Mapper.addComm(vo);
    }
}
```
## B.3. DbConfig.java
> src > main > java > com > ict > bbs > config > DbConfig.java

```java
package com.ict.bbs.config;

import javax.sql.DataSource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;

@Configuration
@MapperScan(basePackages = "com.ict.bbs.mapper")
public class DbConfig {
    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean sqlSessionFactory = new SqlSessionFactoryBean();
        sqlSessionFactory.setDataSource(dataSource);
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        sqlSessionFactory.setMapperLocations(resolver.getResources("classpath:mapper/**/*.xml"));
        return sqlSessionFactory.getObject();
    }

    @Bean
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory factory) {
        return new SqlSessionTemplate(factory);
    }
}
```
## B.4. BbsController.java
> src > main > java > com > ict > bbs > controller > BbsController.java

```java
package com.ict.bbs.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.io.ResourceLoader;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import com.ict.bbs.service.BbsService;
import com.ict.bbs.util.Paging2;
import com.ict.bbs.vo.BbsVO;

import jakarta.servlet.ServletContext;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

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
    private ResourceLoader resourceLoader;

    private String editor_img = "/editor_img";
    private String upload_file = "/upload_file";

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
        mv.addObject("nowPage", nowPage);
        mv.addObject("bname", bname);
        mv.addObject("blockList", page.getBlockList());
        mv.setViewName("bbs/list");

        return mv;
    }
}
```
## B.5. Paging2.java
> src > main > java > com > ict > bbs > util > Paging2.java

```java
package com.ict.bbs.util;

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
		
		if(nowPage > totalPage) {
			nowPage = totalPage;
			this.nowPage = nowPage;
		}
		
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
## B.6. FileRenameUtil.java
> src > main > java > com > ict > bbs > util > FileRenameUtil.java

```java
package com.ict.bbs.util;

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
## B.7. list.jsp
> src > main > webapp > WEB-INF > jsp > bbs > list.jsp

```HTML
<%@page import="com.ict.bbs.util.Paging2"%>
<%@page import="com.ict.bbs.vo.BbsVO"%>
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
			<tbody>
				<c:forEach var="vo" items="${requestScope.ar}" varStatus="vs">
					<tr>
						<td>${totalRecord - ((nowPage - 1) * blockList + vs.index)}</td>
						<td style="text-align: left">
							<a href="view?b_idx=${vo.b_idx}&cPage=${nowPage}&bname=${bname}">
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
			<tfoot>
				<tr>
					<td colspan="4">
						${pageCode }
					</td>
					<td><input type="button" value="글쓰기" onclick="javascript:location.href='write?bname=bbs'"/></td>
				</tr>
			</tfoot>
		</table>
	</div>
</body>
</html>
```
## B.8. write.jsp
> src > main > webapp > WEB-INF > jsp > bbs > write.jsp

```HTML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>WRITE</title>
<link rel="stylesheet" href="css/style.css"/>
<link rel="stylesheet" href="css/summernote-lite.css"/>
</head>
<body>
	<div id="bbs">
	<form action="write" name="frm" method="post" encType="multipart/form-data">
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
						<input type="button" value="목록" onclick="listBbs()"/>
					</td>
				</tr>
			</tbody>
		</table>
		<input type="hidden" name="bname" value="${param.bname}"/>
	</form>
	</div>
	<script src="https://code.jquery.com/jquery-3.7.1.min.js" integrity="sha256-/JqT3SQfawRcv/BIHPThkBvs0OEvtFFmqPF/lYI/Cxo=" crossorigin="anonymous"></script>
	<script src="js/summernote-lite.js"></script>
	<script src="js/lang/summernote-ko-KR.js"></script>
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
		
		function listBbs(){
			document.frm.action = "list";
			document.frm.submit();
		}
	</script>
</body>
</html>
```