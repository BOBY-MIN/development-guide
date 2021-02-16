### tomcat 일반계정 설치

> 설치 파일 다운

```
$ wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.60/bin/apache-tomcat-8.5.60.tar.gz
```

* local 버전과 맞추기 위해 이전 버전으로 다운로드함.

> 압축해제

```
$ tar -zxvf apache-tomcat-8.5.60.tar.gz
```

> 환경변수 설정

```
-- tomcat 설치 경로 확인
$ pwd
/home/was/tomcat/apache-tomcat-8.5.60

-- 계정별 환경변수 설정 파일 오픈
$ vi ~/.bash_profile

export CATALINA_HOME=/home/was/tomcat/apache-tomcat-8.5.60
export CLASSPATH=.:$JAVA_HOME/lib/tools.jar:$CATALINA_HOME/lib/jsp-api.jar:$CATALINA_HOME/lib/servlet-api.jar
export PATH=$CATALINA_HOME/bin:$PATH

-- 설정 바로 적용
$ source ~/.bash_profile
```


> 설치 확인

```
-- tomcat 설치 경로 내 bin 디렉토리 이동
$ cd /home/was/tomcat/apache-tomcat-8.5.60/bin

-- 서버 기동
$ /home/was/tomcat/apache-tomcat-8.5.60/bin/startup.sh
```
