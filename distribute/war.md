### WAR 배포 참고

> .class 파일 누락

```
eclipse
  Project > Build Automatically 체크 해제
```

<br>

> WAR export 시 외부 jar 포함 방법1 (maven local repository 활용)

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

<br>

> WAR export 시 외부 jar 포함 방법2 (java build path -> add jars 활용)

```
-- jar를 아래 directory 에 추가

ROOT
  -- webapp
    -- WEB-INF
      -- lib
        -- xxx.jar
```

* 프로젝트 properties -> java build path -> add jars
* export -> war 방식으로 사용하는 경우 위 방식으로만 가능

<br>

> WAR export 시 외부 jar 포함 방법3 (maven system scope 활용 방법)

```
-- jar를 아래 directory 에 추가

ROOT
  -- webapp
    -- WEB-INF
      -- lib
        -- xxx.jar


-- pom.xml
<dependency>
	<groupId>test</groupId>
	<artifactId>test</artifactId>
	<version>1.0</version>
	<scope>system</scope>
	<systemPath>${project.basedir}/webapp/WEB-INF/lib/test-1.0.jar</systemPath>
</dependency>
```

* dependency 에 groupId 나 artifactId 등은 임의로 지정가능
* systemPath 경로가 중요하며 디렉토리를 꼭 WEB-INF/lib 넣어야 함.
* maven build 를 이용하는 경우 2번 방식은 에러남. 위 방식으로 해야 함.
