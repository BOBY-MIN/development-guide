### JDK 8 일반계정 설치

> 설치 파일 다운

```
$ wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u271-b09/61ae65e088624f5aaa0b1d2d801acb16/jdk-8u271-linux-x64.tar.gz
```

* Linux x64 Compressed Archive 로 다운로드(본인 서버 bit에 맞게 선택)
* --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" 기재해야 정상적인 압축해제가 가능함.(인증서 체크하는 관계로 위 옵션이 필요)

> 압축해제

```
$ tar -zxvf jdk-8u271-linux-x64.tar.gz
```

> 환경변수 설정

```
-- jdk 설치 경로 확인
$ pwd
/home/was/install/jdk1.8.0_271

-- 계정별 환경변수 설정 파일 오픈
$ vi ~/.bash_profile

export JAVA_HOME=/home/was/install/jdk1.8.0_271
export PATH=$JAVA_HOME/bin:$PATH

-- 설정 바로 적용
$ source ~/.bash_profile
```

> 설치 확인

```
$ java -version
java version "1.8.0_271"
Java(TM) SE Runtime Environment (build 1.8.0_271-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.271-b09, mixed mode)
```
