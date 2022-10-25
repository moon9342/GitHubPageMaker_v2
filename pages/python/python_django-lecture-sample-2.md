---
title: Python Django LectureSample Project(2)
sidebar: python_sidebar
summary: "Python Django LectureSample Project에 관한 내용입니다."
permalink: python_django-lecture-sample-2.html
folder: python
---

## Lecture Sample project 개발

이번에는 이전에 구현한 Lecture Sample Project에 다음의 기능을 추가해 보겠습니다.
<br><br>

* `django.contrib.auth module`의 User class 확장

* `User Define BackEnd class`를 이용한 사용자 관리 및 인증(추후 보강)

---

## application 추가

working directory를 LectureSampleProject 폴더로 변경합니다.
<br><br>

> **C:/python-Django> cd LectureSampleProject**

<br>

users application을 생성합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py startapp users**

<br>

이후부터는 PyCharm을 이용해 작업을 진행합니다.

---

## project 환경설정

`INSTALLED_APPS` 부분에 `users` application을 등록합니다.

~~~python

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'bbs.apps.BbsConfig',
    'bootstrap4',
    'users.apps.UsersConfig'
]

~~~

사용자 관리를 위해서 `django.contrib.auth module`의 `User class`를 이용합니다. 그런데 이 User class를
직접사용하는게 아닌 `AbstractUser class`를 상속해서 사용하려 합니다.
확장한 class는 `Member`라는 이름의 class로 하겠습니다.
<br><br>

추후에 사용자 인증은 User class가 아닌 이 상속된 Member class를 이용할 것이고 이에
대한 설정을 `settings.py`에 추가적으로 넣어야 합니다.

~~~python

# 인증에 사용할 class를 지정( app이름.class이름 )
AUTH_USER_MODEL = 'users.Member'

# 인증에 사용할 사용자 정의 BACKEND
# 사용자 정의 BACKEND를 Django의 기본 ModelBackend 이전에 넣어줍니다.
# 일반적으로 타 사이트에 인증이 된 경우 우리 site도 인증된 사용자로 처리할 경우
# 이 방식을 이용합니다. 

AUTHENTICATION_BACKENDS = [
    # 'users.my_auth.UserBackend',
    'django.contrib.auth.backends.ModelBackend',
]

~~~

---

## Model 생성

Model을 생성하기 위해 users application 내의 `models.py` 파일에 다음과 같은 내용을
입력합니다.
<br><br>

여기서 주의해야 할 점은 제공된 User class를 그대로 사용하지 않고
User class를 확장해서 Member class를 정의해서 사용한다는 것입니다.
<br><br>

단순히 User class를 이용해도 되지만 만약 추가적인 필드(전화번호, 사진이미지)가 필요하다면
제공된 User class를 확장해서 사용하며 이 경우 4가지 정도의 방법이 있습니다.
여기서는 간단하게 AbstractUser class를 상속하는 방식으로 진행하겠습니다.
(이것외에 프록시 모델 기법, ForeignKey 이용, AbstractBaseUser를 상속하는 방법이 있습니다.)

~~~python

from django.db import models
from django.contrib.auth.models import AbstractUser
from django.conf import settings

class Member(AbstractUser):
    mobile = models.CharField(max_length=20)
    image = models.ImageField(upload_to=settings.MEDIA_ROOT, 
                              blank=True, null=True)
    
~~~

Admin Page에 반영하기 위해서 users application 내의 `admin.py`에 class를 등록합니다.

~~~python

from django.contrib import admin
from .models import Member

admin.site.register(Member)

~~~

사용자 관리 기능(users application)을 추가했기 때문에 이제 게시판은 로그인한 사람만
사용할 수 있고 게시글의 작성자와 댓글의 작성자는 당연히 현재 로그인한 사람을 기준으로
자동설정되어야 합니다.
<br><br>

이를 위해 bbs application의 `models.py`를 다음과 같이 수정하여 Foreign Key를
설정합니다.

~~~python

from django.db import models
from users.models import Member


class Board(models.Model):
    b_title = models.CharField(max_length=30)
    # Foreign Key 설정
    b_author = models.ForeignKey(Member, on_delete=models.CASCADE)
    b_content = models.CharField(max_length=200)
    # b_date = models.DateTimeField()
    b_date = models.DateTimeField(auto_now=True)  # 자동갱신
    b_comment_count = models.IntegerField(default=0)
    b_like_count = models.IntegerField(default=0)

    def __str__(self):
        return self.b_title


class Comment(models.Model):
    # Foreign Key 설정
    c_author = models.ForeignKey(Member, on_delete=models.CASCADE)
    c_content = models.CharField(max_length=100)
    board = models.ForeignKey(Board, on_delete=models.CASCADE)

    def __str__(self):
        return self.c_content


~~~

Database 변경사항을 반영하기 위해서 migration 초안을 생성하고 설정된 Schema를
Database에 실제로 적용해야 합니다.
<br><br>

기존에 사용하고 있는 Database file과 migration 초안은 삭제하고 Database를 다시
생성합니다.
<br><br>

한가지 주의해야 할 점은 위의 코드를 사용하기 위해서는 `settings.py` 안에 위에서 언급한
`AUTH_USER_MODEL = 'users.Member'` 코드가 반드시 존재해야 한다는 것입니다.
<br><br>

마이그레이션 초안을 생성하기 위해서 다음과 같이 실행합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py makemigrations**

<br>

설정된 Schema를 Database에 적용하기 위해서 다음과 같이 실행합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py migrate**

<br>

관리자 page에 접속하기 위한 계정을 생성합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py createsuperuser**

<br>

project가 정상적으로 생성되었는지 확인하기 위해 내장서버를 이용해 deploy합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py runserver**

<br>

데이터베이스가 정상적으로 생성되었는지 확인하고 넘어가는게 좋습니다.
[DB Browser for SQLite](https://sqlitebrowser.org/){: target="_blank" }와 같은
툴을 이용하면 쉽게 데이터베이스를 확인할 수 있습니다.
`MySQL`같은 경우 MySQL에서 기본적으로 제공되는 `MySQL WorkBench`를 이용하거나
[DATAGRIP](https://www.jetbrains.com/datagrip/){: target="_blank" }을 사용하면 됩니다.

---

## Admin Page 접속 확인

`http://localhost:8000/admin`으로 접속 후 관리자 계정으로 로그인

---

## 회원가입 및 로그인 기능 구현

Project의 ROOT_URLCONF인 `urls.py`파일을 다음과 같이 수정하여 계층적으로
URL을 관리하기 위한 설정을 합니다.

~~~python

from django.contrib import admin
from django.urls import path, include
from django.views.generic.base import TemplateView
from django.conf.urls.static import static
from django.conf import settings

urlpatterns = [
    path('', TemplateView.as_view(template_name='index.html'), name='home'),
    path('admin/', admin.site.urls),
    path('bbs/', include('bbs.urls')),
    path('users/', include('users.urls'))
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

~~~

`/users/` 경로로 들어오는 모든 request를 처리하기 위해서
users application의 `urls.py`파일을 다음과 같이 작성합니다.
<br><br>

일단 urlpatterns을 정의하고 구현시 지금 당장 필요하지 않은 부분은 주석으로 처리하면서
코드 작성을 진행하면 될 듯 합니다.

~~~python

# users/urls.py

from django.urls import path
from . import views

app_name = 'users'

urlpatterns = [
    path('login/', views.login, name='login'),
    path('loginProcess/', views.login_process, name='login_process'),
    path('signup/', views.signup, name='signup'),
    path('signupProcess/', views.signup_process, name='signup_process'),
    path('logout/', views.logout, name='logout'),
    path('update/', views.update, name='update'),
]

~~~

맨 처음 작업할 기능은 `login`, `logout` 입니다.
<br><br>

만약 로그인한 상태로 Home Page로 이동하게 되면 환영메시지를 출력하고 게시판 들어가기 버튼과 로그아웃 버튼을 활성화 시키고 로그인하지 않은
상태면 로그인이 필요하다는 메시지를 출력하고 로그인 버튼을 활성화시키면 될 듯 합니다.
<br><br>

따라서 우리의 Home Page는 다음과 같이 수정하면 됩니다.
<br><br>

다음은 수정된 `index.html` 내용입니다. 추가적으로 사용자 이미지를 표현하기 위한 코드도 포함되어 있습니다.
아래에 `cut:'C:/python-Django/LectureSampleProject'` 이 부분은 각자의 설정에 맞게 변경해야 합니다.

~~~html

{% raw %}

<!doctype html>
<html lang="ko">
<head>
  <meta charset="utf-8">
  <title>LectureSampleProject</title>

  <!-- Bootstrap core CSS -->
  <link rel="stylesheet"
        href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
        integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk"
        crossorigin="anonymous">

  <!-- Custom styles for this template -->
  <link href="/static/css/cover.css" rel="stylesheet">
</head>

<body class="text-center">
<div class="cover-container d-flex w-100 h-100 p-3 mx-auto flex-column">
  <header class="masthead mb-auto">
    <div class="inner">
      <h3 class="masthead-brand">Lecture Sample Project</h3>
    </div>
  </header>

  <main role="main" class="inner cover">
    <p class="lead">
      {% if request.user.is_authenticated %}
      <img src="{{ request.user.image | cut:'C:/python-Django/LectureSampleProject' }}" 
           width="30px">
      {{ request.user.username }}님 환영합니다.
      {% else %}
      로그인이 필요합니다.
      {% endif %}
    </p>
    <p class="lead">
      {% if request.user.is_authenticated %}
      <a href="bbs/list/" class="btn btn-lg btn-primary">Enter BBS</a>
      <a href="users/logout/" class="btn btn-lg btn-danger">Logout</a>
      {% else %}
      <a href="users/login/" class="btn btn-lg btn-warning">Login</a>
      {% endif %}
    </p>
  </main>

  <img src="/static/images/notice-board.png">
  
  <footer class="mastfoot mt-auto">
    <div class="inner">
      <p>Copyright 2022</p>
    </div>
  </footer>
</div>
</body>
</html>

{% endraw %}

~~~

간단한 기능인 logout 기능부터 살펴보도록 하겠습니다. 먼저 users application의 `urls.py` 파일을 수정해야 합니다.


~~~python

from django.urls import path
from . import views

app_name = 'users'

urlpatterns = [
    path('login/', views.login, name='login'),
    # path('loginProcess/', views.login_process, name='login_process'),
    # path('signup/', views.signup, name='signup'),
    # path('signupProcess/', views.signup_process, name='signup_process'),
    path('logout/', views.logout, name='logout'),
    # path('update/', views.update, name='update'),
]

~~~

로그인시 데이터 입력에 ModelForm을 사용하기 위해 `forms.py`에 다음의 class를 정의합니다. 회원가입을 위한
ModelForm은 추후에 추가로 작성합니다.

~~~python

from django import forms
from .models import Member


class LoginForm(forms.ModelForm):
    class Meta:
        model = Member
        fields = ['username', 'password']

        labels = {
            'username': '사용자 이름',
            'password': '비밀번호'
        }

        widgets = {
            'username': forms.TextInput(
                attrs={
                    'class': 'form-control',
                    'placeholder': '사용자이름을 입력하세요.'
                }
            ),
            'password': forms.PasswordInput(
                attrs={
                    'class': 'form-control',
                    'placeholder': '비밀번호를 입력하세요.'
                }
            )
        }


~~~

users application의 `views.py` 파일을 수정해야 합니다.


~~~python

from django.shortcuts import render, redirect
from django.contrib.auth import authenticate, \
    login as django_login, \
    logout as django_logout
from .forms import LoginForm


def login(request):
    login_form = LoginForm()
    return render(request, 'users/login.html', {
        'login_form': login_form
    })


def logout(request):
    django_logout(request)
    return redirect('home')

~~~

로그인이 되어 있는 경우 우리 Home Page에서 `Enter BBS` 버튼을 클릭하면 게시판의
list를 보여주는 화면으로 이동하게 되는데 `/bbs/list/`로 이동(편의상 이동이란 표현을 사용하겠습니다.)할 때
로그인이 되어 있으면 list를 출력하지만 그렇지 않은 경우 login 화면으로 이동하면 될 듯 합니다.
<br><br>

정상적인 형태로 link를 타고 오는게 아니라 직접 게시판 url을 입력할 경우를 대비해서 다음과 같이 처리합니다.
<br><br>

bbs application의 `views.py` 안의 `b_list` 함수를 다음과 같이 수정합니다.

~~~python

def b_list(request):

    if request.user.is_authenticated:
        posts = Board.objects.all().order_by('-id')
        return render(request, 'bbs/list.html', {
            'posts': posts
        })
    else:
        login_form = LoginForm()
        return render(request, 'users/login.html', {
            'login_form': login_form
        })

~~~

users application의 template으로 `login.html` 파일을 생성해야 합니다. 이 파일의 내용은 bootstrap의 example을
이용하겠습니다.
<br><br>

`login.html`에서 사용된 CSS 파일인 `signin.css` 파일은 Bootstrap Example 혹은
다음의 링크에서 다운받으실 수 있습니다.
<br><br>

[signin.css zip 파일](https://drive.google.com/file/d/1RunzM-dMnMx_JGBd8dzhSUuPTvhM3eL8/view?usp=sharing){: target="_blank" }


~~~html

{% raw %}

{% extends 'base.html' %}
{% load bootstrap4 %}

{% block html_header %}
<style>
  .bd-placeholder-img {
    font-size: 1.125rem;
    text-anchor: middle;
    -webkit-user-select: none;
    -moz-user-select: none;
    user-select: none;
  }

  @media (min-width: 768px) {
    .bd-placeholder-img-lg {
      font-size: 3.5rem;
    }
  }
</style>

<!-- Custom styles for this template -->
<link href="/static/css/signin.css" rel="stylesheet">
{% endblock %}

{% block html_body %}

<main class="form-signin">
  <form action="/users/loginProcess/"
        method="POST">
    {% csrf_token %}
    <h1 class="h3 mb-3 fw-normal">로그인하세요</h1>

    {% bootstrap_form login_form %}

    <br>
    <button class="w-100 btn btn-lg btn-primary" type="submit">로그인</button>
    <p class="mt-5 mb-3 text-muted">&copy; 2017–2021</p>
  </form>
</main>

{% endblock %}


{% endraw %}

~~~

로그인 버튼을 클릭하면 로그인 처리를 수행해야 하기 때문에 `urls.py`와 `views.py` 파일을 수정합니다.
<br><br>

아래의 내용은 `urls.py` 입니다.

~~~python

from django.urls import path
from . import views

app_name = 'users'

urlpatterns = [
    path('login/', views.login, name='login'),
    path('loginProcess/', views.login_process, name='login_process'),
    # path('signup/', views.signup, name='signup'),
    # path('signupProcess/', views.signup_process, name='signup_process'),
    path('logout/', views.logout, name='logout'),
    # path('update/', views.update, name='update'),
]

~~~

아래의 내용은 `views.py` 입니다.

~~~python

from django.shortcuts import render, redirect
from django.contrib.auth import authenticate, \
    login as django_login, \
    logout as django_logout
from .forms import LoginForm
from django.http import HttpResponse


def login(request):
    login_form = LoginForm()
    return render(request, 'users/login.html', {
        'login_form': login_form
    })


def logout(request):
    django_logout(request)
    return redirect('home')


def login_process(request):
    if request.method == 'POST':

        login_form = LoginForm(request.POST)

        username = login_form.data['username']
        password = login_form.data['password']

        # 가져온 username과 password에 해당하는 User가 있는지 판단
        # 존재할경우 user변수에는 User인스턴스가 할당되며,
        # 존재하지 않으면 None이 할당된다
        user = authenticate(username=username, password=password)

        if user is not None:
            django_login(request, user)
            return redirect('home')
        else:
            return HttpResponse('로그인 실패. 다시 시도 해보세요.')


~~~

여기까지 진행되면 기본적인 로그인, 로그아웃 처리가 완성됩니다. 이제 회원가입을 처리해보도록 하겠습니다. 타 사이트에서 인증받을 경우
우리 사이트에서 자동으로 회원가입이 되도록 하는 기능을 추후에 프로젝트 등에서 추가해보시기 바랍니다. 여기서는 기본적인 데이터를
입력받아서 회원가입하는 형태로 진행하겠습니다.
<br><br>

회원가입 링크는 Home Page에 만들도록 하겠습니다. `index.html`을 간단히 수정합니다.

~~~html

{% raw %}

<!doctype html>
<html lang="ko">
<head>
  <meta charset="utf-8">
  <title>LectureSampleProject</title>

  <!-- Bootstrap core CSS -->
  <link rel="stylesheet"
        href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
        integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk"
        crossorigin="anonymous">

  <!-- Custom styles for this template -->
  <link href="/static/css/cover.css" rel="stylesheet">
</head>

<body class="text-center">
<div class="cover-container d-flex w-100 h-100 p-3 mx-auto flex-column">
  <header class="masthead mb-auto">
    <div class="inner">
      <h3 class="masthead-brand">Lecture Sample Project</h3>
    </div>
  </header>

  <main role="main" class="inner cover">
    <p class="lead">
      {% if request.user.is_authenticated %}
      <img src="{{ request.user.image | cut:'C:/python-Django/LectureSampleProject' }}" 
           width="30px">
      {{ request.user.username }}님 환영합니다.
      {% else %}
      로그인이 필요합니다.
      {% endif %}
    </p>
    <p class="lead">
      {% if request.user.is_authenticated %}
      <a href="bbs/list/" class="btn btn-lg btn-primary">Enter BBS</a>
      <a href="users/logout/" class="btn btn-lg btn-danger">Logout</a>
      {% else %}
      <a href="users/login/" class="btn btn-lg btn-warning">Login</a>
      <a href="users/signup/" class="btn btn-lg btn-success">회원가입</a>
      {% endif %}
    </p>
  </main>

  <img src="/static/images/notice-board.png">
  
  <footer class="mastfoot mt-auto">
    <div class="inner">
      <p>Copyright 2020</p>
    </div>
  </footer>
</div>
</body>
</html>

{% endraw %}


~~~

`urls.py`를 수정합니다.

~~~python

from django.urls import path
from . import views

app_name = 'users'

urlpatterns = [
    path('login/', views.login, name='login'),
    path('loginProcess/', views.login_process, name='login_process'),
    path('signup/', views.signup, name='signup'),
    path('signupProcess/', views.signup_process, name='signup_process'),
    path('logout/', views.logout, name='logout'),
    # path('update/', views.update, name='update'),
]

~~~

이제 회원가입을 위한 ModelForm을 생성하러 `forms.py` 파일을 수정해야 합니다.
<br><br>

한가지 주의해야 할 점은 회원가입을 위한 `MemberForm`은 `UserCreationForm`을
상속해서 만들어진다는 것입니다.

~~~python

from django import forms
from .models import Member
from django.contrib.auth.forms import UserCreationForm


class MemberForm(UserCreationForm):
    class Meta:
        model = Member
        fields = ['username', 'email', 'mobile',
                  'password1', 'password2', 'image']


class LoginForm(forms.ModelForm):
    class Meta:
        model = Member
        fields = ['username', 'password']

        labels = {
            'username': '사용자 이름',
            'password': '비밀번호'
        }

        widgets = {
            'username': forms.TextInput(
                attrs={
                    'class': 'form-control',
                    'placeholder': '사용자이름을 입력하세요.'
                }
            ),
            'password': forms.PasswordInput(
                attrs={
                    'class': 'form-control',
                    'placeholder': '비밀번호를 입력하세요.'
                }
            )
        }


~~~


`views.py` 파일을 수정합니다.

~~~python

def signup(request):
    member_form = MemberForm()
    return render(request, 'users/signup.html', {
        'member_form': member_form
    })

~~~

회원가입을 위한 template인 `signup.html`을 작성합니다.
<br><br>

이미지파일을 업로드하기 위해 `enctype="multipart/form-data"` 설정을
했다는 것에 주의해야 합니다.

~~~html

{% raw %}

{% extends 'base.html' %}
{% load bootstrap4 %}

{% block html_header %}
<style>
  .bd-placeholder-img {
    font-size: 1.125rem;
    text-anchor: middle;
    -webkit-user-select: none;
    -moz-user-select: none;
    user-select: none;
  }

  @media (min-width: 768px) {
    .bd-placeholder-img-lg {
      font-size: 3.5rem;
    }
  }
</style>

<!-- Custom styles for this template -->
<link href="/static/css/signin.css" rel="stylesheet">
{% endblock %}

{% block html_body %}

<main class="form-signin">
  <form action="/users/signupProcess/"
        method="POST"
        enctype="multipart/form-data">
    {% csrf_token %}
    <h1 class="h3 mb-3 fw-normal">회원가입</h1>

    {% bootstrap_form member_form %}

    <br>
    <button class="w-100 btn btn-lg btn-success" type="submit">회원가입</button>
    <p class="mt-5 mb-3 text-muted">&copy; 2017–2021</p>
  </form>
</main>

{% endblock %}


{% endraw %}

~~~

이제 실제 회원가입 기능을 하면 `views.py`의 함수를 살펴보도록 하겠습니다.
<br><br>

사용자 이미지를 선택했을 경우와 그렇지 않은 경우를 구별해서 처리해야 했습니다.

~~~python

def signup_process(request):
    if request.method == "POST":
        member_form = MemberForm(request.POST)
        if member_form.is_valid():
            if 'image' in request.FILES.keys():
                new_user = Member.objects.create_user(
                    member_form.cleaned_data["username"],
                    email=member_form.cleaned_data['email'],
                    password=member_form.cleaned_data['password1'],
                    mobile=member_form.cleaned_data['mobile'],
                    image=request.FILES['image']
                )
            else:
                new_user = Member.objects.create_user(
                    member_form.cleaned_data["username"],
                    email=member_form.cleaned_data['email'],
                    password=member_form.cleaned_data['password1'],
                    mobile=member_form.cleaned_data['mobile']
                )

            django_login(request, new_user)
            return redirect('home')
            
~~~

여기까지 작성하면 회원가입 기능까지 동작하게 됩니다. 현재 회원가입, 로그인, 로그아웃 기능까지 구현했습니다.
<br><br>

회원정보 상세보기와 수정은 배운내용을 기반으로 개인적으로 작성해보세요.
지금까지의 내용을 잘 따라오셨으면 어떻게 해야 할지 감(?)이 오실거라 생각됩니다.
<br><br>

마지막 단계가 남았습니다. 글을 쓸때 현재 로그인 한 사람의 이름으로 글 작성자가 자동으로 설정되어야 합니다. 또한
글 삭제와 수정 역시 본인이 작성한 글에 대해서만 기능이 동작하도록 해야 합니다.

---

## 게시판 기능 수정(새글 작성)

게시판의 새글 작성 기능부터 손봐야 할 듯 합니다. 새글 작성 화면에서 현재 로그인한 사람의
username으로 author부분을 고정시키면 될 듯 합니다.
<br><br>

구현 방법은 여러가지가 있을 수 있는데 여기서는 너무 어렵지 않게 우리가 배운 jQuery를 활용하는 쪽으로 진행하겠습니다. jQuery는
보안상 약간 우려되는 부분이 있지만 코드가 직관적이기 때문에 이해하는데 어렵지는 않을 듯 합니다.
<br><br>

또 한가지 주의해야 할 점이 있는데 `Board class`의 `b_author`가 `Foreign Key`로 지정이 되면서 기존에
`ModelForm`을 수정해서 사용해야 합니다. 우리는 ModelForm의 각 attribute의 CSS를 지정해주기 위해서
각각 따로 설정했는데 이렇게 되면 username이 아닌 연결된 username의 id값(PK)이 출력됩니다.
<br><br>

조금 복잡했는데 다음과 같이 `BoardForm`과 `BoardDetailForm`을 수정해야 합니다.
<br><br>

`models.py` 파일부터 손봐야 할 듯 합니다.

~~~python

from django import forms
from bbs.models import Board


class BoardForm(forms.ModelForm):
    class Meta:
        model = Board
        fields = ['b_title', 'b_author', 'b_content']

        labels = {
            'b_title': '글 제목',
            'b_author': '글 작성자',
            'b_content': '글 내용'
        }

        widgets = {
            'b_title': forms.TextInput(
                attrs={
                    'class': 'form-control w-50',
                    'placeholder': '글 제목을 입력하세요',
                }
            ),
            # 'b_author': forms.TextInput(
            #     attrs={
            #         'class': 'form-control w-25',
            #         'placeholder': '글 작성자를 입력하세요'
            #     }
            # ),
            'b_content': forms.Textarea(
                attrs={
                    'class': 'form-control w-75',
                    'placeholder': '글 내용을 입력하세요'
                }
            )
        }

    def __init__(self, *args, **kwargs):
        super(BoardForm, self).__init__(*args, **kwargs)
        self.fields['b_author'].widget.attrs.update(
            {'class': 'w-25'}
        )

    # 아래의 코드가 구현 되어 있습니다.
    # def save(self, commit=True):
    #     self.instance = Board(**self.cleaned_data)
    #     if commit:
    #         self.instance.save()
    #     return self.instance


class BoardDetailForm(forms.ModelForm):

    class Meta:
        model = Board
        fields = '__all__'

        labels = {
            'b_title': '글 제목',
            'b_author': '글 작성자',
            'b_content': '글 내용',
            'b_comment_count': '댓글 개수',
            'b_like_count': '좋아요 개수'
        }

        widgets = {
            'b_title': forms.TextInput(
                attrs={
                    'class': 'form-control w-50',
                }
            ),
            # 'b_author': forms.TextInput(
            #     attrs={
            #         'class': 'form-control w-25',
            #     }
            # ),
            'b_content': forms.Textarea(
                attrs={
                    'class': 'form-control w-75',
                }
            ),
            'b_comment_count': forms.TextInput(
                attrs={
                    'class': 'form-control w-25',
                }
            ),
            'b_like_count': forms.TextInput(
                attrs={
                    'class': 'form-control w-25',
                }
            ),
        }

    def __init__(self, *args, **kwargs):
        super(BoardDetailForm, self).__init__(*args, **kwargs)
        self.fields['b_author'].widget.attrs.update(
            {'class': 'w-25'}
        )

    def show_board_detail(self):
        fields = self.get_all_fields_from_form(BoardDetailForm)
        for field in fields:
            self.fields[field].widget.attrs.update({
                'readonly': 'readonly'
            })

    def show_board_update(self):
        self.fields['b_like_count'].widget.attrs.update({
            'readonly': 'readonly'
        })
        self.fields['b_comment_count'].widget.attrs.update({
            'readonly': 'readonly'
        })

    @staticmethod
    def get_all_fields_from_form(instance):

        fields = list(instance().base_fields)

        for field in list(instance().declared_fields):
            if field not in fields:
                fields.append(field)

        return fields


~~~

그 다음에는 `views.py` 파일을 손봐야 합니다.

~~~python

def b_create(request):
    # POST 방식
    if request.method == 'POST':
        # ModelForm을 사용하지 않으면
        # class의 instance를 만들어서 save() 호출
        # board = Board()
        # board.b_title = request.POST['b_title']
        # board.save()

        board_form = BoardForm(request.POST)

        if board_form.is_valid():
            new_post = board_form.save(commit=False)
            # 필요한 경우 field에 값을 수동으로 설정할 수 있다.
            # 예) new_post.b_like_count = 100
            new_post.save()
            return redirect('bbs:b_list')

    # GET 방식
    else:
        member = Member.objects.get(username=request.user.username)
        board_form = BoardForm(initial={'b_author': member})
        board_form.fields['b_author'].widget.attrs.update({
            'disabled': 'disabled'
        })
        # 로그인한 사람의 username으로 author부분을 설정한 후 수정할 수 없도록
        # disabled로 설정합니다.
        # 단, disabled로 설정된 field는 submit이 되지 않기 때문에 jQuery를 이용해
        # submit하기 전에 disabled를 해제하고 submit하는 방식으로 우회합니다.

        context = {'board_form': board_form}
    
        return render(request, 'bbs/create.html', context)

~~~

그 다음 수정해야 할 파일은 `create.html` 입니다.


~~~html

{% raw %}

{% extends 'base.html' %}
{% load bootstrap4 %}
{% block html_body %}

<script src="/static/js/menu_btn.js"></script>
<div class="container">
  <h1>새글 작성</h1>

  <form method="post">
    {% csrf_token %}
    {# {{ board_form }} #}
    {% bootstrap_form board_form %}
    <br>
    {#        기존의 submit button 방식으로는 처리하기가 좀 복잡해서 #}
    {#        간단하게 처리할 수 있는 jQuery방식으로 우회하자 #}
    {#        <button type="submit" #} 
    {#                class="btn btn-primary">등록</button> #}
    <button type="button"
            class="btn btn-primary"
            id="post_create_btn">등록</button>

    <button type="button"
            class="btn btn-secondary"
            id="board_list_btn">리스트로 돌아가기</button>
  </form>
</div>

{% endblock %}

{% endraw %}

~~~

jQuery를 이용하여 submit 처리를 해야 하기 때문에 `menu_btn.js` 파일을 수정합니다.

~~~javascript

$(function(event) {

  // 새글쓰기 버튼 클릭시
  $('#new_post_btn').on('click', function (event) {
    document.location.href = '/bbs/create/'
  })

  // 새글 등록 버튼 클릭시(실제 새글을 저장하기 위한 submit을 수행할 때)
  $('#post_create_btn').on('click', function (event) {
    $('#id_b_author').removeAttr('disabled')
    $('form').submit()
  })

  // 나머지 부분은 동일
  
})

~~~

---

## 게시판 기능 수정(상세보기)

이번에는 게시글 상세보기 화면을 수정하도록 하죠.
<br><br>

제일 먼저 수정해야 할 파일은 `views.py` 입니다. 해당 게시글이 로그인한 사람이 쓴 글인지를
확인해 상세보기 화면에 필요한 정보를 넘겨줘야 하기 때문입니다. 댓글에 대한 처리는 하지 않았습니다.

~~~python

def b_detail(request, board_id):

    post = get_object_or_404(Board, id=board_id)

    is_author = False

    if str(post.b_author) == request.user.username:
        is_author = True
        member = Member.objects.get(username=request.user.username)
    else:
        member = Member.objects.get(username=post.b_author)

    board_detail_form = BoardDetailForm(instance=post,
                                        initial={'b_author': member})

    board_detail_form.show_board_detail()

    # comment 정보도 가져와야 한다.
    comments = post.comment_set.all().order_by('-id')

    return render(request, 'bbs/detail.html', {
        'board_detail_form': board_detail_form,
        'comments': comments,
        'is_author': is_author
    })

~~~

`forms.py` 파일을 살짝 수정해야 합니다. `b_author`는 Foreign key로 설정되어 있고 추후에 disabled로
설정해야 하기 때문에 코드를 수정합니다.

~~~python

class BoardDetailForm(forms.ModelForm):

    # 각 field를 세부적으로 조정하려면 다음과 같이 widget을 이용해야 한다.
    # b_title = forms.CharField(
    #     label='제목',
    #     widget=forms.TextInput(
    #         attrs={
    #             'class': 'form-control',
    #         }
    #     )
    # )
    b_title = forms.CharField(label='글제목')
    # b_author는 일반 CharField로 설정하면 안됩니다.
    # b_author = forms.CharField(label='글작성자')
    b_content = forms.CharField(label='글내용')
    b_comment_count = forms.IntegerField(label='댓글수')
    b_like_count = forms.IntegerField(label='좋아요')

    class Meta:
        model = Board
        fields = '__all__'

    def show_board_detail(self):
        fields = self.get_all_fields_from_form(BoardDetailForm)
        for field in fields:
            self.fields[field].widget.attrs.update({
                'readonly': 'readonly'
            })

       # 작성자는 수정이 불가능해야 합니다. 
        self.fields['b_author'].widget.attrs.update({
            'disabled': 'disabled'
        })

    def show_board_update(self):
    
        # 작성자는 수정이 불가능해야 합니다.
        self.fields['b_author'].widget.attrs.update({
            'disabled': 'disabled'
        })
        self.fields['b_like_count'].widget.attrs.update({
            'readonly': 'readonly'
        })
        self.fields['b_comment_count'].widget.attrs.update({
            'readonly': 'readonly'
        })

    @staticmethod
    def get_all_fields_from_form(instance):

        fields = list(instance().base_fields)

        for field in list(instance().declared_fields):
            if field not in fields:
                fields.append(field)
        return fields


~~~

다음은 `detail.html` 파일입니다. 자신이 작성한 글에 대해서는 수정 버튼과 삭제 버튼이 활성화 되고 좋아요 버튼은
비활성화 시킵니다. 반대로 자신이 작성한 글이 아닌경우 좋아요 버튼만 활성화 시키는 처리를 하면 될 듯 합니다.

~~~html

{% raw %}

{% extends 'base.html' %}
{% load bootstrap4 %}
{% block html_body %}

<script src="/static/js/menu_btn.js"></script>

<div class="container">
  <h1>글 상세보기</h1>

  번호 : <span id="board_id">{{ board_detail_form.initial.id }}</span>
  <form method="post">
    {% csrf_token %}
    {% bootstrap_form board_detail_form %}
    <br>
  </form>
  <button type="button"
          class="btn btn-success"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_list_btn">리스트로 돌아가기</button>
  {% if is_author %}
  <button type="button"
          class="btn btn-primary"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_update_btn">수정</button>
  <button type="button"
          class="btn btn-danger"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_delete_btn">삭제</button>
  {% else %}
  <button type="button"
          class="btn btn-warning"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_like_btn">좋아요</button>
  {% endif %}

</div>
<br><br>
<div class="container">

  <h3>댓글</h3>
  <div>
    <label for="c_name">이름</label>
    <input type="text"
           class="form-control"
           id="c_name">
    <label for="c_content">내용</label>
    <input type="text"
           class="form-control"
           id="c_content">
    <br>
    <button type="button"
            class="btn btn-info"
            id="comment_create_btn">등록</button>
  </div>
  <table class="table table-hover">
    <thead class="thead-dark">
    <tr>
      <th scope="col">글작성자</th>
      <th scope="col">글내용</th>
      <th scope="col">삭제</th>
    </tr>
    </thead>
    <tbody id="comment_body">
    {% for comment in comments %}
    <tr>
      <td>{{ comment.c_author }}</td>
      <td>{{ comment.c_content }}</td>
      <td>
        <button type="button"
                class="btn btn-danger"
                data-comment_id="{{ comment.id }}"
                id="comment_delete_btn">삭제</button>
      </td>
    </tr>
    {% endfor %}
    </tbody>
  </table>
</div>
<br><br><br>
{% endblock %}

{% endraw %}

~~~

---

## 게시판 기능 수정(게시글 수정)

게시글 상세보기에서 수정버튼을 클릭하면 글 내용을 수정할 수 있는 화면으로 전환됩니다. 이때
글 작성자는 고정되어야 하며 글 제목과 내용만 수정될 수 있도록 처리해야 합니다.
<br><br>

먼저 수정해야 할 파일은 `views.py` 파일 입니다.

~~~python

def b_update(request):
    post = get_object_or_404(Board, id=request.GET['board_id'])

    member = Member.objects.get(username=request.user.username)

    board_detail_form = BoardDetailForm(instance=post,
                                        initial={'b_author': member})
    board_detail_form.show_board_update()

    return render(request, 'bbs/update.html', {
        'board_detail_form': board_detail_form
    })


def b_update_process(request, board_id):
    post = get_object_or_404(Board, id=board_id)

    if request.method == 'POST':
        board_detail_form = BoardDetailForm(request.POST, instance=post)

        print(board_detail_form.is_valid())

        if board_detail_form.is_valid():

            board_detail_form.save()
            board_detail_form.show_board_detail()
            return render(request, 'bbs/detail.html', {
                'board_detail_form': board_detail_form,
                'is_author': True
            })

~~~

`update.html` 파일도 약간 손봐야 합니다. `submit button`을 `jQuery`를 이용해서 처리합니다.

~~~html

{% raw %}

{% extends 'base.html' %}
{% load bootstrap4 %}
{% block html_body %}

<script src="/static/js/menu_btn.js"></script>

<div class="container">
  <h1>Post Update</h1>

  번호 : {{ board_detail_form.initial.id }}
  <form action='{% url 'bbs:b_update_process' board_detail_form.initial.id %}'
  method="post">
  {% csrf_token %}
  {# {{ board_detail_form }} #}
  {% bootstrap_form board_detail_form %}
  <br>
  <button type="button"
          class="btn btn-primary"
          id="board_list_btn">리스트로 돌아가기</button>
  <button type="button"
          class="btn btn-primary"
          id="post_update_btn">수정적용</button>
  </form>

</div>

{% endblock %}

{% endraw %}

~~~

`menu_btn.js` 파일도 약간 수정해야 합니다.

~~~javascript


// 수정버튼 클릭시
$('#board_update_btn').on('click',function(event) {
  let query_string = '?board_id=' + $(this).attr('data-board_id')
  document.location.href='/bbs/update' + query_string
})

// 수정적용 버튼 클릭시(실제 수정된 글을 저장하기 위한 submit을 수행할 때)
$('#post_update_btn').on('click',function(event) {
  $('#id_b_author').removeAttr('disabled')
  $('form').submit()
})


~~~

마지막은 거의 코드 위주로 진행이 됬지만 천천히 살펴보시길 바랍니다.
<br><br>

추가적으로 댓글 역시 자신이 작성한 댓글만 삭제할 수 있도록 처리해야 합니다.
이 구현은 여러분들이 직접 한번 해 보세요!
<br><br>

End.

{% include links.html %}
