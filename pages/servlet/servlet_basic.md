---
title: Servlet Basic
sidebar: servlet_sidebar
summary: "Servlet의 동작방식에 대한 내용입니다."
permalink: servlet_basic.html
folder: servlet
---

## Encoding 설정

{% include image.html
file='java-web-encoding-utf8-1.png'
%}

{% include image.html
file='java-web-encoding-utf8-2.png'
%}

{% include image.html
file='java-web-encoding-utf8-3.png'
%}

{% include image.html
file='java-web-encoding-utf8-4.png'
%}

{% include image.html
file='java-web-encoding-utf8-5.png'
%}
<br><br>

---

## Web Project 생성

* Apache Tomcat 설치 후 Dynamic Web Project 생성합니다.
* 간단한 static web(HTML)을 구현하고 실행합니다.

{% include image.html
file='java-web-dynamic-project-create.png'
%}
<br>

---

## Dynamic Web

{% include image.html
file='java-web-servlet-need.png'
%}
<br>

* 자바 서블릿(`Java Servlet`)은 자바를 사용하여 웹 페이지를 동적으로 생성하는 서버 측 프로그램을 의미합니다.
* 현재 `Servlet`을 이용하여 다양한 웹 시스템이 구현되고 있습니다.
* 서블릿은 HTTP 요청을 해석하고 응답을 동적으로 생성한 후 HTML 페이지를 포함하는 응답을 브라우저에 보내는 작업을 수행합니다.
* 서블릿은 컴포넌트 컨테이너(`Component Container`) 구조 내에서 실행됩니다.
이 컨테이너를 다른말로 웹 컨테이너(서블릿 엔진)이라고도 합니다.

---

## 간단한 서블릿 프로그램 (Hello World) 작성

~~~java 

protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
			
		response.setContentType("text/html; charset=UTF-8");
		
		PrintWriter out = response.getWriter();
		out.println("<html>");
		out.println("<head>");
		out.println("<title>로그인 결과</title>");
		out.println("</head>");
		out.println("<body>");
		out.println("안녕하세요!!");
		out.println("</body>");
		out.println("</html>");
		out.close();
}

~~~

## Servlet에 인자 전달

### POST 방식

* form을 이용한 인자 전달방식 실습

### GET 방식

* 브라우저의 주소창에 직접 입력방식으로 실습(Query String 이용)
* `http://127.0.0.1:8080/lab/login?loginId=aaa&pwd=bbb`
* 링크(a 태그 이용)를 이용한 인자 전달방식 실습
* `<a href = "http://127.0.0.1:8080/lab/login?loginId=aaa&pwd=bbb">이동</a>`

---

## Servlet 실행 프로세스

{% include image.html
file='java-web-servlet-process.png'
%}

---

## Servlet 분석

{% include image.html
file='java-web-servlet-code.png'
%}

{% include image.html
file='java-web-servlet-login-code.png'
%}

---

## 한글처리

### GET 방식

* GET 방식의 파라미터 전송은 데이터가 `Query String` 형식으로 `URL`에 포함되어 전송됩니다. 
* 파라미터로 넘길 Data 양이 적은 경우에 사용합니다. (URL 길이의 제한때문에 그렇습니다.)
* 반드시 필요한 경우가 아니면 `POST`방식을 이용하는게 좋습니다.
* `Tomcat`의 `server.xml`에 다음의 설정을 포함합니다.

~~~xml

<Connector URIEncoding="EUC-KR" 
           connectionTimeout="20000" 
           port="8080" 
           protocol="HTTP/1.1" 
           redirectPort="8443"/>
    
~~~

### POST 방식

* POST 방식은 파라미터가 요청(Request)의 `body`에 포함되어 전송됩니다.
* 파라미터로 넘길 Data 양이 큰 경우에 사용하며 파라미터의 내용이 `URL`에 보여지지 않아야 하는 경우에 사용합니다.
* 가급적 POST 방식을 사용하는 것이 좋습니다.

{% include image.html
file='java-web-servlet-post-korean.png'
%}

--- 

## Servlet Context

{% include image.html
file='java-web-servlet-context.png'
%}

{% include image.html
file='java-web-servlet-context-1.png'
%}

{% include image.html
file='java-web-servlet-context-2.png'
%}

{% include image.html
file='java-web-servlet-context-3.png'
%}

---

## Session

* 기본적으로 HTTP는 연결 당 하나의 요청만 처리하는 무상태(stateless) 프로토콜입니다.
* 웹 서버는 하나의 요청에 대해 응답한 후 바로 연결을 종료합니다. 따라서 동일한 사용자가 
다시 요청을 했을 때 웹 서버는 동일한 사용자라는 것을 인식하지 못하게 됩니다.
* 이런 HTTP 프로토콜의 문제점을 극복하기 위해서 사용자의 상태 정보를 관리하는 매커니즘이 필요하게 되는데 
이러한 매커니즘에는 세션, 쿠키, URL 재작성이 있습니다. 

{% include image.html
file='java-web-servlet-session.png'
%}

{% include image.html
file='java-web-servlet-session-1.png'
%}

{% include image.html
file='java-web-servlet-session-2.png'
%}

{% include image.html
file='java-web-servlet-session-3.png'
%}

{% include image.html
file='java-web-servlet-session-4.png'
%}

{% include image.html
file='java-web-servlet-session-5.png'
%}




End.

{% include links.html %}
