
### 경로 설정 방법

> 프로젝트 폴더 외부 경로 설정

```
- servlet-context.xml
<resources mapping="/outsidepath/**" location="file:///C:/home/was/webapps/files/" />
  : windows

<resources mapping="/outsidepath/**" location="file:/home/was/webapps/files/" />
  : linux

```

* web.xml 에 servlet 등록해야 함.

> 프로젝트 폴더 외부 경로 bean으로 설정

```
- servlet-context.xml
<beans:bean id="uploadPath" class="java.lang.String">
 	<beans:constructor-arg value="/home/was/webapps/files"></beans:constructor-arg>
</beans:bean>

- *.java
@Resource(name = "uploadPath")
private String uploadPath;
```

* web.xml 에 servlet 등록해야 함.

> 프로젝트 폴더 내부 경로 설정

```
- servlet-context.xml
<resources mapping="/resources/**" location="/resources/" />
```

* web.xml 에 servlet 등록해야 함.
