# 1. JSTL이란?
JSTL<font color="#7f7f7f">(JSP Standard Tag Library)</font>은 <font color="#92cddc">JSP</font><font color="#7f7f7f">(JavaServer Pages)</font>에서 사용하기 위한 <font color="#92cddc">표준 태그 라이브러리</font>이다. <font color="#7f7f7f">(서버 측에서 동적인 웹 페이지를 생성하는 데)</font> 자주 사용되는 공통적인 작업을 쉽게 수행할 수 있도록 도와주는 <font color="#92cddc">태그들의 집합</font>이다.

## 1.1. JSTL 요구 조건: 라이브러리 추가
두 개의 라이브러리<font color="#7f7f7f">(jar)</font> 파일이 필요하다.
현재 프로젝트에 포함되어 있는지 확인하고 없다면 추가하여야 한다.
필요한 라이브러리는:
- <font color="#fac08f">standard.jar</font>
- <font color="#fac08f">jstl.jar</font>

<font color="#c3d69b">라이브러리를 다운 받아야 한다면:</font>
1. [Apache](https://apache.org)
2. APACHE PROJECT LIST > Tomcat
3. Apache Tomcat > Taglibs
4. Download > Taglibs > Apache Taglibs Downloads > Apache Standard Taglib
5. Standard 1.1 download > bianries > jakarta-taglibs-standard-1.1.2.zip
## 1.2. JSTL 요구 조건: taglib 정의
JSP 페이지의 상단에 <font color="#95b3d7">taglib를 정의</font>해야 한다.
```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
```

추가로 Formatting and Internationalization Tags를 사용해야 한다면
```
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>
```
위 정의를 추가하자.

이 단계까지 완료해야 JSTL을 사용할 수 있다.
# 2. JSTL Tags
## 2.1. Control Flow Tags
* 컨트롤 흐름 태그
* 조건문과 반복문을 포함한 흐름 제어를 위한 태그
### `<c:if>`
### `<c:choose>`
### `<c:forEach>`
## 2.2. Core Tags
- 코어 태그
- 변수 선언, 출력, URL 처리 등과 같은 기본적인 작업을 수행하기 위한 태그
### `<c:set>`
### `<c:out>`
### `<c:url>`
## 2.3. Formatting and Internationalization Tags
- 포맷 및 국제화 태그
- 숫자와 날짜 형식화, 메시지 국제화 등과 같은 작업을 위한 태그
### `<fmt:formatNumber>`
### `<fmt:formatData>`
### `<fmt:setLocale>`