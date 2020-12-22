### logback 환경 잡기

> pom.xml

```
<org.slf4j-version>1.7.25</org.slf4j-version>

    <dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${org.slf4j-version}</version>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>jcl-over-slf4j</artifactId>
			<version>${org.slf4j-version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>${org.slf4j-version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>1.2.3</version>
		</dependency>
		<dependency>
			<groupId>org.logback-extensions</groupId>
			<artifactId>logback-ext-spring</artifactId>
			<version>0.1.2</version>
		</dependency>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.15</version>
			<exclusions>
				<exclusion>
					<groupId>javax.mail</groupId>
					<artifactId>mail</artifactId>
				</exclusion>
				<exclusion>
					<groupId>javax.jms</groupId>
					<artifactId>jms</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.jdmk</groupId>
					<artifactId>jmxtools</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.jmx</groupId>
					<artifactId>jmxri</artifactId>
				</exclusion>
			</exclusions>
			<scope>runtime</scope>
		</dependency>
```

> web.xml

```
<context-param>
    <param-name>logbackConfigLocation</param-name>
    <param-value>classpath:properties/${spring.profiles.active}/logback.xml</param-value>
</context-param>

<listener>
     <listener-class>ch.qos.logback.ext.spring.web.LogbackConfigListener</listener-class>
 </listener>
```

* logback.xml 리스너에 주입


> src/main/resources/log4jdbc.log4j2.properties

```
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator

log4jdbc.dump.sql.maxlinelength=0
```

* log4jdbc.dump.sql.maxlinelength 값이 0인 경우 자동줄바꿈 처리됨.

> logback.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds">

	<property name="LogDir" value="/test/log/" />
	<property name="LogFile" value="catalina_server.log" />
	<property name="Charset" value="UTF-8" />
	<property name="Pattern" value="[%d{yyyy-MM-dd HH:mm:ss.SSS}] %highlight(%-5level) --- [ %thread{10} ] %cyan(%logger{36}) : %m%n" />

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
        	<layout class="test.LogbackLayoutUtil">
        		<prefix>true</prefix>
        		<printThreadName>true</printThreadName>
        	</layout>
        </encoder>
    </appender>

	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<File>${LogDir}${LogFile}</File>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
			<fileNamePattern>${LogDir}${LogFile}.%d{yyyy-MM-dd}</fileNamePattern>
		</rollingPolicy>
		<encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
        	<layout class="test.LogbackLayoutUtil">
        		<prefix>true</prefix>
        		<printThreadName>true</printThreadName>
        	</layout>
        </encoder>
	</appender>

	<logger name="org.springframework.core" level="INFO" />
	<logger name="org.springframework.beans" level="INFO" />
	<logger name="org.springframework.context" level="INFO" />
	<logger name="org.springframework.web" level="INFO" />
	<logger name="org.apache.commons.digester" level="OFF" />

    <logger name="jdbc.sqlonly"	level="OFF" />
	<logger name="jdbc.sqltiming"	level="DEBUG" />
	<logger name="jdbc.audit"	level="DEBUG" />
	<logger name="jdbc.resultset"	level="DEBUG" />
	<logger name="jdbc.resultsettable"	level="DEBUG" />
	<logger name="jdbc.connection"	level="INFO" />

    <!-- root level 지정된 값 이상으로만 로그 찍힘(TRACE < DEBUG < INFO < WARN < ERROR) -->
    <root level="DEBUG">
        <appender-ref ref="CONSOLE"/> <!-- Console에 로그를 출력하고자 할 때 사용 -->
        <appender-ref ref="FILE" /> <!-- File에 로그를 출력하고자 할 때 사용 -->
    </root>

</configuration>
```

* logger name의 하위 패키지는 모두 해당 level 로 로그가 찍힘.
* logger name에만 적용 원할 경우 additivity="false" 지정
* LayoutWrappingEncoder 를 이용하여 custom class 지정 가능(로그내용 형식 변경 가능)
* ConsoleAppender : console 에 찍히는 로그 관리
* RollingFileAppender : 지정된 경로에 날짜별 로그 파일 관리


> LogbackLayoutUtil

```
package test;

import java.text.SimpleDateFormat;

import ch.qos.logback.classic.spi.ILoggingEvent;
import ch.qos.logback.core.LayoutBase;

public class LogbackLayoutUtil extends LayoutBase<ILoggingEvent> {

	boolean prefix = true;
	boolean printThreadName = true;

	public void setPrefix(boolean prefix) {
		this.prefix = prefix;
	}

	public void setPrintThreadName(boolean printThreadName) {
		this.printThreadName = printThreadName;
	}

	public String doLayout(ILoggingEvent event) {

		StringBuffer sbuf = new StringBuffer(256);
		StringBuffer loggerHeaderInfo = new StringBuffer(128);

		// 호출 시간 형식 지정
		SimpleDateFormat simpleFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
		String timeStampFormat = "[" + simpleFormat.format(event.getTimeStamp()) + "]";
		String levelFormat = String.format("[%5s]", event.getLevel());
		String threadFormat = String.format("[%s]", event.getThreadName());
		String loggerFormat = String.format("[%s] ", event.getLoggerName());

		// 접두사를 지정한 경우 접두사 정보 셋팅
		if(prefix) {
			loggerHeaderInfo.append(timeStampFormat);
			loggerHeaderInfo.append(levelFormat);
			loggerHeaderInfo.append(threadFormat);
			loggerHeaderInfo.append(loggerFormat);
		}


		String message = event.getFormattedMessage();

		// 개행이 포함되어 있다면 split하여 앞에 접두사 지정
		if(message.contains("\n")) {
			String[] splitMessage = message.split("\n");

			for(String arg : splitMessage) {
				if(prefix) {
					sbuf.append(loggerHeaderInfo);
				}
				sbuf.append(arg);
			}

			sbuf.append("\n");
		}else {
			if(prefix) {
				sbuf.append(loggerHeaderInfo);
			}
			sbuf.append(message);
			sbuf.append("\n");
		}


		return sbuf.toString();

	}

}

```

* LayoutBase<ILoggingEvent> 상속 받아 doLayout() 에서 메시지 변환 가능
* 출처 : http://logback.qos.ch/manual/layouts.html
