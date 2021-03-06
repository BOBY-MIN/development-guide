
### apache tomcat 설정

* apache 설정 시 http (port 80) 호출은 httpd.conf 로 설정한다.
* apache 설정 시 httpd (port 443) 호출은 httpd.conf 로 설정한다.
* http 와 httpd 모두 서비스하지 않는 이상은 둘 중 하나의 설정만 하면 된다.


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


> apache 설정 파일 (http 통신)

```
- httpd-2.4.46-win64-VC15\Apache24\conf\httpd.conf 아래 내용 추가
# Tomcat connector
Include conf/extra/httpd-mod_jk.conf
Include conf/extra/httpd-myfixip.conf

Include conf/extra/httpd-vhost.conf


- httpd-2.4.46-win64-VC15\Apache24\conf\extra\httpd-vhosts.conf 아래 내용 추가
<VirtualHost *:80>
        ServerAdmin test@localhost
        ServerName test.co.kr
        DocumentRoot "war deploy root 경로"
        ErrorLog "에러로그 위치"
        CustomLog "커스텀 로그 위치"

        <Directory "war deploy root 경로">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>
        DirectoryIndex index.jsp index.html index.php
        JkMount /*.jsp tomcat_test
        JkMount /*.do tomcat_test
        JkMount /api/* tomcat_test
</VirtualHost>

```

* httpd-vhost.conf 로 설정 통합 관리하는 방식


> apache 설정 파일 (httpd 통신)

```
- httpd-2.4.46-win64-VC15\Apache24\conf\httpd.conf 아래 내용 추가
# Tomcat connector
Include conf/extra/httpd-mod_jk.conf
Include conf/extra/httpd-myfixip.conf

Include /etc/httpd/conf/extra/httpd-vhost-ssl.conf


- httpd-2.4.46-win64-VC15\Apache24\conf\extra\httpd-vhost-ssl.conf 아래 내용 추가
<VirtualHost *:443>

DocumentRoot "war deploy root 경로"
ServerAdmin test@localhost
ServerName test.co.kr
<Directory "war deploy root 경로">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
DirectoryIndex index.jsp index.html index.php
JkMount /*.jsp tomcat_test
JkMount /*.do tomcat_test

ErrorLog "|/usr/sbin/rotatelogs /data/webapps/webapp-test-logs/ssl_test_error_log.%Y%m%d_%H%M%S 86400 +540"
CustomLog "|/usr/sbin/rotatelogs /data/webapps/webapp-test-logs/ssl_test_access_log.%Y%m%d_%H%M%S 86400 +540" common
LogLevel warn

SSLEngine on
SSLProtocol all -SSLv2 -SSLv3
SSLCipherSuite HIGH:3DES:!aNULL:!MD5:!SEED:!IDEA
SSLCertificateFile 인증서파일.crt
SSLCertificateKeyFile 인증서키.key

<Files ~ "\.(cgi|shtml|phtml|php3?)$">
    SSLOptions +StdEnvVars
</Files>
<Directory "/var/www/cgi-bin">
    SSLOptions +StdEnvVars
</Directory>

BrowserMatch "MSIE [2-5]" \
         nokeepalive ssl-unclean-shutdown \
         downgrade-1.0 force-response-1.0

CustomLog logs/ssl_request_log \
          "%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x \"%r\" %b"

</VirtualHost>


```

* httpd-vhost-ssl.conf 로 설정 통합 관리하는 방식






> tomcat 설정 파일

```
- server.xml 아래 내용 추가

<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />

<!-- Define an AJP 1.3 Connector on port 8009 -->
 <Connector protocol="AJP/1.3" secretRequired="false" port="8009" redirectPort="8443" />




```

* apache 와 tomcat은 AJP/1.3 방식으로 통신함.
