### WAR 배포 참고

> .class 파일 누락

```
eclipse
  Project > Build Automatically 체크 해제
```

<br>

> 외부 jar 포함 방법

```
- pom.xml

<dependency>
	<groupId>test.jar</groupId>
	<artifactId>base</artifactId>
	<version>0.0.1-SNAPSHOT</version>
</dependency>

<repositories>
  <repository>
  	<id>local-repository</id>
  	<name>LOCAL Repository</name>
  	<url>file://${project.basedir}/lib</url>
  </repository>
</repositories>
```

1. pom.xml 에 local repository 경로 지정
  * file:// : local 디렉토리 지정
  * ${project.basedir} : project ROOT 경로(pom.xml 에 상위 디렉토리)

2. dependency jar 위치 세부 경로 지정
  * directory ex)
    - ROOT
      - lib
        - test
          - jar
            - base
              - 0.0.1-SNAPSHOT
                - base-0.0.1-SNAPSHOT.jar
                - base-0.0.1-SNAPSHOT.pom


  * 위 경로와 같이 구성되어야 하며 .jar 와 .pom 모두 있어야 함.
  * dependency 하려는 jar의 pom.xml 내용 중 아래 내용이 디렉토리 경로와 동일해야 함.
```
<groupId>test.jar</groupId>
<artifactId>base</artifactId>
<version>0.0.1-SNAPSHOT</version>
```
