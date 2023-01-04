---
title: Servlet MVC
sidebar: servlet_sidebar
summary: "Servlet MVC 구조에 대한 내용입니다."
permalink: servlet_mvc.html
folder: servlet
---

## MVC Pattern

{% include image.html
file='java-web-servlet-mvc-1.png'
%}

{% include image.html
file='java-web-servlet-mvc-3.png'
%}

{% include image.html
file='java-web-servlet-mvc-2.png'
%}

{% include image.html
file='java-web-servlet-mvc-4.png'
%}

### 도메인 모델(Domain Model)

* 비즈니스 로직에서 처리하는 데이터를 의미합니다.
* 도메인 모델 역할을 하는 클래스를 도메인 객체(Domain Object), 
VO(Value Object), DTO(Data Transfer Object) 라고 합니다.
* 도메인 객체는 비즈니스 로직을 처리하지 않는다는 점에 주의해야 합니다.

### 서비스 모델(Service Model)

* 도메인 객체를 이용해서 비즈니스 로직을 처리합니다.
* 해당 데이터에 대한 액세스를 제공합니다.

---

## RequestDispatcher

* Servlet에서 응답 View 선택할 때 사용합니다.
* 사용자의 원래 요청을 다른 서블릿이나 JSP페이지 등에 전달합니다.
* `RequestDispatcher`는 `request`객체를 이용하여 가져올 수 있습니다. 

~~~java

RequestDispatcher view = request.getRequestDispatcher("/result.jsp" );
request.setAttribute("Result", "안녕하세요");
view.forward( request, response );

~~~

{% include image.html
file='java-web-servlet-mvc-5.png'
%}

{% include image.html
file='java-web-servlet-mvc-7.png'
%}

{% include image.html
file='java-web-servlet-mvc-8.png'
%}
<br>

--- 

## Scope

{% include image.html
file='java-web-servlet-mvc-6.png'
%}
<br><br>

---

## 도서대여, 반납 MVC

* 지금까지 배운 내용으로 도서대여, 반납 Web App을 구현해보도록 하죠.
* JSP를 이용한 버전과 Front-End Web App을 이용하는 버전 두 가지로 구현합니다. 
<br><br>


End.

{% include links.html %}
