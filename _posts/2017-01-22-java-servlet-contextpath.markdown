---
layout: post
title: "java servlet 에서 contextpath 를 이용한 정적파일 서비스"
date: 2017-01-22 23:08:00 +0900
categories: java
---

java servlet 으로 웹 서비스를 만들때 jsp 에서 css, javascript 와 같은 static file 을 서비스를 해야한다. css, javascript 등의 파일은 주로 WEB-INF 밑에 저장하는 경우가 있는 데, jsp 파일 내에서 경로를 어떻게 설정해주어야할지 애매했다.

빌드 결과로 나오는 (projectname).war 을 그대로 tomcat 에 올릴 경우 아래 경로가 서비스 기본 경로로 잡힌다.

```
http://host/(projectname)/
```

그럼 jsp 내에서 css, js, img 등의 static 파일의 경로를 지정할 떄, 절대경로로 앞에 프로젝트명을 포함해서 붙여야하나 라는 고민이 생긴다.

```
<a href="/(projectname)/static/img/test.jpg>
```

테스트와 배포환경을 위와 같이 동일하게 가져간다면 저렇게 배포해도 큰 문제는 없을 것이다. 하지만 서비스별로 별도의 도메인을 부여하고 그 도메인이 모든 경로의 root 가 된다면, 배포할 떄마다 static 파일의 링크 주소를 다 바꿔줘야하는 문제가 생긴다.

바로 아래와 같은 상황이다.

```
# 프로젝트 root가 http://127.0.0.1/(projectname) 일 경우
<a href="/(projectname)/static/img/test.jpg>

# 프로젝트 root가 http://projectname.com 일 경우
<a href="/static/img/test.jpg">
```

이러면 매번 배포시마다 큰 스트레스 일 것이다. 이 방법을 유연하게 해결하기 위해 projectname 을 서버 설정에서 가져오도록 하면 해결이 될까? 라는 아이디어를 테스트해봤다.

검색해보니 각 프로젝트의 servlet 의 경로는 contextPath 라는 값으로 가져올 수 있었고 아래와 같이 jsp 에서 가져올 수 있다.

```jsp
<%= request.getContextPath() %>
```

그럼 아래와 같이 static path 경로를 설정하면 project 경로가 바뀌더라도 자동으로 반영되기에, 매 배포마다 변경해줄 필요가 없다.

```jsp
<a href="<%= request.getContextPath() %>/static/img/test.jpg>
```

이로써 문제는 해결되었지만 매번 저렇게 긴 문장을 써야하는게 번거롭다. 그래서 저 문장을 변수화 하기로 했다.

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<c:set var="static_path" value="#{pageContext.request.contextPath}/static"/>

<a href="${static_path}/img/test.jpg">

```

좀 더 간편하게 쓸 수 있게 되었고, 경로문제도 말끔히 해결되었다.

