
### 에러 처리 모음

> Class path contains multiple SLF4J bindings.

```
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/E:/DE/.metadata/.plugins/org.eclipse.wst.server.core/tmp0/wtpwebapps/danalenterhome/WEB-INF/lib/logback-classic-1.2.3.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/E:/DE/.metadata/.plugins/org.eclipse.wst.server.core/tmp0/wtpwebapps/danalenterhome/WEB-INF/lib/slf4j-log4j12-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [ch.qos.logback.classic.util.ContextSelectorStaticBinder]
```

* 로컬 환경(윈도우)에서는 큰 영향없이 서버 구동이 됨.
* centOS7 에서는 아예 서버기동이 안되며 에러 로그 또한 남지 않음.
* 위 문제로 db connection에서 timeout 발생할 수 있음.

```
확인법 : 로컬 로그와 linux 로그를 비교하여 위 로그부터 linux 로그가 찍히지 않는지 확인.
해결법 : dependency 에서 중복되는 jar 중 하나를 제거
```

***

> This is very likely to create a memory leak. <br>
> Illegal access: this web application instance has been stopped already.

```
04-Jan-2021 16:22:07.636 WARNING [localhost-startStop-1] org.apache.catalina.loader.WebappClassLoaderBase.clearReferencesThreads The web application [ROOT] appears to have started a thread named [mysql-cj-abandoned-connection-cleanup] but has failed to stop it. This is very likely to create a memory leak. Stack trace of thread:
 java.lang.Object.wait(Native Method)
 java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:144)
 com.mysql.cj.jdbc.AbandonedConnectionCleanupThread.run(AbandonedConnectionCleanupThread.java:85)
 java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
 java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
 java.lang.Thread.run(Thread.java:748)
04-Jan-2021 16:22:07.640 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployWAR Deployment of web application archive [/home/was/tomcat/apache-tomcat-8.5.60/webapps/ROOT.war] has finished in [4,472] ms
04-Jan-2021 16:22:07.642 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/home/was/tomcat/apache-tomcat-8.5.60/webapps/docs]
04-Jan-2021 16:22:07.817 INFO [localhost-startStop-1] org.apache.jasper.servlet.TldScanner.scanJars At least one JAR was scanned for TLDs yet contained no TLDs. Enable debug logging for this logger for a complete list of JARs that were scanned but no TLDs were found in them. Skipping unneeded JARs during scanning can improve startup time and JSP compilation time.
04-Jan-2021 16:22:07.830 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/home/was/tomcat/apache-tomcat-8.5.60/webapps/docs] has finished in [188] ms
04-Jan-2021 16:22:07.830 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/home/was/tomcat/apache-tomcat-8.5.60/webapps/examples]
04-Jan-2021 16:22:08.330 INFO [localhost-startStop-1] org.apache.jasper.servlet.TldScanner.scanJars At least one JAR was scanned for TLDs yet contained no TLDs. Enable debug logging for this logger for a complete list of JARs that were scanned but no TLDs were found in them. Skipping unneeded JARs during scanning can improve startup time and JSP compilation time.
04-Jan-2021 16:22:08.356 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/home/was/tomcat/apache-tomcat-8.5.60/webapps/examples] has finished in [526] ms
04-Jan-2021 16:22:08.356 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/home/was/tomcat/apache-tomcat-8.5.60/webapps/host-manager]
04-Jan-2021 16:22:08.475 INFO [localhost-startStop-1] org.apache.jasper.servlet.TldScanner.scanJars At least one JAR was scanned for TLDs yet contained no TLDs. Enable debug logging for this logger for a complete list of JARs that were scanned but no TLDs were found in them. Skipping unneeded JARs during scanning can improve startup time and JSP compilation time.
04-Jan-2021 16:22:08.477 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/home/was/tomcat/apache-tomcat-8.5.60/webapps/host-manager] has finished in [121] ms
04-Jan-2021 16:22:08.477 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/home/was/tomcat/apache-tomcat-8.5.60/webapps/manager]
04-Jan-2021 16:22:08.628 INFO [localhost-startStop-1] org.apache.jasper.servlet.TldScanner.scanJars At least one JAR was scanned for TLDs yet contained no TLDs. Enable debug logging for this logger for a complete list of JARs that were scanned but no TLDs were found in them. Skipping unneeded JARs during scanning can improve startup time and JSP compilation time.
04-Jan-2021 16:22:08.630 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/home/was/tomcat/apache-tomcat-8.5.60/webapps/manager] has finished in [153] ms
04-Jan-2021 16:22:08.637 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]
04-Jan-2021 16:22:08.647 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in 5573 ms
04-Jan-2021 16:22:12.140 INFO [18] org.apache.catalina.loader.WebappClassLoaderBase.checkStateForResourceLoading Illegal access: this web application instance has been stopped already. Could not load []. The following stack trace is thrown for debugging purposes as well as to attempt to terminate the thread which caused the illegal access.
        java.lang.IllegalStateException: Illegal access: this web application instance has been stopped already. Could not load []. The following stack trace is thrown for debugging purposes as well as to attempt to terminate the thread which caused the illegal access.
                at org.apache.catalina.loader.WebappClassLoaderBase.checkStateForResourceLoading(WebappClassLoaderBase.java:1378)
                at org.apache.catalina.loader.WebappClassLoaderBase.getResource(WebappClassLoaderBase.java:1030)
                at com.mysql.cj.jdbc.AbandonedConnectionCleanupThread.checkThreadContextClassLoader(AbandonedConnectionCleanupThread.java:117)
                at com.mysql.cj.jdbc.AbandonedConnectionCleanupThread.run(AbandonedConnectionCleanupThread.java:84)
                at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
                at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
                at java.lang.Thread.run(Thread.java:748)

```

* DriverManagerDatasource 방식으로 centOS7에 배포 시 위 에러 확인 가능.
* mysql8 이후부터는 memory leak 발생 시 그냥 서버 중지시키는 것 같음.

```
해결법 : jndi 방식으로 connection pool 관리하도록 변경 필요.
```

***

> mysql-cj-abandoned-connection-cleanup

```
04-Jan-2021 16:16:10.454 INFO [mysql-cj-abandoned-connection-cleanup] org.apache.catalina.loader.WebappClassLoaderBase.checkStateForResourceLoading Illegal access: this web application instance has been stopped already. Could not lo     ad []. The following stack trace is thrown for debugging purposes as well as to attempt to terminate the thread which caused the illegal access.
```

* jndi 방식으로 사용 시 db connector jar를 tomcat lib에서 관리하도록 함. <br> webapp lib(배포소스 내 lib) 에서는 제거 필요

```
해결법 : tomcat_server/lib 에 mysql 드라이버 설치 (version에 맞게 설치)
/home/was/tomcat/apache-tomcat-8.5.60/lib/mysql-connector-java-8.0.18.jar

- pom.xml 설정
<!-- mysql start -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>8.0.18</version>
	<scope>provided</scope>
</dependency>
<!-- mysql end -->
```
