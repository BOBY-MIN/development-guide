
### spring data jpa & querydsl 셋팅

> 환경

  * gradle-6.8.3
  * sts4
  * spring-boot 2.4.3

> build.gradle

```
plugins {
	id 'org.springframework.boot' version '2.4.3'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	// querydsl 설정 시작
	// Qclass 생성을 위한 플러그인
	id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
	// querydsl 설정 끝
	id 'java'
	id 'war'
}

group = 'com.gabaek'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'

	// spring data jpa, mysql 설정 시작
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'mysql:mysql-connector-java:8.0.18'
	implementation 'org.apache.tomcat:tomcat-dbcp:8.5.60'
	// spring data jpa, mysql 설정 끝

	// lombok 설정 시작
	compileOnly 'org.projectlombok:lombok'
	// lombok 설정 끝

	// querydsl 설정 시작
	implementation 'com.querydsl:querydsl-jpa'
	annotationProcessor 'org.projectlombok:lombok'
	// querydsl 설정 끝
}

test {
	useJUnitPlatform()
}


// querydsl 설정 시작
def querydslDir = "$buildDir/generated/querydsl"
querydsl {
	jpa = true
	querydslSourcesDir = querydslDir
}
sourceSets {
	main.java.srcDir querydslDir
}
configurations {
	querydsl.extendsFrom compileClasspath
}
compileQuerydsl {
	options.annotationProcessorPath = configurations.querydsl
}
// querydsl 설정 끝
```


* 위 설정 후 @Entity 클래스 찾아서 QClass 생성해 줌.


> QClass 생성

* PC에 gradle 설치
* CMD 창에서 build.gradle 있는 디렉토리로 이동 후 아래 명령어 수행
* gradlew compileQuerydsl
