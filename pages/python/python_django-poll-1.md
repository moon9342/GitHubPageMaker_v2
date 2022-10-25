---
title: Python Django Poll Project(1)
sidebar: python_sidebar
summary: "Python Django Poll Project에 관한 내용입니다."
permalink: python_django-poll-1.html
folder: python
---

## Project 설계

우리가 예제로 개발할 application은 설문에 해당하는 질문을 보여준 후 질문에
포함되어 있는 답변 항목에 투표하면 그 결과를 알려주는 프로그램입니다.
<br><br>

이를 위해 3개의 page개발이 필요하면 다음과 같은 화면으로 구현할 것입니다.

{% include image.html
file='django-sample-ui.png'
%}
<br>

* index.html
: 질문의 리스트를 보여줍니다.

* detail.html
: 하나의 질문에 대해 투표할 수 있도록 답변 항목을 폼으로 보여줍니다.

* results.html
: 질문에 따른 투표 결과를 보여줍니다.

---

## Table 설계

* Question Table 설계

{% include image.html
file='django-sample-table1.png'
%}
<br>

* Choice Table 설계

{% include image.html
file='django-sample-table2.png'
%}

---

## 프로젝트의 뼈대

일단 프로젝트의 구성과 해당 파일의 역할에 대해서 알아보고 프로젝트 뼈대를
구성하는 코드를 실행해보도록 하겠습니다.

{% include image.html
file='django-sample-structure.png'
%}
<br>

* 최상위 디렉토리
: 프로젝트 관련 디렉토리 및 파일을 모아주는 최상위 루트 디렉토리입니다. 보통 settings.py 파일의 BASE_DIR 항목으로 지정됩니다.

* db.sqlite3
: SQLite3 데이터베이스 파일입니다. 테이블이 포함되어있습니다.

* manage.py
: 장고의 명령어를 처리하는 파일입니다.

* mysite 디렉토리
: 프로젝트명으로 만들어진 디렉토리입니다. 프로젝트 관련 파일들이 들어갑니다.

* settings.py
: 프로젝트 설정 파일입니다.

* urls.py
: 프로젝트 레벨의 URL 패턴을 정의하는 최상위 URLConf 파일입니다. 보통은 Application 디렉토리마다 
하위 urls.py 파일이 또 존재합니다.

* wsgi.py
: Apache와 같은 웹서버와 WSGI 규격으로 연동하기 위한 파일입니다.

* polls 디렉토리
: application명으로 만들어진 Application 디렉토리입니다. 해당 application 관련 파일들이 들어갑니다.

* admin.py
: Admin site에 모델 클래스를 등록해주는 파일입니다.

* apps.py
: Application의 설정 클래스를 정의하는 파일입니다.

* migrations 디렉토리
: 데이터베이스 변경사항을 관리하기 위한 디렉토리입니다. 데이터베이스에 추가, 삭제, 변경등이 발생하면 
변경 내역을 기록한 파일들이 위치합니다.

* models.py
: 데이터베이스 모델 클래스를 정의하는 파일입니다.

* views.py
: View 함수를 정의하는 파일입니다. 함수형 View 및 클래스형 View 모두 이 파일에 정의합니다.

* template 디렉토리
: template 파일들이 들어있습니다. 프로젝트 레벨과 application 레벨의 템플릿으로 구분합니다.

* static 디렉토리
: CSS, image, JavaScript 파일들이 들어있습니다. 이 역시 프로젝트 레벨과 application 레벨로 구분하며 
`static`이라는 이름으로 디렉토리를 생성합니다.

---

## Project Setup

시작은 프로젝트의 뼈대를 만드는 일부터 시작합니다. 프로젝트에 필요한
디렉토리 및 파일을 구성하고 설정 파일을 세팅하도록 하겠습니다.
<br><br>

간단하게 `C:/python-Django` 폴더를 하나 생성한 후 `command`창을 열어서
다음과 같이 입력해서 프로젝트를 시작합니다.

> **C:/python-Django> django-admin startproject mysite**

<br>

생성된 폴더 구조를 보면 `C:/python-Django/mysite` 가 생성되고 그 아래에 다시
`C:/python-Django/mysite/mysite` 폴더가 생성되게 됩니다.
<br><br>

여기서 상위 폴더인 `C:/python-Django/mysite` 폴더는 단순히 프로젝트 관련파일과
폴더를 모아놓은 폴더이기 때문에 다른 이름으로 변경할 것입니다. 하위 폴더인
`C:/python-Django/mysite/mysite` 폴더가 우리의 프로젝트 디렉토리 입니다.

> **C:/python-Django> move mysite MyFirstWebPoll**

<br>

이름을 변경한 후 해당 폴더로 이동합니다. 그리고 그 안에 `polls`라는 application을
생성하는 명령을 입력합니다.

> **C:/python-Django> cd C:/python-Django/MyFirstWebPoll**

> **C:/python-Django/MyFirstWebPoll> python manage.py startapp polls**

이 과정을 거치면 `polls`라는 이름의 application 디렉토리와 그 하위에 필요한 파일들을
자동으로 생성해 줍니다. 파일 이름은 Django가 알아서 설정하게 되는데 이렇게 개발에 반드시
필요한 파일들은 Django가 알아서 생성해주고 개발자는 그 내용을 채워넣는 식으로 진행됩니다.

---

## Project 설정파일 변경

프로젝트에 필요한 설정값은 `settings.py` 파일에 지정합니다.
<br><br>

지금부터는 PyCharm을 이용하기로 하죠.
<br><br>

* `ALLOWED_HOSTS`
: Django는 `DEBUG=True`이면 개발모드로 False이면 운영모드로 인식합니다. 운영 모드인 경우 ALLOWED_HOSTS에는 
반드시 서버의 IP나 Domain을 지정해야 합니다. 개발모드인 경우에는 값을 지정하지 않으면 `['localhost','127.0.0.1']`로 간주됩니다.
지금은 개발모드 이기 때문에 `['localhost','127.0.0.1']`로 설정합니다.

~~~python

ALLOWED_HOSTS = ['localhost', '127.0.0.1']

~~~

* `INSTALLED_APPS`
: 프로젝트에 포함되는 application들은 모두 설정 파일에 등록되어야 합니다. 따라서 우리가 개발하고 있는 `polls` application도 
등록해야 되겠죠. 일반적으로 application의 설정 클래스로 등록하게 됩니다.  `polls` application의 설정 클래스는 자동으로 
생성된 `apps.py` 파일안에 `PollsConfig`라고 정의되어 있습니다. 이 부분을 `INSTALLED_APPS`에 추가합니다.

~~~python

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'polls.apps.PollsConfig'
]

~~~

* `DATABASES`
: 프로젝트에 사용할 데이터베이스 엔진입니다. Django는 default로 SQLite3 데이터 베이스 엔진을 사용하도록 설정되어 있습니다. 
이번에는 `SQLite3`를 사용하고 다음 예제에서는 `MySQL`를 사용하도록 하겠습니다. 따라서 이번에는 설정을 변경하지 않겠습니다.
참고로 MySQL 설정은 아래의 코드와 같이 작성합니다. MySQL을 사용할 경우 `pip install mysqlclient`를 실행해 `mysqlclient` 
module을 설치해야 합니다.

~~~python

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

~~~

~~~python

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'my_db',
        'USER': 'root',
        'PASSWORD': 'root_password',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}

~~~


* `TIME_ZONE`
: 마지막은 타임존 지정입니다. `UTC`(세계표준시)로 되어 있는 부분을 한국 시간으로 변경합니다.


~~~python

TIME_ZONE = 'Asia/Seoul'

~~~

---

## 기본 테이블 생성

기본 테이블을 생성하기 위해서 다음의 명령을 실행합니다.

> **C:/python-Django/MyFirstWebPoll> python manage.py migrate**

<br>

`migrate` 명령은 데이터베이스에 변경 사항이 있을 때 이를 반영해 주는 명령어 입니다.
Django는 모든 웹 프로젝트 개발 시 반드시 사용자와 그룹 테이블 등이 필요하다는 가정하에
설계되었습니다. 따라서 우리가 현 시점에서 Database를 전혀 사용하지 않는다 할지라도
사용자 및 그룹 테이블등을 만들어 주어야 합니다.
<br><br>

정상적으로 실행되면 `db.slqite3` 파일이 생성되게 됩니다.

---

## 서버 기동

기본적인 작업을 수행했으니 이제 제공된 테스트용 웹 서버를 이용해서 우리 프로젝트를
`deploy`해 보겠습니다.

> **C:/python-Django/MyFirstWebPoll> python manage.py runserver**

<br>

만약 IP와 Port를 지정하지 않으면 default로 127.0.0.1:8000으로 실행됩니다.
IP와 Port를 지정하시려면 다음과 같이 수행하면 됩니다.

> **C:/python-Django/MyFirstWebPoll> python manage.py runserver 0.0.0.0:8888**

<br>

서버를 기동하고 웹 브라우져로 접속해보죠. 화면이 잘 나오나요??

{% include image.html
file='python-django-welcome.png'
%}

---

## Admin site 접속

Django에서 제공하는 기본적인 Admin site에 접속해서 Table이 생성된 것을 확인해 보도록
하겠습니다. 사용할 url은 `http://127.0.0.1:8000/admin` 입니다.
<br><br>

로그인을 하려면 Username, Password를 입력해야 하는데 아직 생성하지 않았습니다. Admin
site에 로그인하기 위해 관리자계정을 하나 생성합니다.

> **C:/python-Django/MyFirstWebPoll> python manage.py createsuperuser**

<br>

{% include image.html
file='python-django-createsuperuser.png'
%}
<br>

이렇게 생성된 Username, Password를 이용하여 admin site에 접속해서 현재 생성되어 있는
`Groups`, `Users` 테이블을 확인합니다. 사실 이 테이블이 존재하는 이유는 `settings.py`에
`django.contrib.auth` application이 등록되어 있기 때문입니다. Django에서 기본적으로 제공하는
application에 Users와 Groups Table이 미리 정의되어 있었다는 얘기입니다.
<br><br>

기본적인 프로젝트의 뼈대를 세우고 환경설정파일을 살짝 수정한 후 테스트 웹서버를 이용해서
우리 웹 프로젝트를 deploy까지 진행해봤습니다. 이제 poll Application을 하나씩 순차적으로
작성해 보도록 하겠습니다.

End.

{% include links.html %}
