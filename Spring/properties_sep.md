### 환경별 properties 분리

* profiles 설정을 통해서 분리 진행


> web.xml

```
classpath:properties/${spring.profiles.active}/logback.xml
```

> root-context.xml

```
<util:properties id="config" location="classpath:properties/#{systemProperties['spring.profiles.active']}/config.properties" />

사용 예
<property name="username" value="#{config['db.username']}"></property>
```

* classpath : src/main/java, src/main/resources 를 말함.


### 로컬 셋팅

server arguments 에 아래 내용 추가

```
-Dspring.profiles.active=local
```

### 서버 셋팅

```
Linux
setenv.sh 파일 생성 후 아래와 같이 작성

  JAVA_OPTS="$JAVA_OPTS -Dspring.profiles.active={profile_name}"

Window
setenv.bat 파일 생성 후 아래와 같이 작성

  JAVA_OPTS=%JAVA_OPTS% -Dspring.profiles.active={profile_name}
```
