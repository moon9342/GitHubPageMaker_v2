---
title: Python Django LectureSample Project(1)
sidebar: python_sidebar
summary: "Python Django LectureSample Project에 관한 내용입니다."
permalink: python_django-lecture-sample-1.html
folder: python
---

## Lecture Sample project 개발

앞서 살펴본 Poll Project의 내용을 기반으로 이번에는 다음의 내용을 포함하는
Lecture Sample Project를 구현해보도록 하겠습니다.
<br><br>

* `ModelForm`을 이용한 BBS(게시판) CRUD 구현(댓글기능은 AJAX로 구현)
<br><br>

* Bootstrap 적용

---

## project 생성

새로운 project lecture를 생성합니다.
<br><br>

> **C:/python-Django> django-admin startproject lecture**

<br>

project 설정과 application을 모두 포함하는 폴더 이름을 `LectureSampleProject`로 변경합니다.
<br><br>

> **C:/python-Django> move lecture LectureSampleProject**

<br>

working directory를 LectureSampleProject 폴더로 변경합니다.
<br><br>

> **C:/python-Django> cd LectureSampleProject**

<br>

`bbs` application을 생성합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py startapp bbs**

<br>

이후부터는 `PyCharm`을 이용해 작업을 진행합니다.

---

## project 환경설정

환경설정을 위해 project 폴더의 `settings.py`파일을 수정합니다.
기본적인 `DEBUG=TRUE` 설정에 따른 `ALLOWED_HOSTS`에 대한 내용을
다음과 같이 수정합니다.

~~~python

ALLOWED_HOSTS = ['localhost', '127.0.0.1']

~~~

`INSTALLED_APPS` 부분에 `bbs` application을 등록합니다.

~~~python

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'bbs.apps.BbsConfig'
]

~~~

최상위 project 폴더안에 `templates` 폴더를 생성한 후
`TEMPLATES` 부분을 수정하여 `templates` 폴더를 추가로 설정합니다.

~~~python

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

~~~

Database는 기본으로 제공되는  `slqlite3`를 사용합니다. 이 경우 설정을 변경할
필요없습니다. 하지만 MySQL과 같은 외부 Database를 사용할 경우 설정을 다음과
같이 변경해야 합니다.
<br><br>

mysql module을 설치해야 하며 외부 데이터베이스 설정 역시 아래의 설정대로
처리해야 합니다. (`pip install mysqlclient`)

~~~python

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydatabase',
        'USER': 'test1234',
        'PASSWORD': 'test1234',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}

~~~

`TIME_ZONE` 부분은 세계표준시에서 한국시간으로 변경합니다.

~~~python

TIME_ZONE = 'Asia/Seoul'

~~~


Static File(CSS, JavaScript, Image)을 사용하기 위해서 Static File 폴더를 지정하고
폴더를 `BASE_DIR` 하단에 생성합니다. 그 후 static 폴더 하단에 css, js, image 폴더를
각각 생성합니다.

~~~python

STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]

~~~

Static File은 서버에서 정적으로 제공하는 파일들을 저장하는 곳입니다. 이와는 약간
다르게 사용자가 파일 올리기등의 기능을 이용하여 동적으로 media 파일들이 서버에
저장될 수 있는데 이 경우 다음과 같이 media url과 디렉토리를 설정합니다.

~~~python

# media file : 사용자가 웹 애플리케이션에 업로드한 파일(image)
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

~~~

---

## 기본 Table 생성

기본 테이블을 생성하기 위해 다음의 명령을 수행합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py migrate**

<br>

## 관리자 계정 생성

관리자 page에 접속하기 위한 계정을 생성합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py createsuperuser**

---

## 서버 기동

project가 정상적으로 생성되었는지 확인하기 위해 내장서버를 이용해 deploy합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py runserver**

---

## Admin Page 접속 확인

`http://localhost:8000/admin`으로 접속 후 관리자 계정으로 로그인

---

## Model 생성

Model을 생성하기 위해 bbs application 내의 `models.py` 파일에 다음과 같은 내용을
입력합니다.

~~~python

from django.db import models


class Board(models.Model):
    b_title = models.CharField(max_length=30)
    b_author = models.CharField(max_length=20)
    b_content = models.CharField(max_length=200)
    # b_date = models.DateTimeField()
    b_date = models.DateTimeField(auto_now=True)  # 자동갱신
    b_comment_count = models.IntegerField(default=0)
    b_like_count = models.IntegerField(default=0)

    def __str__(self):
        return self.b_title


class Comment(models.Model):
    c_author = models.CharField(max_length=20)
    c_content = models.CharField(max_length=100)
    board = models.ForeignKey(Board, on_delete=models.CASCADE)

    def __str__(self):
        return self.c_content

        
~~~

Admin Page에 반영하기 위해서 bbs application 내의 `admin.py`에 class를 등록합니다.

~~~python

from django.contrib import admin
from .models import Board, Comment

admin.site.register(Board)
admin.site.register(Comment)

~~~

Database 변경사항을 반영하기 위해서 migration 초안을 생성하고 설정된 Schema를
Database에 실제로 적용해야 합니다.
<br><br>

마이그레이션 초안을 생성하기 위해서 다음과 같이 실행합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py makemigrations**

<br>

설정된 Schema를 Database에 적용하기 위해서 다음과 같이 실행합니다.
<br><br>

> **C:/python-Django/LectureSampleProject> python manage.py migrate**

<br>

데이터베이스가 정상적으로 생성되었는지 반드시 확인하고 넘어가는게 좋습니다.
[DB Browser for SQLite](https://sqlitebrowser.org/){: target="_blank" }와 같은
툴을 이용하면 쉽게 데이터베이스를 확인할 수 있습니다.
`MySQL`같은 경우 MySQL에서 기본적으로 제공되는 `MySQL WorkBench`를 이용하거나
[DATAGRIP](https://www.jetbrains.com/datagrip/){: target="_blank" }을 사용하면 됩니다.

---

## URL 경로 설정

Project의 `ROOT_URLCONF`인 `urls.py`파일을 다음과 같이 수정하여 계층적으로
URL을 관리하기 위한 설정을 합니다. 추가적으로 media를 위한 서빙도 추가해야 합니다.

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
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

~~~

프로젝트 첫 페이지 처리는 가장 쉬운 방식인 `TemplateView.as_view()`를 이용하여
처리하도록 하겠습니다.
<br><br>

아시겠지만 `url()` 함수는 이전 장고버전에서 사용된 기법이고 장고가 2.0으로 버전업되면서
좀 더 사용하기 쉬운 `path()`와 정규식을 지원하는 `re_path()`로 변경되었습니다.
<br><br>

url() 혹은 re_path()를 이용하면 정규표현식으로 표현된 url을 특정 HTML과
쉽게 연결할 수 있습니다.
<br><br>

우리가 이전에 학습한 대로 view함수와 연결해서 처리해도 되며 위와 같이
TemplateView.as_view()와 같은 함수를 이용하여 HTML을 바로 연결할 수 있습니다.
해당 HTML에서 Template tag도 사용할 수 있습니다.
<br><br>

메인 페이지와 몇몇 화면은 `Bootstrap`의 Examples 중에서 적당한 화면을 가져와서
사용했습니다. 프로젝트에서 사용한 모든 CSS파일은 Bootstrap Examples에서 구할 수
있습니다.
<br><br>

아래의 파일은 `BASE_DIR/templates/base.html` 입니다.
메인 페이지인 index.html을 제외한 다른 template html 파일은 모두 base.html을
확장해서 사용합니다.

~~~html
{% raw %}

<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>{{ page_title }}</title>

    <!-- JQuery CDN -->
    <script src="https://code.jquery.com/jquery-2.2.4.min.js" integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44=" crossorigin="anonymous"></script>

    <!-- Bootstrap core CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/js/bootstrap.bundle.min.js" integrity="sha384-ygbV9kiqUc6oa4msXn9868pTtWMgiQaeYH7/t7LECLbyPA2x65Kgf80OJFdroafW" crossorigin="anonymous"></script>

    {% block html_header %}
    {% endblock %}
</head>
<body>
{% block html_body %}
{% endblock %}
</body>
</html>

{% endraw %}
~~~

아래의 파일은 `BASE_DIR/templates/index.html` 입니다.
index.html은 base.html을 확장해서 사용하지 않았습니다.
<br><br>

index.html에서 사용된 CSS 파일인 `cover.css` 파일은 Bootstrap Example 혹은
다음의 링크에서 다운받으실 수 있습니다. 또한 메인 화면이 너무 썰렁해서 가져다 사용한
이미지 역시 아래에서 다운로드 할 수 있습니다.
<br><br>

[cover.css zip 파일](https://drive.google.com/file/d/1aoQG05KRGpEhrctIbE5PbBq8gdoFeLq2/view?usp=sharing){: target="_blank" }

[메인화면 이미지 파일](https://drive.google.com/file/d/1HoZb1umez-U1QzLnvzMREV1d47bssNnC/view?usp=sharing){: target="_blank" }

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
        <p class="lead">Django Framework</p>
        <p class="lead">
            <a href="bbs/list/" class="btn btn-lg btn-primary">Enter BBS</a>
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

---

## 기본 Template 설정

하나의 html 파일을 생성해 모든 template 파일의 `base html`로 사용합니다.
<br><br>

project folder 하단의 `templates` 폴더안에 기본적인 형태로 `base.html`을 생성합니다.
<br><br>

Django에 Bootstrap4을 적용하기 위해서는 django-bootstrap4를 설치해야 합니다. 물론 Bootstrap은
간단히 CDN을 포함시켜서 사용할 수 있습니다. 이 모듈을 사용하는 이유는 뒤에서 나올 ModelForm에서 Bootstrap을
적용하기 위함입니다. 아래의 명령어로 package를 설치합니다.
<br><br>

> **pip install django-bootstrap4**

<br>

설치가 완료된 다음에  blog project 폴더안의 `settings.py` 내
`INSTALLED_APPS`을 다음과 같이 수정하여 bootstrap application을 추가합니다.

~~~python

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'posts.apps.PostsConfig',
    'bootstrap4',
]

~~~

---

## list 페이지 작성

bbs application의 `urls.py`의 내용을 수정합니다.

~~~python

from django.urls import path
from . import views

app_name = 'bbs'

urlpatterns = [
    path('list/', views.b_list, name='b_list'),
]

~~~

bbs application의 `views.py`의 내용을 수정합니다.

~~~python

from django.shortcuts import render, redirect
from .models import Board


def b_list(request):
    posts = Board.objects.all().order_by('-id')
    return render(request, 'bbs/list.html', {
        'posts': posts
    })


~~~

`list.html` 파일을 Bootstrap을 이용해서 수정합니다.

~~~html

{% raw %}
{% extends 'base.html' %}
{% block html_body %}

<script src="/static/js/menu_btn.js"></script>
<div class="container">
  <h1>Bulletin Board System(BBS)</h1>
  <button type="button"
          class="btn btn-primary"
          id="new_post_btn">새글 작성</button>
  <div class="m-1"></div>

  <table class="table table-hover">
    <thead class="thead-dark">
    <tr>
      <th scope="col">#</th>
      <th scope="col">글제목</th>
      <th scope="col">글작성자</th>
      <th scope="col">글작성일</th>
      <th scope="col">댓글수</th>
      <th scope="col">좋아요</th>
    </tr>
    </thead>
    <tbody>
    {% for post in posts %}
    <tr>
      <th scope="row">{{ post.id }}</th>
      <td><a href="/bbs/{{ post.id }}/detail/">{{ post.b_title }}</a></td>
      <td>{{ post.b_author }}</td>
      <td>{{ post.b_date }}</td>
      <td>{{ post.b_comment_count }}</td>
      <td>{{ post.b_like_count }}</td>
    </tr>
    {% endfor %}
    </tbody>
  </table>
</div>

{% endblock %}
{% endraw %}

~~~

`menu_btn.js`의 내용은 다음과 같습니다.

~~~javascript

$(function(event) {
    
    // 새글쓰기 버튼 클릭시
    $('#new_post_btn').on('click',function(event) {
        document.location.href = '/bbs/create/'
    })
  
})

~~~

---

## Model Form 생성

Model Form을 이용해서 `model`과 `fields`를 지정하면 Model Form이 자동으로 폼 필드를 생성해주기 때문에
Form 처리를 상당히 쉽게 처리할 수 있습니다.
<br><br>

어떤 Model을 기반으로 폼을 작성할 것인지를 `Meta.model` 에 지정하고
`fields`는 Model class의 field 중 일부만 폼 클래스에서 사용하고자 할 때 지정하는
옵션입니다. 모든 field를 대상으로 하려면 `__all__`을 field에 대입하면 됩니다.
<br><br>

bbs application 폴더 내에 `forms.py` 파일을 생성한 후 아래의 코드와 같이
Model Form class를 생성합니다.

~~~python

from django import forms
from .models import Board


class BoardForm(forms.ModelForm):
    class Meta:
        model = Board
        fields = ['b_title', 'b_author', 'b_content']

    # 아래의 코드가 상속되어 구현되어 있습니다. 
    # def save(self, commit=True):
    #     self.instance = Board(**self.cleaned_data)
    #     if commit:
    #         self.instance.save()
    #     return self.instance

~~~

---

## create 페이지 작성

`urls.py`의 내용을 수정합니다.

~~~python

from django.urls import path
from . import views

app_name = 'bbs'

urlpatterns = [
    path('list/', views.b_list, name='b_list'),
    path('create/', views.b_create, name='b_create'),
]

~~~

`views.py` 내용을 수정합니다.
<br><br>

`POST`방식일때와 `GET`방식일때를 구분하여 ModelForm을 create.html로 전달합니다.
<br><br>

`POST`방식일 경우는 ModelForm객체에 데이터가 담겨서 저장되어야 하는 경우이며 `GET`방식인
경우는 사용자에게 빈 양식을 보여주기 위해서 사용됩니다.
<br><br>

여기서 중요한 것 중 하나가 cleaned_data 사용입니다.
`(ModelForm instance).is_valid()`를 통해서 검증에 통과한 값은
`cleaned_data`라는 변수명으로 `dictionary`형태로 제공됩니다.
<br><br>

따라서 `request.POST['key']` 보다는 `form.cleaned_data['key']`와 같이 사용하는게
좋습니다. 그 이유는 `request.POST` 데이터는 form instance의 초기 데이터 입니다. 따라서
특정 목적의 함수(예를 들면 제목의 양쪽 공백을 제거해준다든지 하는)등을 통해 변경될 가능성이
있기때문입니다.

~~~python

from django.shortcuts import render, redirect, get_object_or_404
from .models import Board
from .forms import BoardForm, BoardDetailForm


def b_list(request):
    posts = Board.objects.all().order_by('-id')
    return render(request, 'bbs/list.html', {
        'posts': posts
    })


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
            # form.save(commit=False)를 통해서 save를 지연시킬 수 있습니다.
            # 아래의 예시처럼 추가적인 값을 설정하기 위해서 사용합니다. 
            new_post = board_form.save(commit=False)
            # 필요한 경우 field에 값을 수동으로 설정할 수 있다.
            # 예) new_post.b_like_count = 100
            new_post.save()   # 실제 저장
            return redirect('bbs:b_list')

    # GET 방식
    else:
        board_form = BoardForm()

    return render(request, 'bbs/create.html', {
        'board_form': board_form
    })


~~~

`create.html` 파일을 Bootstrap을 이용해서 수정합니다.

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
    <button type="submit" class="btn btn-primary">등록</button>
    <button type="button"
            class="btn btn-secondary"
            id="board_list_btn">리스트로 돌아가기</button>
  </form>
</div>

{% endblock %}

{% endraw %}

~~~

여기까지 작성한 후 서버를 기동하여 정상적으로 동작하는지 확인하면 됩니다.
<br><br>

추가적으로 한가지만 더 수정하도록 하죠. 글 작성 페이지의 화면을 보면 다음과 같습니다.

{% include image.html
file='python-django-bbs-create-original-css.png'
%}
<br>

ModelForm class의 몇몇 속성을 추가하면 조금 더 나은 CSS를 사용할 수 있습니다.

~~~python

from django import forms
from bbs.models import Board


class BoardForm(forms.ModelForm):
    class Meta:
        model = Board
        fields = ['b_title', 'b_author', 'b_content']

    # 아래의 코드가 구현 되어 있습니다.
    # def save(self, commit=True):
    #     self.instance = Board(**self.cleaned_data)
    #     if commit:
    #         self.instance.save()
    #     return self.instance

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
            'b_author': forms.TextInput(
                attrs={
                    'class': 'form-control w-25',
                    'placeholder': '글 작성자를 입력하세요'
                }
            ),
            'b_content': forms.Textarea(
                attrs={
                    'class': 'form-control w-75',
                    'placeholder': '글 내용을 입력하세요'
                }
            )
        }

~~~

{% include image.html
file='python-django-bbs-create-custom-css.png'
%}

---

## detail 기능 작성

이번에는 list 화면에서 글제목을 클릭했을 때 글 내용을 보여주는 `detail` 기능을 구현해 보겠습니다.
<br><br>

`urls.py` 를 수정합니다.

~~~python

from django.urls import path
from . import views

app_name = 'bbs'

urlpatterns = [
    path('list/', views.b_list, name='b_list'),
    path('create/', views.b_create, name='b_create'),
    path('<int:board_id>/detail/', views.b_detail, name='b_detail'),
]

~~~

`forms.py`를 수정해야 합니다. 이전에는 설명하기 위한 간단한 형태의 ModelForm을 이용했는데
이번에는 조금 복잡한 형태의 ModelForm을 사용해야 합니다.

~~~python

from django import forms
from bbs.models import Board


class BoardForm(forms.ModelForm):
    class Meta:
        model = Board
        fields = ['b_title', 'b_author', 'b_content']

    # 아래의 코드가 구현 되어 있습니다.
    # def save(self, commit=True):
    #     self.instance = Board(**self.cleaned_data)
    #     if commit:
    #         self.instance.save()
    #     return self.instance

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
            'b_author': forms.TextInput(
                attrs={
                    'class': 'form-control w-25',
                    'placeholder': '글 작성자를 입력하세요'
                }
            ),
            'b_content': forms.Textarea(
                attrs={
                    'class': 'form-control w-75',
                    'placeholder': '글 내용을 입력하세요'
                }
            )
        }


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
            'b_author': forms.TextInput(
                attrs={
                    'class': 'form-control w-25',
                }
            ),
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

`views.py`를 수정합니다. 다음과 같이 작성합니다. (b_detail 함수만 살펴보겠습니다.)

~~~python

def b_detail(request, board_id):

    post = get_object_or_404(Board, id=board_id)

    board_detail_form = BoardDetailForm(instance=post)
    board_detail_form.show_board_detail()

    context = {'board_detail_form': board_detail_form}

    return render(request, 'bbs/detail.html', context)
    
~~~


`detail.html`을 작성해야 합니다. 다음과 같이 작성합니다.

~~~html

{% raw %}

{% extends 'base.html' %}
{% load bootstrap4 %}
{% block html_body %}

<script src="/static/js/menu_btn.js"></script>

<div class="container">
  <h1>글 상세보기</h1>

  글 번호 : {{ board_detail_form.initial.id }}
  <form method="post">
    {% csrf_token %}
    {% bootstrap_form board_detail_form %}
    <br>
  </form>
  <button type="button"
          class="btn btn-success"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_list_btn">리스트로 돌아가기</button>
  <button type="button"
          class="btn btn-primary"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_update_btn">수정</button>
  <button type="button"
          class="btn btn-danger"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_delete_btn">삭제</button>
  <button type="button"
          class="btn btn-warning"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_like_btn">좋아요</button>

</div>

{% endblock %}

{% endraw %}

~~~

여기서 나오는 각 버튼에 대한 이벤트 처리는 jQuery를 이용해서 처리했습니다. 따라서 최종
`menu_btn.js`는 다음과 같이 작성합니다.

~~~javascript


$(function(event) {

    // 새글쓰기 버튼 클릭시
    $('#new_post_btn').on('click',function(event) {
        document.location.href = '/bbs/create/'
    })

    // 리스트버튼 클릭시
    $('#board_list_btn').on('click',function(event) {
        document.location.href='/bbs/list/'
    })

    // 수정버튼 클릭시
    $('#board_update_btn').on('click',function(event) {
        let query_string = '?board_id=' + $(this).attr('data-board_id')
        document.location.href='/bbs/update' + query_string
    })

    // 삭제버튼 클릭시
    $('#board_delete_btn').on('click',function(event) {
        let result = confirm('정말 삭제할까요?')
        if(result) {
            let query_string = '?board_id=' + $(this).attr('data-board_id')
            document.location.href='/bbs/delete' + query_string
        }
    })

    // 좋아요버튼 클릭시
    $('#board_like_btn').on('click',function(event) {
        let query_string = '?board_id=' + $(this).attr('data-board_id')
        document.location.href='/bbs/like' + query_string
    })

})

~~~

---

## delete 기능 작성

이번에는 detail 화면에서 삭제버튼을 클릭했을 때 글을 삭제하는 `delete` 기능을 구현해 보겠습니다.
위의 jQuery에서 사용하는 URL을 이용하여 `urls.py` 를 알맞게 수정합니다.

~~~python

from django.urls import path
from . import views

app_name = 'bbs'

urlpatterns = [
    path('list/', views.b_list, name='b_list'),
    path('create/', views.b_create, name='b_create'),
    path('<int:board_id>/detail/', views.b_detail, name='b_detail'),
    path('delete/', views.b_delete, name='b_delete'),
]

~~~

`views.py`를 수정합니다. (b_delete 함수만 살펴보겠습니다.)
<br><br>

삭제처리가 된 후 list화면으로 redirect처리 됩니다.

~~~python

def b_delete(request):
    post = get_object_or_404(Board, id=request.GET['board_id'])
    post.delete()
    return redirect('bbs:b_list')
    
~~~

---

## 좋아요 기능 작성

좋아요 버튼을 클릭했을 때 처리되는 과정에 대해서 알아보겠습니다. 먼저 `urls.py` 파일부터
수정해야 될 듯 합니다.

~~~python

from django.urls import path
from . import views

app_name = 'bbs'

urlpatterns = [
    path('list/', views.b_list, name='b_list'),
    path('create/', views.b_create, name='b_create'),
    path('<int:board_id>/detail/', views.b_detail, name='b_detail'),
    path('delete/', views.b_delete, name='b_delete'),
    path('like/', views.b_like, name='b_like'),
]

~~~

그 다음으로는 해당 `views.py`파일의 view 함수를 구현해야 합니다. 좋아요 개수를 1 증가한 후
detail 화면으로 전환합니다.

~~~python

def b_like(request):
    post = get_object_or_404(Board, id=request.GET['board_id'])
    post.b_like_count += 1
    post.save()

    board_detail_form = BoardDetailForm(instance=post)
    board_detail_form.show_board_detail()
    return render(request, 'bbs/detail.html', {
        'board_detail_form': board_detail_form
    })

~~~

---

## update 기능 작성

마지막으로 수정기능을 구현해보겠습니다. 수정기능은 2개의 url로 구성됩니다.
먼저 수정할 수 있는 화면을 보여줘야 하고 내용을 수정한 후 실제 데이터베이스 처리가
진행되어야 하기 때문입니다.
<br><br>

`urls.py` 파일을 수정합니다.

~~~python

from django.urls import path
from . import views

app_name = 'bbs'

urlpatterns = [
    path('list/', views.b_list, name='b_list'),
    path('create/', views.b_create, name='b_create'),
    path('<int:board_id>/detail/', views.b_detail, name='b_detail'),
    path('delete/', views.b_delete, name='b_delete'),
    path('like/', views.b_like, name='b_like'),
    path('update/', views.b_update, name='b_update'),
    path('<int:board_id>/updateProcess/', views.b_update_process, name='b_update_process'),
]


~~~

`views.py` 파일을 수정합니다.

~~~python

from django.shortcuts import render, redirect, get_object_or_404
from .models import Board
from .forms import BoardForm, BoardDetailForm

def b_update(request):
    post = get_object_or_404(Board, id=request.GET['board_id'])

    board_detail_form = BoardDetailForm(instance=post)
    board_detail_form.show_board_update()

    return render(request, 'bbs/update.html', {
        'board_detail_form': board_detail_form
    })


def b_update_process(request, board_id):
    post = get_object_or_404(Board, id=board_id)

    if request.method == 'POST':
        board_detail_form = BoardDetailForm(request.POST, instance=post)

        if board_detail_form.is_valid():
            board_detail_form.save()
            board_detail_form.show_board_detail()
            return render(request, 'bbs/detail.html', {
                'board_detail_form': board_detail_form
            })


~~~

마지막으로 `update.html` 파일을 수정합니다.

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
  {% bootstrap_form board_detail_form %}
  <br>
  <button type="button"
          class="btn btn-primary"
          id="board_list_btn">리스트로 돌아가기</button>
  <button type="submit"
          class="btn btn-danger">수정적용</button>
  </form>

</div>

{% endblock %}

{% endraw %}

~~~

---

## 댓글(comment) 기능 작성

댓글기능은 detail 화면에서 AJAX를 이용하여 구현합니다.
<br><br>

제일 먼저 손봐야 할 파일은 `views.py` 일 듯 합니다. 기존의 `b_detail` 함수는 특정 글에 대한 상세정보를
보여주기 위한 view 함수인데 여기에 댓글에 대한 처리가 같이 이루어져야 하기 때문입니다.

~~~python

def b_detail(request, board_id):

    post = get_object_or_404(Board, id=board_id)

    board_detail_form = BoardDetailForm(instance=post)
    board_detail_form.show_board_detail()

    # comment 정보도 가져와야 한다.
    comments = post.comment_set.all().order_by('-id')

    context = {'board_detail_form': board_detail_form,
               'comments': comments}

    return render(request, 'bbs/detail.html', context)

~~~

그 다음으로 수정해야 할 파일은 아마도 `detail.html`일 겁니다. view함수에서 넘어온 댓글 데이터를 출력하기 위한
내용이 포함되어야 할 듯 하네요. 또한 AJAX관련 처리를 하기 위해 HTML Element에 id 속성도 몇개 추가했습니다.
<br><br>

특히 글번호가 `AJAX`에서 사용되어야 하기 때문에 `detail.html`에서 글 번호에 해당하는 부분이 변경되어야 합니다.
jQuery를 이용해서 글 번호를 가져와야 하기 때문입니다.

~~~html

{% raw %}

{% extends 'base.html' %}
{% load bootstrap4 %}
{% block html_body %}

<script src="/static/js/menu_btn.js"></script>

<div class="container">
  <h1>글 상세보기</h1>

  글 번호 : <span id="board_id">{{ board_detail_form.initial.id }}</span>
  <form method="post">
    {% csrf_token %}
    {% bootstrap_form board_detail_form %}
    <br>
  </form>
  <button type="button"
          class="btn btn-success"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_list_btn">리스트로 돌아가기</button>
  <button type="button"
          class="btn btn-primary"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_update_btn">수정</button>
  <button type="button"
          class="btn btn-danger"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_delete_btn">삭제</button>
  <button type="button"
          class="btn btn-warning"
          data-board_id="{{ board_detail_form.initial.id }}"
          id="board_like_btn">좋아요</button>

</div>
<br><br>
<div class="container">

  <h3>댓글</h3>
  <div>
    <label for="c_name">이름</label>
    <input type="text"
           class="form-control w-25"
           id="c_name">
    <label for="c_content">내용</label>
    <input type="text"
           class="form-control w-50"
           id="c_content">
    <br>
    <button type="button"
            class="btn btn-info"
            id="comment_create_btn">등록</button>
  </div>
  <br>  
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

댓글 등록과 삭제기능은 JQuery를 이용한 AJAX로 처리했습니다. 이를 확인하기 위해 일단 Javascript 파일을 봐야할 듯 합니다.
`menu_btn.js`의 내용은 다음과 같습니다.

~~~javascript


$(function(event) {

    // 새글쓰기 버튼 클릭시
    $('#new_post_btn').on('click',function(event) {
        document.location.href = '/bbs/create/'
    })

    // 리스트버튼 클릭시
    $('#board_list_btn').on('click',function(event) {
        document.location.href='/bbs/list/'
    })

    // 수정버튼 클릭시
    $('#board_update_btn').on('click',function(event) {
        let query_string = '?board_id=' + $(this).attr('data-board_id')
        document.location.href='/bbs/update' + query_string
    })

    // 삭제버튼 클릭시
    $('#board_delete_btn').on('click',function(event) {
        let result = confirm('정말 삭제할까요?')
        if(result) {
            let query_string = '?board_id=' + $(this).attr('data-board_id')
            document.location.href='/bbs/delete' + query_string
        }
    })

    // 좋아요버튼 클릭시
    $('#board_like_btn').on('click',function(event) {
        let query_string = '?board_id=' + $(this).attr('data-board_id')
        document.location.href='/bbs/like' + query_string
    })

    // 댓글 등록버튼 클릭시
    $('#comment_create_btn').on('click',function(event) {

        $.ajax({
            async : true,
            url : '/bbs/commentCreate/',
            type : 'GET',
            data : {
                board_id : $('#board_id').text(),
                comment_author : $('#c_name').val(),
                comment_content : $('#c_content').val()
            },
            dataType : 'json',
            timeout : 3000,
            success : function(result) {
                let tr = $('<tr></tr>')
                let author_td = $('<td></td>').text(result.c_author)
                let content_td = $('<td></td>').text(result.c_content)
                let btn_td = $('<td></td>')
                let del_btn = $('<button></button>').attr('type','button')
                del_btn.attr('data-comment_id', result.c_id)
                del_btn.addClass('btn btn-danger')
                del_btn.attr('id','comment_delete_btn')
                del_btn.text('삭제')
                del_btn.on('click',function(event) {
                    $.ajax({
                        async : true,
                        url : '/bbs/commentDelete/',
                        type : 'GET',
                        data : {
                            comment_id : result.c_id
                        },
                        dataType : 'json',
                        timeout : 3000,
                        success : function(result) {
                            tr.remove()
                        },
                        error : function(err) {
                            alert('댓글 삭제 실패!')
                        }
                    })
                })

                btn_td.append(del_btn)

                tr.append(author_td)
                tr.append(content_td)
                tr.append(btn_td)

                $('#comment_body').append(tr)

            },
            error : function(err) {

                alert('댓글 등록 실패!')
            }
        })
    })

    // 댓글 삭제버튼 클릭시
    $('#comment_delete_btn').on('click',function(event) {
        let del_tr = $(this)
        $.ajax({
            async : true,
            url : '/bbs/commentDelete/',
            type : 'GET',
            data : {
                comment_id : $(this).attr('data-comment_id')
            },
            dataType : 'json',
            timeout : 3000,
            success : function(result) {
                del_tr.parent().parent().remove()
            },
            error : function(err) {
                alert('댓글 삭제 실패!')
            }
        })
    })

})

~~~

AJAX 코드에서 보이는 URL에 대한 처리를 하기 위해 `urls.py`를 수정해야 합니다.

~~~python

from django.urls import path
from . import views

app_name = 'bbs'

urlpatterns = [
    path('list/', views.b_list, name='b_list'),
    path('create/', views.b_create, name='b_create'),
    path('<int:board_id>/detail/', views.b_detail, name='b_detail'),
    path('delete/', views.b_delete, name='b_delete'),
    path('like/', views.b_like, name='b_like'),
    path('update/', views.b_update, name='b_update'),
    path('<int:board_id>/updateProcess/', views.b_update_process, name='b_update_process'),
    path('commentCreate/', views.c_create, name='c_create'),
    path('commentDelete/', views.c_delete, name='c_delete'),

]


~~~

마지막으로 로직처리와 AJAX 호출의 결과로 보내지는 JSON을 어떻게 처리하는지를 확인하기 위해 `views.py` 파일의 내용을
살펴보겠습니다.

~~~python

from django.shortcuts import render, redirect, get_object_or_404
from .models import Board, Comment
from .forms import BoardForm, BoardDetailForm
from django.http import JsonResponse


def c_create(request):

    comment = Comment()
    comment.c_author = request.GET['comment_author']
    comment.c_content = request.GET['comment_content']
    comment.board_id = request.GET['board_id']
    comment.save()
    return JsonResponse({
        'code': '200',   # code 200의 의미는 삭제성공의 의미로 가정
        'c_author': request.GET['comment_author'],
        'c_content': request.GET['comment_content'],
        'c_id': comment.id
    }, json_dumps_params={'ensure_ascii': True})


def c_delete(request):

    comment = get_object_or_404(Comment, id=request.GET['comment_id'])
    comment.delete()
    return JsonResponse({
        'code': '200'   # code 200의 의미는 삭제성공의 의미로 가정
    }, json_dumps_params={'ensure_ascii': True})

~~~

End.

{% include links.html %}
