> 카카오 개발자 센터인 [Kakao Developers](https://developers.kakao.com/)에서 카카오 API 사용을 위한 설정을 정리해둔 페이지이다.

# 1. 내 애플리케이션 추가
> [Kakao Developers](https://developers.kakao.com/) > 내 애플리케이션 > 애플리케이션 추가하기

<font color="#ddd9c3">입력 예:</font>

|구분|입력|
|:---:|:---:|
|앱 이름|Login_ICTedu|
|사업자명|ICT인재교육원|
|카테고리|교육|

## 1.1. 요약 정보 확인
### 1.1.1. 앱 키
> Login_ICTedu 기준 키

|     Key      | Value |
| :----------: | :---: |
|   네이티브 앱 키   |       |
|  REST API 키  |       |
| JavaScript 키 |       |
|   Admin 키    |       |

### 1.1.2. 플랫폼 등록 및 관리
> 내 애플리케이션 > Login_ICTedu > 요약 정보 > Web 플랫폼 등록
> Login_ICTedu 기준 값

|      구분      |  값  |
| :----------: | :-: |
|   사이트 도메인    |     |
| Redirect URI |     |

<font color="#92cddc">Redirect URI 위치:</font>
- 카카오 로그인 사용 시 Redirect URI 등록하러 가기
- <font color="#95b3d7">활성화 설정 상태 ON 체크</font>
## 1.2. 동의 항목
> 내 애플리케이션 > 제품 설정 > 카카오 로그인 > 동의 항목

<font color="#ddd9c3">Login_ICTedu 기준 입력 예:</font>

| 항목 | 동의 수준 | 동의 목적 |
| :--: | :--: | :--: |
| 닉네임 | 필수 동의 | 정보 수집 |
| 프로필 사진 | 필수 동의 | 정보 수집 |

# 2. 버튼 아이콘 다운로드
> 문서 > 카카오 로그인 > 디자인 가이드

<font color="#ddd9c3">Login_ICTedu 기준 다운로드 예:</font>
1. 카카오 로그인 버튼 > 리소스 다운로드 > 다운로드(.png)
2. "src/main/webapp/resources/img/"에 담기
# 3. 카카오 로그인 로직
> 문서 > 카카오 로그인 > JavaScript
> 문서 > 카카오 로그인 > REST API

![[Pasted image 20240115125607.png]]
## 3.1. 인가 코드 받기
1. 서비스 서버가 카카오 인증 서버로 [인가 코드 받기](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-code)를 요청합니다.
2. 카카오 인증 서버가 사용자에게 카카오계정 로그인을 통한 [인증](https://developers.kakao.com/docs/latest/ko/kakaologin/common#intro)을 요청합니다.
    - 클라이언트에 유효한 카카오계정 세션이 있거나, 카카오톡 인앱 브라우저에서의 요청인 경우 4단계로 넘어갑니다.
3. 사용자가 카카오계정으로 로그인합니다.
4. 카카오 인증 서버가 사용자에게 동의 화면을 출력하여 [인가](https://developers.kakao.com/docs/latest/ko/kakaologin/common#intro)를 위한 사용자 동의를 요청합니다.
    - 동의 화면은 서비스 애플리케이션(이하 앱)의 [동의항목 설정](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#consent-item)에 따라 구성됩니다.
5. 사용자가 필수 동의항목, 이 외 원하는 동의항목에 동의한 뒤 [동의하고 계속하기] 버튼을 누릅니다.
6. 카카오 인증 서버는 서비스 서버의 Redirect URI로 인가 코드를 전달합니다.
### 3.1.1. 기본 정보 / 인가 코드 받기
| <font color="#92cddc">메서드</font> |     <font color="#92cddc">URL</font>      | 인증 방식 |
| :------------------------------: | :---------------------------------------: | :---: |
|              `GET`               | `https://kauth.kakao.com/oauth/authorize` |   -   |

| 권한 | 사전 설정                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | [카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/common) | [사용자 동의](https://developers.kakao.com/docs/latest/ko/kakaologin/common#authorization-consent-item) |
|:----:| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- |
|  -   | [플랫폼 등록](https://developers.kakao.com/docs/latest/ko/getting-started/app#platform)  <br>[카카오 로그인 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-activate)  <br>[Redirect URI 등록](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-redirect-uri)  <br>[동의항목](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#consent-item)  <br>[OpenID Connect 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-oidc)(선택)  <br>[간편가입](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#simple-signup)(선택) | 필요                                                                           | 필요:  <br>필수 동의항목                                                                                |
### 3.1.2. 쿼리 파라미터 / 요청 / 인가 코드 받기
|     이름      |   타입   | 설명                                                                                                      | 필수 |
|:-------------:|:--------:| --------------------------------------------------------------------------------------------------------- |:----:|
|   <font color="#92cddc">client_id</font>   | `String` | 앱 REST API 키  <br>[내 애플리케이션] > [앱 키]에서 확인 가능                                             |  O   |
| <font color="#92cddc">redirect_uri</font>  | `String` | 인가 코드를 전달받을 서비스 서버의 URI  <br>[내 애플리케이션] > [카카오 로그인] > [Redirect URI]에서 등록 |  O   |
| <font color="#92cddc">response_type</font> | `String` | `code`로 고정                                                                                             |  O   |
## 3.2. 토큰 받기
1. 서비스 서버가 Redirect URI로 전달받은 인가 코드로 [토큰 받기](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-token)를 요청합니다.
2. 카카오 인증 서버가 토큰을 발급해 서비스 서버에 전달합니다.
### 3.2.1. 기본 정보 / 토큰 받기
| <font color="#92cddc">메서드</font> |                  <font color="#92cddc">URL</font>                  | 인증 방식 |
|:------:|:-------------------------------------:| --------- |
| `POST` | `https://kauth.kakao.com/oauth/token` | -         |

| 권한 | 사전 설정                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | [카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/common) | [사용자 동의](https://developers.kakao.com/docs/latest/ko/kakaologin/common#authorization-consent-item) |
|:----:| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- |
|  -   | [플랫폼 등록](https://developers.kakao.com/docs/latest/ko/getting-started/app#platform)  <br>[카카오 로그인 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-activate)  <br>[Redirect URI 등록](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-redirect-uri)  <br>[동의항목](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#consent-item)  <br>[OpenID Connect 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-oidc)(선택) | 필요                                                                           | 필요:  <br>필수 동의항목                                                                                |
### 3.2.2. 헤더 / 요청 / 토큰 받기
|                   이름                    | 설명                                                                                  | 필수 |
|:-----------------------------------------:| ------------------------------------------------------------------------------------- |:----:|
| <font color="#92cddc">Content-type</font> | `Content-type: application/x-www-form-urlencoded;charset=utf-8`  <br>요청 데이터 타입 |  O   |

Content-type
- GET 방식 : null
- POST 방식 : application...
### 3.2.3. 본문 / 요청 / 토큰 받기
|     이름     |   타입   | 설명                                                          | 필수 |
|:------------:|:--------:| ------------------------------------------------------------- |:----:|
|  grant_type  | `String` | `authorization_code`로 고정                                   |  O   |
|  client_id   | `String` | 앱 REST API 키  <br>[내 애플리케이션] > [앱 키]에서 확인 가능 |  O   |
| redirect_uri | `String` | 인가 코드가 리다이렉트된 URI                                  |  O   |
|     code     | `String` | 인가 코드 받기 요청으로 얻은 인가 코드                        |  O   |
### 3.2.4. 본문 / 응답 / 토큰 받기
|           이름           |   타입    | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | 필수 |
|:------------------------:|:---------:| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |:----:|
|        token_type        | `String`  | 토큰 타입, `bearer`로 고정                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |  O   |
|       <font color="#92cddc">access_token</font>       | `String`  | 사용자 액세스 토큰 값                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |  O   |
|         id_token         | `String`  | [ID 토큰](https://developers.kakao.com/docs/latest/ko/kakaologin/common#oidc-id-token) 값  <br>OpenID Connect 확장 기능을 통해 발급되는 ID 토큰, Base64 인코딩 된 사용자 인증 정보 포함  <br>  <br>**제공 조건**: [OpenID Connect](https://developers.kakao.com/docs/latest/ko/kakaologin/common#oidc)가 활성화 된 앱의 토큰 발급 요청인 경우  <br>또는 `scope`에 `openid`를 포함한 [추가 항목 동의 받기](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#additional-consent) 요청을 거친 토큰 발급 요청인 경우 |  X   |
|        expires_in        | `Integer` | 액세스 토큰과 ID 토큰의 만료 시간(초)  <br>  <br>**참고**: 액세스 토큰과 ID 토큰의 만료 시간은 동일                                                                                                                                                                                                                                                                                                                                                                                                                             |  O   |
|      <font color="#92cddc">refresh_token</font>       | `String`  | 사용자 리프레시 토큰 값                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |  O   |
| refresh_token<br>_expires_in | `Integer` | 리프레시 토큰 만료 시간(초)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |  O   |
|          scope           | `String`  | 인증된 사용자의 정보 조회 권한 범위  <br>범위가 여러 개일 경우, 공백으로 구분  <br>  <br>**참고**: [OpenID Connect](https://developers.kakao.com/docs/latest/ko/kakaologin/common#oidc)가 활성화된 앱의 토큰 발급 요청인 경우, ID 토큰이 함께 발급되며 `scope` 값에 `openid` 포함                                                                                                                                                                                                                                               |  X   |
## 3.3. 사용자 정보 가져오기
- 서비스 서버가 발급받은 액세스 토큰으로 [사용자 정보 가져오기](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#req-user-info)를 요청해 사용자의 회원번호 및 정보를 조회하여 서비스 회원인지 확인합니다.
- 서비스 회원 정보 확인 결과에 따라 서비스 로그인 또는 회원 가입 과정을 진행합니다.
- 이 외 서비스에서 필요한 로그인 절차를 수행한 후, 카카오 로그인한 사용자의 서비스 로그인 처리를 완료합니다.
### 3.3.1. 기본 정보 / 사용자 정보 가져오기
|   <font color="#92cddc">메서드</font>   |                 <font color="#92cddc">URL</font>                 |              인증 방식               |
|:----------:|:-----------------------------------:|:------------------------------------:|
| `GET/POST` | `https://kapi.kakao.com/v2/user/me` | 액세스 토큰  <br>서비스 앱 어드민 키 |

| 권한 | 사전 설정                                                                                                                                                                                                                                                                                                       | [카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/common) | [사용자 동의](https://developers.kakao.com/docs/latest/ko/kakaologin/common#authorization-consent-item) |
|:----:| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- |
|  -   | [플랫폼 등록](https://developers.kakao.com/docs/latest/ko/getting-started/app#platform)  <br>[카카오 로그인 활성화](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login-activate)  <br>[동의항목](https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#consent-item) | 필요                                                                           | 필요:  <br>사용자 정보를 요청할 모든 동의항목                                                           |
### 3.3.2. 헤더 / 요청 : 엑세스 토큰 방식
|     이름      | 설명                                                                                  | 필수 |
|:-------------:| ------------------------------------------------------------------------------------- |:----:|
| Authorization | `Authorization: Bearer ${ACCESS_TOKEN}`  <br>인증 방식, 액세스 토큰으로 인증 요청     |  O   |
| Content-type  | `Content-type: application/x-www-form-urlencoded;charset=utf-8`  <br>요청 데이터 타입 |  O   |

# 4. 로그인 구현
## 4.1. 로그인 버튼 구현
### 4.1.1. JSP 구현
> src > main > webapp > WEB-INF > views > JSP File

```HTML
<h1>Hello world!</h1>
<c:if test="${sessionScope.mvo eq null}">
	<form action="" method="post">
		ID:<input type="text" name="id"/><br/>
		PW:<input type="password" name="pw"/><br/>
		<input type="button" value="LOGIN"/>
	</form>
	<br/>
	<a href="https://kauth.kakao.com/oauth/authorize?client_id=e789fe6fe29601ced8ba27c1ab2ac965&redirect_uri=http://localhost:8080/kakao/login&response_type=code">
		<img src="resources/img/kakao_login.png"/>
	</a>
</c:if>
```
- form 태그는 임의로 작성한 로그인 화면 구성이다.
- <font color="#fac08f">중요한 부분은 a 태그</font>이다.
	- href의 경로는 `https://kauth.kakao.com/oauth/authorize`이다.
	- <font color="#95b3d7">파라미터</font>로 `client_id`, `redirect_uri`, `response_type`을 <font color="#95b3d7">함께 전송</font>한다.
	- [[#3.1.2. 쿼리 파라미터 / 요청 / 인가 코드 받기|쿼리 파라미터]]를 참고하면:
		- `client_id`: 앱 REST API 키
		- `redirect_uri`: <font color="#76923c">인가 코드를 전달받을 서비스 서버의 URI</font>
		- `response_type`: `code`로 고정
	- `redirect_uri`로 인해 이 a 태그를 클릭하면 `http://localhost:8080/kakao/login`으로 인가 코드를 전달하게 된다. 즉 <font color="#95b3d7">Controller</font>로 와서 <font color="#95b3d7">RequestMapping("kakao/login")</font>인 메서드를 찾게 된다.
- img 태그는 [[#2. 버튼 아이콘 다운로드|버튼 아이콘 다운로드]] 절차에서 받은 파일의 경로를 지정하고 있다.

![[Pasted image 20240115131529.png|150]]
- 위와 같이 출력된다.
## 4.2. 라이브러리 및 세팅
### 4.2.1. Server: path 수정
> Servers > Tomcat v9.0 Server at localhost-config > server.xml

```xml
<Context docBase="0115_kakao" path="/" reloadable="true" source="org.eclipse.jst.jee.server:0115_kakao"/>
```
- 위와 같이 path를 수정하자.
### 4.2.2. JSON Parsing Library 추가
> [MVNRepository](https://MVNRepository.com) > search: JSON.simple > 1.1.1 > Maven
> -> 0115_kakao > pom.xml

```xml
<dependency>
    <groupId>com.googlecode.json-simple</groupId>
    <artifactId>json-simple</artifactId>
    <version>1.1.1</version>
</dependency>
```
- 하나의 문자열로 되어있는 JSON 표현식을 JSON 객체로 변환해주는 라이브러리
- 이 외에도 JSON Parsing 관련 라이브러리는 무수히 많다.
### 4.2.3. VO 객체 생성
> src/main/java > mybatis.vo > Java Class : MemberVO

```java
package mybatis.vo;

public class MemberVO {
	
	private String nickName, p_img, thum_img;
	private String email, phone, addr;
	
	public String getNickName() {
		return nickName;
	}
	public void setNickName(String nickName) {
		this.nickName = nickName;
	}
	public String getP_img() {
		return p_img;
	}
	public void setP_img(String p_img) {
		this.p_img = p_img;
	}
	public String getThum_img() {
		return thum_img;
	}
	public void setThum_img(String thum_img) {
		this.thum_img = thum_img;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getPhone() {
		return phone;
	}
	public void setPhone(String phone) {
		this.phone = phone;
	}
	public String getAddr() {
		return addr;
	}
	public void setAddr(String addr) {
		this.addr = addr;
	}

}
```
## 4.3. Controller 구성
> src/main/java > com.ict.kakao > Java Class : KakaoController

### 4.3.1. 인가 코드 받기
```java
@Controller
public class KakaoController {
```
- KakaoController 클래스를 Controller로 선언했다.

```java
@Autowired
private HttpSession session;
```
- 멤버 변수로 HttpSession 타입의 객체를 선언하고, Autowired를 이용해 객체를 자동으로 주입받는다.

```java
@RequestMapping("/kakao/login")
public ModelAndView login(String code) {
	ModelAndView mv = new ModelAndView();
	System.out.println(code); // for Test!
	return mv;
}
```
- login() 메서드에 RequestMapping("/kakao/login")을 걸었다.
	- JSP에서 카카오 인증 서버로 인가 코드 받기를 요청할 때 파라미터로 redirect_uri를 지정하였는데, `http://localhost:8080/kakao/login`이므로 이 메서드가 인가 코드를 받는다.
	- 이 메서드의 인자인 String code로 인가 코드가 들어오게 된다.
- 서버 구동 > 카카오 로그인 클릭 시 다음과 같은 화면이 나타난다.
	![[Pasted image 20240115150203.png|200]]
	- 이때 로그인을 할 경우 Console에 인가 코드가 찍히는 것을 볼 수 있다.
### 4.3.2. 토큰 받기
```java
String access_Token = "";
String refresh_Token = "";
```
- 토큰 값을 저장할 변수를 생성한다.

```java
String reqURL = "https://kauth.kakao.com/oauth/token";
```
- 토큰 발급을 요청할 URL을 변수에 저장한다.

<font color="#ddd9c3">이하: try-catch 구문의 try 구문</font>
```java
URL url = new URL(reqURL);
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("POST");
conn.setDoOutput(true);
```
- 웹 상의 경로<font color="#7f7f7f">(reqURL)</font>를 객체화 시키기 위해 java.net의 URL 객체를 생성한다.
- 주어진 URL에 대한 연결을 만들기 위해 java.net의 HttpURLConnection 객체를 생성한다.
- `Add cast to "HttpURLConnection"`을 선택하여 형 변환을 하자.
	- url.openConnection()은 URLConnection 타입으로 반환된다.
	- URLConnection은 HttpURLConnection의 부모 클래스이다.
	- HttpURLConnection은 URLConnection을 상속받아 HTTP에 특화된 기능을 제공하는 클래스이며 HTTP 프로토콜을 사용하기 때문이다.
- 토큰 요청은 <font color="#95b3d7">POST 방식</font>으로 해야 하므로 <font color="#95b3d7">HttpURLConnection</font>의 <font color="#95b3d7">setRequestMethod</font>를 <font color="#95b3d7">POST</font>, <font color="#95b3d7">setDoOutput</font>을 <font color="#95b3d7">true</font>로 설정한다.
	- setDoOutput은 출력 스트림을 사용하도록 설정하는 메서드이다. 이는 POST 요청응ㄹ 보낼 때 요청 본문에 데이터를 쓰기 위해 필요한 설정이다. 설정하지 않으면 POST 데이터를 서버에 보낼 수 없다.

```java
BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
```
- 토큰 요청시 파라미터를 같이 보내기 위해 OutputStream을 만든다.
- HttpURLConnection을 통해 얻은 출력 스트림<font color="#7f7f7f">(OutputStream)</font>을 문자 출력 스트림<font color="#7f7f7f">(OutputStreamWriter)</font>으로 변환하고, 버퍼링된 쓰기<font color="#7f7f7f">(BufferedWriter)</font>를 수행한다.
	- <font color="#95b3d7">BufferedWriter는</font> 데이터를 조금씩 버퍼에 쓰고, <font color="#95b3d7">버퍼가 가득 차</font>거나 <font color="#95b3d7">flush() 메서드가 호출</font>될 때까지 실제 출력 스트림으로 <font color="#95b3d7">전송을 지연</font>시켜 <font color="#95b3d7">성능을 향상</font>시킬 수 있다.

```java
StringBuffer sb = new StringBuffer();
sb.append("grant_type=authorization_code");
sb.append("&client_id=");
sb.append("&redirect_uri=http://localhost:8080/kakao/login");
sb.append("&code=" + code);
```
- StringBuffer 객체에 4개의 파라미터를 담는다.

```java
bw.write(sb.toString());
bw.flush();
```
- 준비된 파라미터를 BufferedWriter 인스턴스에 담아 보낸다.
	- BufferedWriter는 HttpURLConnection을 갖고 있고, 이는 URL을 갖고 있기 때문이다.

```java
int res_code = conn.getResponseCode();
System.out.println("RES_CODE : " + res_code); // for Test!
```
- res_code가 200번이면 요청에 성공한 것이다.
### 4.3.3. 사용자 정보 가져오기
```java
if(res_code == 200) {
	BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
}
```
- 요청에 성공<font color="#7f7f7f">(res_code = 200)</font>한 경우 이하를 수행한다.
- 카카오 서버로부터 받은 JSON 타입의 <font color="#95b3d7">결과 메시지를</font> BufferedReader를 이용해 <font color="#95b3d7">읽어오자</font>.

<font color="#ddd9c3">이하: if 문 안쪽</font>
```java
StringBuffer result = new StringBuffer();
String line = null;
```
- 결과 메시지를 한 줄씩 읽어 StringBuffer 객체에 적재하기 위해 StringBuffer result를 생성하자.

```java
while((line = br.readLine()) != null) {
	result.append(line);
}
System.out.println(result.toString()); // fot Test!
```
- <font color="#95b3d7">더 이상 읽을 내용이 없을 때까지</font> while 문을 수행한다.
- print 문으로 내용을 확인해보면 JSON 형태로 access_token, token_type, refresh_token, expires_in, scope, refresh_token_expires_in 값이 온 것을 알 수 있다.
	```
	{
		"access_token":"TrJnE-596rx4_LAmIoI-ieqR2OpoNbt8578KPXUbAAABjQwRj2wWphHJzwXJqw",
		"token_type":"bearer",
		"refresh_token":"CGJmqSyvHStYshD8iEfdCaWlPMaE0E1LkR4KPXUbAAABjQwRj2kWphHJzwXJqw",
		"expires_in":21599,
		"scope":"profile_image profile_nickname",
		"refresh_token_expires_in":5183999
	}
	```
- Parsing을 하자.

```java
JSONParser pars = new JSONParser();
```
- access_token과 refresh_token 값을 <font color="#95b3d7">JSON Parsing 처리</font>로 잡아내자.
	1. 이 값으로 카카오 API 서버에 <font color="#76923c">사용자 정보를 요청</font>한 후
	2. <font color="#76923c">ModelAndView에 저장</font>하여
	3. result.<font color="#76923c">jsp에서 결과를 표현</font>할 수 있도록 하기 위함이다.
- org.json.simple.parser의 JSONParser 객체를 생성하자.

```java
Object obj = pars.parse(result.toString());
```
- 결과 메시지를 객체화 한다.

```java
JSONObject json = (JSONObject) obj;
```
- 위에서 객체화 한 결과 메시지를 JSONObject로 변환한다.
- 자료형이 달라지므로 `Add cast to 'JSONObject'`를 선택하자.

```java
access_Token = (String) json.get("access_token");
refresh_Token = (String) json.get("refresh_token");
```
- 위에서 생성한 변수에 각각의 값을 할당해 주자.
- 자료형이 달라지므로 `Add cast to 'String'`을 선택하자.

```java
System.out.println(access_Token); // for Test!
System.out.println(refresh_Token); // for Test!
```
- 알맞게 값이 저장되었는지 확인하자.

```java
String apiURL = "https://kapi.kakao.com/v2/user/me";
String header = "Bearer " + access_Token;
```
- access_Token을 가지고 사용자 정보 요청을 위한 변수를 생성하자.
- [[#3.3.2. 헤더 / 요청 엑세스 토큰 방식|요청 엑세스 토큰 방식]]을 참고하면 `Authorization: Bearer ${ACCESS_TOKEN}`을 요구하였다. 즉 header는 Authorization 값에 해당한다.

```java
URL url2 = new URL(apiURL);
```
- <font color="#95b3d7">Java 객체</font>에서 특정 <font color="#95b3d7">웹상의 경로를 호출</font>하려면 <font color="#95b3d7">URL 객체가 필요</font>하다.

```java
HttpURLConnection conn2 = (HttpURLConnection) url2.openConnection();
conn2.setRequestMethod("POST");
conn2.setDoOutput(true);
```
- 연결 객체인 HttpURLConnection 객체를 생성하자. [[#4.3.2. 토큰 받기|토큰 받기]] 과정과 동일하게 진행한다.

```java
conn2.setRequestProperty("Authorization", header);
```
- [[#3.3.2. 헤더 / 요청 엑세스 토큰 방식|요청 엑세스 토큰 방식]]에 따라 HTTP Request header를 설정한다.

```java
res_code = conn2.getResponseCode();
System.out.println("RES_CODE : " + res_code + " / HttpURLConnection : " + HttpURLConnection.HTTP_OK); // for Test!
```
- 서버에서 받은 HTTP 응답 코드를 반환하면 res_code에 저장한다.
- res_code와 HttpURLConnection.HTTP_OK 값을 비교해 보자.
	- <font color="#95b3d7">정상적으로 연결</font>되었다면 <font color="#95b3d7">둘 다 200</font>번이 출력되어야 한다.

```java
if(res_code == HttpURLConnection.HTTP_OK) {
	
}
```
- res_code와 HttpURLConnection.HTTP_OK를 이용하여 요청에 성공한 경우의 if 문을 작성한다.

<font color="#ddd9c3">이하: if 문 안쪽</font>
```java
BufferedReader brdm = new BufferedReader(new InputStreamReader(conn2.getInputStream()));
String str = null;
StringBuffer res = new StringBuffer();
```
- 요청에 성공한 경우 카카오 서버측에서 사용자 정보를 보낸다.
- BufferedReader를 이용해 내용을 읽고 StringBuffer에 담자.

```java
while((str = brdm.readLine()) != null) {
	res.append(str);
}
System.out.println("RES : " + res.toString()); // for Test!
```
- while 문을 이용해 StringBuffer에 정보들을 한 줄씩 추가한다.
- StringBuffer에 저장된 내용을 확인해 보면 다음과 같다.
	```
	RES : {
		"id":3287078213,
		"connected_at":"2024-01-15T07:05:39Z",
		"properties":{
			"nickname":"주영",
			"profile_image":"http://k.kakaocdn.net/dn/img_640x640.jpg",
			"thumbnail_image":"http://k.kakaocdn.net/dn/img_110x110.jpg"},
			"kakao_account":{
				"profile_nickname_needs_agreement":false,
				"profile_image_needs_agreement":false,
				"profile":{"nickname":"주영",
				"thumbnail_image_url":"http://k.kakaocdn.net/dn/img_110x110.jpg",
				"profile_image_url":"http://k.kakaocdn.net/dn/img_640x640.jpg",
				"is_default_image":false
			}
		}
	}
	```

```java
obj = pars.parse(res.toString());
json = (JSONObject) obj;
```
- 사용자 정보를 담은 StringBuffer res를 JSON 객체로 변환한다.
- 위에서 이미 JSONObject 객체를 생성했으므로 재활용한다.

```java
JSONObject props = (JSONObject) json.get("properties");
```
- 원하는 정보는 JSON 객체 안에 properties라는 key에 또 JSON 형태로 저장되어 있는 상태이다. 따라서 JSONObject 객체를 하나 더 생성해 properties에 저장된 내용을 JSON 객체로 변환하자.

```java
String nickName = (String) props.get("nickname");
String p_img = (String) props.get("profile_image");
String thum_img = (String) props.get("thumbnail_image");
```
- properties에 있는 JSON 표현식을 JSONObject props에 JSON 객체로 저장하였다.
- 이제 필요한 nickname, profile_image, thumbnail_image를 추출하자.
- 이들을 VO 객체에 저장하자.

```java
MemberVO mvo = new MemberVO();
mvo.setNickName(nickName);
mvo.setP_img(p_img);
mvo.setThum_img(thum_img);
```
- VO 객체에 정보를 담는다.

```java
session.setAttribute("mvo", mvo);
```
- 사용자 정보를 저장한 MemberVO mvo를 session에 mvo라는 이름으로 저장한다.

```java
mv.setViewName("result");
```
- result.jsp로 보여주기 위한 뷰 정보를 등록한다. [[#4.4.1. JSP - 결과 화면 구현|>> 뷰 만들러 가기]]
### 4.3.4. 최종 코드 : Controller
```java
package com.ict.kakao;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;
import java.net.URL;

import javax.servlet.http.HttpSession;

import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import mybatis.vo.MemberVO;

@Controller
public class KakaoController {
	
	@Autowired
	private HttpSession session;
	
	@RequestMapping("/kakao/login")
	public ModelAndView login(String code) {
		ModelAndView mv = new ModelAndView();
		
		String access_Token = "";
		String refresh_Token = "";
		String reqURL = "https://kauth.kakao.com/oauth/token";
		
		try {
			URL url = new URL(reqURL);
			HttpURLConnection conn = (HttpURLConnection) url.openConnection();
			conn.setRequestMethod("POST");
			conn.setDoOutput(true);
			
			BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(conn.getOutputStream()));
			
			StringBuffer sb = new StringBuffer();
			sb.append("grant_type=authorization_code");
			sb.append("&client_id=");
			sb.append("&redirect_uri=http://localhost:8080/kakao/login");
			sb.append("&code=" + code);
			
			bw.write(sb.toString());
			bw.flush();
			
			int res_code = conn.getResponseCode();
			
			if(res_code == 200) {
				BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
				StringBuffer result = new StringBuffer();
				String line = null;
				
				while((line = br.readLine()) != null) {
					result.append(line);
				}
				
				JSONParser pars = new JSONParser();
				Object obj = pars.parse(result.toString());
				JSONObject json = (JSONObject) obj;
				
				access_Token = (String) json.get("access_token");
				refresh_Token = (String) json.get("refresh_token");
				
				String apiURL = "https://kapi.kakao.com/v2/user/me";
				String header = "bearer " + access_Token;
				
				URL url2 = new URL(apiURL);
				HttpURLConnection conn2 = (HttpURLConnection) url2.openConnection();
				conn2.setRequestMethod("POST");
				conn2.setDoOutput(true);
				
				conn2.setRequestProperty("Authorization", header);
				
				res_code = conn2.getResponseCode();
				if(res_code == HttpURLConnection.HTTP_OK) {
					BufferedReader brdm = new BufferedReader(new InputStreamReader(conn2.getInputStream()));
					String str = null;
					StringBuffer res = new StringBuffer();
					
					while((str = brdm.readLine()) != null) {
						res.append(str);
					}
					
					obj = pars.parse(res.toString());
					json = (JSONObject) obj;
					
					JSONObject props = (JSONObject) json.get("properties");
					String nickName = (String) props.get("nickname");
					String p_img = (String) props.get("profile_image");
					String thum_img = (String) props.get("thumbnail_image");
					
					MemberVO mvo = new MemberVO();
					mvo.setNickName(nickName);
					mvo.setP_img(p_img);
					mvo.setThum_img(thum_img);
					
					session.setAttribute("mvo", mvo);
					mv.setViewName("result");
				}
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
		
		return mv;
	}

}
```
## 4.4. 결과 화면
### 4.4.1. JSP - 결과 화면 구현
> src > main > webapp > WEB-INF > views > JSP File

```HTML
<div>
	<header>
		<h1>회원정보</h1>
	</header>
	<article>
		<h2>${sessionScope.mvo.nickName}</h2>
		<img src="${mvo.p_img}"/>
		<a href="/">HOME</a>
	</article>
</div>
```
- Controller에서 세션에 MemberVO mvo를 저장하였으므로 EL을 이용해 VO 객체에 저장된 nickName, p_img를 사용할 수 있다.
### 4.4.2. 결과 화면
![[Pasted image 20240115181605.png|200]]
- 로그인에 성공하면 뷰는 result.jsp로 이동하며, 위와 같이 출력된다.