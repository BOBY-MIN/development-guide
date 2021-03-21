
### JNDI connection pool 변경

* tomcat 8.5.60 - mysql 8.0.18
* datasource manager 방식 -> JNDI 방식 변경으로 이미 db 설정 되어 있다고 전제
* tomcat/lib 폴더 내 mysql-connector-java-8.0.18.jar 파일 복사

> pom.xml

```
<!-- mysql start -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>8.0.18</version>
	<scope>provided</scope>
</dependency>
<!-- mysql end -->

<!-- tomcat dbcp start -->
<dependency>
	<groupId>org.apache.tomcat</groupId>
	<artifactId>tomcat-dbcp</artifactId>
	<version>8.5.60</version>
</dependency>
<!-- tomcat dbcp end -->
```

* mysql-connector-java 는 tomcat/lib 에서 사용하므로 배포 시 jar 미포함.
* webapp 소스 내 tomcat-dbcp 추가

> root-context.xml

```
<!-- 기존 datasource manager 방식 -->
<!-- <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	<property name="driverClassName" value="#{config['db.driverClassName']}"></property>
	<property name="url" value="#{config['db.url']}"></property>
	<property name="username" value="#{config['db.username']}"></property>
	<property name="password" value="#{config['db.password']}"></property>
</bean> -->

<bean id="dataSourceSpied" class="org.springframework.jndi.JndiObjectFactoryBean">
	<property name="jndiName" value="java:comp/env/jdbc/jndiname"/>
</bean>

<bean id="dataSource" class="net.sf.log4jdbc.sql.jdbcapi.DataSourceSpy">
    <constructor-arg ref="dataSourceSpied"/>
</bean>

<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource" />
	<property name="configLocation" value="classpath:/mybatis-config.xml"></property>
	<property name="mapperLocations" value="classpath:mappers/**/*Mapper.xml"></property>
</bean>

<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate" destroy-method="clearCache">
	<constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"></constructor-arg>
</bean>

<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"></property>
</bean>
```

* JAVA의 경우 java:comp/env/ 로 jndi 이름을 찾으므로 기재 필수
* jdbc/jndiname 은 tomcat/server.xml tomcat/context.xml web.xml 의 jndi name과 동일해야 함.

> web.xml

```
<!-- JNDI start -->
<resource-ref>
	<description>DB Connection</description>
	<res-ref-name>jdbc/jndiname</res-ref-name>
	<res-type>javax.sql.DataSource</res-type>
	<res-auth>Container</res-auth>
</resource-ref>
<!-- JNDI stop -->
```

> tomcat/context.xml

```
<ResourceLink name="jdbc/jndiname" global="jdbc/jndiname" type="javax.sql.DataSource" />
```

* Context 태그 내에 추가
* linux $CATALINA_HOME/conf/context.xml


> tomcat/server.xml

```
<Resource
    	auth="Container"
    	type="javax.sql.DataSource"
    	username="user"
    	password="password"
    	driverClassName="com.mysql.cj.jdbc.Driver"
    	factory="org.apache.tomcat.dbcp.dbcp2.BasicDataSourceFactory"
    	url="jdbc:mysql://ip:port/dbName?autoReconnect=true&amp;serverTimezone=UTC"
    	maxTotal="500"
    	maxIdle="50"
    	minIdle="50"
    	initialSize="50"
    	maxWaitMillis="5000"
    	validationQuery="SELECT 1"
    	testOnBorrow="true"
    	testWhileIdle="true"
    	testOnReturn="false"
    	minEvictableIdleTimeMillis="30000"
    	numTestsPerEvictionRun="10"
    	timeBetweenEvictionRunsMillis="30000"
		removeAbandonedOnBorrow="true"
    	removeAbandonedOnMaintenance="true"
    	removeAbandonedTimeout="60"
		logAbandoned="true"
		poolPreparedStatements="true"
    	defaultTransactionIsolation="READ_COMMITTED"
		name="jdbc/jndiname"
		/>
```

* GlobalNamingResources 태그 내에 추가
* linux $CATALINA_HOME/conf/server.xml
* 위 속성 셋팅 관련 내용 참고 http://tomcat.apache.org/tomcat-8.5-doc/jndi-resources-howto.html
