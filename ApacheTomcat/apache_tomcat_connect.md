
### apache tomcat 연동

> apache 호출용 파일 생성

```
- \src\main\webapp\index.jsp

<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>

 <script>

	history.replaceState({ data: '${pageContext.request.contextPath}/main'}, '', "${pageContext.request.contextPath}/main");
	location.reload();

</script>

- web.xml
<welcome-file-list>
	<welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

* WEB-INF 바깥 경로에 index.jsp 생성하여 메인화면 서비스 호출
* web.xml에 welcomefile 셋팅


> apache 설정 파일

```
- httpd-2.4.46-win64-VC15\Apache24\conf\httpd.conf 아래 내용 추가
Include conf/extra/httpd-vhost.conf


- httpd-2.4.46-win64-VC15\Apache24\conf\extra\httpd-vhosts.conf 아래 내용 추가
<VirtualHost *:80>
        ServerAdmin test@localhost
        ServerName test.co.kr
        DocumentRoot "war deploy root 경로"
        ErrorLog "에러로그 위치"
        CustomLog "커스텀 로그 위치"

        <Directory "/data/dalpay_front_test">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>
        DirectoryIndex index.jsp index.html index.php
        JkMount /*.jsp lb_dalpay_front_test
        JkMount /*.do lb_dalpay_front_test
        JkMount /api/* lb_dalpay_front_test
</VirtualHost>

```

* httpd-vhost.conf 로 설정 통합 관리하는 방식

> tomcat 설정 파일

```
- server.xml 아래 내용 주석 제거
<!-- Define an AJP 1.3 Connector on port 8009 -->
<!--
<Connector protocol="AJP/1.3"
           address="::1"
           port="8009"
           redirectPort="8443" />
-->

```

* apache 와 tomcat은 AJP/1.3 방식으로 통신함.
