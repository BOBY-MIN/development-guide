
> 플러그인 목록

```
- anyEdit : 대소문자, 카멜표기법 변경
- Subclipse : SVN
  1 Windows > Perspective > Customize Perspective 이동
  2 Action Set Availability tab 선택 후 SVN 체크
  3 Apply and Close

- Spring Tools 3 (Standalone Edition) 3.9.14.RELEASE
  : spring legacy project 생성

- Enhanced Class Decompiler : class 디컴파일
  1 Window > Preferences 이동
  2 java > Decompiler Default Class Decompiler FernFlower(Support JDK8) 선택
  3 General > Editors > File Associations 이동
    3-1 *.class 선택 후 하단 Associated editors 에서 Class Decompiler Viewer Default 지정
    3-2 *.class without source 선택 후 하단 Associated editors 에서 Class Decompiler Viewer Default 지정
  4 Apply and Close

- windowbuilder
  swing GUI 개발 시 활용 가능
  https://www.eclipse.org/windowbuilder/download.php
```

> maven repositoty 경로 변경

```
1. window > preferences > Maven > User Settings
  > User Settings 에 아래 내용 기재된 파일 지정하면 자동으로 Local Repository 경로 변경됨.


<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

<localRepository>C:\Users\tester\.m2\test\repository</localRepository>


</settings>

2. window > preferences > Maven > Installations
  > Add 클릭하여 apache-maven 경로 추가

```

* localRepository 태그 내용이 경로


> 단축키

```
--  anyEdit 플러그인 설치 후 사용 가능
ctrl + alt + k : camel
ctrl + shift + x : 대문자
ctrl + shift + y : 소문자
ctrl + shift + l : 텍스트찾기


-- 이클립스 범용
ctrl + shift + { : 같은 소스 분할

ctrl + alt + H : 메서드 호출 보기 (Call Hierarchy)
ctrl + shift + R : 리소스 찾기
ctrl + O : 클래스 메서드 보기
alt + enter : 자원확인

** 단축키 등록방법
window > preferences > general > keys 에서 등록
```

> tomcat server에 spring mvc project 등록 안되는 경우

* SVN 에서 check out 받은 후 간혹 tomcat server 에 프로젝트가 등록 안되는 경우 있음(spring mvc project 로 생성한 프로젝트에 대해서)

1. 위와 같은 현상 시에는 아래와 같이 처리
  + Windows > Preferences > Server > Runtime Environments 에 tomcat 서버 등록
  + 프로젝트 우클릭 > Run As > Run On Server 에서 등록한 tomcat 서버 선택


2. 1번으로 안되는 경우 아래와 같이 처리
  + Windows > Preferences > Server > Runtime Environments 에 tomcat 서버 등록
  + 프로젝트 우클릭 > Properties > Project Facets 이동
    - Dynamic Web Module 체크 후 2.5 변경
    - Java 체크 1.8 변경
    - JavaScript 체크 1.0 변견
    <br> ※ 본인 프로젝트 버전에 맞추기
  + Apply 클릭 전 하단 Further configuration available 항목 클릭
  + Source folders on build path 설정은 build 항목이니 그대로 두고 Next
  + Context root 는 그대로, Content directory src\main\webapp 으로 변경 후 OK
  + Prject Facets Apply
  + server에서 해당 프로젝트 추가 가능

* 출처 : https://roxxy.tistory.com/m/entry/Spring-eclipse-Tomcat%EC%97%90-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%B6%94%EA%B0%80-%EC%95%88%EB%90%A0-%EB%95%8C
