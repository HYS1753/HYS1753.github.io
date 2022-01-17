---
author_profile: true
date: 2022-01-01
title: "Spring MVC + Gradle Project 4 (DB 사용 1)"
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

# Spring MVC + Gradle Project 4 (DB 사용 1)

---

## 0. Data Access Layer(Spring-Java)

```
Application       JDBC Interface          JDBC Implementations        Persistence Layer

    DAO     →     JDBC Template      →      JDBC Driver          →        Database
                        ↓
                    Data Source(Configuration for connection)
```
##  **1. JPA(Java Persistence API)**
자바 ORM 기술에 대한 표준 명세로 JAVA에서 제공하는 API이다. 스프링에서 제공하는 API가 아니다.</p>
JPA는 특정 기능을 하는 라이브러리가 아닌 인터페이스이며, orm을 사용하기 위해 만들어진 인터페이스이다.</p>
ORM이기 때문에 Java 클래스와 DB 테이블을 매핑한다.(SQL을 매핑하는 것이 아님.)</p>
JPA는 어플리키에션과 JDBC 사이에서 동작하며, 개발자가 JPA를 사용하면 JPA 내부에서 JDBC API를 사용하여 SQL을 호출해 DB와 통신한다.</p>

- JPA 특징
  - 데이터를 객체지향적으로 관리할 수 있기 때문에 개발자는 비지니스 로직에 집중할 수 있고 객체 지향 개발이 가능하다.
  - 자바 객체와 DB 테이블 사이의 매핑 설정을 통해 SQL을 생성한다.
  - 객체를 통해 쿼리를 작성할 수 있는 JPQL(Java Persistence Query Language)를 지원한다.
  - JPA는 성능 향상을 위해 지연로딩이나 즉시로딩과 같은 몇가지 기법을 제공하는데 이것을 잘 활용하면 SQL을 직접 사용하는 것과 유사한 성능을 발휘할 수 있다.

- Spring에서의 JPA 구조
  - Client ↔ `<DTO>` ↔ Controller(Servlet) ↔ `<DTO>` ↔ Service ↔ `<DTO>` ↔ Repository(DAO) ↔ `<Entity class>` ↔ DB
  - **Entity(Domain)**
    - DB의 테이블에 존재하는 Column들을 필드로 가지는 객체를 말한다. 
    - Entity는 DB의 테이블과 1:1로 대응되며, 따라서 테이블이 가지지 않는 컬럼을 필드로 가져서는 안된다.
    - 또한, Entity 클래스는 다른 클래스를 상속받거나 인터페이스의 구현체여서는 안된다.

  - **DAO(Data Access Object)**
    - DAO는 실제로 DB에 접근하는 객체이다. 
    - Service와 DB를 연결하는 고리의 역할을 한다.
    - DAO는 개발자가 직접 코딩해야 되는 부분이다. (SQL을 이용해 DB에 접근한 후 적절한 CRUD API 제공한다.)
    - "Object"단위 - (SQL을 이용한 CRUD) - DB의 "Recode" 단위로 저장한다.
      - 따라서 Object와 Recode간의 불일치가 발생할 수 있으며 이러한 문제는 개발자가 해결해야 한다.

  - **DTO(Data Transfer Object)**
    - 계층간 데이터 교환을 위한 객체(Java Bean)이다.
    - DB에서 데이터를 얻어 Service나 Controller 등으로 보낼 때 사용하는 객체를 말한다.
    - 로직을 가지고 있지 않은 순수한 데이터 객체이며, getter/setter 메서드만 가지고 있다.
    - 하지만, DB에서 꺼낸 값을 임의로 변경할 필요가 없기 때문에 DTO 클래스에는 setter가 없고 생성자에서 값을 할당한다.
    - `Service(Business Tier) ↔ Controller(Presentation Tier)`

  - **VO(Value Object)**
    - 말 그대로 값 객체 라는 의미를 가지고 있으며 VO의 핵심 역할을 equals(), hashcode()를 오버라이딩 하는 것이다.
    - VO 내부에 선언된 속성(Field)의 모든 값들이 VO 객체 마다  값이 같아야 똑같은 객체라고 판별한다.
    - VO는 getter, setter를 가질 수 있으며, 테이블 내에 있는 속성 외에 추가적인 속성을 가질 수 있으며, 여러 테이블에 대한 공통 속성을 모아 만든 BaseVO클래스를 상속받아 사용할 수 있다.
    - VO는 DTO와 동일한 개념이지만 read only 속성을 갖는다.
    - VO는 특정한 비지니스 값을 담는 객체이고 DTO는 Layer간 통신 용도로 오고가는 객체를 의미한다.
    - `Service(Business Tier) ↔ Controller(Presentation Tier)`

  - **Entity vs DTO**
    - Entity와 DTO를 분리하는 이유는 Entity의 값이 변하면 Repository 클래스의 Entity Manager의 flush가 호출될 때 DB에 값이 반영되고, 이는 다른 로직들에게로 영향을 미치기 때문
    - 즉, DTO는 View와 통신을 하면서 필연적으로 데이터의 변경이 빈번히 일어나기 때문에 DTO 클래스를 분리해 일관성을 유지한다.


  - **VO vs DTO**
    - VO와 DTO는 Data를 전달하는 객체로 동일한 개념이지만 VO는 좀 더 특정한 Business Logic의 결과 값을 담는 객체이다.
    - equals, hashCode Method를 구현하여 특정 중요한 Data를 전달할 때는 VO를 생성하여 이를 동일한 객체 비교까지 필요한 Logic 내에서 주로 사용한다.
    - DTO는 Layer간 단순 통신 용도로 오고가는 Data 전달 객체이다. 조금 더 포괄적으로 제한 없이 사용할 수 있는 객체로 민감하지 않거나 해당 객체 안의 값들을 통해 동일한 객체임을 비교하는 로직에 사용되지 않을 때는 단순히 DTO 만 사용해도 된다.

- ORM vs SQL Mapper
  - ORM은 DB 테이블을 자바 객체로 매핑함으로써 객체간의 관걔를 바탕으로 SQL을 자동으로 생성하지만, Mapper는 SQL을 명시해 주어야 한다.
  - ORM은 RDB의 관계를 Object에 반영하는 것이 목적이라면, Mapper는 단순히 필드를 매핑시키는 것이 목적이라는 점에서 지향점의 차이점이 있다.
  - ORM(Object-Relation Mapping/객체-관계-매핑)
    - DB데이터 ← mapping → Object 필드
      - 객체를 통해 간접적으로 DB를 다룬다.
    - 객체와 DB의 데이터를 자동으로 매핑해준다.
      - SQL쿼리가 아니라 메서드로 데이터를 조작할 수 있다.
      - 객체간 관계를 바탕으로 SQL을 자동으로 생성한다.
    - Persistant API라 할 수 있다. 
    - Ex. JPA, Hibernate
  - SQL Mapping
    - SQL ← mapping → Object 필드
    - SQL 문으로 직접 DB를 조작한다.
    - Ex. Mybatis, jdbcTemplate

## **2. Mybatis - SQL Mapping**

객체지향 언어인 자바의 관계형 DB 프로그래밍을 보다 쉽게 도와주는 프레임워크이다. </p>
즉, Mybatis는 JDBC를 보다 편하게 사용하기 위해 개발된 SQL Mapper 방식의 프레임워크이다. 

- Mybatis 특징
  - **SQL문이 코드로부터 완전히 분리된다.**
    - 기존에는 DAO 파일에 모든 SQL문을 작성하였다. 하지만 Mybatis에서는 Mapper파일에 SQL코드를 입력해 놓고 DAO파일에서 필요할 대마다 가져와서 사용할 수 있다.
    - XML, 어노테이션 방식으로 SQL 문을 별도로 처리하는 작업이 가능하고, 필요한 경우 두가지 방식을 혼용해서 사용하는 것도 가능하다.
    - 예로는 복잡하고 긴 쿼리의 경우 xml 사용 짧고 간단한 쿼리는 어노테이션 사용 
  - **생산성**
    - 간결한 코드 처리가 가능하다.
    - JDBC작업을 위한 반복적인 코드(Try~Catch~Finally, PreparedStatement, ResultSet)을 직접 작성하지 않아도 된다.
  - **Spring과의 연동으로 자동화된 처리**
    - 스프링과 Mybatis를 연계하는 Mybatis-Spring 라이브러리를 이용하면 개발자는 직접 SQL문의 호출 없이도 원하는 결과를 얻을 수 있다.
    - 이 때문에 Mybatis는 단독으로 사용하는 것 보다는 Spring과 연계해서 사용하는 편이 오히려 코드의 양을 줄일 수 있다.
  - **유지보수성 향상(동적 SQL을 이용한 제어 기능)**
    - Mapper 파일에만 SQL코드를 입력하고 나중에 SQL 코드를 변경할 때 이곳에서만 유지보수하면 DAO에서는 아무런 영향이 없다.
    - Mybatis는 기본적으로 SQL문을 처리하지만, 약간의 제어문이나 loop문을 사용할 수 있따. 이를 통해 SQL 과 관련된 처리를 java코드에서 분리할 수 있다.

- Mybatis 구성
  - **Mybatis 환경설정 파일**
    - SqlSessionConfig.xml
    - Mybatis가 JDBC코드를 실행하는데 필요한 전반에 걸친 설정을 한다.
    - TypeAlias 설정: 사용할 모델 클래스에 대한 별칭 설정
    - Environment 설정: Database 접속 설정
    - Mapper 설정: Mapper 설정파일 등록
  - **Mybatis Mapper 설정 파일**
    - sql문과 관련된 설정을 하는 파일로 Mybatis 설정파일에 등록해 사용한다.
    - 주요 구성요소
      - SQL문 등록 태그(Parameter, Result, SQL문)
      - select 결과 처리 설정(Resultmap)

- 일반적인 스프링 웹 프로젝트의 구성
  - Presentation Layer : UI를 담당하는 구성요소들이 들어간다. 웹인지 앱인지에 따라 사용되는 기술이 변경된다.
  - Business Layer : 서비스 계층이라고도 하며, 고객의 요구사항을 반영하는 계층이다. 사용자의 환경이 아닌 기능적인 요구사항을 구현한 곳으로 즉, 비지니스 계층은 어떤 형태의 데이터가 필요하고 반환될 것인지 결정
  - Data Access Layer : 흔히 Persistence Layer라고도 불리며 데이터 처리를 전문으로 담당하는 계층이다. 

## **3. Mybatis 설정 및 사용**

### **1. build.gradle 수정**
  
```
// commons-dbcp : Java Apache Commons Database Connection Pool
//			      Connection pool(연결 풀) 사용 목적은 DB Connection 정보를 캐시(메모리 영역)에 저장/관리하여 애플리케이션 단에서  
//                DB Connection 정보가 필요할 때마다 Connection pool에서 연결 정보를 가지고 와 사용하도록 하는 것이다. 
//                connection pool에서 Connection 정보를 관리하기 때문에 DB에 연결하기 위한 연결 정보 생성 시간이 없어 DB Connection을 위한 시간이 월등히 줄어든다. 
//				  버전 별 요구 사항.
// 				  DBCP 2.9.0 compiles and runs under Java 8 only (JDBC 4.2)
// 				  DBCP 2.8.0 compiles and runs under Java 8 only (JDBC 4.2)
// 				  DBCP 2.7.0 compiles and runs under Java 8 only (JDBC 4.2)
// 				  DBCP 2.6.0 compiles and runs under Java 8 only (JDBC 4.2)
// 				  DBCP 2.5.0 compiles and runs under Java 8 only (JDBC 4.2)
// 				  DBCP 2.4.0 compiles and runs under Java 7 only (JDBC 4.1)
// 				  DBCP 1.4 compiles and runs under Java 6 only (JDBC 4)
// 				  DBCP 1.3 compiles and runs under Java 1.4-5.0 only (JDBC 3)
    
dependencies {
  // mybatis 연동위한 모듈 import
  compile "org.mybatis:mybatis:3.5.7"
  compile "org.mybatis:mybatis-spring:2.0.6" // Spring과 Mybatis 를 연결해주는 프레임워크
  compile "org.springframework:spring-jdbc:5.3.10" // Spring 용 JDBC
  complie "org.apache.commons:commons-dbcp2:2.9.0" // DataBase Connection Pool
  compile "org.postgresql:postgresql:42.2.22"		// PostgreSQL JDBC Driver
}
```

### **2. PostgreSQL과 연결을 담당하는 DataSource 설정**
1. 스프링과 Mybatis를 같이 사용하는 경우에는 주로 스프링의 설정으로 JDBC 연결을 처리하려는 경우가 많기 때문에 위에서 추가한 spring-jdbc 모듈의 클래스를 이용해 applicationContext.xml에 다음과 같이 datasource를 추가한다.
2. DataSource에는 JDBC의 커넥션을 처리하는 기능을 가지고 있기 때문에 DB와 연동 작업에 반드시 필요하다.
3. 다음은 DataSource 설정하는 코드이며 `${spring.profiles.active}`부분은 WAS(Tomcat, Resin) 등에서 `-DSpring.profiles.active`를 통해 설정해준 변수이다. 이와 같이 활성 상태를 변경하는 이유는 개발, 운영, 로컬 등의 다양한 환경에서 프로젝트르 실행시키기 위함이다.
4. `/resource/config/properties/${spring.profiles.active}/dataSource.xml` 생성 및 작성

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
    <properties>
      <entry key="jdbc.postgresql.driverClassName">org.postgresql.Driver</entry>
      <entry key="jdbc.postgresql.url">jdbc:postgresql://127.0.0.1:5432/springmvc</entry>
      <entry key="jdbc.postgresql.username">testuser</entry>
        <entry key="jdbc.postgresql.password">testuser</entry>
    </properties>
    ```

5. `/resource/config/spring/${spring.profiles.active}/context-datasource.xml` 생성 및 작성
      
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:util="http://www.springframework.org/schema/util"
          xmlns:jee="http://www.springframework.org/schema/jee"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                      http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd
                      http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.3.xsd">
        
      <!-- Database의 URL, PW, USER 등의 정보가 저장된 datasource.xml파일을 불러옴. -->
      <util:properties id="datasourcProperties" location="classpath*:/config/properties/${spring.profiles.active}/datasource.xml" />
        
      <!-- PostgreSQL용 DataSource Bean 생성 -->
      <!-- Class 는 common-dbcp를 사용해 해당하는 properties를 통해 connection pool을 만든다. -->
      <bean id="dataSourcePostgre" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="driverClassName" value="#{datasourcProperties['jdbc.postgresql.driverClassName']}"/>
        <property name="url" value="#{datasourcProperties['jdbc.postgresql.url']}"/>
        <property name="username" value="#{datasourcProperties['jdbc.postgresql.username']}"/>
        <property name="password" value="#{datasourcProperties['jdbc.postgresql.password']}"/>		
      </bean>
    </beans>
    ```

### **3. DataSource 테스트 진행**
1. 스프링은 하나의 설정에 문제가 있다면 정상적으로 로딩이 되지 않기 때문에 최대한 빨리 변경된 설정에 대해서 테스트를 진행해야만 한다.
2. 스프링은 별도의 test라이브러리를 활용해 개발자가 손쉽게 테스트 할 수 있는 방법을 제시해 준다.
3. src/test/java/Gradle_SpringMVC/ 디렉터리 아래 testDataSource.java 파일을 만들어준다.
4. testDataSource.java에 사용될 인스턴스 변수를 자동으로 생성해주는 @inject 어노테이션 모듈을 사용하기위해 build.gradle에 다음과 같은 모듈을 추가해 준다.

    ```
    dependencies {
      // test 
      compile "javax.inject:javax.inject:1"   // java 파일에 사용될 인스턴스 변수를 자동으로 생성해주는 @Inject 어노테이션 모듈 추가
      compile "junit:junit:4.13.2"			// Unit TEST 프레임워크
      compile "org.springframework:spring-test:5.3.10"	// Spring Framework test 도구
    }
    ```

5. testDataSource.java 작성

    ```
    package Gradle_SpringMVC;

    import java.sql.Connection;

    import javax.inject.Inject;
    import javax.sql.DataSource;

    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.test.context.ContextConfiguration;
    import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
    import org.springframework.test.context.web.WebAppConfiguration;


    @WebAppConfiguration
    // @RunWith 어노테이션은 현재 테스트 코드를 실행할 때 스프링이 로딩되도록 하는 부분이다.
    @RunWith(SpringJUnit4ClassRunner.class)
    // @ContextConfiguration 어노테이션은 locations 속성 경로의 xml 파일을 이용해서 스프링이 로딩되도록 한다.
    @ContextConfiguration(locations = {"classpath:config/spring/dev/context-datasource.xml"})
    public class testDataSource {
        
      // @Inject 어노테이션 처리된 DataSource를 스프링이 생성해서 주입해 주도록 해 개발자가 객체 생성 혹은 다른 작업을 하지 않도록 도와준다.
      @Inject
      private DataSource ds;
        
      @Test
      public void testConnection() throws Exception {
        try {
          Connection con = ds.getConnection();
          System.out.println(con);
        }catch (Exception e){
          e.printStackTrace();
        }
      }

    }
    ```

6. 실행 방법 : 해당 java 파일 우클릭 후 run as - JUnit test 클릭
7. 출력 결과 : `1489946715, URL=jdbc:postgresql://127.0.0.1:5432/springmvc, PostgreSQL JDBC Driver` 연동 확인
8. 만약 오류 발생 시 dataSource.xml 파일이 spring.profiles.active 설정 때문일 수도 있기 때문에 해당하는 경로로 직접 입력해준다. 

### **4. Mybatis 연결**
1. DataSource의 연결은 Mybatis의 설정과 관계가 있으므로 먼저 설정하고 테스트를 진행해 보아야 한다.
2. 위에서 테스트 결과가 정상적으로 설정되었다면 이후의 작업은 Mybatis와 MySQL을 연동시키는 작업이다.
3. SqlSessionFactory 객체 설정
   1. Mybatis와 스프링 연동 작업에서의 핵심은 connection을 생성하고 처리하는 SqlSessionFactory의 존재이다.
   2. SqlSessionFactory는 데이터베이스와의 연결과 SQL의 실행에 대한 모든 것을 가진 중요한 객체이다.
   3. 스프링을 이용할 때 SqlSessionFactory를 생성해 주는 특별한 객체를 설정해 주는데 SqlSessionFactoryBean이라는 클래스이다.
4. `/resource/config/spring/${spring.profiles.active}/context-mapper-postgre.xml` 파일 생성 후 작성

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xmlns:jee="http://www.springframework.org/schema/jee"
          xmlns:p="http://www.springframework.org/schema/p"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
                              http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.0.xsd
                              http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
                              http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd">
                                
      <!-- org.mybatis.spring.SqlSessionFactoryBean 는 mybatis-spring 모듈에 있는 클래스 이다. -->
      <bean id="dataPostgreSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSourcePostgre"/>
      </bean>

    </beans>
    ```

### **5. Mybatis 연결 테스트**
1. 3번의 DataSource 테스트와 마찮가지로 같은 위치에 다음과 같은 testMybatis.java 파일을 생성 및 작성하고 테스트를 진행한다.

    ```
    package Gradle_SpringMVC;

    import javax.inject.Inject;

    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.test.context.ContextConfiguration;
    import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(locations = {"classpath:config/spring/dev/context-mapper-postgre.xml", "classpath:config/spring/dev/context-datasource.xml"})
    public class testMybatis {
      @Inject
      private SqlSessionFactory sqlSessionFactory;
        
      @Test
      public void testFactory() {
        System.out.println(sqlSessionFactory);
      }
        
      @Test
      public void testSession() throws Exception{
        try {
          SqlSession sqlSession = sqlSessionFactory.openSession();
          System.out.println(sqlSession);
        }catch (Exception e){
          e.printStackTrace();
        }
      }
       
    }
    ```
  
2. 출력결과가 다음과 같이 나온다면 정상적으로 DB와 Session이 이루어 진 것이다.

    ```
    org.apache.ibatis.session.defaults.DefaultSqlSessionFactory@35342d2f
    org.apache.ibatis.session.defaults.DefaultSqlSession@7159a5cd
    ```

### **6. Mybatis-config.xml 설정**

1. Mybatis-config.xml 파일 생성 및 작성
   1. `/resource/config/mybatis/config 디렉터리를 생성하고 하위에 mybatis-config.xml 파일을 생성한다.
   2. 생성한 xml 파일에 다음과 같이 작성한다.
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
          <!--  -->
        </settings>
          
        <!-- TypeAlias를 사용하면 Mapper.xml(xxxDAO.xml)에서 resultType을 alias로 지정 가능 -->
        <!-- EX -->
        <!-- <typeAlias type="com.example.vo.TestVO" alias="TestVO" /> 로 해당 mybatis-config에서 지정하게 된다면, Mapper.xml에서 -->
        <!-- <select id="selectMenu" resultType="com.example.vo.TestVO"> 를 <select id="selectMenu" resultType="TestVO"> 로 표현 가능하다.-->
        <typeAliases>
          <!--  -->
        </typeAliases>

        <!-- environments: DB에 연결할 설정에 대한 정보 선언 -->
        <!-- context-datasource.xml의 datasource와 context-mapper-postgre.xml에 정의한 dataPostgreSessionFactory로 해당 environment 대체 -->  
        
        <!-- mapper : DB에 사용되는 쿼리문들을 담은 Mapper 파일을 등록 선언 -->
        <!-- context-mapper-postgre.xml에 정의한 dataPostgreSessionFactory의 mapperLocations property로 대체 -->
        
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
   3. Mybatis Setting 관련 설정

      |설정|설명|사용가능한 값들|디폴트|
      |-|-|-|-|
      |cacheEnabled|설정에서 각 매퍼에 설정된 캐시를 전역적으로 사용할지 말지에 대한 여부|true / false|true|
      |lazyLoadingEnabled|지연로딩을 사용할지에 대한 여부. 사용하지 않는다면 모두 즉시 로딩할 것이다. 이 값은 fetchType 속성을 사용해서 대체할 수 있다.|true / false|false|
      |aggressiveLazyLoading|활성화되면 모든 메서드 호출은 객체의 모든 lazy properties 을 로드한다. 그렇지 않으면 각 property 가 필요에 따라 로드된다. (lazyLoadTriggerMethods 참조).|true / false|false (3.4.1 부터 true)|
      |multipleResultSetsEnabled|한 개의 구문에서 여러 개의 ResultSet을 허용할지의 여부(드라이버가 해당 기능을 지원해야 함)|true / false|true|
      |useColumnLabel|칼럼명 대신에 칼럼라벨을 사용. 드라이버마다 조금 다르게 작동한다. 문서와 간단한 테스트를 통해 실제 기대하는 것처럼 작동하는지 확인해야 한다.|true / false|true|
      |useGeneratedKeys|생성키에 대한 JDBC 지원을 허용. 지원하는 드라이버가 필요하다. true로 설정하면 생성키를 강제로 생성한다. 일부 드라이버(예를들면, Derby)에서는 이 설정을 무시한다.|true / false	|False|
      |autoMappingBehavior|마이바티스가 칼럼을 필드/프로퍼티에 자동으로 매핑할지와 방법에 대해 명시. PARTIAL은 간단한 자동매핑만 할뿐 내포된 결과에 대해서는 처리하지 않는다. FULL은 처리가능한 모든 자동매핑을 처리한다.|NONE, PARTIAL, FULL|PARTIAL|
      |autoMappingUnknownColumnBehavior|자동매핑 대상 중 알 수 없는 칼럼(이나 알 수 없는 프로퍼티 타입)을 발견했을 때 행위를 명시`NONE: 아무것도 하지 않음`, `WARNING: 경고 로그를 출력('org.apache.ibatis.session.AutoMappingUnknownColumnBehavior'의 로그레벨은 WARN이어야 한다.)`, `FAILING: 매핑이 실패한다. (SqlSessionException예외를 던진다.)`|NONE, WARNING, FAILING|NONE|
      |defaultExecutorType|디폴트 실행자(executor) 설정. SIMPLE 실행자는 특별히 하는 것이 없다. REUSE 실행자는 PreparedStatement를 재사용한다. BATCH 실행자는 구문을 재사용하고 수정을 배치처리한다.|	SIMPLE REUSE BATCH|SIMPLE|
      |defaultStatementTimeout|데이터베이스로의 응답을 얼마나 오래 기다릴지를 판단하는 타임아웃을 설정|양수|설정되지 않음(null)|
      |defaultFetchSize|조회결과를 가져올때 가져올 데이터 크기를 제어하는 용도로 드라이버에 힌트를 설정 이 파라미터값은 쿼리 설정으로 변경할 수 있다.|양수|설정하지 않음(null)|
      |defaultFetchSize|결과를 가져오는 크기를 제어하는 힌트처럼 드라이버에 설정한다. 이 파라미터는 쿼리설정으로 변경할 수 있다.|양수|셋팅되지 않음(null)|
      |defaultResultSetType|Specifies a scroll strategy when omit it per statement settings. (Since: 3.5.2)|FORWARD_ONLY / SCROLL_SENSITIVE / SCROLL_INSENSITIVE / DEFAULT(same behavior with 'Not Set')|Not Set (null)|
      |safeRowBoundsEnabled|중첩구문내 RowBound사용을 허용 허용한다면 false로 설정|true / false|False|
      |safeResultHandlerEnabled|중첩구문내 ResultHandler사용을 허용 허용한다면 false로 설정|true / false|True|
      |mapUnderscoreToCamelCase|전통적인 데이터베이스 칼럼명 형태인 A_COLUMN을 CamelCase형태의 자바 프로퍼티명 형태인 aColumn으로 자동으로 매핑하도록 함|true / false|False|
      |localCacheScope|마이바티스는 순환참조를 막거나 반복된 쿼리의 속도를 높히기 위해 로컬캐시를 사용한다. 디폴트 설정인 SESSION을 사용해서 동일 세션의 모든 쿼리를 캐시한다. localCacheScope=STATEMENT 로 설정하면 로컬 세션은 구문 실행할때만 사용하고 같은 SqlSession에서 두 개의 다른 호출 사이에는 데이터를 공유하지 않는다.|SESSION / STATEMENT|SESSION|
      |jdbcTypeForNull|JDBC타입을 파라미터에 제공하지 않을때 null값을 처리한 JDBC타입을 명시한다. 일부 드라이버는 칼럼의 JDBC타입을 정의하도록 요구하지만 대부분은 NULL, VARCHAR 나 OTHER 처럼 일반적인 값을 사용해서 동작한다.|JdbcType 이늄. 대부분은 NULL, VARCHAR 나 OTHER 를 공통적으로 사용한다.	|OTHER|
      |lazyLoadTriggerMethods|지연로딩을 야기하는 객체의 메소드를 명시|메소드 이름을 나열하고 여러 개일 경우 콤마(,) 로 구분|equals,clone,hashCode,toString|
      |defaultScriptingLanguage|동적으로 SQL을 만들기 위해 기본적으로 사용하는 언어를 명시|타입별칭이나 패키지 경로를 포함한 클래스명|org.apache.ibatis.scripting.xmltags.XMLLanguageDriver|
      |defaultEnumTypeHandler|Enum에 기본적으로 사용되는 TypeHandler 를 지정합니다. (3.4.5 부터)|타입별칭이나 패키지 경로를 포함한 클래스명|org.apache.ibatis.type.EnumTypeHandler|
      |callSettersOnNulls|가져온 값이 null일 때 setter나 맵의 put 메소드를 호출할지를 명시 Map.keySet() 이나 null값을 초기화할때 유용하다. int, boolean 등과 같은 원시타입은 null을 설정할 수 없다는 점은 알아두면 좋다.|	true / false|false|
      |returnInstanceForEmptyRow|MyBatis 는 기본적으로 모든 열들의 행이 NULL 이 반환되었을 때 null을 반환한다. 이 설정을 사용하면 MyBatis가 대신 empty 인스턴스를 반환한다. nested results(collection 또는 association) 에도 적용된다. 3.4.2 부터|true / false|false|
      |logPrefix|마이바티스가 로거(logger) 이름에 추가할 접두사 문자열을 명시|문자열|설정하지 않음|
      |logImpl|마이바티스가 사용할 로깅 구현체를 명시 이 설정을 사용하지 않으면 마이바티스가 사용할 로깅 구현체를 자동으로 찾는다.|SLF4J / LOG4J(deprecated since 3.5.9) / LOG4J2 / JDK_LOGGING / COMMONS_LOGGING / STDOUT_LOGGING / NO_LOGGING	|설정하지 않음|
      |proxyFactory|마이바티스가 지연로딩을 처리할 객체를 생성할 때 사용할 프록시 툴을 명시|CGLIB / JAVASSIST|JAVASSIST (마이바티스 3.3과 이상의 버전)|
      |vfsImpl|VFS 구현체를 명시|콤마를 사용해서 VFS구현체의 패키지를 포함한 전체 클래스명|설정하지 않음|
      |useActualParamName|메소드 시그니처에 명시된 실제 이름으로 구문파라미터를 참조하는 것을 허용 이 기능을 사용하려면 프로젝트를 자바 8의 -parameters옵션을 사용해서 컴파일해야만 한다.(마이바티스 3.4.1이상의 버전)|	true / false|true|
      |configurationFactory|Configuration 인스턴스를 제공하는 클래스를 지정한다. 반환된 Configuration 인스턴스는 역직렬화 된 객체의 지연로딩 속성들을 불러오는 데 사용된다. 이 클래스는 static Configuration getConfiguration() 메서드를 가져야 한다. (3.2.3 부터)|타입별칭이나 패키지 경로를 포함한 클래스명|설정하지 않음|
      |shrinkWhitespacesInSql|SQL에서 여분의 whitespace 문자들을 삭제한다. 이는 SQL의 리터럴 문자열에도 영향을 미친다. (Since 3.5.5)|true / false|false|
      |defaultSqlProviderType|Provider method를 가지고 있는 sql provider class를 지정한다. (3.5.6 부터). 이 클래스는 sql provider annotation(예: @SelectProvider)의 type (혹은 value)속성이 누락되었을때 기본으로 적용된다.|타입별칭이나 패키지 경로를 포함한 클래스명	|설정하지 않음|
      |nullableOnForEach|Specifies the default value of 'nullable' attribute on 'foreach' tag. (Since 3.5.9)|true / false|false|




2. `/resource/config/spring/${spring.profiles.active}/context-mapper-postgre.xml` 파일에 다음과 같이   configLocation property 를 추가해 준다.

    ```
    <!-- configLocation : mybatis 설정파일이 위치한 경로를 지정한다.(주로 mybatis-config.xml 위치) -->
    <bean id="dataPostgreSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		  <property name="dataSource" ref="dataSourcePostgre"/>
      <property name="configLocation" value="classpath:/config/mybatis/config/mybatis-config.xml" />
	  </bean>
    ```

### 7. 여기까지 mybatis 설정 부분 완료 다음 실습에서 실제 데이터를 Spring과 Mybatis를 통해 연동한다.