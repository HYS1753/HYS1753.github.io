---
author_profile: true
date: 2021-08-20
title: "Spring용어정리"
categories: 
    - Spring
tag: 
    - Java
    - Spring

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Spring용어 정리

---

## 1. Servlet

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

## 2. AOP(Aspect Oriented Programming)

AOP는 부가기능을 Aspect로 정의하여, 핵심기능에서 부가기능을 분리함으로써 핵심기능을 설계하고 구현할 때 객체지향적인 가치를 지킬 수 있도록 도와주는 개념.

- AOP 용어
  - Target :  핵심기능을 담고 있는 모듈로 타겟은 부가기능을 부여할 대상이 된다.
  - Advice : 타겟에 제공할 부가 기능을 담고 있는 모듈
  - Join Point : 어드바이스가 적용될 수 있는 위치, 타겟 객체가 구현한 인터페이스의 모든 메서드는 조인 포인트가 된다.
  - Pointcut : 어드바이스를 적용할 타겟의 메서드를 선별하는 정규 표현식,  extenstion으로 시작하고 매서드의 Signature를 비교하는 방법을 주로 이용
  - Aspect : AOP의 기본 모듈로 Advice + Pointcut 을 의미한다.
  - Advisor : Advice + Pointcut을 의미

- advice 종류
  - Around : 타겟 메서드 호출 이전 이후 모두 처리해야 할 필요가 있는 부가기능
  - before : 타겟 메서드 호출 이전 처리해야 할 필요성이 있는 부가기능 
  - after Returning : 타겟 메서드 호출 이후 처리해야 할 필요성이 있는 부가기능
  - After Throwing : 타겟 메서드가 예외를 발생시킨 후 처리해야 할 필요성이 있는 부가기능

## 3. DI(Dependency Injection)

DI란 스프링이 다른 프레임워크와 차별화 되어 제공하는 의존관계 주입 기능으로 객체를 직접 생성하는게 아니라 외부에서 생성한 후 주입시켜 주는 방식이다.
이를 통해 모듈간의 결합도가 낮아지고 유연성이 높아진다.

Spring에서는 객체를 Bean이라 부르며 프로젝트가 실행될 떄 사용자가 Bean으로 관리하는 객체들의 생성과 소멸에 관한 작업을 자동적으로 수행해 주는데 객체가 생성되는 곳을 스프링네서는 Bean 컨테이너라 부른다.

## 4. IoC(Inversion of Control)

제어권 역전 즉, 직접적으로 의존성(객체가 사용해야 할 객체, new등을 써서 만들어 쓰면 자기가 직접 만들어 쓰는것.)을 만들지 않고 외부에서 의존성을 가저오는 경우로 DI가 IoC의 일종이라 할 수 있다.

IoC 컨테이너가 관리하는 객체들을 Bean이라 부르며 스프링은 이러한 Bean의 의존성을 관리하고 객체를 만들어 주며, Bena으로 등록 및 관리한다.

- 기존의 객체 생성 방법
  - 객체 생성
  - 의존성 객체 생성(클래스 내부에서 생성)
  - 의존성 객체 메소드 호출

- 스프링의 객체 생성방법
  - 객체 생성
  - 의존성 객체 주입(스스로 클래스에서 만드는 것이 아닌 제어권을 스프링에게 위임해 스프링이 만들어 놓은 객체를 주입한다.)
  - 의존성 객체 메소드 호출


## 5. BEAN

Sping IoC 컨테이너가 관리하는 자바 객체를 빈(Bean)이라 부른다.
단순히 new를 통해 생성되는 객체는 Bean이 아니고, ApplicationContext.getBean()으로 얻을 수 있는 객체가 빈이다.
즉, Spring에서 빈은 ApplicationContext가 알고 있는 객체, 즉 ApplicationContext가 만들어서 그 안에 담고 있는 객체를 의미한다.

- Spirng IoC에 컨테이너 빈 등록 방법
  - Component Scanning
    - @ComponentScan 어노테이션과 @Componant 어노테이션을 사용해서 빈을 등록하도록 하는 방법
    - @ComponentScan 어노테이션은 어느 지점부터 컴포넌트를 찾으라고 알려주는 역할
    - @Component 어노테이션은 실제로 찾아서 빈으로 등록할 클래스 의미
    - Spring IoC 컨테이너가 IoC컨테이너를 만들고 그 안에 빈을 등록할 때 사용하는 인터페이스들을 라이프 사이클 콜백이라 한다.
    - 라이프 사이클 콜백에는 @Component 어노테이션을 찾아 해당 클래스들의 인스턴스를 생성해 빈으로 등록하는 작업을 수행한다. SpringBoot에서는 @SpringBootApplication 어노테이션 안에 해당 작업을 수행하는 @ComponentScan이 있다.
    - @ComponentScan 어노테이션이 붙어 있는 클래스는 해당 패키지부터 모든 하위 패키지의 클래스를 훑으며 @Component 어노테이션이 붙은 클래스를 빈으로 등록한다.
  - Bean 설정파일에 직접 빈을 등록
    - XML
      -  일반적인 bean 선언
      - `<bean id="testBean" class="com.ljw.TestBean" /> `
    	- id : spring container 에서 유일하게 식별할수 있는 이름
		- class : 해당 bean의 full path
      - Property bean 설정 방식
        ```
            <bean id="testServcie" class="con.ljw.TestService" >
                <property name="testDao">
                    <ref bean="implTestDao"/>
                </property>
	        </bean>
        ```
        - TestService class의 SetTestDAO 메소드를 호출하면서 인자값으로 ImplTestDAO 객체를 넘겨준다는 의미
    - JAVA config
      - 자바 설정 파일은 자바 클래스를 생성해서 작성할 수 있으며 일반적으로 xxxConfiguration과 같이 클래스 명을 설정한다.
      - 또한 해당 클래스에 @Configuration 어노테이션을 붙이고 안에 @Bean 어노테이션을 사용해 직접 빈을 정의한다.
      - 