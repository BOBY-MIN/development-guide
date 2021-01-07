
### server.xml 설정 내용

> ROOT 경로 변경

```
$ vi /tomcat/conf/server.xml

<Host name="localhost"  appBase="/home/was/webapps"
           unpackWARs="true" autoDeploy="true">

 <!-- SingleSignOn valve, share authentication between web applications
      Documentation at: /docs/config/valve.html -->
 <!--
 <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
 -->

 <!-- Access log processes all example.
      Documentation at: /docs/config/valve.html
      Note: The pattern used is equivalent to using pattern="common" -->
 <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
        prefix="localhost_access_log" suffix=".txt"
        pattern="%h %l %u %t &quot;%r&quot; %s %b" />
</Host>
```

* Host 태그 내 appBase 가 자동 deploy 되는 경로 해당 경로에 ROOT.war 이동 후 서버 재시작하면 자동 deploy 됨.(본인이 원하는 경로로 변경 가능)
* 위와 같이 appBase 로 deploy 처리 시 하나의 tomcat에 war를 여러개 추가하더라도 자동 배포가 가능하게 됨.
* 자동 deploy 된 war명이 path 명으로 지정됨. ip:port/ 로 호출하고 싶은 경우 ROOT.war 로 배포하거나 다른 처리 필요. 여러 개의 war를 배포하고 싶을 경우 아래 내용 참고하여 path 명에 의존적이지 않게 개발 진행.

```
<Context docBase="baseproject" path="/baseproject" reloadable="false" >
</Context>
```

* 웹소스 개발 시 ${pageContext.request.contextPath} 로 지정하여 deploy 된 프로젝트 명에 의존적이지 않게 해야 함.
* 위 path 설정에 따라 기본 ip:port 뒤에 path가 붙어서 호출되게 됨. ${pageContext.request.contextPath}로 처리하지 않는 경우 path 명이 변경될 경우 수정을 수동으로 해줘야 함.(자바에서는 request.getContextPath()로 확인 가능)
* reloadable 이 true 인 경우 자동배포가 진행되면서 tomcat 서버 에러가 남을 수 있으므로 false 로 지정 후 수동으로 서버를 껐다 켜는 게 좋음.(deploy된 소스 우선 제거)

```
$CATALINA_HOME//conf/Catalina/localhost
```

* 위 경로에 contextPath 명에 맞춰 contextPath.xml 생성 시 contextPath 별 환경 설정 가능
