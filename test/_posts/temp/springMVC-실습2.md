---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 2"
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

# Spring MVC + Gradle Project 2 (Logger 사용)

---

## 0. logging 정의

- 자바에서는 로깅을 위해 다양한 로깅 프레임워크를 사용해 로그를 남긴다. 
- 이러한 기능은 웹 서비스 동작 과정에서 일어나는 인령의 모든 기록을 남겨 침해사고 발생 및 이상징후 정검, 데이터 분석 등을 위해 필수적인 기능이다.
- 1. Logback : Log4j를 개발한 Ceki Glucu가 기존에 사용되던 Log4j를 더 발전 시킨 것
- 2. java.util.logging : JDK 1.4 부터 포함된 로깅 API
- 3. log4j2 : 아파치 재단에서 제공하는 로깅 API
- 4. Apache Commons logging

- **SLF4J(Simple Logging Facade For Java)**
  - SLF4J 는 Facade(퍼사드) 디자인 패턴에 따라 만들어진 클래스
  - SLF4J 가 실제 사용하게 될 구현 클래스( 위에서 말한 로깅 프레임워크 ) 사용자가 선정
  - Facade 디자인 패턴을 사용하기 덕분에, 다양한 구현체를 하나의 통일된 방식으로 사용 가능
  - SLF4J는 로깅에 대한 추상 레이어를 제공하는 interface의 모음
  - 즉, SLF4J는 로깅 프레임 워크가 아닌 이를 쉽게 사용할 수 있또록 도와주는 Facade에 불과하다.
- SLF4J가 제공하는 3가지 모듈
  - SLF4J API : SLF4J를 사용하기 위한 인터페이스 제공, `slf4j-api-{version}.jar`를 통해 사용, 반드시 slf4j 바인딩을 하나만 써야 한다.
  - SLF4J Binding : SLF4J 인터페이스를 로깅 구현체와 연결하는 어뎁터 역할을 하는 라이브러리
  - SLF4J Bridging Modules : 다른 로깅 API로 Logger 호출을 할 때, SLF4J 인터페이스로 연결(redirect)하여 SLF4J API가 대신 Logger 처리를 할 수 있도록 하는 어뎁터 역할의 라이브러리
- `다른 logging API -> Bridge(redirect) -> SLF4J API`

## 1. Dependencies 추가

log4j 1.2 버전대는 2021.12월에 취약점 발견으로 사용하지 말것을 권고하고 있다. 따라서 이번 프로젝트에서는 가장 성능이 좋고 보안성이 뛰어난 log4j2 를 사용해 log를 남겨 본다.

- build.gradle에 Dependency 추가
  - log4j를 사용하기 위해서는 log4j-api와 log4j-core를 의존성으로 가지고 있어야 하나 log4j-slf4j-impl은 이 두가지를 가지고 있어 따로 추가할 필요 없다.
- log4j-slf4j-impl:2.17.1의 의존성
    - Compile Dependencies : log4j-api:2.17.1, slf4j-api:1.7.25, slf4j-ext:1.7.25(optional)
    - Runtime Dependencies : log4j-core:2.17.1

### **build.gradle**

```
// 의존성 추가 부분
dependencies {
    compile "javax.servlet:javax.servlet-api:4.0.1"
    compile "org.springframework:spring-context:5.3.10"
    compile "org.springframework:spring-webmvc:5.3.10"    
    // 추가
	"org.apache.logging.log4j:log4j-slf4j-impl:2.17.1"
}
```

## 2. log4j2 설정

- log4j2는 properties, json, yml, xml 등의 파일 확장자로 설정파일을 지정할 수 있다.
- log4j2가 설정파일을 찾는 메커니즘은 classpath를 기준으로 이루어진다.
- xml확장자를 기주능로 system property -> log4j2-test.xml -> log4j.xml 순으로 설정파일을 찾는다.
- 만약 설정파일을 찾지 못하면 defualt 설정이 적용되게 된다. 
- Configuration 태그 안에 log4j2의 설정정보를 작성할 수 있다.
- Configuration 태그의 status는 log4j2 내부적으로 어떻게 동작되는지를 볼 수 있는 로그 수준을 결정하는 속성으로 INFO로 하면 안보이지만, TRACE, DEBUG로 설정하게 되면 내부 로그를 볼 수 있다.
- Appender태그는 로그를 어떻게 출력할 것인지 결정하는 태그이다.
- loggers에서는 각 logger들에 대한 설정을 정의할 수 있다. 각 Logger들은 level, 경로, additivity 등의 속성을 지정하여 사용할 수 있다.


### pattern layout

- %c, %logger : 해당 로그를 쓰는 로거의 이름.
- %C, %class : 해당 로그를 요청한 클래스 이름
- %d, %date : 해당 로그가 발생한 시간
- %enc, %encode : 특정 언어에서의 출력을 위한 문자 인코딩
- %ex, %exception, %throwable : 예외 로그. 길이를 설정할 수 있음.
- %F, %file : 해당 로그가 발생한 클래스 파일명
- %l, %location : 해당 로그가 발생한 클래스명.메소드명(파일:라인)
- %L, %line : 해당 로그가 발생한 라인 번호
- %m, %msg, %message : 로그문에 전달된 메시지
- %n : 줄바꿈
- %p, %level : 로그 레벨
- %r, %relative : 로그 처리시간
- %t, %thread : 해당 로그가 발생한 스레드명
- %style{pattern}{ANSI style} : ANSI를 사용해 특정 패턴을 스타일링함
- %highlight{pattern}{style} : 로그 레벨명을 ANSI 색깔로 하이라이트


### Appenders
- Appender는 Log 메시지를 특정 위치에 전달해 주는 역할을 한다. 
- Appender는 layout을 통해 로그를 formmating하고, 어떤 방식으로 로그를 제공할지 결정한다.
- Appender의 종류
  - ConsoleAppender
  - RollingFileAppender
  - AsyncAppender
  - FileAppender
  - JDBCAppender
  - SMTP
- ConsoleAppender

    ```
    <Appenders>
        <Console name="Console_Appender" target="SYSTEM_OUT">
            <PatternLayout patterns="${layoutPattern}>
        </Console>
    </Appenders>
    ```

  - Console Appender는 말 그대로 Console에 system.out으로 로그를 출력하는 Appender이다.
  - 로그 formmating은 PatternLayout 방식으로 설정한다.
  - 속성은 다음과 같다.
    - layout: 출력 레이아웃 양식
    - name : Appender 이름
    - target : 출력 방식, 기본값은 SYSTEM_OUT, SYSTEM_ERR로 출력 가능
- RollingFileAppender
  - 

https://velog.io/@bread_dd/Log4j-2-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-%EA%B0%9C%EB%85%90

### **/src/resources/log4j2.xml**

