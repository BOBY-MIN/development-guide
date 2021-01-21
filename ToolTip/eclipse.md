
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

** 단축키 등록방법
window > preferences > general > keys 에서 등록
```
