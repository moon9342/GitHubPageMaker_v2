---
title: Python Django Introduction
sidebar: python_sidebar
summary: "Python Django 개요에 관한 내용입니다."
permalink: python_django-introduction.html
folder: python
---

## Web introduction

`Django`에 대해서 알아보기전에 기본적인 몇가지 개념부터 살펴보기로 하겠습니다.
<br><br>

* `Web Server`
: 인터넷을 통해 요청된 web contents의 전달을 도와주는 하드웨어와 소프트웨어를 지칭합니다. 
일반적으로 정적 resource에 대한 요청을 처리하며 Web Server가 관장하는 resource를 그대로 보내주는 역할을 수행합니다.

{% include image.html
file='python-django-web-server.png'
%}

* `CGI(Common Gateway Interface)`
: Web Server에서 Application을 수행시키기위한 규약입니다. 정적 기능을 하는 Web Server가 동적인 프로그램을 수행시키기 
위한 목적으로 만들어졌다고 생각하시면 됩니다. 클라이언트의 요청을 웹서버(Apache, NginX)가 받고 웹서버가 직접 프로그램을 실행시키는 
구조입니다. 대표적인 구현 프로그래밍 언어로는 C, Perl 등이 있습니다.

{% include image.html
file='python-django-web-server-cgi.png'
%}

* `WAS(Web Application Server)`
: 일반적으로 Web Server와 Web Application을 동작시킬 수 있는 Web Container로 구성되며 클라이언트의 요청을 
웹서버(Apache, NginX)가 받고 Web Server가 수행할 수 없는 요청이기 때문에 WAS(Tomcat, JBoss)에게로 request를
전달하면 WAS가 프로그램을 실행하는 구조입니다.

{% include image.html
file='python-django-web-server-was.png'
%}

* `WSGI` 
: python에 종속된 개념입니다. python에서 python script(Web Application)이 Web Server와 통신하기 위한 interface(규약)입니다. 
클라이언트의 요청을 웹서버(Apache, NginX)가 받고 이를 WSGI Server(middleware)로 request를 전달하면 WSGI Server가 WSGI를 
지원하는 Web Application(Django, flask)을 실행시키는 구조입니다. WSGI middleware는 WSGI 규약의 구현체라고 볼 수 있는데 아래의
그림과 같은 구조로 생각하면 됩니다. 아래의 그림에서 녹색부분이 결국 WAS이고 파란색으로 되어 있는 두 부분을 합쳐서 WSGI 
middleware라고 합니다. 하나로 구성된 것이 아니라 일부는 Web Server에 내장되어있고 일부는 프로세스로 띄워진 형태입니다. 
이런 WSGI middleware의 종류는 mod_wsgi, Gunicorn, uWSGI등이 있습니다.

{% include image.html
file='wsgi_structure.png'
%}

---

## Django introduction

`Django`는 Python으로 만들어진 무료 오픈소스 웹 애플리케이션 프레임워크(web application framework)입니다.
<br><br>

우리는 Web Application을 개발할 때 굉장히 빈번하게 반복적인 문제를 해결해야 될 때가 많습니다.
사용자들의 회원가입, 로그인, 로그아웃 등의 사용자 인증 및 관리에 대한 구현 혹은 게시판과 같이
사용자들이 커뮤니케이션을 할 수 있는 공간과 파일 업로드, 다운로드를 할 수 있도록 하는
기능 구현등이 이에 해당합니다.
<br><br>

우리가 아는 거의 모든 웹 페이지들은 이러한 기능들을 기본적으로 제공합니다.
이러한 기능들을 구현할 때 편리한 개발환경을 만들어 놓은 것이 바로 프레임워크라고 생각하시면 됩니다.

---

## Django 특징

* MVC pattern 기반의 `MVT`
: Django는 `MVC(Model-View-Controller)`기반의 Framework입니다. 하지만 기존의 View의 개념을 Template이 수행하고 
Controller의 역할을 View가 수행합니다. 그래서 Django를 `MVT(Model-View-Template)` Framework이라고 부르기도 합니다.

* `ORM`(Object Relational Mapping - 객체관계매핑) 
: Django의 ORM은 데이터베이스 시스템과 데이터 모델 class를 연결시키는 역할을 수행합니다. 데이터베이스 테이블은 결국 데이터 모델 
class와 매핑되며 각각의 record는 class의 instance로 표현되게 됩니다. 이런 객체를 우리가 제어하면 Database의 내용을 
제어할 수 있게 됩니다. ORM의 장점 중 하나는 이미 구축한 데이터베이스 시스템을 다른 데이터베이스로 변경하는 경우에 설정만 
변경하면 바로 새로운 Database에 우리의 시스템을 적용할 수 있다는 것입니다.

* 자동으로 구성되는 관리자 화면
: Django는 프로젝트를 시작하는 시점에 기본기능으로 관리자 화면을 제공합니다. 이런 관리자 화면을 이용하여 Application에서 
사용하는 데이터들을 쉽게 생성하거나 변경할 수 있으며 개발자가 별도로 관리 기능을 개발할 필요가 없습니다.

* `Elegant URL` 
: 웹 프로그래밍에서 URL design은 필수적인 작업인데 Django는 이런 URL design을 직관적이고 쉽게 할 수 있습니다. 
이를  Elegant URL이라고 합니다. 특히 `Regular Expression`(정규표현식)을 이용해 URL을 디자인 할 수 있는게 특징입니다.

* 자체 Template 시스템 
: Django는 내부적으로 확장이 가능하고 디자인이 쉬운 강력한 Template 시스템을 가지고 있습니다. 이를 이용하면 HTML과 같은 언어를 
좀 더 쉽게 다룰 수 있습니다.

* Cache 시스템 
: 내부적으로 자주 사용하는 내용을 저장해 두었다가 재사용하여 성능을 높이는 기능입니다.

* 소스 변경사항 자동반영 
: Django에서는 .py 파일의 변경 여부를 감시하고 있다가 변경이 되면 변경 내역을 바로 반영해 주기 때문에 웹 서버를 다시 시작할 필요 없이 
개발을 할 수 있습니다.

---

## Django 설치

Windows 10을 기준으로 다음과 같이 `pip`(python install package)를 이용하여
`PyPI`(Python Package Index)에 있는 package를 설치하면 됩니다.
만약 anaconda환경이면 conda 명령을 이용하여 설치해도 됩니다.

> `conda install django`

Django의 설치 디렉토리를 알고 싶다면 다음의 코드를 실행하면 됩니다.

> `python -c "import django; print(django.__path__)"`

또한 Django는 python환경에서 동작하는 package이므로, Django가 정상적으로
설치되었는지를 확인하려면 다음과 같은 명령을 이용하면 됩니다.

> `python -m django --version`

일반적인 python환경에서는 다음과 같이 pip 명령을 이용하여 설치하면 됩니다.

> `pip install Django`

<br>
원래는 Django의 Application개발 방식에 대한 설명을 해야 합니다.
`MVT` 방식에 대한 이해라던지 `URLConf` 처리 순서, `Model`, `View`, `Template`의 동작원리와
동작순서같은 것들 말이죠.  
<br><br>

처음 Django를 접하거나 아예 웹 프로그래밍을 접해보지 않은 사람들에게는 사실 너무 어려운
내용이기는 합니다. 하지만 기본적인 개념을 알고 시작하는것이 좋기 때문에 간단하게나마
설명을 하고 그 후에 조금 쉽게 풀어가기 위해 간단한 Application을 하나 만들어 가면서
부가적으로 설명을 곁들이는 방식으로 진행하겠습니다.
<br><br>

일단 먼저 용어부터 정리하고 들어가야 할 듯 합니다. Django에서는 우리가 구현하는
웹 어플리케이션을 다른 Framework에 비해 조금 더 구체적으로 명시하고 있습니다.
<br><br>

다른 언어(예를 들면 Java Servlet)에서는 전체 프로그램을 `Web Application`이라고 부릅니다.
하지만 Django에서는 약간 다르게 표현합니다. Django에서는 웹 사이트(웹 어플리케이션)에
대한 전체 프로그램을 `Project`(프로젝트)라고 합니다. 
그리고 모듈화된 단위 프로그램들을 `Application`(어플리케이션)이라고 부릅니다. 즉,
Application들이 모여서 Project를 개발하는 개념으로 이해하시면 됩니다.

---

## MVT Pattern

일반적으로 application을 개발할 때 흔히 사용하는 구조는 `MVC` pattern입니다.
MVC pattern은 프로그램의 구성 요소를 `Model-View-Controller`로 구분해 한 요소가
다른 요소에 미치는 영향을 최소화 하도록 설계하는 방식을 의미합니다.
<br><br>

Django도 이런 MVC pattern 개념을 그대로 받아들여서 사용합니다. 단, MVC pattern에서의
View를 `Template`으로 Controller를 `View`라고 표현하며 이를 `MVT` pattern이라고 합니다.
<br><br>

장고의 MVT pattern을 그림을 묘사하면 아래와 같습니다.

{% include image.html
file='python-django-mvt.png'
%}
<br>

웹 클라이언트로부터 요청을 받고, Django에서 MVT pattern에 따라서 처리하는 과정을
개념적으로 살펴보면 다음과 같습니다.
<br><br>

* 클라이언트로부터 요청을 받으면 `URLConf`를 이용하여 URL을 분석합니다.
* URL 분석 결과를 통해 해당 URL에 대한 처리를 담당한 `View`를 결정합니다.
* View는 자신의 로직을 실행하면서 데이터베이스 처리가 필요하면 `Model`을 통해 처리합니다.
* View는 로직처리가 끝나면 `Template`을 사용하여 클라이언트에게 전송할 HTML 파일을 생성합니다.
* View는 최종결과로 HTML 파일을 클라이언트에게 전송합니다.

---

## Model

`Model`은 사용될 데이터에 대한 정의를 담고 있는 Django의 `class`입니다. Django는 `ORM` 기법을
사용하여 데이터베이스를 class로 mapping해서 사용할 수 있습니다. 쉽게 말하면 하나의 Model class는
하나의 Table에 mapping되고 Model class의 속성은 Table의 column에 mapping됩니다.
<br><br>

이런 ORM기법을 이용하면 Application에서는 데이터베이스에 대한 access를 SQL없이도 class를 다루는 것으로 할 수 있습니다. 
이러한 Model class는 `models.py` 파일에 저장하게 됩니다.
<br><br>

장점은 무엇일까요?
<br><br>

ORM을 이용하면 Database에 대한 처리를 SQL없이 class를 다루는 것처럼 할 수 있어서 편리합니다. 또한 Database Engine을 
바꾸어도 ORM을 통한 API는 변경할 필요가 없습니다.
<br><br>

간단한 예로 아래의 Model class를 예로 들어보죠

~~~python

from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)

~~~

위의 Model class의 정의는 내부적으로 다음의 SQL 명령을 사용하여 Database Table을
생성하게 됩니다.

~~~sql

CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);

~~~

위에서 보듯이 Table명은 application명과 Model class명을 `_` 로 연결하고 모두 소문자로 표현합니다. 
다른 이름으로 직접 지정할 수 도 있습니다.

`PRIMARY KEY`는 Person class에서 지정하지 않아도 Django에서 자동으로 부여합니다. 
이 부분 역시 직접 지정할 수 도 있습니다.

---

## URLConf

클라이언트로부터 요청을 받으면 Django는 가장 먼저 요청에 들어 있는 URL을 분석합니다. 즉,
URL이 `urls.py` 파일에 정의된 URL pattern과 매칭되는지를 분석한다는 거죠.
<br><br>

Python의 URL 정의 방식을 `Elegant URL`이라고 부릅니다. 기존 다른 언어의 URL 정의방식보다
쉽고 직관적이기 때문입니다.
<br><br>

URL을 정의하기 위해서는 `urls.py` 파일에 URL과 View 함수를 매핑하는 코드를 작성해야 합니다.
이를 `URLConf` 라고 합니다. 아래는 예제로 작성된 `urls.py`입니다.

~~~python

from django.urls import path
from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]

~~~

웹 클라이언트가 웹 서버에 Request를 보낼 때, Django에서 URL을 분석하는 순서를 보면
다음과 같습니다.
<br><br>

* `settings.py` 파일의 `ROOT_URLCONF` 항목을 읽어서 최상위 URLConf 파일의 위치를 알아냅니다.
* URLConf를 로딩하여 URL list를 검사합니다.
* 검사 순서는 위에서부터 순서대로 URL list를 검사하며 URL pattern이 매치되면 검사를 종료합니다.
* 매치된 View 함수를 호출합니다. 호출 시 HttpRequest객체와 URL에서 추출된 데이터를 View 함수에
  인자로 넘겨줍니다.
* URL list 끝가지 검사했는데도 매칭에 실패하면 에러를 처리하는 View 함수를 호출합니다.
<br><br>

여기서 주의해야 할 부분은 `<int:month>` 처럼 꺽쇠를 사용하는 부분입니다. 이는 URL 에서 일부
데이터를 추출하기 위한 것으로 `<type:name>` 형태로 사용합니다. 만약 요청 URL이
`/articles/2020/`이라면 호출되는 View 함수는 `view.year_archive(request,year=2020)` 처럼
호출합니다.
<br><br>

이런 꺽쇠 부분을 Django에서는 `Path Converter`라고 부릅니다. 여기에서 사용되는 타입은
`str`, `int`, `slug`, `path` 등이 있습니다.
<br><br>

* `str` : `/`를 제외한 모든 문자열과 매치됩니다. 타입이 지정되지 않으면 default로 str입니다.
* `int` : 0 또는 양의 정수와 매치됩니다.
* `slug` : slug형식의 문자열과 매치됩니다. (ASCII, 숫자, 하이픈, 밑줄)
* `path` : `/`를 포함한 모든 문자열과 매치됩니다. 이는 URL의 일부가 아니라 전체를 추출하고자 할때 사용됩니다.
<br><br>

또한 `Regular Expression`(정규 표현식)을 사용해서 URL을 좀 더 세밀하게 표현 할 수 도 있습니다.
이 부분은 책이나 다른 문서를 참조하세요~

---

## View

View는 일반적으로 Request를 받아서 Database 처리 등 로직에 맞는 처리를 하고, 그 결과
데이터를 HTML로 변환하기 위해서 Template 처리를 한 후 최종 HTML로 된 응답 데이터를 웹
클라이언트에게 반환하는 역할을 수행합니다.
<br><br>

일반적으로 `views.py` 파일에 작성되며 다른 파일에 작성해도 상관은 없습니다.
<br><br>

간단한 예로 현재의 날짜와 시간을 HTML로 반환해주는 View 함수는 다음과 같습니다.

~~~python

from django.http import HttpResponse
import datetime

def current_datetime(request):
    now = datatime.datetime.now()
    html = "<html><body>현재 시각은 : %s</body></html>" % now
    return HttpResponse(html)

~~~

만약 Error를 반환하고 싶다면 아래처럼 `HttpResponseNotFound`와 같은
에러 응답 객체를 반환하면 됩니다.

~~~python

return HttpResponseNotFound("<h1>페이지를 찾을 수 없습니다.</h1>")

~~~

---

## Template

Django가 클라이언트에게 반환하는 최종 응답은 HTML text입니다. 개발자가
응답에 사용할 html 파일을 작성하면, 장고는 이를 해석해서 데이터를 적용해
최종 HTML text를 생성하고 이를 클라이언트에게 보내주게 됩니다.
<br><br>

이런 과정에서 개발자가 작성하는 html 파일을 `Template` 파일이라고 부릅니다.
<br><br>

Template file은 `.html` 확장자를 가지며 Django의 Template 시스템 문법에 맞게
작성합니다. 조심해야 할 사항은 이 Template 파일을 적절한 디렉토리에 위치시켜야
한다는 점 입니다.
<br><br>

Django에서 Template 파일을 찾을 때 `TEMPLATE_DIRS` 와 `INSTALLED_APPS`에서 지정된
application의 디렉토리를 검색합니다. 이 항목들은 `settings.py`에 지정되어 있습니다.
<br><br>

우리의 첫번째 Project는 `파이썬 웹 프로그래밍(한빛미디어)` 책에서 소개된 `Poll` project입니다.

그럼 다음 절에서 천천히 시작해 보도록 하죠.

End.

{% include links.html %}
