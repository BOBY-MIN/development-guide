
### lombok 적용

> pom.xml

```
<dependency>
	<groupId>org.projectlombok</groupId>
	<artifactId>lombok</artifactId>
	<version>1.18.10</version>
	<scope>provided</scope>
</dependency>
```

* 추가 후 local은 따로 설치과정 필요함.(구글링)
* 운용서버 배포 시에는 war로 배포하는 경우 컴파일된 파일이 넘어가므로 따로 설치할 필요 없음.


> vo

```
@Getter
@Setter
@ToString
@NoArgsConstructor
@Builder
```

* 어노테이션 다양하게 제공됨.
* mybatis 이용 시 builder 클래스는 따로 생성하여 사용 필요(mybatis 값 주입 시 기본 생성자 없으면 에러나는데 @NoArgsConstructor 추가하더라도 @Builder 추가하면 에러 발생함)
