
#### transaction XML AOP 설정(spring)

> root-context.xml

```
<bean id="dataSourceSpied" class="org.springframework.jndi.JndiObjectFactoryBean">
	<property name="jndiName" value="java:comp/env/jdbc/testDbJndiName"/>
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

<!-- Batch 처리를 위한 선언 -->
<!-- 아래와 같이 batch 처리 선언으로 SqlSessionTemplate bean이 두 개 있을 경우에는
     Dao 에서 @Resource 명까지 기재해줘야함.
  @Autowired
	@Resource(name="sqlBatchSession")
	private SqlSession session; -->
<bean id="sqlBatchSession" class="org.mybatis.spring.SqlSessionTemplate" destroy-method="clearCache">
	<constructor-arg index="0" ref="sqlSessionFactory"></constructor-arg>
	<constructor-arg index="1" value="BATCH"></constructor-arg>
</bean>

<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"></property>
</bean>

<!-- 아래 설정은 @Transactional 어노테이션 활용 시 선언 -->
<!-- <tx:annotation-driven transaction-manager="transactionManager" /> -->

<!-- aop 방식으로 transaction 관리 시 아래 내용으로 활용 -->
<aop:config proxy-target-class="true">
  <!-- 패키지 내 이름이 Service 로 끝나는 클래스의 모든 메서드를 pointCut으로 선언 -->
	<aop:pointcut id="taskPointCut_service" expression="execution(* com.example..*Service.*(..))"/>
	<aop:advisor advice-ref="txAdvice" pointcut-ref="taskPointCut_service" />
</aop:config>

<tx:advice id="txAdvice" transaction-manager="transactionManager" >
	<tx:attributes>
		<tx:method name="create*" rollback-for="Exception"/>
		<tx:method name="modify*" rollback-for="Exception"/>
		<tx:method name="delete*" rollback-for="Exception"/>

		<tx:method name="select*_newTx" rollback-for="Exception" propagation="REQUIRES_NEW"/>
		<tx:method name="create*_newTx" rollback-for="Exception" propagation="REQUIRES_NEW"/>
		<tx:method name="modify*_newTx" rollback-for="Exception" propagation="REQUIRES_NEW"/>
		<tx:method name="delete*_newTx" rollback-for="Exception" propagation="REQUIRES_NEW"/>
	</tx:attributes>
</tx:advice>

```

* transaction 은 interface를 구현한 class 에서 사용가능하므로 service 단에서 구현하는 것이 일반적임.
* Service의 A method 호출 후 새로운 transaction으로 시작하고자 한다면 Service 단위의 호출에서만 transaction이 새롭게 시작되므로(e.g. select_newTx 호출의 경우), association 클래스를 활용해 Service B mehtod(method 명 select_newTx)를 재호출하는 방식으로 구현해야 함.
  + Controller -> Service A method -> AssociationService method -> Service B method(mehtod명 select_newTx)
    <br>: 위와 같이 호출 시 B method는 A method와 다른 새로운 transaction으로 처리 가능함.
