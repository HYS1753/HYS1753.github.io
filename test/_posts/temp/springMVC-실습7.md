---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 7 (Error 공통 페이지 설정)"
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

# Spring MVC + Gradle Project 6 (Error 공통 페이지 설정)

---

## 1. Error 공통페이지 작성 이유 및 오류 페이지 호출 우선 순위

- 흔히 발생하는 오류 (400, 404, 500. ... 등)가 발생했을 때 따로 정의해 둔 페이지로 연결하는 설정 방법
- 오류 공통 페이지를 개발자가 설정하지 않으면, Web Container에 의해 기본 오류 페이지가 보이게 된다.
- 이때 기본 오류 페이지에는 왜 오류가 발생했는지, 심지어 SQL 관련 오류의 경우 Query 문이 보일 수도 있기 때문에 보안상 취약점이 발생할 수 있다.
- 오류 페이지의 호출 우선순위
  - 1. JSP 내에서 Error Page 호출
  - 2. Web.xml > exception-type에 에러 선언
  - 3. web.xml > error-code에 선언한 경우
  - 4. web container가 제공하는 기본 오류 페이지 출력(보안 위험)

## 2. 발생 오류에 관한 정보의 종류 및 사용 예시

- 오류 정보 종류

    ```
    javax.servlet.error.status_code (오류 상태 코드)
    javax.servlet.error.exception_type (예외처리 클래스)
    javax.servlet.error.message (오류 메시지)
    javax.servlet.error.request_uri (오류가 발생한 요청 URI)
    javax.servlet.error.exception (발생한 예외처리 내용)
    javax.servlet.error.servelt_name (오류가 발생한 Servlet)
    ```
    
- 사용 예시(Java)

    ```
    log.info("STATUS_CODE : "+request.getAttribute("javax.servlet.error.status_code"));
    log.info("EXCEPTION_TYPE : "+request.getAttribute("javax.servlet.error.exception_type"));
    log.info("MESSAGE : "+ request.getAttribute("javax.servlet.error.message"));
    log.info("REQUEST_URI : "+ request.getAttribute("javax.servlet.error.request_uri"));
    log.info("EXCEPTION : "+ request.getAttribute("javax.servlet.error.exception"));
    log.info("SERVLET_NAME : "+ request.getAttribute("javax.servlet.error.servlet_name"));
    ```

## 3. Error 공통 페이지 구현

- apllicationContext.xml (Bean 구현 xml)에 view를 사용하기 위한 Bean 등록

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">
        
        <!-- JSP 사용 경로 지정 -->
        <!-- prefix + controller returned String + suffix 로 경로가 지정된다.-->
        <!-- ex. /WEB-INF/views/index.jsp -->
        
        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/views/" />
            <property name="suffix" value=".jsp" />
        </bean>
        
    </beans>
    ```

- error jSP 생성
  - 위치는 위의 xml 에 정의한 경로에 jsp 파일을 생성한다.

    ```
    <%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Common Error</title>
    </head>
    <body>
        <div>
            <h3>${error.MESSAGE }</h3>
            <p>에러 코드는 다음과 같습니다</p> 
            <p>STATUS_CODE - ${error.STATUS_CODE }</p>
            <p>REQUEST_URI - ${error.REQUEST_URI }</p>
            <p>EXCEPTION_TYPE - ${error.EXCEPTION_TYPE }</p>
            <p>EXCEPTION - ${error.EXCEPTION }</p>
            <p>SERVLET_NAME - ${error.SERVLET_NAME }</p>
        </div>
    </body>
    </html>
    ```

- web.xml 수정
  - 다음과 같이 해당하는 error코드가 발생하면 해당 Controller로 매핑 시켜 준다.

    ```
    <!-- ERROR PAGES: START -->
  	<!-- 
	<error-page>
	  <exception-type>java.lang.Throwable</exception-type>
	  <location>/common/errorThrowable.do</location>
	</error-page>
	<error-page>
	  <exception-type>java.lang.Exception</exception-type>
	  <location>/common/errorException.do</location>
	</error-page>
	-->
	<error-page>
	  <error-code>400</error-code>
	  <location>/common/error400.do</location>
	</error-page>
	<error-page>
	  <error-code>403</error-code>
	  <location>/common/error403.do</location>
	</error-page>
	<error-page>
	  <error-code>404</error-code>
	  <location>/common/error404.do</location>
	</error-page>
	<error-page>
	  <error-code>405</error-code>
	  <location>/common/error405.do</location>
	</error-page>
	<error-page>
	  <error-code>500</error-code>
	  <location>/common/error500.do</location>
	</error-page>
	<error-page>
	  <error-code>503</error-code>
	  <location>/common/error503.do</location>
	</error-page>
	<!-- ERROR PAGES: END --> 
    ```

- Controller 작성(CommonController.java)

    ```
    package com.test.application.main.controller;

    import java.security.KeyStore.Entry;
    import java.util.HashMap;
    import java.util.Iterator;
    import java.util.Map;

    import javax.servlet.http.HttpServletRequest;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    import com.test.api.main.controller.ApiController;

    @Controller
    public class CommonController {
        
        // logger 등록
        private static final Logger logger = LoggerFactory.getLogger(ApiController.class);
        
        @RequestMapping(value="/common/error{error_code}.do")
        public ModelAndView error(HttpServletRequest request, @PathVariable String error_code) {
            ModelAndView mv = new ModelAndView("/common/error");
            String msg = (String) request.getAttribute("javax.servlet.error.message");
            
            HashMap<String, Object> map = new HashMap<>();
            map.put("STATUS_CODE", request.getAttribute("javax.servlet.error.status_code"));
            map.put("REQUEST_URI", request.getAttribute("javax.servlet.error.request_uri"));
            map.put("EXCEPTION_TYPE", request.getAttribute("javax.servlet.error.exception_type"));
            map.put("EXCEPTION", request.getAttribute("javax.servlet.error.exception"));
            map.put("SERVLET_NAME", request.getAttribute("javax.servlet.error.servlet_name"));
            
            try {
                int status_code = Integer.parseInt(error_code);
                switch (status_code) {
                case 400: msg = "잘못된 요청입니다."; break;
                case 403: msg = "접근이 금지되었습니다."; break;
                case 404: msg = "페이지를 찾을 수 없습니다."; break;
                case 405: msg = "요청된 메소드가 허용되지 않습니다."; break;
                case 500: msg = "서버에 오류가 발생하였습니다."; break;
                case 503: msg = "서비스를 사용할 수 없습니다."; break;
                default: msg = "알 수 없는 오류가 발생하였습니다."; break;
                }
            } catch(Exception e) {
                msg = "기타 오류가 발생하였습니다.";
            } finally {
                map.put("MESSAGE", msg);
            }
            
            // logging
            // Map에 값을 전체 출력하기 위해서는(key, value 모두) entrySet() 사용, key의 값만 필요한 경우 keySet() 메서드를 사용한다.
            if(map.isEmpty() == false) {
                Iterator<Map.Entry<String, Object>> iterator = map.entrySet().iterator();
                Map.Entry<String, Object> entry = null;
                while(iterator.hasNext()) {
                    entry = iterator.next();
                    logger.debug("key: "+entry.getKey()+", value : "+entry.getValue());
                }
            }
            
            // setViewName("VIEW 의 경로")
            mv.setViewName("/common/error");
            
            // addObject("변수이름", "데이터 값")
            mv.addObject("error", map);
            return mv; 
        }
    }
    ```

## 4. Error 발생 결과

![Error 발생시 출력되는 화면 결과](/assets/images/springmvc18.PNG){: .align-center}