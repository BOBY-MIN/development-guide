### 네이버 오픈소스 lucy_xss 적용

> pom.xml

```
<dependency>
	<groupId>com.navercorp.lucy</groupId>
	<artifactId>lucy-xss-servlet</artifactId>
	<version>2.0.0</version>
</dependency>
```

> web.xml

```
<filter>
	<filter-name>xssEscapeServletFilter</filter-name>
	<filter-class>com.navercorp.lucy.security.xss.servletfilter.XssEscapeServletFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>xssEscapeServletFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

<br>

> classpath 하단 파일 추가
  + lucy-xss.xml
  + lucy-xss-default-sax.xml
  + lucy-xss-sax.xml
  + lucy-xss-servlet-filter-rule.xml
  + lucy_xss-superset-sax.xml

* 위 내용은 아래 출처에서 모두 확인 가능
* 화이트리스트 정책을 사용하며 일부서비스에 대한 별도의 정책을 가져가야 하는 경우도 lucy-xss-servlet-filter-rule.xml 에서 지정 가능


* 출처 : <https://github.com/naver/lucy-xss-servlet-filter>
