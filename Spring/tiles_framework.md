
### tiles_framework 적용

> pom.xml

```
<apachetiles.version>3.0.3</apachetiles.version>

<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-core</artifactId>
    <version>${apachetiles.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-api</artifactId>
    <version>${apachetiles.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-servlet</artifactId>
    <version>${apachetiles.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-jsp</artifactId>
    <version>${apachetiles.version}</version>
</dependency>
```

* tiles_framework 예전 버전 사용하는 경우 structs-tiles 사용으로 보안문제의 소지가 있으므로 dependency 주의

> servlet-context.xml

```
<!-- tiles framework viewresolver start -->
<beans:bean id="tilesViewResolver" class="org.springframework.web.servlet.view.UrlBasedViewResolver">
    <beans:property name="viewClass" value="org.springframework.web.servlet.view.tiles3.TilesView" />
    <beans:property name="order" value="0" />
</beans:bean>

<beans:bean id="tilesConfigurer" class="org.springframework.web.servlet.view.tiles3.TilesConfigurer">
    <beans:property name="definitions">
        <beans:list>
             <beans:value>/resources/tiles/tiles.xml</beans:value>
        </beans:list>
    </beans:property>
</beans:bean>
<!-- tiles framework viewresolver end -->

<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<beans:property name="prefix" value="/WEB-INF/views/" />
	<beans:property name="suffix" value=".jsp" />
	<beans:property name="order" value="1"/>
</beans:bean>
```

* 기존 viewresolver 위에 tiles-framework 용 viewresolver으로 씌워서 tiles.xml에서 지정한 화면으로 이동되도록 처리함.
* order 순위를 우선 적용하도록 해야함.

> tiles.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE tiles-definitions PUBLIC
			"-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN"
			"http://tiles.apache.org/dtds/tiles-config_3_0.dtd">

<tiles-definitions> <!-- base-definition -->

	<!-- 상단 메뉴 포함된 기본 화면(고객용 화면) -->
	<definition name="navi_home_form" template="/resources/tiles/layouts/homeLayout.jsp">
		<put-attribute name="title" value="" />
		<put-attribute name="header" value="/WEB-INF/views/header.jsp" />
		<put-attribute name="body" value="" />
		<put-attribute name="footer" value="/WEB-INF/views/footer.jsp" />
	</definition>

	<definition name="*" extends="navi_home_form">
		<put-attribute name="title" value="제목" />
		<put-attribute name="body"	value="/WEB-INF/views/{1}.jsp" />
	</definition>

	<definition name="*/*" extends="navi_home_form">
		<put-attribute name="title" value="제목" />
		<put-attribute name="body"	value="/WEB-INF/views/{1}/{2}.jsp" />
	</definition>

	<definition name="*/*/*" extends="navi_home_form">
		<put-attribute name="title" value="제목" />
		<put-attribute name="body"	value="/WEB-INF/views/{1}/{2}/{3}.jsp" />
	</definition>

</tiles-definitions>
```

* 서버에서 넘겨준 문자열을 기준으로 맵핑되며 해당 문자열의 jsp를 찾아서 body를 재정의함.

> homeLayout.jsp

```
<%@ taglib uri="http://tiles.apache.org/tags-tiles" prefix="tiles"%>

<%@ page session="false"%>
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<title><tiles:getAsString name="title" /></title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

</head>

<body>
<div>

	<header id="homeLayout_header" >
		<tiles:insertAttribute name="header" />
	</header>
	<section id="homeLayout_content">
		<tiles:insertAttribute name="body" />
	</section>
	<footer id="homeLayout_footer">
		<tiles:insertAttribute name="footer" />
	</footer>

</div>

</body>
</html>
```

* 기본 form 예제

> controller return 예시

```
return "screen/test";
```

* /WEB-INF/views/screen/test.jsp 찾아서 body 변경함.
