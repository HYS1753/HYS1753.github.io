---
author_profile: true
date: 2022-01-01
title: "SpringBoot 1 (프로젝트 생성)"
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

# SpringBoot 1 (프로젝트 생성)

---

## 0. 실습 Implementation

```
JAVA : 11

Template Engine : Thymeleaf(JSP 대신 HTML5 기반)
Configuration : Java Config (기존 XML 대체)
DataBase : PostgreSQL
Persistence framework : Mybatis 
```

## 1. 프로젝트 생성

- STS에서 File -> New -> Other -> SpringStarterProject 클릭
- Name : SpringBoot
- Type : Gradle
- Java Version : 11
- Packaging : war
- Dependencies : Spring Boot DevTools, Lombok, Spring Configuration Processor, Spring Data JPA, MyBatis Framework, PostgreSQL Driver, Thymeleaf, Spring Web

![Spring Starter Project 생성](/assets/images/springBoot1.PNG){: .align-center}

![Spring Starter Project 생성2](/assets/images/springBoot2.PNG){: .align-center}

- STS 가 업데이트 됨에 따라 HTML, CSS 등이 기본항목이 아니게 되는 문제점 있음
  - Help -> Install New Software -> sts 검색 -> Spring Tool Suite 4 - https://download.springsource.com/release/TOOLS/sts4/update/latest 선택 -> 가장 하단의 Web, XML, java EE, and OSGI Enterprise Development 체크 -> install -> STS Reboot
  - 설치 완료 후 편의를 위해 Window -> Perspective -> Customize Perspective -> Shortcuts 에서 (Web - HTML, CSS, XML-XML File ) 클릭 후 Apply

- Mybatis 플러그인 설치
  - Help -> Eclipse Marketplace -> mybatis 검색 -> MyBatipse 설치 (경고창이 떠도 Insatll Anyway 로 설치)
  - 위에서와 마찮가지로 shortcut에 추가해 준다.

- Thymeleaf 플러그인 설치
  - 타임리프는 마켓플레이스에서 지원 안하기 때문에 따로 설치 필요
  - Help -> isntall new software -> `http://www.thymeleaf.org/eclipse-plugin-update-site/` 검색후 설치

## 2. SpringBoot 프로젝트 구조

### 1. src/main/java 디렉터리
- Spring MVC와 마찬가지로 클래스, 인터페이스 등 자바 파일이 위치하는 디렉터리

### 2. Application.java
- springBoot를 실행시키는 SpringApplication.run()을 호출하는 main() 함수가 있다. 
- @SpringBootApplication 어노데이션
  - @EnableAutoConfiguration : 스프링부트의 다양한 설정들이 자동으로 완성시켜 주는 어노테이션
  - @ComponentScan : 기존의 xml 방식의 스프링 Bean의 등록 및 스캔을 위해 수동으로 ComponentScan을 여러개 선언하는 방식을 사용했으나 SpringBoot에서는 해당 어노테이션을 통해 Application.java 하위 디렉터리에 있는 모든 컴포넌트 클래스를 검색해, 스프링 어플리케이션 컨텍스트(IoC 컨테이너)에 Bean으로 등록한다.
  - @Configuration : 해당 어노테이션이 선언된 클래스는 자바기반 설정 파일로 인식된다. xml 설정을 대신한다.

### 3. src/main/resources 디렉터리
- templates 디렉터리: SpringMVC는 JSP를 사용하고, 디렉터리 위치는 웹 디렉터리에 해당하는 /src/main/webapp 안에 존재했었다. 하지만, 이러한 방식은 war로 묶였을 때만 정적 리소스를 정상적으로 사용할 수 있어 Boot에서는 해당 디렉터리에서 화면과 관련된 파일을 관리한다. 
- static 디렉터리 : css, font, images, plugin, script 등의 정적 리소스 파일이 위치하는 디렉터리
- application.properties : 웹 어플리케이션을 실행하면서 자동으로 로딩되는 파일으로 WAS의 정보, DB 정보등의 설정을 기존 SpringMVC에서는 XML로 관리하였지만 Boot에서는 properties 파일에 key-value 형식으로 저정해 처리할 수 있다.
  
### 4. src/test/java 디렉터리
- 각각의 개발단계에 맞는 단위테스트를 진행하는것.
- 기존의 SpringMVC와달리 곧바로 테스트가 가능하다.

### 5. build.gradle
- pom.xml을 사용해 dependencies를 관리하는 Maven 방식이 아닌 Gradle을 사용하는 방식으로 
- 라이르버릴 버전 문제, 충돌, 종속 문제 등에서 비교적 좋다. 또한 간결하다는 장점이 있다.

## 3. MVC 패턴
- SpringBoot도 기존 레거시 프로젝트인 SpringMVC와 마찮가지로 MVC 패턴을 사용해 개발한다.
- 이외에는 Hexagonal architecture 등의 패턴을 사용해 개발할 수도 있다.
  - Model : 데이터를 처리하는 영역으로, 비지니스 로직을 처리하는 영역이다. 해당 영역은 DB와 통신하고 사용자가 원하는 데이터를 가공하는 역할을 한다.
  - View : 사용자가 보는 화면을 의미하며 HTML과 Thymeleaf 등을 이용해 화명을 처리한다.(사용자 = 화면 = 뷰)
  - Controller : 모델과 뷰 상의 다리역할을 하는 영역으로 사용자의 요청에 따라 어떠한 로직을 처리하고 처리한 결과를 반환해 주는 역할을 한다.