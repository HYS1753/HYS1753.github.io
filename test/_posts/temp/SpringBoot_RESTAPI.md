---
author_profile: true
date: 2021-08-20
title: "SpringBoot - REST API Ⅰ"
categories: 
    - Spring
tag: 
    - Java
    - Spring
    - SpringBoot
    - Hexagonal Architecture
    - REST API

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# SpringBoot - REST API Ⅰ

---

## 1. Spring 개념

### Spring

- 오픈소스 경량 프레임워크
- 자바 개발자가 간단하고 안정적이며 확장 가능한 엔트프라이즈 애플리케이션을 빌드 할 수 있도록 함
- JDBC, JSP, Java servlet 등 기존 Java 프레임워크 및 API에 비해 웹 어플리케이션 개발이 간단해짐
- AOP, POJO, DI 같은 새로운 기술을 사용하여 엔터프라이즈 급 어플리케이션 개발 가능 

### Spring MVC

- Spring은 확장 가능한 어플리케이션을 만드는데 널리 사용되는 Spring MVC 프레임 워크를 제공한다.
- Spring MVC 프레임워크는 모델 뷰 컨트롤러라는 모듈의 분리를 가능하게 하고 어플리케이션 통합을 원할하게 한다.
- 개발자는 일반 Java 클래스를 사용하여 복잡한 웹 어플리케이션 제작 가능
- 모델 객체는 Map을 사용하여 뷰와 컨트롤러 간에 전달

### Spring Boot
- Spring Boot는 기존의 스프링 프레임 워크 위에 구축 (스프링 프레임 워크 기반)
- 스프링의 모든 기능을 제공하면서도 스프링보다 사용하기 쉬움
- Spring Boot는 마이크로 서비스 기반 프레임 워크이며 매우 짧은 시간에 프로덕션 준비 애플리케이션을 만듦
- Spring Boot에서는 모든 것이 자동으로 구성됨
- 특정 기능을 활용하기 위해 적절한 구성을 사용하기 만
- Spring Boot는 REST API를 개발하려는 경우 매우 유용
- Spring Boot는 프로젝트를 war 또는 jar 파일로 변환하는 기능을 제공
- Tomcat의 인스턴스는 클라우드에서도 실행 가능
- 자주 사용하는 라이브러리가 미리 조합되어있음
- 복잡한 설정이 자동 처리됨
- 내장서버를 포함 (톰캣) 서버를 추가로 설치하지 않아도 바로 개발 가능
- 톰캣, 제티와 같은 WAS에 배포하지 않고도 실행할 수 있는 jar 파일로 웹어플리케이션 개발 가능

#### Spring MVC와 Spring Boot 간의 차이점 
- web.xml의 유무
  - Spring MVC
Servlet Container(WAS)로 들어오는 Request를 Spring에 정의한 각 Controller로 매핑하기 위해 Dispatcher Servlet이 사용되는데 이를 위해 개발자는 Servelt Container로 Dispatcher Servlet에 관한 정보를 web.xml에 정의해주어야 한다.

  - Spring Boot
Spring Boot는 MVC 방식과 달리 자동으로 Dispatcher Servlet을 생성해 주기 때문에 개발자가 따로 설정할 필요가 없다.

- DI(의존성 주입)방식의 변화
  - Spring Boot는 Annotation을 통해 Bean을 설정하고 주입 받는 것을 표준으로 삼는다.
  - 따라서 기존에 MVC 방식에서 xml 파일을 통해 Bean을 설정했다면, Boot는 Java Config를 통해 Bean을 설정해 준다. 
  - 이러한 DI의 변화의 장점으로는 다음과 같다.
    - Bean으로 등록되는 설정들을 파악하는데 효과적인 가독성을 제공한다.
    - 기존 JAVA와 동일한 언어로 설정(BEAN)을 생성하기 때문에 수정 및 작성에 용이하다.
    - XML은 컴파일러가 오류를 잡아주지 못해 원인 찾기 어려우나, JAVA Config로 작성 시 컴파일러를 통해 수정에 용이하다.


## 2. Hexagonal Architecture

기존의 Layered Architecture(Ex. MVC Pattern)에서는 Business Logic, Validation, Database Access 등 로직들이 Controller 또는 Service에 몰아서 작성되는 경우가 많아 소스코드가 길어지고, 리펙토링이 어렵거나 불가능한 상황이 발생할 수도 있다.

이러한 문제를 해결하기 위한 방법 중 한가지가 Hexagonal Achitecture 이며 외부기술과 실제 비지니스 로직의 분리, 각 도메인 별 비지니스 로직의 분리를 통해 프로그램 소스간의 의존성 및 결합도를 낮추고 같은 종류의 프로그램 소스 간의 응집도를 높일 수 있다.

- MVC vs Hexagonal Architecture
  - MVC
    - Actor ↔ Controller ↔ Service ↔ DAO ↔ DB
  - Hexagonal Architecture
    - Actor ↔ Controller(Adapter) ↔ inPort ↔ Service ↔ outPort ↔ Persistence Adaper ↔ DB


## 3. REST API 작성(MVC 기반)

1. STS 4 실행

![STS Main](/assets/images/SpringBoot1.PNG){: .align-center}

2. Spring Starter 프로젝트 생성
   1. File > New > Other... > Spring Starter Project 선택
   ![Select Spring Starter Project](/assets/images/SpringBoot2.PNG){: .align-center}
   2. Project Setting
   ![Project Setting](/assets/images/SpringBoot3.PNG){: .align-center}
   3. Project Dependency 에서 Spring web 만 설정
3. 

