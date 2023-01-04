---
title: Servlet Introduction
sidebar: servlet_sidebar
summary: "Servlet의 개요 및 특징에 관한 내용입니다."
permalink: servlet_introduction.html
folder: servlet
---

## Servlet Introduction

자바 서블릿(`Java Servlet`)은 자바를 사용하여 웹 페이지를 동적으로 생성하는 서버 측 프로그램을 의미합니다.
<br><br>

Servlet에 대해 알아보기 이전에 기본적인 개념들을 한번 정리하고 가도록 하겠습니다.

---

## 기본 개념

### Internet

{% include image.html
file='java-internet.png'
%}
<br><br>

### IP Address

{% include image.html
file='java-web-ip-address.png'
%}
<br><br>

### DNS (Domain Name System)

{% include image.html
file='java-web-dns.png'
%}
<br><br>

### Port

* IP 주소는 하나의 컴퓨터에 할당된 번호입니다.
* 이 컴퓨터에서 network를 사용하는 여러 개의 프로그램이 동시에 실행되고 있다고 가정하면
이 때, IP 주소 만을 가지고는 이 컴퓨터의 특정 network 프로그램에 접근할 수 없습니다.
* 이런 상황에서 해당 프로그램들을 구분할 수 있게 해 주는 것이 바로 port 번호입니다.
<br><br>

### HyperText

* `HyperText`(하이퍼텍스트)는 사용자의 선택에 따라 관련된 정보 쪽으로 옮겨갈 수 있도록 조직화된 정보를
의미합니다. 이러한 관련정보쪽으로 옮겨 갈수록 도와주는 것이 바로 링크, 또는 하이퍼링크라고 부릅니다.
* 하이퍼텍스트는 월드 와이드 웹 (World Wide Web, WWW)의 발명을 이끈 주요개념입니다.
* 웹이란 결국은 많은 수의 하이퍼텍스트 링크에 의해 거대한 양의 정보가 서로 연결되어 있는 것을 의미하죠.
<br><br>

### World Wide Web(WWW, W3)

* 인터넷에 연결된 컴퓨터들을 통해 사람들이 정보를 공유할 수 있는 전세계적인 정보 공간을 의미하며 
간단히 웹(Web)이라고 부릅니다.
* 이 용어는 인터넷과 동의어로 쓰이는 경우가 많으나 엄격히 말해 서로 다른 개념입니다. 
웹은 인터넷 상에서 동작하는 하나의 서비스일 뿐이죠.
* 하이퍼텍스트는 웹 브라우저라 불리는 프로그램을 통해 웹 서버에서 문서나 웹 페이지 등의 정보 조각을 읽어 들여 
출력하는 형태로 보이게 됩니다. 그러고 나서 사용자는 각 페이지에 있는 하이퍼링크를 따라 다른 문서로 이동하거나, 
그 페이지를 서비스하고 있는 서버로 일련의 정보를 보낼 수도 있습니다.
* 하이퍼링크를 따라 이동하는 행위를 흔히 웹 서핑 혹은 웹 브라우징이라 합니다. 그리고 관련된 내용들이 모여있는 
웹 페이지들의 집합을 웹 사이트라 합니다.
<br><br>

### HTTP

* `HyperText Transfer Protocol`의 약자로 `Web`에서 정보를 주고 받기 위해 사용하는 프로토콜입니다.
* 주로 HTML 문서를 주고 받는 데 사용하며, `Client-Server` 구조를 이용하고 서버 프로세스의 기본 포트번호는 80번을
사용합니다.
* 클라이언트와 서버 사이에 이루어지는 요청/응답(`request`/`response`) 프로토콜입니다.
* HTTP 프로토콜에서는 클라이언트의 데이터 요청에 대한 응답 후 바로 연결을 해제 합니다.
지속적인 연결을 하고 있으면서 데이터를 주고 받는 것이 아니라 필요할 때마다 연결하고 데이터를 받자마자 
바로 연결을 끊어버리는 방식이 바로 HTTP 프로토콜의 연결 메커니즘입니다. 
이런 프로토콜을 `stateless protocol`이라고 부르는데 장점은 많은 사용자가 동시에 서버 접속이 가능해진다는 점이며
단점은 각 사용자의 행위를 `tracking`하기 힘들다는 것입니다.

### Web Browser

* 웹 브라우저는 웹 서버로부터 받은 HTML 문서나 파일과 연동하고 출력하는 응용 소프트웨어입니다.
* 개인용 컴퓨터에서 주로 쓰는 웹 브라우저에는 MS의 인터넷 익스플로러(Edge), 
모질라 파이어폭스, 오페라사의 오페라, 애플의 사파리, 구글의 크롬 등이 있습니다.
* 웹 브라우저는 웹 서버로부터 웹 페이지를 가져오기 위해 HTTP 프로토콜을 이용하고 웹 페이지를 가져올 뿐 아니라 
웹 서버에 정보를 송신하기도 합니다.
<br><br>

### Web Server

* 웹 브라우저(web client)로부터 HTTP 요청을 받아들이고, HTML 문서와 같은 웹 페이지를 전송해주는 서비스 프로그램을
지칭합니다.
* 또한 위의 기능을 제공하는 컴퓨터 프로그램을 실행하는 컴퓨터(하드웨어)를 웹 서버라고 부르기도 합니다.
<br><br>

### URL

{% include image.html
file='java-web-url-structure.png'
%}
<br><br>

### HTTP Client-Server 구조

{% include image.html
file='java-web-client-server-process.png'
%}
<br><br>

### HTTP Request

{% include image.html
file='java-web-http-request-detail.png'
%}
<br><br>

### HTTP Response

{% include image.html
file='java-web-http-response-detail.png'
%}
<br><br>

End.

{% include links.html %}
