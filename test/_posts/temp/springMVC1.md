
File->
![new gradle project](/assets/images/spingMVC1.PNG)


sts 4에서는 spring legacy project를 지원하지 않으므로 따로 추가해 줘야 한다.

help-> eclipse marketplace -> Spring Tools 3 Add-On for Spring Tools 4 설치

- spring mvc 주요 Annotation
  - @Controller: Controller 객체임을 명시
  - @Repository : DAO 객체임을 명시
  - @Service : Service 객체임을 명시
  - @RequestMapping : 특정 URI에 매칭되는 클래스나, 메서드임을 명시
  - @RequestParam : 요청에서 특정한 파라미터 값을 찾아 낼 때 사용
  - @RequestHeader : 요청에서 특정 HTTP 헤터 정보를 추출할 때 사용
  - @PathVariable : 현재의 URI에서 원하는 정보를 추출할 때 사용
  - @CookieValue : 현재 사용자긔 쿠키가 존재하는 경우 쿠키의 이름을 이용해서 쿠키 값을 추출
  - @ModelAttribute : 세션 상에서 모델의 정보르 ㄹ유지하고 싶은 경우 사용
  - @InitBinder : 파라미터를 수집해서 객체로 만들 경우
  - @ResponseBody : 리턴 타입이 HTTP의 응답 메시지로 전송
  - @RequestBody : 요청 문자열이 그래도 파라미터로 전달.


- Mybatis
  - 장점:
    - 간결한 코드 처리: JDBC작업을 위한 반복적인 코드(Try~Catch~Finally, PreparedStatement, ResultSet)을 직접 작성하지 않아도 된다.
    - SQL 문 분리 운영 : XML또는 Annotation 방식으로 SQL문을 별도로 처리하는 작업이 가능하다.
    - Spring과 연동으로 자동화된 처리 : Mybatis-Spring라이브러리를 이용하여 직접 SQL 문 호출 없이도 원하는 결과 얻을 수 있다.
    - 동적 SQL을 이용한 제어 기능: 제어문이나 반복문 등의 처리 기능을 통해 SQL과 관련된 처리를 JAVA코드에서 분리할 수 있다.
  - 사용방법
    - build.gradle 추가

        ```
        def version = [
            spring : "5.3.10",
            mybatisSpring : "2.0.6",
            mybatis : "3.5.7",
        ]

        dependencies{
            compile "org.springframework:spring-jdbc:${version.spring}"
            compile "org.mybatis:mybatis-spring:${version.mybatisSpring}"
            compile "org.mybatis:mybatis:${version.mybatis}"
        }
        ```

    - classpath*:/config/properties/${spring.profiles.active}/datasource.xml 작성

        ```
        <properties>
            <!-- PostgreSQL -->
            <entry key="jdbc.postgresql.driverClassName">org.postgresql.Driver</entry>
            <entry key="jdbc.postgresql.url">jdbc:postgresql://127.0.0.1:5432</entry>
            <entry key="jdbc.postgresql.username">test</entry>
            <entry key="jdbc.postgresql.password">testpw</entry>
        </properties>
        ```

    - classpath*:/config/spring/${spring.profiles.active}/context-datasource.xml 작성

        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:util="http://www.springframework.org/schema/util"
            xmlns:jee="http://www.springframework.org/schema/jee"
            xsi:schemaLocation="http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.3.xsd
                http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
                http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">
            
            <!-- datasourcProperties로 location 위치에 있는 파일의 설정 정보 가저온다. -->
            <util:properties id="datasourcProperties" location="classpath*:/config/properties/${spring.profiles.active}/datasource.xml" />
            
            <bean id="dataSourcePostgre" class="org.apache.commons.dbcp.BasicDataSource">
                <property name="driverClassName" value="#{datasourcProperties['jdbc.postgresql.driverClassName']}"/>
                <property name="url" value="#{datasourcProperties['jdbc.postgresql.url']}" />
                <property name="username" value="#{datasourcProperties['jdbc.postgresql.username']}"/>
                <property name="password" value="#{datasourcProperties['jdbc.postgresql.password']}"/>
            </bean>
        
            <bean id="dataSourcePostgreLog" class="net.sf.log4jdbc.Log4jdbcProxyDataSource">
                <constructor-arg ref="dataSourcePostgre" />
                <property name="logFormatter">
                    <bean class="net.sf.log4jdbc.tools.Log4JdbcCustomFormatter">
                        <property name="loggingType" value="MULTI_LINE" />
                        <property name="sqlPrefix" value="SQL  :  " />
                    </bean>
                </property>
            </bean>
            
            <jee:jndi-lookup id="dataSourcePostgre" jndi-name="dataSourcePostgre" resource-ref="true" />
            
        </beans>
        ```

    - classpath*:/config/spring/${spring.profiles.active}/context-mapper-postgre.xml 작성
      - SqlSessionFactory 객체 설정

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

            <tx:annotation-driven transaction-manager="transactionManagerPostgre" />

            <bean id="dataPostgreSessionFactory" class="com.kyobobook.api.common.abstracts.dao.RefreshableSqlSessionFactoryBean">
                <property name="dataSource" ref="dataSourcePostgreLog" />
                <property name="typeAliasesPackage" value="com.kyobobook.api.biz" />
                <property name="configLocation" value="classpath:/config/mybatis/config/mybatis-config.xml" />
                <property name="mapperLocations" value="classpath:/config/mybatis/dao/postgre/**/*.xml" />
            </bean>   
            
            <bean id="dataPostgreSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate" destroy-method="clearCache">
                <constructor-arg index="0" ref="dataPostgreSessionFactory" />
                <constructor-arg index="1" value="REUSE" />
            </bean>
            
            <bean id="dataPostgreAbstractMapper" class="org.mybatis.spring.mapper.MapperFactoryBean"
                p:sqlSessionFactory-ref="dataPostgreSessionFactory" p:sqlSessionTemplate-ref="dataPostgreSessionTemplate" 
                abstract="true" />
            
            <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer"
                p:basePackage="com.kyobobook.api.**.dao.postgre"
                p:annotationClass="com.kyobobook.api.framework.mybatis.MapperPostgre"
                p:sqlSessionFactoryBeanName="dataPostgreSessionFactory" />
                
        </beans>
        ```
    

    