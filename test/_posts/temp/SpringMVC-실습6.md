---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 6"
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

# Spring MVC + Gradle Project 6 (MessageSource 설정)

---

## 0. MessageSource란? 필요성?

- 간단히 테스트 시에는 소스안에 메시지를 하드 코딩 식으로 넣어 사용할 수 있지만, 이런식의 사용은 공통 메시지 변경 시 일일히 모든 소스파일을 찾아 바꿔줘야 하기 때문에 관리하기 좋지 않다.
- 따라서, Spring에서 제공하는 MessageSource를 Bean으로 등록해 사용하면 관리가 편해지고 심지어 웹어플리케이션의 리로딩 없이 메시지 변경이 가능하다. 

## 1. Spring에서 제공하는 주요 MessageSource 구현체

- **org.springframework.context.support.StaticMessageSource** : MessageSource의 가장 간단한 구현체로, 기본적인 다국어 기능을 지원하면서 테스트용도로 사용된다.
- **org.springframework.context.support.ResourceBundleMessageSource** : ResourceBundle Class, MessageFormat Class 기반으로 만들어져 Bundle에 특정 명칭으로 접근가능
- **org.springframework.context.support.ReloadableResourceBundleMessageSource** : Property 설정을 통해 Reloading 정보를 입력해 주기적인 Message Reloading을 수행한다. App 종료 없이 변경 가능

## 2. ReloadableResourceBundleMessageSource 구현체를 사용한 메시지 설정 구조

![MessageSource 구조](/assets/images/springmvc17.PNG){: .align-center}

참고 : https://devks.tistory.com/42

- web.xml을 통해 message-servlet을 읽도록 한다.(applicationContext-properties.xml 실습 상- 각 프로젝트 별 환경에 맞는 servlet)
- 사용할 Message Property 생성
- message-servlet.xml에 MessageSource와 사용하기 위한 MessageAccessor를 설정한다.
- MessageSourceAccessor를 사용할 수 있도록 MessageUtils.java를 생성한다.
- Test

## 3. web.xml 설정

```
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath*:/config/spring/${spring.profiles.active}/*.xml
		classpath*:/config/spring/applicationContext*.xml
	</param-value>
</context-param>
<servlet>
  	<servlet-name>dispatcherServlet</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	<init-param>
  		<param-name>contextConfigLocation</param-name>
  		<param-value>/WEB-INF/dispatcher-servlet.xml</param-value>	<!-- 해당 서블릿에서 추가할 빈 설정 -->
  	</init-param>
  	<load-on-startup>1</load-on-startup>
</servlet>
```

## 4. Message Property 파일 작성

- Message Source의 Properties는 다음과 같은 Naming Rule을 지켜야 한다.
- `{basename}_언어코드_국가코드.properties`
- basename : 각 프로젝트에 맞는 이름으로 임의 지정 가능
  - **{basename}.properties** : Default Message로 시스템의 언어 및 지역에 맞는 Property 파일이 존재 하지 않을 경우 사용.
  - **{basename}_en.properties** : 시스템 언어 코드가 영어일 떄 사용.
  - **{basename}_ko.properties** : 시스템 언어 코드가 한글일 때 사용.
  - **{basename}_en_UK.properties** : 시스템 언어코드가 영어일 때, 영국 국가코드를 위한 메시지

### **resources/config/message/message_ko_KR.properties** 작성

```
error.common=오류가 발생했습니다.
error.minlength={0}은 {1}자 이상의 문자를 입력하셔야 합니다.
```

- {0}, {1} String을 배열 파라미터로 받아서 오류 메시지를 탬플릿에 맞춰 출력하기 위함.
  
## 5. Application Context 설정 : message-servlet.xml

- 해당 실습에서의 messsage-servlet.xml인 applicationContext-properties.xml에 다음과 같이 작성한다.

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:context="http://www.springframework.org/schema/context"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://www.springframework.org/schema/beans"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
                        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    
    <!-- MessageSource 설정 -->
    <bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
        <!-- Encoding 설정 -->
        <property name="defaultEncoding" value="UTF-8"/>
        <!-- Reload Cache 설정 -->
        <property name="cacheSeconds" value="60"/>
        <!-- basename 설정: 다음과 같이 설정하면 classpath 아래 config/message 디렉터리 아래 있는 message로 시작하는 모든 properties를 바라보게 된다. -->
        <!-- 즉, message로 시작하는 모든 property를 읽어 사용하는 locale에 맞는 Property를 사용하게 된다. -->
        <property name="basenames">
            <list>
                <value>classpath:/config/messsage/message</value>
            </list>
        </property>
    </bean>

    <!-- MessageSource를 사용하기 위한 Accessor 설정 -->
    <bean id="messageSourceAccessor" class="org.springframework.context.support.MessageSourceAccessor">
        <constructor-arg ref="messageSource"/>
    </bean>

    <!-- MessageSource를 사용하기 위한 MessageUtils 매핑 -->
    <bean id="message" class="com.mybatis_postgre.common.utils.MessageAccessUtil">
        <property name="messageSourceAccessor" ref="messageSourceAccessor"/>
    </bean>

    <!-- Default Location 설정 -->
    <bean id="localeResolver" class="org.springframework.web.servlet.i18n.SessionLocaleResolver">
        <property name="defaultLocale" value="ko"/>
    <bean>
</beans>
```

- MessageSource를 설정하기 위한 Bean의 ID는 반드시 `messageSource`여야 한다.(고정)
  

## 6. MessageUtils.java 작성(MessageAccessUtil.java)

- /java/com/mybatis_postgre/common/utils/MessageAccessUtil.java를 다음과 같이 작성한다.

```
package com.mybatis_postgre.common.utils;

import java.util.Locale;

import org.springframework.context.support.MessageSourceAccessor;

public class MessageAccessUtil {
	
	private static MessageSourceAccessor msAcc = null;
	
	public void setMessageSourceAccessor(MessageSourceAccessor msAcc) {
		MessageAccessUtil.msAcc = msAcc;
	}
	
	public static String getMessage(String code) {
		return msAcc.getMessage(code, Locale.getDefault());
	}
	
	public static String getMessage(String code, Object[] objects) {
		return msAcc.getMessage(code, objects, Locale.getDefault());
	}

}
```

- MessageSourceAccessor(msAcc) 는 앞의 `applicationContext-properties.xml`에서 정의한 bean id="message" 와 연결된다. 
- 파라미터로 들어오는 String code는 `message_ko_KR.properties`에서 작성한 error.common과 같은 식별자가 된다.

## 7. Test

- 실습 5번에 이어서 DictionaryController.java에 다음과 같이 추가 한다.

```
import com.mybatis_postgre.common.utils.MessageAccessUtil;

@RestController
@RequestMapping("/dictionary")
public class DictionaryController {
    // log4j-slf4j logger setting
	private static final Logger logger = LoggerFactory.getLogger(DictionaryController.class);
	
	//Service 연결
	@Autowired
	DictionaryService dictionaryService;
	
	@Autowired
	DefaultListableBeanFactory df;

    ...

    @RequestMapping(value="/test5", method=RequestMethod.GET)
	public void test5() throws Exception{
		System.out.println("error.common: "+MessageAccessUtil.getMessage("error.common"));
		System.out.println("error.minlength: " + MessageAccessUtil.getMessage("error.minlength", new String[] {"테스트글자", "2"}));
	}
```


- 출력

```

다음 정리

https://devks.tistory.com/40?category=709595