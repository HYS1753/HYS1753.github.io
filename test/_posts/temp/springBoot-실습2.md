---
author_profile: true
date: 2022-01-01
title: "SpringBoot 2 (DB 연결)"
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

# SpringBoot 2 (DB 연결)

---

## 1. DataSource 설정

- 스프링부트에서 DataSource의 설정은 두가지 방법을 사용할 수 있다.
- @Bean 어노테이션, application.properties 이다.

### JDBC Connection Pool
- 데이터베이스와 어플리케이션을 효율적으로 연결하기 위해 동시 접속자가 가질 수 있는 Connection을 하나로 모아 관리하는 것이 JDBC Connection Pool이다.
- Pool에서 사용할 수 있는 Connection을 생성해 놓고 Connection이 필요할 때마다 제공하여, Connection 생성 닫기 하는 소비 시간을 줄이고, 계속 재사용이 가능하기 때문에 어플리케이션 속도향상을 기대할 수 있으며, 동시접속자도 제어할 수 있어 어플리케이션의 과부하를 막아주는 역할도 한다.
- dbcp2, tomcat, hikari 등의 JDBC Connection Pool 이 있는데 SpringBoot2.0 이후부터는 성능의 문제로 HikariCP로 Default가 변경되었다. 다음은 각 Connection Pool 별 벤치마킹 소스이다. [HikariCP-benchmark](https://github.com/brettwooldridge/HikariCP-benchmark)

![밴치마킹](/assets/images/springBoot3.PNG){: .align-center}

- 따라서 해당 실습에서도 SpringBoot에서 기본으로 제공하는 HirakiCP를 사용하여 DB를 Connection 한다.

### build.gradle

```
implementation 'com.zaxxer:HikariCP:5.0.0'
```

### application.properties

```
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.url=jdbc:postgresql://127.0.0.1:5432/DBS
spring.datasource.username=testuser
spring.datasource.password=testuser
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.hikari.pool-name=hikari-cp
spring.datasource.hikari.maximum-pool-size=30
spring.datasource.hikari.minimum-idle=2
spring.datasource.hikari.data-source-properties.cachePrepStmts=true
spring.datasource.hikari.data-source-properties.prepStmtCacheSize=250
spring.datasource.hikari.data-source-properties.prepStmtCacheSqlLimit=2048
spring.datasource.hikari.data-source-properties.useServerPrepStmts=true

```

## 2. ContextDataSource.java 생성 및 작성

- com.spring.config 디렉터리 아래 ContextDataSource.java 파일을 생성한후 다음과 같이 작성한다.

```
package com.spring.config;

import javax.sql.DataSource;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

@Configuration
@PropertySource("classpath:/application.properties")
public class ContextDataSource {
	
	@Autowired
	private ApplicationContext applicationContext;
	
	@Value("${spring.datasource.driver-class-name}")
	private String driverClassName;
	
	@Value("${spring.datasource.url}")
	private String url;
	
	@Value("${spring.datasource.username}")
	private String id;
	
	@Value("${spring.datasource.password}")
	private String pw;
	
	@Bean
	public DataSource dataSource() {		
		HikariConfig hikariConfig = new HikariConfig();
		hikariConfig.setDriverClassName(driverClassName);
		hikariConfig.setJdbcUrl(url);
		hikariConfig.setUsername(id);
		hikariConfig.setPassword(pw);
		HikariDataSource dataSource = new HikariDataSource(hikariConfig);
		return dataSource;
	}
	
	@Bean
	public SqlSessionFactory sqlSessionFactory() throws Exception {
		SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
		sqlSessionFactoryBean.setDataSource(dataSource());
		//sqlSessionFactoryBean.setMapperLocations(applicationContext.getResources("classpath:/mapper/**/*Mapper.xml"));
		return sqlSessionFactoryBean.getObject();
	}
	
	@Bean
	public SqlSessionTemplate sqlSessionTemplate() throws Exception {
		return new SqlSessionTemplate(sqlSessionFactory());
	}
}

```

- @Configuration : 해당 어노테이션이 지정된 자바 클래스는 자바 기반의 설정파일로 인식
- @PropertySource : 해당 클래스를 참조할 Properties 파일 위치를 지정
- @Autowired : Bean으로 등록된 객체(인스턴스)를 클래스에 주입하는데 사용(@Resource, @Inject 등이 있음)
- @Value : @PropertySource에서 지정한 Properties 내의 값을 불러오는 어노테이션
- ApplicationContext: ApplicationContext는 스프링 컨테이너 중 하나로 Bean의 생성과 사용, 관계, 생명 주기등을 관리한다.
- @Bean: @Configuration 클래스의 메서드 레벨에서만 지정이 가능하며, @Bean이 지정된 객체는 컨테이너에 의해 관리되는 Bean으로 등록이 된다. 
- @ConfigurationProperties : 해당 어노테이션은 인자에 prefix 속성을 지정할 수 있으며, @PropertySource에 지정된 파일에서 해당 prefix로 시작하는 설정을 모두 읽어 해당 메서드에 매핑(바인딩)한다. 
- hirakiConfig: HikariCP 객체를 생성한다. (Connection Pool 생성)
- datasource: 데이터 소스 객체를 생성한다. 순수 JDBC는 SQL을 실행하라 때마다 Connection을 맺고 끊는 I/O 작업을 하는데 상당한 리소스를 잡아먹는다. 이러한 문제를 해결하기 위해 JDBC Connection Pool이 등장하게 되었으며 이는 Connection 객체를 생성해 두고 DB에 접근할려는 사용자에게 미리 생성해 둔 Connection을 제공했다가 돌려받는식으로 리소스를 절약한다. DataSource는 이러한 Connection Pool을 지원하기 위한 인터페이스이다.
- sqlSessionFactory: SqlSessionFactory는 DB의 Connection과 SQL 실행에 대한 모든 것을 갖는 역할을 한다. SqlSessionFactoryBean은 Mybatis와 Spring 연동모듈로써 사용되는데 Mybatis의 XML Mapper 위치, 설정파일 위치 등을 지정하고 SqlSessionFactoryBean 자체가 아닌 getObject()메서드가 리턴하는 SqlSessionFactory를 생성한다.
- sqlSessionTemplate :SqlSession을 구현하는 역할을 하며, SqlSession은 DB의 커밋 롤백 등 SQL의 실행에 필요한 모든 메서드를 갖는 객체이다.

## 3. DB 설정 Test

- src/test/java 디렉터리 아래 ApplicationTest.java 안에 다음과 같이 작성하고 실행하면 아래와 같이 같은 Bean을 가리키는 것을 알 수 있다.

```
package com.spring;

import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.context.ApplicationContext;

@SpringBootTest
class ApplicationTests {

	@Autowired
	private ApplicationContext context;
	
	@Autowired
	private SqlSessionFactory sessionFactory;
	
	@Test
	void contextLoads() {
		
	}
	
	@Test
	public void testByApplicationContext() {
		try {
			System.out.println(context.getBean("sqlSessionFactory"));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	// 결과 : org.apache.ibatis.session.defaults.DefaultSqlSessionFactory@3212d853
	
	@Test
	public void testBySqlSessionFactory() {
		try {
			System.out.println(sessionFactory.toString());
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	// 결과 : org.apache.ibatis.session.defaults.DefaultSqlSessionFactory@3212d853

}
```

- 추가로 @Bean 지정시 name을 지정하게 된다면 해당 이름을 통해 bean을 지정할 수 있다.
- 이때 이름을 지정하게 된다면 메서드의 이름으로 Bean을 지정할 수 없기 때문에 getBean을 통해 메서드 이름으로 Bean을 호출하면 `No bean naemd 'sqlSessionTemplate' available` 이 출력되며 오류 발생한디.

```
@Bean(name="testBean")
public SqlSessionTemplate sqlSessionTemplate() throws Exception {
	return new SqlSessionTemplate(sqlSessionFactory());
}

----
@Autowired
private ApplicationContext context;
context.getBean("testBean")
```


## 4. DB CRUD 

### Test Table

```
drop table springmvc.dictionary;

CREATE TYPE yn_check AS ENUM('Y', 'N');

create table springmvc.dictionary (
	idx serial primary key,
	english varchar(200) not null,
	korean varchar(200),
	delete_yn yn_check NOT NULL DEFAULT 'N',
	modified_date timestamp NULL
);


insert into springmvc.dictionary (english, korean, delete_yn, modified_date) values('culture', '문화, 교양', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('experience', '경험', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('education', '교육', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('symbol', '상징', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('effect', '결과, 영향, 효과', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('liberty', '자유', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('affair', '사건, 일', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('comfort', '안락, 위안', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('tradition', '전통, 전설', 'N', now());
insert into springmvc.dictionary (english, korean, delete_yn, modified_date)values('subject', '학과, 주제, 주어', 'N', now());


select * from springmvc.dictionary
```

- 위와 같이 영어 사전 테이블을 작성하며 각각 컬럼은 다음과 같다
- idx: 생성될 때 마다 자동 증가 oracle의 auto increment 와 같은 역할
- english : 영어 (PK)
- korean : 한국어
- delete_yn : 해당 row의 삭제 여부(요즘엔 실제로 데이터를 삭제하는 것이 아닌 이와 같이 표시를 해 기록을 남긴다.)
- modified_date : 수정 일시

### 도메인 클래스 생성
- 테이블 구조화 클래스는 xxxVO, xxxDTO 로 네이밍 짓는다.
- VO는 ReadOnly의 특성을 가지고 있다. 
- com.spring 디렉터리(패키지) 아래 controller, domain, mapper, service 4개의 패키지를 추가한다.
- domain 디렉터리 아래 DictionaryDTO.java 클래스를 생성하고 다음과 같이 작성한다.

```
package com.spring.domain;

import java.time.LocalDateTime;
import lombok.Data;

@Data
public class DictionaryDTO {
	// PK
	private int idx;
	private String english;
	private String korean;
	private String deleteYN;
	private LocalDateTime modifiedDate;

} 
```

### Mapper 인터페이스 생성

- DB와 통신 역할을 하는 Mapper 인터페이스 작성한다. 
- DictionaryMapper.java를 생성하고 다음과 같이 작성한다.

```
package com.spring.mapper;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;

import com.spring.domain.DictionaryDTO;

@Mapper
public interface DictionaryMapper {
	
	// insert
	public int insertDictionary(DictionaryDTO DicParams);
	// delete
	public int deleteDictionary(long idx);
	// update
	public int updateDictionary(DictionaryDTO DicParams);
	// select one
	public DictionaryDTO selectDictionaryDetail(long idx);
	// select all
	public List<DictionaryDTO> selectDictionaryList();
	// select count(*)
	public int selectDictionaryCount();
}
```

- @Mapper: 기존의 스프링은 DAO(Data Access Object) 클래스에 @Repository를 선언하여 해당 클래스가 DB와 통신하는 클래스임을 알렸다. 하지만 Mybatis의 경우 @Mapper만 지정해 주면 XML Mapper에서 메서드 이름과 일치하는 SQL 문을 찾아 실행한다. Mapper영역은 DB와 통신 즉 SQL쿼리를 호출하는것이 전부이면 다른 로직이 필요 없는 인터페이스이다.
- insertDictionary : Dictionary 내 데이터 삽입
- deleteDictionary : Dictionary 내 데이터 PK 인 idx 를 통해 삭제(실질적 삭제가 아닌 delete_yn flag를 통해 삭제 여부만 표시한다.)
- updateDictionary : Dictionary 내 데이터 수정
- selectDictionaryDetail : Dicationry 내 데이터 1개만 select
- selectDictionaryList : Dictionary내 모든 데이터 select
- selectDictionaryCount : Dictioinary 내 데이터 갯수 확인


### XML Mapper 생성

- resource/mappers/postgre/DictionaryMapper.xml 파일 생성후 다음과 같이 작성

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.spring.mapper.DictionaryMapper">

	<sql id="dictionaryCol">
		english
		, korean
		, delete_yn
		, modified_date
	</sql>

	<resultMap type="DictionaryDTO" id="DicMap">
		<id property="idx" column="idx"/>
		<result property="english" column="english"/>
		<result property="korean" column="korean"/>
		<result property="deleteYN" column="delete_yn"/>
		<result property="modifiedDate" column="modified_date"/>
	</resultMap>
	
	<insert id="insertDictionary" parameterType="DictionaryDTO">
		INSERT INTO dictionary(
			<include refid="dictionaryCol"/>
		) VALUES (
			#{english}
			, #{korean}
			, coalesce(#{deleteYN}::springmvc.yn_check, springmvc.yn_check 'N')
			, NOW()
		)
	</insert>
	
	<update id="deleteDictionary" parameterType="long">
		UPDATE dictionary
		SET
			delete_yn = 'Y'
			, modified_date = NOW()
		WHERE
			idx = #{idx}
	</update>
	
	<update id="updateDictionary" parameterType="DictionaryDTO">
		UPDATE springmvc.dictionary
		SET
			english = #{english}
			, korean = #{korean}
			, delete_yn = #{deleteYN}::springmvc.yn_check
			, modified_date = NOW()
		WHERE
			idx = #{idx}
	</update>
	
	<select id="selectDictionaryDetail" parameterType="long" resultMap="DicMap">
		SELECT * 
		FROM dictionary 
		WHERE
			delete_yn = 'N'
		AND
			idx = #{idx}
	</select>
	
	<select id="selectDictionaryList" resultMap="DicMap">
		SELECT * 
		FROM dictionary
		WHERE
			delete_yn = 'N'
		ORDER BY idx
	</select>
	
	<select id="selectDictionaryCount" resultType="int">
		SELECT count(*)
		FROM dictionary
		WHERE
			delete_yn = 'N'
	</select>
</mapper>
```

- Mapper : Mybatis에서 SQL쿼리문에 정의되어 있는 파일을 XML Mapper라 한다. 태그의 시작과 끝 사이에는 쿼리문과 관련되니 여러개의 태그가 존재하며, mapper 태그를 여는 부분의 namespace 속성에는 Mapper.java 인터페이스의 경로가 지정된다. 
- sql : mybatis는 sql태그를 이용해서 태그의 시작과 끝사이에 공통으로 사용되거나 반복적으로 사용되는 SQL 조각을 정의할 수 있다. 예를 들어 Select 절에 * 을 이용해 전체를 조회할 수 있지만 서브쿼리나 조인을 이용하면 컬럼을 일일이 Select 절에 지정해야 하는 경우가 발생한다. 이럴경우 더욱 효율적으로 SQL 문을 처리할 수 있도록 위와 같이 컬럼 조각을 정의할 수 있다.
- include : sql 태그에 정의한 sql 조각을 사용하기 위한 태그
- parameterType : 쿼리 실행에 필요한 파라미터의 타입을 속성에 지정
- resultType : 쿼리 실행 결과를 매핑할 타입을 지정한다. 
- #{} : 파라메터 표현식
- resultMap : DTO 와 DB의 컬럼사이의 이름 차이가 있을 경우 이를 통해 매핑 시켜 준다.

### ContextDataSource.java 내 alias 및 mapper 연동 정의

- application.properties에 다음과 같이 추가

```
mybatis.config-location=classpath:/mybatis/config/mybatis-config.xml
```

- resources/mybatis/config/mybatis-config.xml 생성 및 다음과 같이 작성

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

	<!-- 연결 정보 따로 생성 후 불러오기
	<properties resource="/driver.properties"></properties>
	 -->
	 
	 <!-- mybatis 설정과 관련된 기본 세팅 선언 -->
    <settings>
    	<!-- 
    	<setting name="jdbcTypeForNull" value="NULL" /> 			# mybatis로 들어오는 데이터가 null로 전달이 되었으면 빈칸으로 인지말고 null로 인식해라 
    	<setting name="safeRowBoundsEnabled" value="false" />		# 중첩 구문내 RowBound사용허가 여부
    	<setting name="localCacheScope" value="SESSION" />			# 로컬 캐시 사용 여부(SESSION:세션을 사용해서 모든 쿼리를 캐시 한다.)
    	<setting name="lazyLoadTriggerMethods" value="equals, clone, hashCode, toString" /> # 지연 로딩을 야기하는 객체의 메소드 명을 명시한다.
    	-->
        <setting name="cacheEnabled" value="true" />				<!-- mybatis cahce 사용 여부 -->
        <setting name="lazyLoadingEnabled" value="false" />			<!-- 지연로딩 사용 여부 -->
        <setting name="multipleResultSetsEnabled" value="true" />	<!-- 한 개의 구문에서 여러개의 ResultSet을 허용할지 여부 -->
        <setting name="useColumnLabel" value="true" />				<!-- 컬럼명 대신 컬럼 라벨을 사용할지 여부 결정 -->
        <setting name="useGeneratedKeys" value="false" />			<!-- 생성키에 대한 JDBC 지원 허용 여부 -->
        <setting name="defaultExecutorType" value="SIMPLE" />		<!-- 디폴트 Excutor 설정 (SIMPLE은 특별히 동작하는것이 없다는 것.) -->
        <setting name="defaultStatementTimeout" value="60000" />	<!-- DB응답 타임아웃 설정 -->
        <setting name="mapUnderscoreToCamelCase" value="true"/>		<!-- 전통적 DB 컬럼명을 JAVA의 Camel 표기법으로 자동 매핑 설정 -->
    </settings>
    
	<typeAliases>
		<typeAlias type="com.spring.domain.DictionaryDTO" alias="DictionaryDTO"/>
	</typeAliases>
	
	<!-- environments: DB에 연결할 설정에 대한 정보 선언 -->
	<!-- ContextDatSource.java의 datasource와 dataPostgreSessionFactory로 해당 environment 대체 -->  
	
	<!-- mapper : DB에 사용되는 쿼리문들을 담은 Mapper 파일을 등록 선언 -->
	<!-- ContextDataSource.java에 정의한 setMapperLocations로 대체 -->
	
	
	<!-- mybatis가 PreparedStatement에 파라미터를 설정하고 ResultSet에서 값을 가져올 때마다 TypeHandler라는 적절한 자바 타입의 값을 가져오기 위해 사용된다. -->
	<!-- 3.4.5 버전부터, MyBatis는 JSR-310(Date 와 Time API) 를 기본적으로 지원한다. -->
	<!-- 즉, 예를들어 DB상에서는 01, 02, 03 이렇게 저장되어 있지만 RESTAPI로 전달할 때 경영-01, 회계-02, 전산-03 이런식으로 코드를 변환하고 싶을 때 사용한다. -->
	<!-- https://mybatis.org/mybatis-3/ko/configuration.html#typeHandlers 확인 -->
    <typeHandlers>
        <!--  java.sql.Timestamp 를 java.util.Date 형으로 반환 -->
        <typeHandler javaType="java.sql.Timestamp" handler="org.apache.ibatis.type.DateTypeHandler" />
        <typeHandler javaType="java.sql.Time" handler="org.apache.ibatis.type.DateTypeHandler" />
        <typeHandler javaType="java.sql.Date" handler="org.apache.ibatis.type.DateTypeHandler" />
    </typeHandlers>
  
</configuration>
```



- com/spring/config/ContextDataSource.java 파일 다음과 같이 추가

```
package com.spring.config;

...

@Configuration
@PropertySource("classpath:/application.properties")
public class ContextDataSource {
	
	@Value("${mybatis.config-location}")
	private String mybatisConfigLocation;

	...

	@Bean
	@ConfigurationProperties(prefix="mybatis.configuration")
	public org.apache.ibatis.session.Configuration mybatisConfig() {
		return new org.apache.ibatis.session.Configuration();
	}
	
	@Bean
	public SqlSessionFactory sqlSessionFactory() throws Exception {
		SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
		sqlSessionFactoryBean.setDataSource(dataSource());
		sqlSessionFactoryBean.setMapperLocations(applicationContext.getResources("classpath:/mapper/**/*Mapper.xml"));
		sqlSessionFactoryBean.setTypeAliasesPackage("com.spring.domain");
		sqlSessionFactoryBean.setConfiguration(mybatisConfig());
		return sqlSessionFactoryBean.getObject();
	}

	...
}
```

- setMapperLocation : getResource메서드의 인자로 지정된 패턴에 포함되는 XML Mapper를 인식하도록 하는역할
- setTypeAliasPackage : XML Mapper에서 도메인명만 사용해서 정의하기 위해 별칭 정의
- mybatisConfig: application.properties에서 mybatis.configuration으로 시작하는 모든 설정을 읽어 Bean으로 등록
- setConfiguration : mybatis 관련 설정 Bean을 설정파일로 지정해 등록


### CRUD 테스트

-src/test/java/com/spring/MapperTest.java를 만들고 다음과 같이 작성한다.

```
package com.spring;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import com.spring.domain.DictionaryDTO;
import com.spring.mapper.DictionaryMapper;

@SpringBootTest
class MapperTest {
	
	@Autowired
	private DictionaryMapper dictionaryMapper;
	
	//Insert
	@Test
	public void testOfInsert() {
		DictionaryDTO dicParams = new DictionaryDTO();
		dicParams.setEnglish("hello");
		dicParams.setKorean("안녕");
		int result = dictionaryMapper.insertDictionary(dicParams);
		System.out.println("Insert Result " + result);
	}
	// 결과 : Insert Result 1
	// 11 hello	안녕	N	2022-01-19 14:23:24.337

	// Select
	@Test
	public void testOfSelect() {
		DictionaryDTO dic = dictionaryMapper.selectDictionaryDetail((long) 1);
		try {
			String dicJson = new ObjectMapper().registerModule(new JavaTimeModule()).writeValueAsString(dic);
			System.out.println(dicJson);
		}catch (JsonProcessingException e) {
			e.printStackTrace();
		}
	}
	// 결과 
	// {"idx":1,"english":"culture","korean":"문화, 교양","deleteYN":"N","modifiedDate":[2022,1,19,15,4,20,737450000]}

	// Insert Update
	@Test
	public void testOfUpdate() {
		DictionaryDTO dicParams = new DictionaryDTO();
		dicParams.setIdx((long) 11);
		dicParams.setEnglish("hello");
		dicParams.setKorean("안녕!!!");
		
		int result = dictionaryMapper.updateDictionary(dicParams);
		if (result == 1) {
			DictionaryDTO dic = dictionaryMapper.selectDictionaryDetail((long) 11);
			System.out.println(dic);
		}
	}
	// 결과
	// DictionaryDTO(idx=11, english=hello, korean=안녕!!!, deleteYN=N, modifiedDate=2022-01-19T15:09:03.645374)

	// delete 
	@Test
	public void testOfDelete() {
		int result = dictionaryMapper.deleteDictionary((long) 11);
		if (result == 1) {
			DictionaryDTO dic = dictionaryMapper.selectDictionaryDetail((long) 11);
			System.out.println(dic);
		}
	}
	// 결과 
	// null

}
```

- @autowired : Autowired를 이용해서 DictionaryMapper 빈을 주입한다.
- selectdetail : Jackson 라이브러리를 이용해서 JSON 문자열로 변경한 뒤에 콘솔에 출력.
- result에 들어가게되는 int 값을 해당 쿼리를 수행한 row 수가 된다.