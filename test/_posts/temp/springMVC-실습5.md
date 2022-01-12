---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 5"
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

# Spring MVC + Gradle Project 5 (DB 사용 2)

---

# **1. Mybatis 설정 및 사용 Spring MVC + Gradle Project 4 (DB 사용 1)에 이어서 작성됨**

## 1. PostgreSQL TEST 테이블 및 데이터 생성

```
create table springmvc.test (
	english varchar(200) not null,
	korean varchar(200),
	CONSTRAINT test_pk PRIMARY KEY (english)
)

insert into springmvc.test values('culture', '문화, 교양');
insert into springmvc.test values('experience', '경험');
insert into springmvc.test values('education', '교육');
insert into springmvc.test values('symbol', '상징');
insert into springmvc.test values('effect', '결과, 영향, 효과');
insert into springmvc.test values('liberty', '자유');
insert into springmvc.test values('affair', '사건, 일');
insert into springmvc.test values('comfort', '안락, 위안');
insert into springmvc.test values('tradition', '전통, 전설');
insert into springmvc.test values('subject', '학과, 주제, 주어');

select * from test
```
- test Table

    |english|korean|
    |-|-|
    |culture|문화, 교양|
    |experience|경험|
    |education|교육|
    |symbol|상징|
    |effect|결과, 영향, 효과|
    |liberty|자유|
    |affair|사건, 일|
    |comfort|안락, 위안|
    |tradition|전통, 전설|
    |subject|학과, 주제, 주어|

## 2. Mybatis 데이터 흐름 순서

- Spring 시작 -> web.xml 스캔 -> dispatcher-servlet.xml 스캔(option) -> 서블릿 및 Bean 객체 생성

- 사용자 요청 -> Servlet -> Controller -> Service -> ServiceImpl -> Mapper -> Mapper.xml -> DBCP -> DB

## 3. 실습 디렉터리 구조

![mybatis 실습 구조](/assets/images/springmvc16.PNG){: .align-center}

## 4. VO 작성

```
package com.mybatis_postgre.dictionary.main.vo;

import lombok.Data;

@Data
public class DictionaryVO {
	private String english;
	private String korean;
}
```

## 5. Mapper 작성

```
package com.mybatis_postgre.dictionary.main.mapper.postgre;

import java.util.ArrayList;
import java.util.List;

import org.apache.ibatis.annotations.Mapper;

import com.mybatis_postgre.dictionary.main.vo.DictionaryVO;

@Mapper
public interface DictionaryMapper {
	ArrayList<DictionaryVO> selectAllDic();
}
```

## 6. Controller 작성

```
package com.mybatis_postgre.dictionary.main.controller;

import java.util.ArrayList;
import java.util.List;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.support.DefaultListableBeanFactory;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import com.mybatis_postgre.dictionary.main.vo.DictionaryVO;
import com.mybatis_postgre.dictionary.service.DictionaryService;

@RestController
@RequestMapping("/dictionary")
public class DictionaryController {
	
	// log4j-slf4j logger setting
	private static final Logger logger = LoggerFactory.getLogger(DictionaryController.class);
	
	// Service 연결
	@Autowired
	DictionaryService dictionaryService;
	
    // BeanFactory 연결
	@Autowired
	DefaultListableBeanFactory df;
	
	@RequestMapping(value="/", method= RequestMethod.GET)
	public void mainpage() {
		// [DEBUG] DATE [] MainController - / : This is main page! 형식으로 지정됨
		// {} 안에 뒤의 string 들어가게 된다.
		logger.debug("/ : {}", "This is main page!");
		System.out.println("This is main page!");
	}
	
	@RequestMapping(value="/test", method= RequestMethod.GET)
	public void test() throws Exception {
		logger.debug("/test : {}", "test");
		try {
			List<DictionaryVO> dictlist = dictionaryService.getAllDic();
			System.out.println(dictlist);
		} catch (Exception e) {
			System.out.println(e);
		}
	}
	
    // Bean으로 등록된 객체 확인
	@RequestMapping(value="/test2", method= RequestMethod.GET)
	public void test3() throws Exception{
		for (String name: df.getBeanDefinitionNames()) {
			System.out.println(name+"\t"+df.getBean(name).getClass().getName());
		}
	}
}
```

## 7. Service, ServiceImpl 작성

### **Service**

```
package com.mybatis_postgre.dictionary.service;

import java.util.List;

import com.mybatis_postgre.dictionary.main.vo.DictionaryVO;

public interface DictionaryService {
	
	List<DictionaryVO> getAllDic() throws Exception;
}
```

### **ServiceImpl**

```
package com.mybatis_postgre.dictionary.service.serviceImpl;

import java.util.ArrayList;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.mybatis_postgre.dictionary.main.mapper.postgre.DictionaryMapper;
import com.mybatis_postgre.dictionary.main.vo.DictionaryVO;
import com.mybatis_postgre.dictionary.service.DictionaryService;

@Service
public class DictionaryServiceImpl implements DictionaryService{
	
	@Autowired
	DictionaryMapper dictionaryMapper;
	
	@Override
	public List<DictionaryVO> getAllDic() { 
		ArrayList<DictionaryVO> dicList = new ArrayList<DictionaryVO>();
		dicList.addAll(dictionaryMapper.selectAllDic());
		return dicList;
	}
}
```

## 8.Mapper.xml 작성

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper 
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.mybatis_postgre.dictionary.main.mapper.postgre.DictionaryMapper">
	<resultMap type="com.mybatis_postgre.dictionary.main.vo.DictionaryVO" id="dicInfo">
		<id property="english" column="english"/>
		<result property="korean" column="korean"/>
	</resultMap>
	
	<select id="selectAllDic" parameterType="String" resultMap="dicInfo">
		SELECT * FROM springmvc.test <!-- where english=#{en}-->
	</select>
</mapper>
```

- `<!Doctype>` 부분 필수 작성

## 9. context-mapper-postgre.xml 추가

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:jee="http://www.springframework.org/schema/jee"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"
       xsi:schemaLocation="http://mybatis.org/schema/mybatis-spring http://mybatis.org/schema/mybatis-spring-1.2.xsd
       					   http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
                           http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.0.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd">
                          
	<bean id="dataPostgreSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSourcePostgre"/>
		<property name="typeAliasesPackage" value="com.mybatis_postgre.dictionary" />
        <property name="configLocation" value="classpath:/config/mybatis/config/mybatis-config.xml" />
        <property name="mapperLocations" value="classpath:/config/mybatis/mapper/postgre/**/*.xml" />
	</bean>
	
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.mybatis_postgre.dictionary.**.mapper.postgre"></property>
		<property name="sqlSessionFactoryBeanName" value="dataPostgreSessionFactory"></property>
	</bean>
	
</beans>
```

- **org.mybatis.spring.SqlSessionFactoryBean** 는 mybatis-spring 모듈에 있는 클래스 이다.
- **typeAliasesPackage** : Aliases에 해당하는 클래스를 스캔하기 위한 패키지 경로를 지정.(별칭 쓸 클래스 패키지 위치 주로 DAO), 해당하는 하위의 데렉터리 클래스들은 모두 Mybatis Mapper XML에서 Parameter Type이나 result Type으로 사용할 수 있다. (VO 등), DB에서 가져온 데이터들을 담을 자바 빈들이 모여있는 패키지를 등록하는 것, 쉽게 말하면 MVC패턴에서 Model에 해당하는 패키지를 등록 
- **configLocation** : mybatis 설정파일이 위치한 경로를 지정한다.(주로 mybatis-config.xml 위치)
- **mapperLocation** : Mapper를 스캔하기 위한 XML 파일 경로를 지정한다.(*Mapper.xml 파일 위치)

### **기존 mybatis 형식**

- 순서
  - User <p>
-> Template(세션 유지 정보, Factory 추상) <p>
-> Factory(데이터 베이스 기능을 제공해주는 클래스) <p>
-> DataSource(데이터 베이스 연결 정보, DBCP 적용) <p>
-> DB<p>

- 따라서 다음과 같이 SessionTemplate를 만들어 주고 DAO를 통해 해당 객체를 Autowired를 통해 싱글톤 객체로 받아와 실행한다.

    ```
    <bean id="dataPostgreSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0" ref="dataPostgreSessionFactory"/>
    </bean>
    ```

- 하지만 간단한 Mapper를 통해 Mybatis를 사용할 수 있으며 사용방법은 위에서 처럼 Mapper들을 정의해 주고 해당 Mapper를 `MapperScannerConfigurer`로 연결시켜준다.

## 10. 결과

- localhost:8080/dictionary/test 로 접속하게 되면 Spring 콘솔창에 다음과 같이 출력됨을 확인할 수 있다.

```
[DictionaryVO(english=culture, korean=문화, 교양), 
DictionaryVO(english=experience, korean=경험), 
DictionaryVO(english=education, korean=교육), 
DictionaryVO(english=symbol, korean=상징), 
DictionaryVO(english=effect, korean=결과, 영향, 효과), 
DictionaryVO(english=liberty, korean=자유),
DictionaryVO(english=affair, korean=사건, 일), 
DictionaryVO(english=comfort, korean=안락, 위안), 
DictionaryVO(english=tradition, korean=전통, 전설), 
DictionaryVO(english=subject, korean=학과, 주제, 주어)]
```