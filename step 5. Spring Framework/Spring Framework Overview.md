Java 기반의 오픈 소스 프레임워크이다.
# 1. Spring Framework의 흐름 이해를 위한 주요 개념
## 1.1. Dependency Injection
- DI: 의존성 주입
- Spring은 의존성 주입을 통해 객체 간의 결합도를 낮춘다.
- 개발자가 직접 객체를 생성하는 대신 <font color="#fac08f">Spring container가 객체를 생성하고 관리하며, 필요한 곳에 주입</font>한다.
### 1.1.1. 의존성 주입 예시
- Service Class와 Client Class가 있다고 가정해 보자.
#### 1.1.1.1. 의존성이 직접 생성되는 경우
```java
// 서비스 클래스
public class MyService {
    public void doSomething() {
        System.out.println("MyService is doing something.");
    }
}

// 클라이언트 클래스
public class MyClient {
    private MyService service = new MyService(); // 직접 생성

    public void useService() {
        service.doSomething();
    }
}
```
- `MyClient` 클래스는 `MyService` 객체를 직접 생성한다.
- 이렇게 되면 `MyClient`와 `MyService`는 <font color="#fac08f">강하게 결합되어 있어, 유지보수와 테스트가 어려워진다</font>.
#### 1.1.1.2. 의존성 주입을 이용한 경우
```java
// 서비스 인터페이스
public interface MyService {
    void doSomething();
}

// 실제 서비스 구현 클래스
public class MyServiceImpl implements MyService {
    public void doSomething() {
        System.out.println("MyServiceImpl is doing something.");
    }
}

// 클라이언트 클래스
public class MyClient {
    private MyService service; // 의존성 주입

    // 생성자를 통한 주입
    public MyClient(MyService service) {
        this.service = service;
    }

    public void useService() {
        service.doSomething();
    }
}
```
- `MyClient`는 `MyService` 인터페이스에만 의존하고, 실제 구현은 외부에서 주입받는다.
- 서비스 인터페이스에서 `doSomething` 메서드를 선언하였다.
- 실제 서비스 구현 클래스에서는 
	- `MyService` 인터페이스를 구현하였다.
	- `doSomething` 메서드를 구현하여 특정 동작을 정의하였다.
- 클라이언트 클래스는
	- `MyService` 인터페이스에 의존하고, 이를 통해 특정 동작을 수행하는 클라이언트 클래스이다.
	- 생성자를 통해 의존성을 주입받았다.
	- `useService` 메서드를 호출하여 실제 서비스의 `doSomething` 메서드를 실행하였다.

```java
// 클라이언트 클래스를 사용하는 예시
public class MyApp {
    public static void main(String[] args) {
        MyService actualService = new MyServiceImpl();
        MyClient client = new MyClient(actualService);
        client.useService();
    }
}
```
- 애플리케이션 실행 클래스인 `MyApp`에서는 `MyServiceImpl`을 생성하여 `MyClient`에 주입하여 사용한다.
- 이로써 `MyClient`는 `MyService` 인터페이스에만 의존하게 되어 유연성과 테스트 용이성이 증가하게 된다.
- 나중에 다른 서비스를 만들어 이를 주입할 경우에도 클라이언트 코드를 수정할 피요가 없어진다.
## 1.2. Spring container
- Spring 애플리케이션의 핵심이자 중심 역할을 하는 container는 <font color="#92cddc">Bean의 생명주기를 관리</font>하고 <font color="#92cddc">의존성 주입을 수행</font>한다.
- <font color="#c3d69b">ApplicationContext는 Spring container의 한 종류</font>로, XML 또는 JavaConfig와 같은 설정 파일을 통해 정의된다.
### 1.2.1. ApplicationContext
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

// 서비스 인터페이스
interface MyService {
    void doSomething();
}

// 서비스 구현 클래스
class MyServiceImpl implements MyService {
    public void doSomething() {
        System.out.println("MyServiceImpl is doing something.");
    }
}

// AppConfig 클래스: 빈 구성 정보를 제공
class AppConfig {
    MyService myService() {
        return new MyServiceImpl();
    }
}

public class MainApp {
    public static void main(String[] args) {
        // ApplicationContext 생성 및 설정 클래스 등록
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        // 빈(Bean) 획득
        MyService service = context.getBean(MyService.class);

        // 빈 사용
        service.doSomething();
    }
}
```
- AnnotationConfigApplicationContext를 사용하여 AppConfig 클래스를 등록하고, 이를 통해 MyService Bean을 획득하고 사용한다.
- AppConfig 클래스는 Bean을 구성하는 역할을 하며, MyServiceImpl을 Bean으로 등록하고 있다.

	|    R to C     | MyService  | MyServiceImpl | AppConfig | MainApp |
	|:-------------:|:----------:|:-------------:|:---------:|:-------:|
	|   MyService   |            |               |           |         |
	| MyServiceImpl | implements |               |           |         |
	|   AppConfig   |   method   |    return     |           |         |
	|    MainApp    |  getBean   |               |  context  |         |

### 1.2.2. BeanFactory
```java
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.xml.XmlBeanFactory;
import org.springframework.core.io.ClassPathResource;

// 서비스 인터페이스
interface MyService {
    void doSomething();
}

// 서비스 구현 클래스
class MyServiceImpl implements MyService {
    public void doSomething() {
        System.out.println("MyServiceImpl is doing something.");
    }
}

public class MainApp {
    public static void main(String[] args) {
        // BeanFactory 생성 및 설정 파일 로딩
        BeanFactory factory = new XmlBeanFactory(new ClassPathResource("beans.xml"));

        // 빈(Bean) 획득
        MyService service = (MyService) factory.getBean("myService");

        // 빈 사용
        service.doSomething();
    }
}
```
- XmlBeanFactory를 사용하여 XML 파일에서 Bean을 정의하고, 해당 Bean을 획득하여 사용한다.
- `beans.xml` 파일에 MyServiceImpl이라는 Bean이 정의되어 있어서 이를 획득하고 사용하고 있다.
### 1.2.3. Bean
- Spring IoC<font color="#7f7f7f">(Inversion of Control)</font> Container에 의해 관리되는 객체
	- 이는 일반적으로 Spring Application 개발자가 작성한 클래스의 인스턴스를 의미한다.
- Spring IoC Container에 의해 생성, 구성, 관리되며, 이를 통해 <font color="#fac08f">객체의 생명주기</font>와 <font color="#fac08f">의존성 주입을 관리</font>할 수 있다.
- 아래에서 정의되는 `MyBean`은 그 자체로 일반적인 Java 객체이지만, Spring IoC Container에 의해 <font color="#c3d69b">Bean으로 관리되기 위해서는 특정 규칙</font>을 따라야 한다.
	- Bean 클래스는 기본 생성자나 IoC Container가 호출할 수 있는 <font color="#92cddc">생성자</font>를 가져야 한다.
	- 필요한 경우 의존성 주입을 위해 Property를 설정할 수 있는 <font color="#92cddc">Getter와 Setter</font> 메서드를 제공해야 한다.
	- Bean은 일반적으로 Singleton, Prototype과 같은 Scope를 가질 수 있다.
		- Singleton은 하나의 인스턴스를 공유한다.
		- Prototype은 요청시마다 새로운 인스턴스를 생성한다.
	- `@PostConstruct`와 `@PreDestroy` Annotation 또는 `InitializingBean`과 `DisposableBean` Interface를 통해 Bean의 초기화 및 소멸 과정을 제어할 수 있다.
#### 1.2.3.1. Inversion of Control
- Spring Framework에서 제공하는, 객체의 생명주기와 의존성 관리를 담당하는 시스템이다.
- 객체지향 프로그래밍의 설계 원칙 중 하나로, <font color="#d99694">객체 간의 의존성을 역전시키는 개념</font>이다.
- 일반적으로 객체를 직접 생성하고 관리하는 대신 외부에서 생성 및 관리하도록 하는 것을 의미한다.
- 주요 Spring IoC Container
	- BeanFactory
		- Spring IoC Container의 가장 기본적인 형태
		- Bean의 생성, 구성, 관리, 제거 등의 기능 제공
		- Bean을 지연 로딩하는 특징
		- XML이나 Java Config 파일을 통해 Bean의 설정을 정의
	- ApplicationContext
		- BeanFactory의 확장된 형태
		- Bean의 지연 로딩, AOP, 이벤트 발행, 메시지 처리, Transaction 관리 등
		- AnnotationConfigApplicationContext, ClassPathXmlApplicationContext 등
		- <font color="#fac08f">XML, Java Config, Annotation을 사용하여 Bean의 설정을 정의</font>
- Spring IoC Container의 주요 동작
	- 설정 파일<font color="#7f7f7f">(XML, Java Config)</font>이나 Annotation을 통해 Bean의 정의를 읽는다.
	- 정의된 Bean들을 실제로 생성한다. Bean의 Scope에 따라 Singletone이면 한 번만, Prototype이면 요청마다 새로운 인스턴스를 생성한다.
	- Bean 간의 의존성이 있다면, IoC Container가 해당 의존성을 자동으로 주입한다.
	- Bean의 생명주기에 따라 초기화 메서드를 호출하고, Bean이 소멸할 때 소멸 메서드를 호출하여 Bean의 생명주기를 관리한다.
	- Bean이 더 이상 필요하지 않을 때 해당 Bean을 제거한다.
#### 1.2.3.2. XML 설정 파일을 통한 Bean 정의
```xml
<beans>
    <bean id="myBean" class="com.example.MyBean"/>
</beans>
```
#### 1.2.3.3. Java 설정 클래스를 통한 Bean 정의
```java
@Configuration
public class AppConfig {
    @Bean
    public MyBean myBean() {
        return new MyBean();
    }
}
```
#### 1.2.3.4. Component Scan을 통한 Bean 정의
```java
@Component
public class MyBean {
    // 클래스 내용
}
```
#### 1.2.3.5. Annotation을 이용한 Bean 정의
```java
@Service("myBean")
public class MyBean {
    // 클래스 내용
}
```
## 1.3. MVC Architecture
- Spring은 Model-View-Controller<font color="#7f7f7f">(MVC)</font> Architecture를 기반으로 하는 웹 애플리케이션 개발을 지원한다.
- <font color="#fac08f">사용자 요청은 Controller</font>에서 처리되고, <font color="#92cddc">그 결과는 View</font>로 표시되며, <font color="#d99694">비즈니스 로직은 Model</font>에서 처리된다.
## 1.4. Controller
- <font color="#c3d69b">사용자의 요청을 받아들이고, 비즈니스 로직을 호출한 뒤 적절한 View로 결과를 반환하는 역할을 하는 클래스</font>이다.
- Spring MVC에서는 `@Controller` <font color="#d99694">annotation을 이용하여 클래스를 컨트롤러로 선언</font>할 수 있다.
## 1.5. DispatcherServlet
- 모든 요청을 받아들이고, <font color="#92cddc">적절한 Controller에게 전달</font>하는 중앙 제어기이다.
- web.xml 파일에서 설정된다.
### 1.5.1. Servlet Context
- Spring Web MVC Application에서
	- DispatcherServlet이 동작하는 Web Application의 Root Context 및 
	- Servlet 스펙에 따라서 생성되는 Servlet Context를 의미한다.
- 이는 일반적으로 `servlet-context.xml` 파일에서 설정되며, <font color="#fac08f">Spring Web MVC 관련 Bean들을 정의</font>하고, <font color="#fac08f">Web Application에 대한 설정</font>을 담당한다.
- 예제 코드
	```xml
	<!-- servlet-context.xml -->
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:mvc="http://www.springframework.org/schema/mvc"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	                           http://www.springframework.org/schema/beans/spring-beans.xsd
	                           http://www.springframework.org/schema/mvc
	                           http://www.springframework.org/schema/mvc/spring-mvc.xsd">
	
	    <!-- ViewResolver 설정 -->
	    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	        <property name="prefix" value="/WEB-INF/views/" />
	        <property name="suffix" value=".jsp" />
	    </bean>
	
	    <!-- Controller, Interceptor, 등의 설정 -->
	
	    <mvc:annotation-driven />
	</beans>
	```
### 1.5.2. Root Context
- Spring Web MVC Application에서 Servlet Context와 분리된 부분을 말한다.
- 보통 `root-context.xml` 파일에 정의되며, <font color="#fac08f">데이터베이스 연결, 서비스, Repository와 같은 비웹 관련 Bean들을 정의</font>한다.
- 예제 코드
	```xml
	<!-- root-context.xml -->
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	                           http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <!-- DataSource 설정 -->
	    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
	        <property name="url" value="jdbc:mysql://localhost:3306/mydatabase" />
	        <property name="username" value="root" />
	        <property name="password" value="password" />
	    </bean>
	
	    <!-- JPA EntityManagerFactory 설정 등 -->
	
	</beans>
	```
## 1.6. ViewResolver
- <font color="#c3d69b">Controller에서 반환된 View 이름을</font> 기반으로 실제 View를 찾아내는 역할을 하는 클래스이다.
- ViewResolver는 View 이름을 <font color="#c3d69b">특정 View의 경로로 해석</font>하고 이를 <font color="#c3d69b">DispatcherServlet에게</font> 알려준다.
## 1.7. Model
- 비즈니스 로직의 처리 결과를 저장하고 전달하는 역할을 하는 객체이다.
- Controller에서 View로 데이터를 전달할 때 사용된다.
## 1.8. Interceptors and Filters
- Interceptors와 Filters는 요청과 응답의 처리 과정에 개입하여 부가적인 작업을 수행할 수 있다.
- 로깅, 보안 체크, 세션 관리 등을 말한다.
## 1.9. Data Access
- Spring은 Database Access를 위해 JDBC, ORM Framework인 Hibernate 등을 통해 Database와의 통합이 가능하며, Transaction 관리도 지원한다.
## 1.10. Aspect-Oriented Programming
- AOP: 횡단 관심사
- logging, transaction 관리 등을 모듈화하여 코드의 재사용성과 유지보수성을 높이는 기술이다.
- Spring은 AOP를 통해 <font color="#92cddc">관점 지향 프로그래밍</font>을 구현할 수 있다.
### 1.10.1. logging
- 컴퓨터 프로그램이 실행될 때 발생하는 이벤트나 상태에 대한 정보를 기록하는 프로세스
- 디버깅, 성능 분석, 보안 검사, 사용자 추적, 통계 수집 등 다양한 목적으로 활용된다.
### 1.10.2. transaction
- 데이터베이스에서 하나 이상의 작업을 수행하는 논리적인 작업 단위
- ACID 원칙
	- Atomicity<font color="#7f7f7f">(원자성)</font>
		- 모든 작업이 성공적으로 완료되거나 아무런 작업도 수행되지 않은 상태로 유지되어야 한다.
		- 하나의 작업이라도 실패하면 전체 트랜잭션이 롤백되어야 한다.
	- Consistency<font color="#7f7f7f">(일관성)</font>
		- 시작되기 전과 끝난 후의 데이터베이스는 일관된 상태를 유지해야 한다.
	- Isolation<font color="#7f7f7f">(고립성)</font>
		- 동시에 실행되는 여러 트랜잭션이 서로 간섭하지 않도록 보장해야 한다.
		- 하나의 트랜잭션이 완료될 때까지 다른 트랜잭션에서 해당 작업의 결과를 볼 수 없어야 한다.
	- Durability<font color="#7f7f7f">(지속성)</font>
		- 트랜잭션이 성공적으로 완료된 경우, 그 결과는 영구적으로 저장되어야 한다.
- 예를 들어, <font color="#c3d69b">은행 이체 작업을 하나의 트랜잭션으로 볼 수 있다</font>. 이체 과정에서 돈을 출금하는 작업과 입금하는 작업이 원자성을 유지하여 둘 다 성공하거나 실패해야 한다. 만약 이 중 하나만 성공하고 다른 하나가 실패하면, 잘못된 데이터 상태가 발생할 수 있다.
- 트랜잭션은 데이터베이스 관리 시스템<font color="#7f7f7f">(DBMS)</font>에서 지원되며, 명시적으로 <font color="#fac08f">시작, Commit, Rollback하는 등의 작업을 수행</font>할 수 있다.



# 1. 경량 컨테이너 Lightweight Container
EJB<font color="#7f7f7f">(Enterprise JavaBeans)</font>보다 가볍고 빠른 자체적인 컨테이너 제공
# 2. 의존성 주입 Dependency Injection
객체간의 의존성을 프레임워크가 직접 주입하는 방식으로 작동
# 3. AOP 지원 Aspect-Oriented Programming
관심사의 분리를 통해 횡단 관심사<font color="#7f7f7f">(Cross-cutting Concerns)</font>를 모듈화 가능
로깅, 트랜잭션 관리 등을 별도의 모듈로 분리하여 적용 가능
# 4. 트랜잭션 관리
선언적 트랜잭션 관리를 지원하며 XML이나 어노테이션을 사용하여 트랜잭션을 정의 가능
트랜잭션 관련 로직을 명시하지 않고도 트랜잭션을 적용 가능
## 4.1. 트랜잭션이란?
Transaction.
데이터베이스에서 하나 이상의 작업을 수행하는 논리적인 작업 단위
ACID 원칙을 준수한다.
- Atomicity<font color="#7f7f7f">(원자성)</font>
	- 모든 작업이 성공적으로 완료되거나 아무런 작업도 수행되지 않은 상태로 유지되어야 한다.
	- 하나의 작업이라도 실패하면 전체 트랜잭션이 롤백되어야 한다.
- Consistency<font color="#7f7f7f">(일관성)</font>
	- 시작되기 전과 끝난 후의 데이터베이스는 일관된 상태를 유지해야 한다.
- Isolation<font color="#7f7f7f">(고립성)</font>
	- 동시에 실행되는 여러 트랜잭션이 서로 간섭하지 않도록 보장해야 한다.
	- 하나의 트랜잭션이 완료될 때까지 다른 트랜잭션에서 해당 작업의 결과를 볼 수 없어야 한다.
- Durability<font color="#7f7f7f">(지속성)</font>
	- 트랜잭션이 성공적으로 완료된 경우, 그 결과는 영구적으로 저장되어야 한다.

예를 들어, <font color="#c3d69b">은행 이체 작업을 하나의 트랜잭션으로 볼 수 있다</font>. 이체 과정에서 돈을 출금하는 작업과 입금하는 작업이 원자성을 유지하여 둘 다 성공하거나 실패해야 한다. 만약 이 중 하나만 성공하고 다른 하나가 실패하면, 잘못된 데이터 상태가 발생할 수 있다.

트랜잭션은 데이터베이스 관리 시스템<font color="#7f7f7f">(DBMS)</font>에서 지원되며, 명시적으로 시작, Commit, Rollback하는 등의 작업을 수행할 수 있다.
# 5. MVC 웹 프레임워크
Model-View-Controller 아키텍쳐 제공
# 6. 데이터 액세스 지원
JDBC, ORM<font color="#7f7f7f">(Object-Relational Mapping)</font> 프레임워크<font color="#7f7f7f">(예 : Hibernate)</font>, JPA<font color="#7f7f7f">(Java Persistence API)</font> 등을 통해 데이터베이스와의 통합을 지원
# 7. 스프링 부트 Spring Boot
스프링 기반의 애플리케이션을 개발하고 설정하는데 사용되는 도구
내장된 Tomcat 서버 등의 설정을 제공하여 별도의 설정 없이 간단하게 실행 및 배포 가능
## 7.1. 빠른 개발 및 간소화된 설정
간단한 설정과 자동 구성을 통해 빠르게 개발할 수 있도록 도와준다.
## 7.2. 내장된 서버 지원
Tomcat, Jetty, Undertow 등을 내장하고 있어 별도의 웹 서버를 설치하거나 구성할 필요가 없다.
이를 통해 배포와 실행이 간단해지고 빠르다.
## 7.3. 자동 의존성 관리
의존성 버전을 자동으로 관리해주어 버전 충돌 문제를 줄여주고, 개발자가 수동으로 의존성을 관리하는 부담을 덜어준다.
## 7.4. 자동 구성과 스타터
클래스패스 상에 존재하는 라이브러리나 프레임워크에 따라 자동으로 애플리케이션을 구성해주는 자동 구성 기능을 제공한다.
스타터 패키지를 통해 특정한 작업이나 도메인에 필요한 의존성을 미리 정의해두어 사용자가 추가 할 수 있다.
## 7.5. 외부 구성
외부에서 설정을 도르할 수 있는 기능을 제공한다.
다양한 소스에서 설정값을 가져오고 환경변수, 프로퍼티 파일, YAML 파일 등으로 설정을 관리할 수 있다.
## 7.6. 스프링 액추에이터
Actuator는 애플리케이션의 상태를 모니터링하고 관리하는 기능을 제공한다.
`/actuator` 엔드포인트를 통해 애플리케이션의 여러 측면에 대한 정보를 확인 가능하다.
## 7.7. 통합 테스트 용이성
## 7.8. 보안 및 안정성
## 7.9. 풍부한 생태계
# 8. IoC Inversion of Control
제어의 역전 개념을 통해 객체의 생명 주기와 의존성 관리를 프레임워크에게 맡김
# Spring Framework와 Spring Boot
## 요약

| 구분 | Spring Framework | Spring Boot |
| :--: | :--: | :--: |
| 설정 | XML or JavaConfig<br>설정 직접 구성 | 자동 구성 제공 |
| 내장 서버 지원 | Tomcat 등 외부 서버 | Tomcat, Jetty, Undertow 등<br>기본적으로 제공 |
| 의존성 관리 | 수동 관리 | 자동화<br>스타터 패키지 제공 |
| Auto-Configurations | 수동 등록 | 자동 등록 |
| 외부 구성 | 추가 구성 | Actuator 모듈 간편화 |
## 상세
- 설정의 간소화
	- Spring Framework는 XML 또는 JavaConfig와 같이 설정이 상세하게 이루어져 있다. DB 연결, Transaction 설정, Bean 정의 등을 직접 구성해야 한다.
	- Spring Boot는 Application을 빠르게 시작할 수 있도록 자동 구성을 제공한다. 따라서 별다른 설정 없이도 동작할 수 있도록 설계되어 있다.
- 내장 서버의 지원
	- Spring Framework는 서버를 선택하고 설정해야 한다. Tomcat과 같은 외부 서버를 사용해야 한다.
	- Spring Boot는 내장 서버<font color="#7f7f7f">(예 : Tomcat, Jetty, Undertow)</font>를 기본적으로 제공하며, 별도의 서버 설정이 필요 없이 단일한 JAR 파일로 Application을 실행할 수 있다.
- 의존성 관리
	- Spring Framework는 각 라이브러리나 프레임워크의 버전을 수동으로 관리해야 한다.
	- Spring Boot는 의존성 관리를 자동화하고 버전 충돌을 최소화하는 데 도움이 되는 스타터 패키지를 제공한다.
- 매트릭, 상태 확인 및 외부 구성
	- Spring Framework는 항상 추가적인 구성이 필요하다.
	- Spring Boot는 Application의 매트릭, 상태 확인, 외부 구성 등을 위한 기능을 제공한다. Actuartor 모듈을 통해 이런 기능을 사용할 수 있다.
# STS
Spring Tool Suite
## Spring Legacy Project
Spring Framework의 Project Template이다. 전통적인 XML 기반의 설정 방식을 사용하며, 주로 Spring 3.x 이전의 버전에서 사용된다. 여러 설정 파일들이 XML 형식으로 작성되고, 주로 <font color="#c3d69b">web.xml과 같은 전통적인</font> Web Application 구조를 따르게 된다.
## Spring MVC Project
주로 Spring의 Mode-View-Controller Archtecture에 기반한 Web Application을 개발할 때 사용되는 Project Template이다. 주로 <font color="#fac08f">Annotation을 사용</font>하여 설정하고, <font color="#fac08f">Java 기반의 설정 파일을 선호</font>한다.