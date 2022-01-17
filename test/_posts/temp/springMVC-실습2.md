---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 2 (Logger 사용)"
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

# Spring MVC + Gradle Project 2 (Log4j2)

---

## 0. logging 정의

- 자바에서는 로깅을 위해 다양한 로깅 프레임워크를 사용해 로그를 남긴다. 
- 이러한 기능은 웹 서비스 동작 과정에서 일어나는 인령의 모든 기록을 남겨 침해사고 발생 및 이상징후 정검, 데이터 분석 등을 위해 필수적인 기능이다.
- 1. Logback : Log4j를 개발한 Ceki Glucu가 기존에 사용되던 Log4j를 더 발전 시킨 것
- 2. java.util.logging : JDK 1.4 부터 포함된 로깅 API
- 3. log4j2 : 아파치 재단에서 제공하는 로깅 API
- 4. Apache Commons logging

## 1. log4j2
- log4j2는 properties, json, yml, xml 등의 파일 확장자로 설정파일을 지정할 수 있다.
- log4j2가 설정파일을 찾는 메커니즘은 classpath를 기준으로 이루어진다.
- xml확장자를 기주능로 system property -> log4j2-test.xml -> log4j.xml 순으로 설정파일을 찾는다.
- 만약 설정파일을 찾지 못하면 defualt 설정이 적용되게 된다. 
- Configuration 태그 안에 log4j2의 설정정보를 작성할 수 있다.
- Configuration 태그의 status는 log4j2 내부적으로 어떻게 동작되는지를 볼 수 있는 로그 수준을 결정하는 속성으로 INFO로 하면 안보이지만, TRACE, DEBUG로 설정하게 되면 내부 로그를 볼 수 있다.
- Appender태그는 로그를 어떻게 출력할 것인지 결정하는 태그이다.
- loggers에서는 각 logger들에 대한 설정을 정의할 수 있다. 각 Logger들은 level, 경로, additivity 등의 속성을 지정하여 사용할 수 있다.


### **1-1. pattern layout**

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


### **1-2. Appenders**

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

  ```
  <RollingFile name="Rolling_File_Appender" fileName="logs/${logName}.log filePattern="logs/${logName}_%d{yyyyMMdd}_%i.log.gz">
    <PatternLayout pattern="${layoutPattern}/>
    <Policies>
      <SizeBasedTriggeringPolicy size="200KB"/>
      <TimeBasedTriggeringPolicy interval="1"/>
      ...
    </Policy>
    <DefaultRolloverStrategy max="10" fileIndex="min"/>
  </RollingFile>

  -> 로그파일이 200KB이상이 되는 시점과 하루마다 해당 패턴을 통해 로그파일이 만들어진다.
  ```

  - 로그가 한 파일에 계속 저장된다면, 파일이 너무 커져서 실행이 불가능해질 수동 있고, 파일에 문제가 생기면 로그를 전부 날려버릴수 있다는 단점이 있다.
  - RollingFileAppender는 이런 문제를 해결하기 위한 Appender 이다.
  - 파일에 로그를 기록하고 특정 기준에 따라 압축하여 저장하는 방식의 Appender이다.
  - policy
    - Policy는 FIie Rolling Up 기준이며 하나의 RollingFileAppender에 여러 Policy 적용 가능하다.
    - OnStartupTriggeringPolicy : jvm 시작에 따른 트리거
    - TimeBasedTriggeringPolocy : 시간에 따른 트리거
      - interval을 통해 트리거링 되는 시점을 결정하는데 이는 파일 패턴의 최소단위로 결정된다. 
      - 즉, yyyyMMdd로 날짜가 최소단위면 1일씩, 초단위까지 표현되면 1초마다로 생성된다.
    - SizeBasedTriggeringPolicy : 파일 크기에 따른 트리거
    - CronTriggeringPolicy: Cron Expression(시간에 관한 표현)에 따른 트리거
  - DefaultRolloverStrategy
    - 파일 패턴의 %d{yyyyMMdd} 와 %i 같은 값들을 어떻게 설정할지에 대한 부분
    - Strategy 속성
      - fileIndex: max로 설정 시 높은 index가 더 최신 파일이 된다. min으로 설정 시 작은 index가 최신 파일이 된다. 이는 기존의 파일들을 rename하는 방식으로 동작하게 된다.
      - min: counter의 최소값. 기본값은 1
      - max: counter의 최대값. 만약 최대값에 도달하면 오래된 파일 삭제 한다. 기본값은 7
      - compressionLevel : 0~9까지의 정수값으로 0은 압축하지 않으며 1은 최고속도 압축, 9는 최대 압축이다.
      - tempCompressedFilePattern : 압축하는동안의 파일이름 패턴...


### **1-3. Logger**

```
<Loggers>
  <Root level="INFO" additivity="false">
    <AppenderRef ref="Console_Appender"/>
    <AppenderRef ref="File_Appender"/>
  </Root>
  <Logger name="org.springframework" level="DEBUG" additivity="false">
    <AppenderRef ref="Console_Appender" />
    <AppenderRef ref="File_Appender"/>
  </Logger>
  <Logger name="로깅 할 프로젝트 위치(src.java. 이후부터)" level="INFO" additivity="false">
    <AppenderRef ref="Console_Appender" />
    <AppenderRef ref="File_Appender"/>
  </Logger>
</Loggers>
```

- 로거는 로깅을 직접하는 요소이다. 로거는 패키지 별로 설정할 수 있다.
- Root 패키지의 로거는 필수적이고, 추가적인 로거는 Logger를 통해 설정한다. 
- Additivity는 중복된 로그를 남길지에 대한 속성이다.



## 2. SLF4J(Simple Logging Facade For Java)
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


## 3. log4j2 및 slf4j 설정 및 사용.

### **3-1. Dependencies 추가**
log4j 1.2 버전대는 2021.12월에 취약점 발견으로 사용하지 말것을 권고하고 있다. 따라서 이번 프로젝트에서는 가장 성능이 좋고 보안성이 뛰어난 log4j2 를 사용해 log를 기록한다.

- build.gradle에 Dependency 추가
  - log4j를 사용하기 위해서는 log4j-api와 log4j-core를 의존성으로 가지고 있어야 하나 log4j-slf4j-impl은 이 두가지를 가지고 있어 따로 추가할 필요 없다.
- log4j-slf4j-impl:2.17.1의 의존성
    - Compile Dependencies : log4j-api:2.17.1, slf4j-api:1.7.25, slf4j-ext:1.7.25(optional)
    - Runtime Dependencies : log4j-core:2.17.1

#### **build.gradle**

```
// 의존성 추가 부분
dependencies {
    compile "javax.servlet:javax.servlet-api:4.0.1"
    compile "org.springframework:spring-context:5.3.10"
    compile "org.springframework:spring-webmvc:5.3.10"    
    // 추가
    compile "org.apache.logging.log4j:log4j-slf4j-impl:2.17.1"
}
```

- build.gradle 작성 후 해당 파일 우클릭 - Gradle - Refresh Gradle Project 클릭해 해당 의존성 추가한다.
- 해당 프로젝트 우클릭 - properties - java build path - libraries - classpath - project and External Dependencies 아래 보면 다음과 같이 .jar 파일이 추가된것을 확인할 수 있다.

![의존성 추가](/assets/images/springmvc13.PNG){: .align-center}

### **3-2. log4j2.xml 설정**

- 위의 log4j2에 대한 설정 부분을 xml형식으로 작성해 `/src/main/resources` 아래 `log4j2.xml` 명으로 설정파일을 생성한다.

#### **/src/main/resources/log4j2.xml**

```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">

	<!-- 변수 설정 -->
	<properties>
		<!-- 로그파일 저장될 경로 -->
    	<property name="LOG_HOME" value="D:/logs/log4j2/Gradle_SpringMVC" />
    	<!-- 로깅되는 로그들의 패턴 -->
    	<property name="LOG_PATTERN" value="[%-5level] %d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %c{1} - %msg%n" />
    </properties>
    
    <!-- Appender 설정 -->
	<Appenders>
		<RollingFile name="FILE">
    		<FileName>${LOG_HOME}/Gradle_SpringMVC.log</FileName>
    		<FilePattern>${LOG_HOME}/%d{yyyyMMdd}_Gradle_SpringMVC.log</FilePattern>
    		<PatternLayout>
    			<Pattern>${LOG_PATTERN}</Pattern>
    		</PatternLayout>
    		<Policies>
    			<!-- 로그파일 명의 최소 단위가 일자 이기 떄문에 interval 1로 하면 하루 단위로 로그파일 생성된다. -->
    			<TimeBasedTriggeringPolicy interval="1"/>
    		</Policies>
    		<DefaultRolloverStrategy>
    			<!-- 로그파일 기록 최대 개수로 30일이 지난 로그는 삭제 한다. -->
    			<max>30</max>
    		</DefaultRolloverStrategy>
    	</RollingFile>
	</Appenders>
	
	<!-- Logger 설정 -->
	<Loggers>
		<!-- root 설정은 반드시 있어야 하며 다음과 같은 규칙을 따른다. -->
		<!-- 로그 레벨이 높은게 위쪽으로 설정해야 정상 작동함..(ERROR -> WARN -> INFO -> DEBUG -> TRACE) -->
		<root level="ERROR">
			<!-- 해당 로거가 저장할 Appender Name 설정 -->
			<appender-ref ref="FILE"/>
		</root>
		
		<!-- 각종 프레임워크들에 대한 로거 등록 -->
		<!-- Spring, jdbc, actionlog 등등 -->
		<logger name="org.springframework" level="DEBUG" additivity="false">
			<appender-ref ref="FILE"/>
		</logger>
		
		<!-- 생성한 프로젝트에 대한 로거 등록 -->
		<logger name="Gradle_SpringMVC.api" level="TRACE" additivity="false">
			<appender-ref ref="FILE"/>
		</logger>
	</Loggers>
	
</Configuration>
```

### **3-3. slf4j를 통해 컨트롤러에서 log4j를 호출하기 위한 설정**

- [실습 1](/post/springMVC-실습1.md) 에서 설정했던 컨트롤러에 slf4j를 상속받은 logger 객체를 생성해 log를 남겨 본다.

#### **/src/main/java/Gradle_SpringMVC/api/main/controller/MainController.java**

```
package Gradle_SpringMVC.api.main.controller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MainController {
	
	// log4j-slf4j logger setting
	private static final Logger logger = LoggerFactory.getLogger(MainController.class);
	
	@RequestMapping(value="/", method= RequestMethod.GET)
	public void mainpage() {
    // [DEBUG] DATE [] MainController - / : This is main page! 형식으로 지정됨
		// {} 안에 뒤의 string 들어가게 된다.
		logger.debug("/ : {}", "This is main page!");
		System.out.println("This is main page!");
	}
	
	@RequestMapping(value="/test", method= RequestMethod.GET)
	public void test() {
		logger.debug("/test : {}", "Hello!");
		System.out.println("Hello!");
	}

}
```

### **3-4. 출력 결과**

- log4j2.xml에 RollingFileAppender를 통해 `D:/logs/log4j2/Gradle_SpringMVC`에 저장하도록 설정했기 때문에 해당 위치에 `Gradle_SpringMVC.log`의 형태로 저장되어 있는 것을 확인할 수 있다.

```
...
[INFO ] 2022-01-04 10:08:34.794 [localhost-startStop-1] DispatcherServlet - Completed initialization in 505 ms
[DEBUG] 2022-01-04 10:08:34.868 [http-bio-8080-exec-1] DispatcherServlet - GET "/", parameters={}
[DEBUG] 2022-01-04 10:08:34.871 [http-bio-8080-exec-1] RequestMappingHandlerMapping - Mapped to Gradle_SpringMVC.api.main.controller.MainController#mainpage()
[DEBUG] 2022-01-04 10:08:34.881 [http-bio-8080-exec-1] MainController - / : This is main page!
[DEBUG] 2022-01-04 10:08:34.889 [http-bio-8080-exec-1] RequestResponseBodyMethodProcessor - Using 'text/html', given [text/html, image/gif, image/jpeg, */*;q=.2, */*;q=.2] and supported [*/*]
[DEBUG] 2022-01-04 10:08:34.889 [http-bio-8080-exec-1] DispatcherServlet - Completed 200 OK
[DEBUG] 2022-01-04 10:08:55.837 [http-bio-8080-exec-4] DispatcherServlet - GET "/test", parameters={}
[DEBUG] 2022-01-04 10:08:55.838 [http-bio-8080-exec-4] RequestMappingHandlerMapping - Mapped to Gradle_SpringMVC.api.main.controller.MainController#test()
[DEBUG] 2022-01-04 10:08:55.838 [http-bio-8080-exec-4] MainController - /test : Hello!
[DEBUG] 2022-01-04 10:08:55.839 [http-bio-8080-exec-4] RequestResponseBodyMethodProcessor - Using 'text/html', given [text/html, application/xhtml+xml, image/avif, image/webp, image/apng, application/xml;q=0.9, application/signed-exchange;v=b3;q=0.9, */*;q=0.8] and supported [*/*]
[DEBUG] 2022-01-04 10:08:55.839 [http-bio-8080-exec-4] DispatcherServlet - Completed 200 OK
```

## 4. 추가사항

- src/main/resources 디렉토리는 src/main/java와 마찬가지로 calsspath: 로 복사가 된다. 
- 즉, WEB-INF/classes로 디렉토리로 복사가 되는데 
- log4j2라이브러리가 기본적으로 설정파일을 classpath:에서 읽도록 프로그래밍 되어 있기 때문에 
- 그냥 src/main/resources 에 log4j2 설정파일을 두게 된다면 자동으로 설정되기 때문에 따로 web.xml에 정의할 필요가 없으며, 자동으로 로그가 출력된다.
- 이러한 위치를 사용자가 정의한 대로 설정하기 위해서는 Log4jConfigListener를 사용해야 한다.

### **web.xml** 
```
<listener>
  <listener-class>org.springframework.web.util.Log4j2ConfigListener</listener-class>
</listener>
<context-param>
  <param-name>log4j2ConfigLocation</param-name>
  <param-value>/WEB-INF/config/log4j.xml</param-value>
  </context-param>
```


## 5. log4jdbc-log4j2 사용하기

DB 관련한 로그를 보다 보기 쉽게 로그로 남겨주는 라이브러리 
이후의 실습 4, 5를 통해 DB 연결 한 후 해당 설정을 통해 DB 관련 logging을 할 수 있다.

### **build.gradle 추가**

```
// log4jdbc-log4j
	compile "org.bgee.log4jdbc-log4j2:log4jdbc-log4j2-jdbc4.1:1.16"
```

### **resources 디렉터리 바로아래(log4j2.xml 위치에) log4jdbc.log4j2.properties 파일생성**

```
# slf4를 사용하기 위함.
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
# 출력할 최대 라인 수 0은 무제한
log4jdbc.dump.sql.maxlinelength = 0

```

### **log4j2.xml 수정 및 추가**

```
<Appenders>
  ...
  <!-- Console에 출력 -->
  <Console name="console" target="SYSTEM_OUT">
    <PatternLayout pattern="${LOG_PATTERN}"/>
  </Console>
</Appenders>

<loggers>
  <!-- DB 관련 로거 등록 -->
  <!-- 열러있는 모든 번호와 연결 수립 및 해제 이벤트 로깅(connection Pool 설정 시 사용) -->
	<logger name="jdbc.connection" level="OFF" additivity="false">
		<appender-ref ref="console"/> 
	</logger>
		
	<!-- ResultSet을 제외한 모든 JDBC 호출 정보를 로깅한다. -다량의 로그 생성되므로 JDBC 추적 이외에는 사용 자제 -->
	<logger name="jdbc.audit" level="OFF" additivity="false"> 
		<appender-ref ref="console"/> 
	</logger> 
	    
	<!-- ResultSet을 포함한 모든 JDBC 호출 정보를 로그로 남긴다. - 다량의 로그 생성되므로 JDBC 추적 이외에는 사용 자제 -->
	<logger name="jdbc.resultset" level="OFF" additivity="false">
		<appender-ref ref="console"/> 
	</logger>
		
	<!-- SQL 문만 로그로 남기며 PreparedStatement일 경우 관련된 argumnet 값으로 대체된 SQL표시 -->
	<logger name="jdbc.sqlonly" level="INFO" additivity="false"> 
		<appender-ref ref="console"/> 
	</logger>

	     
	<!-- SQL 결과 조회된 데이터의 테이블 로깅 -->
	<logger name="jdbc.resultsettable" level="INFO" additivity="false"> 
		<appender-ref ref="console"/> 
	</logger> 

		
	<!-- SQL 문과 해당 SQL 실행시키는데 수행된 시간 정보 로깅 -->
	<logger name="jdbc.sqltiming" level="OFF" additivity="false">
		<appender-ref ref="console"/> 
	</logger>

</loggers>
```

### **jdbc 연결**

|logger name|설명|
|-|-|
|jdbc.connection|열려있는 모든 번호와 연결 수립 및 해제 이벤트를 기록. 이는 연결 문제를 찾아내는데 매우 유용 (Connection Pool 설정)|
|jdbc.audit|ResultSet을 제외한 모든 JDBC 호출 정보를 로그로 남긴다. 많은 양의 로그가 생성되므로 특별히 JDBC 문제를 추적해야 할 필요가 있는 경우를 제외하고는 사용을 권장하지 않는다.|
|jdbc.resultset|ResultSet을 포함한 모든 JDBC 호출 정보를 로그로 남기므로 매우 방대한 양의 로그가 생성된다.|
|jdbc.sqlonly|SQL문만을 로그로 남기며, PreparedStatement일 경우 관련된 argument 값으로 대체된 SQL문이 보여진다. (원래는 ? 로 표시되는데, log4j 덕분에 완전한 쿼리로 보여지는 것임)|
|jdbc.resultsettable|	SQL 결과 조회된 데이터의 table을 로그로 남긴다.|
|jdbc.sqltiming|SQL문과 해당 SQL을 실행시키는데 수행된 시간 정보(milliseconds)를 포함한다.|

### **datasource.xml 수정**

```
ASIS

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<entry key="jdbc.postgresql.driverClassName">org.postgresql.Driver</entry>
	<entry key="jdbc.postgresql.url">jdbc:postgresql://127.0.0.1:5432/DBS</entry>
	<entry key="jdbc.postgresql.username">testuser</entry>
    <entry key="jdbc.postgresql.password">testuser</entry>
</properties>

TOBE

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<entry key="jdbc.postgresql.driverClassName.origin">org.postgresql.Driver</entry>
	<entry key="jdbc.postgresql.url.origin">jdbc:postgresql://127.0.0.1:5432/DBS</entry>
	<entry key="jdbc.postgresql.driverClassName">net.sf.log4jdbc.sql.jdbcapi.DriverSpy</entry>
	<entry key="jdbc.postgresql.url">jdbc:log4jdbc:postgresql://127.0.0.1:5432/DBS</entry>
	<entry key="jdbc.postgresql.username">testuser</entry>
    <entry key="jdbc.postgresql.password">testuser</entry>
</properties>

```

### **출력**

```
[INFO ] 2022-01-13 16:28:09.192 [http-bio-8080-exec-2] sqlonly - SELECT * FROM springmvc.test

[INFO ] 2022-01-13 16:28:09.228 [http-bio-8080-exec-2] resultsettable - 
|-----------|-----------|
|english    |korean     |
|-----------|-----------|
|culture    |문화, 교양     |
|experience |경험         |
|education  |교육         |
|symbol     |상징         |
|effect     |결과, 영향, 효과 |
|liberty    |자유         |
|affair     |사건, 일      |
|comfort    |안락, 위안     |
|tradition  |전통, 전설     |
|subject    |학과, 주제, 주어 |
|-----------|-----------|

```


## 5. log4j2 Spring profiles 에 따른 동적 사용

- logback의 경우 기본 logback 설정파일(resource/ 아래 기본 설정파일)에서 다음과 같이 설정하면 원하는 디렉터리에 있는 logback 설정파일을 추가할 수 있다.

  - resources/logback.xml
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <include resource="config/${spring.profiles.active}/logback.xml"/>
    </configuration>
    ```

  - resources/local/logback.xml

    ```
    <included>
      <properties> ... </properties>
      <appenders> ... </appenders>
      <loggers> ... </loggers>
    </included>
    ```

- log4j2 또한 Logback 과 같이 외부 파일을 import 할 수 있는 [XInclude](https://logging.apache.org/log4j/log4j-2.1/manual/configuration.html#XInclude) 가 있다.

  - resources/log4j2.xml
    
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration xmlns:xi="http://www.w3.org/2001/XInclude"
                  status="warn" name="log4j2">
      <properties>
        <property name="filename">log4j2.log</property>
      </properties>
      <xi:include href="log4j2-local.xml" />
    </configuration>
    ```

  - resources/log4j2-local.xml

    ```
    <appenders> ... </appenders>
    <loggers> ... </loggers>
    ```

- 하지만, log4j2의 XInclude 의 경우 href 를 통해 추가하려는 설정 파일의 위치를 정의하게 되는데 이때 ${spring.profiles.active}와 같은 변수를 입력할 수 없다는 단점이 있다. (각 설정에 맞는 분기를 할 수 없음.)

-이러한 문제를 해결하기 위해 web.xml 상에서 log4j2.xml 설정파일을 기존 /resource/ 디렉터리 아래에서 각 spring profiles에 맞는 위치에 있는 log4j2 설정파일을 읽어오도록 설정해 주어 각 개발 환경 별 logging을 동적으로 할당해 준다.

1. build.gradle 
   1. `compile "org.apache.logging.log4j:log4j-web:${version.log4j2}"` 추가
   2. gradle - refresh gradle project 수행(build)
   3. 아래 2번의 Log4jSevletContextListener가 log4j-web 안에 있음
2. web.xml 수정(추가)
    
    ```
    <!-- spring profiles에 따른 log4j2 분기 -->
  	<context-param>
  		<param-name>log4jConfiguration</param-name>
  		<param-value>classpath:${spring.profiles.active}/log4j2.xml</param-value>
  	</context-param>

    <!-- log4j2 설정파일 읽어오기 위한 listener -->
  	<listener>
  		<listener-class>org.apache.logging.log4j.web.Log4jServletContextListener</listener-class>
  	</listener>
    ```
3. context-param에 정의한 log4j2.xml 파일위치에 log4j2 설정파일을 생성 및 작성한다.


