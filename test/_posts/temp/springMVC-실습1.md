---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 1"
categories: 
    - Spring
tag: 
    - Java
    - Spring
    - SpringMVC

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Spring MVC + Gradle Project 1 (프로젝트 생성)

---

## 0. Implementation

- STS4 4.10.0
- jdk 11.0.2
- gradle 6.9.1
- Web Module : 3.0
- Tomcat v7.0
- Spring dependencies
  - javax.servlet:javax.servlet-api:4.0.1
  - org.springframework:spring-context:5.3.10
  - org.springframework:spring-webmvc:5.3.10


## 1. Gradle Project 생성

- File - New - Other... - gradle - Gradle Project
  
![New Gradle Project](/assets/images/springmvc1.PNG){: .align-center}

![New Gradle Project](/assets/images/springmvc2.PNG){: .align-center}

![New Gradle Project](/assets/images/springmvc3.PNG){: .align-center}

![New Gradle Project](/assets/images/springmvc4.PNG){: .align-center}

## 2. 프로젝트에 Dynamic Web Module 추가

- 프로젝트 우클릭 - Properties - Project Facet - Convert to faceted form - Dynamic Web Module 추가 - Apply - Apply and Close

![New Gradle Project](/assets/images/springmvc5.PNG){: .align-center}

- 추가 완료되면 src/main/ 디렉터리 아래 webapp 디렉터리 생성된 것을 확인할 수 있다.

![New Gradle Project](/assets/images/springmvc6.PNG){: .align-center}

## 3. 프로젝트 구조 정의

- 단순한 api 호출을 위한 프로젝트의 구성이며, 추후에 기능을 추가하거나 사용자의 편의대로 수정해도 된다.

![New Gradle Project](/assets/images/springmvc7.PNG){: .align-center}

## 4. build.gradle 설정

- 해당 Spring 프로젝트에 대한 정의 및 의존성 추가
- 아래 코드를 작성 및 저장 후 build.gradle 우클릭 - Gradle - Refresh Gradle Project 해 설정한 dependencies 를 프로젝트에 추가해 준다.

### **build.gradle**
```
// 사용할 레파지토리 설정
buildscript {
    repositories {
        mavenCentral()
    }
}

// 플러그인 설정
apply plugin: 'java'
apply plugin: 'eclipse-wtp'
apply plugin: 'war'

// 프로젝트 명
def projectName = "Gradle_SpringMVC"

// 이클립스의 프로젝트 facet 설정
// 해당 설정 없을 시 `Source folder is not a Java project.`와 같은 오류 발생 가능
eclipse {
    project.natures "org.springsource.ide.eclipse.gradle.core.nature"
    wtp {
        facet {
            facet name: 'jst.web', version: '3.0'
            facet name: 'jst.java', version: '11' 
        }
    }
}

// 개발 환경 설정 local, dev, prod, staging (gradle -Pprofile= )
final String DEFAULT_PROFILE = 'local'

allprojects {
    def srcDir = ["src/main/resources"]
    
    sourceSets {
        main {
            resources {
                srcDirs = srcDir
            }
        }
    }
}

// 해당 프로젝트에서 사용하는 Java Verstion 및 인코딩 형식
sourceCompatibility = 11
targetCompatibility = 11
version = 1.0
compileJava.options.encoding = 'UTF-8'


repositories {
    mavenCentral()
    flatDir {
        dirs 'lib'
    }
}

configurations {
    providedRuntime
}

// 의존성 추가 부분
dependencies {
    compile "javax.servlet:javax.servlet-api:4.0.1"
    compile "org.springframework:spring-context:5.3.10"
    compile "org.springframework:spring-webmvc:5.3.10"    
}

war {
    archiveName = "${projectName}.war"
}
```

- 추가 완료되면 프로젝트 우클릭 - properties - java Build Path - Libraries - Classpath - Project and External Dependencies 에 보면 추가한 jar 파일을 확인할 수 있다.

![New Gradle Project](/assets/images/springmvc8.PNG){: .align-center}

## 5. web.xml 설정

- WAS 에서 Spring 프로젝트를 실행시키면 webapp/WEB-INF/web.xml 을 가장 먼저 참조하게 되며, 서블릿을 생성하게 된다.

### **Servlet**

자바를 이용하여 웹을 만들기 위해 필요한 기술. 즉, 클라이언트가 어떠한 요청을 하면 그에 대한 결과를 전달해 주는 자바 프로그램이다.

- 특징
  - 클라이언트의 요청에 대해 동적으로 작동하는 웹 어플리케이션 컴포넌트
  - html을 사용하여 요청에 응답한다.
  - java Thread를 이용하여 동작한다.
  - MVC 패턴에서 Controller로 이용된다.
  - HTTP 프로토콜 서비스를 지원하는 javax.servlet.http.HttpServlet 클래스를 상속받는다.
  - UDP보다 속도 측면에서 느리다.
  - HTML 변경 시 Servlet을 재 컴파일 해야하는 단점이 있다 .
  - 동적인 페이지를 제공한다.


- Servlet Container
  - 서블릿 컨테이너는 서블릿들의 생성, 실행, 파괴를 담당한다.
  - 대표적인 서블릿 컨테이너로는 Tomcat이 있다.
  - 동작 방식
    - 클라이언트가 URL을 입력하면 HTTP Request가 Servlet Container로 전송된다.
    - 요청을 받은 Servlet Container는 HttpServletRequest, HttpServletResponse 객체를 생성한다.
    - web.xml에 정의한 Servlet 정의에 따라 요청된 URL이 어느 서블릿에 대한 요청인지 찾는다.
    - 해당 서블릿에서 service 메서드를 호출한 후 클라이언트의 GET, POST 여부에 따라 doGet(), doPost()를 호출한다.
    - doGet(), doPost() 메서드는 동적 페이지를 생성한 후 HttpServletResponse 객체에 응답을 보낸다.
    - 응답이 끝나면 HttpServletRequest, HttpServletResponse 객체를 소멸시킨다.

    ```       
    Client  ↔   Http Request/Response   ↔   Container
                                                ↓   
                                            객체 생성          ←      doGet(), doPost()
                                        (HttpServletRequest)                ↑
                                        (HttpServletResponse)           Service()
                                                ↓                           ↑
                                            서블릿 분석         →       찾은 서블릿
                                            (Web.xml)
    ```

### **/src/main/webapp/WEB-INF/web.xml**
```
<?xml version="1.0" encoding="UTF-8"?>

<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns="http://Java.sun.com/xml/ns/javaee" 
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" 
	version="3.0">
	
	<!-- web.xml의 title 및 주석 보통은 프로젝트 명으로 기술한다. -->
	<display-name>Gralde_SpringMVC</display-name>
	
	<!-- Servlet init parameter는 그 매개변수가 선언된 서블릿에서만 사용가능 -->
	<!-- 여러 서블릿이 공통의 환경정보를 사용하기 위해선 context-param을 사용한다. -->
	<!-- classpath 위치는 프로젝트명 마우스 우클릭->Properties->Java Build Path->Source에서 확인 가능 -->
	<!-- 여기서는 Gralde_SpringMVC/src/main/java, Gralde_SpringMVC/src/main/resources 이다. -->
	<context-param>
		<param-name>webAppRootKey</param-name>
		<param-value>api</param-value>
	</context-param>
	<!-- DBS/src/main/resources/config/spring/아래 applicationConext로 시작하는 xml 추가 -->
	<!-- spring.profiles.active(dev, local, prod, staging)은 톰켓, 레진과 같은 WAS에서 spring.profile.active=local 과 같이 설정한대로 해당 디렉터리 아래 xml 추가. 전역에서 사용할 빈 설정-->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath*:/config/spring/${spring.profiles.active}/*.xml
    	</param-value>
  	</context-param>
  	<context-param>
  		<param-name>defaultHtmlEscape</param-name>
  		<param-value>true</param-value>
  	</context-param>
  	
  	<!-- servlet : spring에서는 DispatcherServlet이 모든 요청을 받고, 요청의 URL과 매핑하는 Controller에 요청을 위임한다. -->
  	<!-- 서블릿(Servlet)이란 보통 자바에서 동적 웹 프로젝트를 개발할 때 사용자의 요청과 응답을 처리해 주는 역할을 한다. -->
  	<!-- dispatcher servlet은 httpServlet을 상속받는 실제 서블릿이다. -->
  	<!-- init-param의 contextConfigLocation로 정의한 경로에 있는 xml 파일을 기준으로 객체를 생성한다. -->
  	<servlet>
  		<servlet-name>dispatcherServlet</servlet-name>
  		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  		<init-param>
  			<param-name>contextConfigLocation</param-name>
  			<param-value>/WEB-INF/dispatcher-servlet.xml</param-value>	<!-- 해당 서블릿에서 추가할 빈 설정 -->
  		</init-param>
  		<load-on-startup>1</load-on-startup>
  	</servlet>
  	<!-- dispatcherServlet이 처리할 URL을 매핑해 준다. 여기서는 '/' -->
  	<servlet-mapping>
  		<servlet-name>dispatcherServlet</servlet-name>
  		<url-pattern>/</url-pattern>
  	</servlet-mapping>
  	<servlet>
  		<servlet-name>rest-dispatcher</servlet-name>
  		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  		<init-param>
  			<param-name>throwExceptionIfNoHandlerFound</param-name>
  			<param-value>true</param-value>
  		</init-param>
  	</servlet>
  	
  	<!-- filter : 모든 서블릿 앞단에서 공통적으로 처리해야 할 변경 정보 -->
  	<!-- url-pattern의 /* 은 모든 요청에 대한 것 -->
  	<filter>
  		<filter-name>encodingFilter</filter-name>
  		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  		<init-param>
  			<param-name>encoding</param-name>
  			<param-value>UTF-8</param-value>
  		</init-param>
  		<init-param>
  			<param-name>forceEncoding</param-name>
  			<param-value>true</param-value>
  		</init-param>
  	</filter>
  	<filter-mapping>
  		<filter-name>encodingFilter</filter-name>
  		<url-pattern>/*</url-pattern>
  	</filter-mapping>
  	
  	
  	<!-- security-constraint : HTTP 메소드 제한 -->
  	<!-- HTTP 규약에 따라 웹 서버들은 GET, POST, HEAD, OPTIONS, DELETE, TRACE의 7개 메소드를 가진다. -->
  	<security-constraint>
  		<display-name>Forbidden</display-name>
  		<web-resource-collection>
  			<web-resource-name>Forbidden</web-resource-name>
  			<url-pattern>/*</url-pattern>
  			<http-method>HEAD</http-method>
  			<http-method>TRACE</http-method>
  			<http-method>OPTIONS</http-method>
  		</web-resource-collection>
  		<auth-constraint>
  			<role-name></role-name>
  		</auth-constraint>
  	</security-constraint>
  	
  	
  	<!-- Controller가 공유하는 BEAN(Dao, DataSource, Service)들을 포함하는 Spring 컨테이너 생성. -->
  	<!-- dispatcher 서블릿을 통해 생성된 Bean은 ContextLoaderListener에 의해 생성된 Bean을 참조할 수 있다. -->
  	<listener>
    	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  	</listener>
  	
</web-app>
```

## 6. dispatcher-servlet 설정

- web.xml에서 정의한 dispatcher-servlet(이름은 변경 가능)에 대한 정의로 Spring에서 사용할 객체(bean) 등록, 생성 등을 정의한다.

### **/src/main/webapp/WEB-INF/dispatcher-servlet.xml**
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:util="http://www.springframework.org/schema/util"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.3.xsd">
	
	<!-- Annotation-driven -->
	<!-- Spring MVC 컴포넌트들을 디폴트 설정을 통해 활성화 한다. -->	
	<!-- Spring MVC @Controller 에 요청을 보내기 위해 필요한 HandlerMapping과 HandlerAdapter를 Bean으로 등록한다. -->
	<!-- HandlerMapping : HTTP 요청 정보를 이용해서 컨트롤러를 찾아주는 기능 -->
	<!-- HandlerAdapter : HandlerMapping을 통해 찾은 컨트롤러를 직접 실행하는 기능을 수행한다. -->
	<mvc:annotation-driven />
	
	<!-- context:component-scan -->
	<!-- 특정 패키지 내의 클래스를 스캔하고 Annotation(@Component, @Controller, @Service, @Repository 등)을 확인 후 Bean 인스턴스로 생성한다. -->
	<!-- 이를 이용하면 @Autowired와 @Qualifier Annotation을 인식할 수 있다. -->
	<!-- base-package 는 설정한 디렉터리를 포함한 하위 전체 디렉터리를 스캔하겠다는 의미. /src/main/java/ 이후의 디렉터리 경로를 적어준다.-->
	<context:component-scan base-package="Gradle_SpringMVC" />
</beans>
```

## 7. contextConfigLocation 설정

- web.xml에서 정의한 contextConfigLocation 을 정의한다.
- 위에서 정의한 경로는 src/main/resources/config/spring/${spring.profiles.active}/*.xml 이며 해당 디렉터리에 dispatcher-servlet에 정의할 bean을 정의한다.
- 즉, dispathcer-servlet.xml은 프로젝트 전체에 대한 bean 설정, contextConfigLocation은 Spring이 실행되는 환경에 따른 Bean 설정이다.
- 이번 api 설정에서는 추가로 필요하지 않으나 web.xml에 정의한 대로 서블릿이 생성되면 오류 발생하는 것을 방지하기 위해 아무런 .xml 파일을 생성해 다음을 추가해 둔다.

### **temp.xml**
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">
</beans>
```

## 8. MainController 설정

- 위에서 설정하고 생성된 Servlet이 서비스 별로 기능을 수행하기 위해 구분하는 Controller를 생성한다.
- 이클립스에서는 ctrl + space로 자동완성 기능을 사용할 수 있다.

### **MainController.java**
```
package Gradle_SpringMVC.api.main.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MainController {
	
	@RequestMapping(value="/", method= RequestMethod.GET)
	public void mainpage() {
		System.out.println("This is main page!");
	}
	
	@RequestMapping(value="/test", method= RequestMethod.GET)
	public void test() {
		System.out.println("Hello!");
	}

}
```
  

## 9. Tomocat 연동

- STS 네비게이션바의 Window - Show view - other... - Servers 를 추가하면 다음과 같은 설정화면을 볼 수 있다.

![New Gradle Project](/assets/images/springmvc9.PNG){: .align-center}

- 여기서 `No servers are available. Click this link to create a new server...`를 클릭한다.
- Tomcat v7.0 Server 클릭 후 Next

![New Gradle Project](/assets/images/springmvc10.PNG){: .align-center}

- Available 쪽에 있는 Gradle_SpringMVC 를 Configured쪽으로 옮기고 Finish

![New Gradle Project](/assets/images/springmvc11.PNG){: .align-center}

- 만들어진 서버를 더블클릭해 추가 설정을 진행한다.
  - Overview - General Infomation - Open Launch configureation - Arguments - VM arguments 부분에 `-Dspring.profiles.active="local"`을 추가해 준다.
  - 하단의 Modules의 Web Modules에서 설정된 path를 (기본값 : /Gradle_SpringMVC) Edit를 통해 `/`으로 변경한다.

- Server 우클릭 Start

## 10. 결과

- 서버를 실행시키면 `/` 경로로 처음 실행하기 때문에 MainController에서 정의한 "This is main page!"가 출력되는 것을 확인할 수 있다.
- 또한, 인터넷 브라우저를 통해 `localhost:8080/test`로 접속하면 다음과 같이 Hello가 출력되는 것을 확인할 수 있다.

![New Gradle Project](/assets/images/springmvc12.PNG){: .align-center}