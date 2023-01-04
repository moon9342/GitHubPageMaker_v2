---
title: JSP
sidebar: servlet_sidebar
summary: "JSP의 동작방식에 대한 내용입니다."
permalink: servlet_jsp.html
folder: servlet
---

## JSP 기본

* `JSP`는 Java를 이용한 서버 사이드 템플릿 엔진입니다. 
* `JSP` 는 자바를 웹서버에서 쉽게 쓰기 위한 기술이며 언어가 아니라는 점에 주의해야 합니다.
*  `Java Server Pages`의 약자이며, 오라클에서 자바상표권 문제로 오픈소스인 JSP는 자카르타 서버 페이지로 이름을 
바꾸었습니다. Java의 점유율을 대폭 상승시킨 대표적 기술입니다.
<br><br>

* `JSP` page는 단적으로 표현하면 Java 코드를 포함하는 `html`입니다.
* JSP page는 `Web Container`에 의해 `Servlet`으로 변환 되며 변환된 Servlet은 해당 JSP page에 대한 요청을 처리합니다. 
결국은 Servlet으로 실행된다는 의미입니다. 

{% include image.html
file='java-web-jsp.png'
%}

{% include image.html
file='java-web-jsp-process-1.png'
%}

{% include image.html
file='java-web-jsp-process-2.png'
%}
<br><br>

* JSP 파일이 변경되지 않는다면, `.jsp` 파일에 대한 컴파일은 다시 일어나지 않습니다.
* JSP 파일이 변경될 때 마다, Web Container는 translation, compile, load, initialization 과정을 수행하게 됩니다.
* JSP page의 배포 환경은 HTML과 동일합니다. 즉, `WEB_ROOT` 폴더 하단에 저장해서 배포되게 됩니다. 
<br><br>

---

## JSP Scripting Element

{% include image.html
file='java-web-jsp-scripting-element.png'
%}

~~~html

<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<form action="hello.jsp" method="post">
		이름 : <input type="text" name="myname">
		<input type="submit" value="서버로 전송">
	</form>
</body>
</html>

~~~

~~~java
{% raw %}

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%! private static final String DEFAULT_NAME = "World!"; %>    
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<%-- JSP 주석 --%>
<%
	request.setCharacterEncoding("UTF-8");
	String name = request.getParameter("myname");
	if ((name == null) || name.length() == 0) {
		name = DEFAULT_NAME;
	}
%>
<b>Hello, <%= name %></b>
</body>
</html>

{% endraw %}
~~~

### directive

{% include image.html
file='java-web-jsp-scripting-element-1.png'
%}
<br><br>

### declaration

{% include image.html
file='java-web-jsp-scripting-element-2.png'
%}
<br><br>

### scriptlet

{% include image.html
file='java-web-jsp-scripting-element-3.png'
%}
<br><br>

### expression

{% include image.html
file='java-web-jsp-scripting-element-4.png'
%}
<br><br>

---

## JSP 내장객체

{% include image.html
file='java-web-jsp-object-1.png'
%}

{% include image.html
file='java-web-jsp-object-2.png'
%}
<br><br>


End.

{% include links.html %}
