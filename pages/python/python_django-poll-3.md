---
title: Python Django Poll Project(3)
sidebar: python_sidebar
summary: "Python Django Poll Project에 관한 내용입니다."
permalink: python_django-poll-3.html
folder: python
---

## View와 Template

아래의 그림은 우리의 Poll Project의 전체 처리 흐름을 도식화 한 것입니다.
다시한번 느끼지만 그림에는 정말 소질이 없군요.(글자도 잘 안보이고...)

{% include image.html
file='python-django-polls-view-template.png'
%}
<br>

기본적으로 4개의 URL을 사용하고 여기에 Admin site까지 포함하여 총 5개의 URL을 이용할
것입니다. 그리고 각각의 URL은 View와 1:1로 mapping되어 있습니다. 일반적으로 URL은 View와
1:1 mapping을 하지만 N:1의 관계도 가능합니다.
<br><br>

각각의 View는 Template을 이용하여 결과 화면을 클라이언트에게 전송하게 됩니다.
<br><br>

제일 먼저 처리해야 하는 내용은 URL과 View의 mapping처리 입니다. 이것을 `URLConf`라고 하며
`urls.py` 파일에 작성합니다.

~~~python

from django.contrib import admin
from django.urls import path
from polls import views


urlpatterns = [
    path('admin/', admin.site.urls),
    path('polls/', views.index, name='index'),
    path('polls/<int:question_id>/', views.detail, name='detail'),
    path('polls/<int:question_id>/vote/', views.vote, name='vote'),
    path('polls/<int:question_id>/results/', views.results, name='results'),
]

~~~

몇가지 기억해야 하는 사항이 있습니다.
<br><br>

* URL pattern matching은 위에서 아래로 진행됩니다. 따라서 정의하는 순서에 유의해야 합니다.
<br><br>

* Django의 Admin site에 대한 URLConf는 이미 정의 되어 있습니다. 항상 똑같이 사용하세요.
<br><br>

* path() 함수를 이용하여 URL과 View를 mapping하고 있습니다.
<br><br>

여기서 중요한 것은 `path()` 함수인데 path()함수는 2개의 필수인자를 가집니다.
우리예에서 보듯이 route와 view입니다. 또한 kwargs와 name 2개의 선택인자를 가질 수 있습니다.
<br><br>

* `route`
: URL pattern을 표현하는 문자열입니다. URL String이라고도 합니다.

* `view`
: URL String이 matching되면 호출되는 View의 함수입니다. HttpRequest 객체와 URL String에서 추출된 항목이 View 함수의 
인자로 전달됩니다.

* `kwargs`
: URL String에서 추출된 항목외에 추가적인 인자를 View 함수에 전달할 목적으로 dict 형태로 사용됩니다.

* `name`
: 각 URL pattern에 대한 논리적인 이름입니다. 이 이름은 Template에서 사용됩니다.

<br>

두가지 예만 들어보겠습니다.
<br><br>

만약 요청 URL이 `/polls/` 라면 `path('polls/', views.index, name='index')` 부분이
matching됩니다. 이 경우 URL String에서 추출되는 항목이 없기 때문에 `views.index(request)` 처럼
View 함수가 호출됩니다. 그리고 이 URL pattern의 이름은 `index`로 정의했습니다.
<br><br>

만약 요청 URL이 `/polls/5` 라면 URL String에서 추출되는 항목이 있기 때문에
`views.detail(request,question_id=3)`처럼 인자가 대입되서 View 함수가 호출되게 됩니다. 그리고
이 URL pattern의 이름은 `detail`로 정했습니다.
<br><br>

기억해야 할 점 또 하나는 `mysite/settings.py` 파일에 `ROOT_URLCONF` 항목이 정의되어 있는데
Django는 URL 분석 시, 이 항목에 정의된 `urls.py`파일을 가장 먼저 분석하게 됩니다.
<br><br>

여기서 조금 복잡한 사항이 하나 나옵니다. 지금 우리가 하는 것처럼 `mysite/urls.py` 파일에
모든 URL과 View의 mapping을 기술 할 수도 있지만 이런 경우 재사용과 관리에 문제가 발생할 수 있습니다.
<br><br>

그래서 일반적으로 urls.py를 applciation단위로 나누어서 작성합니다.
<br><br>

즉, `mysite/urls.py`의 내용은 아래와 같이 작성합니다.

~~~python

from django.contrib import admin
from django.urls import path, include
from polls import views 

urlpatterns = [
    path('admin/', admin.site.urls),
    path('polls/', include('polls.urls')),
]

~~~

그리고 `MyFirstWebPoll/polls/urls.py` 파일은 아래와 같이 작성합니다.

~~~python

from django.urls import path
from . import views

app_name = 'polls'

urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
    path('<int:question_id>/results/', views.results, name='results'),
]

~~~

이렇게 파일을 나누어서 URL과 View의 mapping을 관리할 수 있습니다. 당연히 파일을
분리해서 관리하는 것이 훨씬 좋은 방법입니다. `app_name = 'polls'` 부분은
URL pattern이 충돌나는 것을 방지하기 위한 `namespace` 설정입니다.
<br><br>

우리 예제에서는 polls가 하나뿐이지만 보통의 프로젝트에서는 여러 개의 application으로
구성되는 경우가 대부분입니다. 만약 polls application의 URL 패턴 이름과 blog application의
URL 패턴 이름이 모두 detail이면 이 둘을 구분해야 하고 `app_name` 변수로 이름 공간을 지정하는 것입니다.
<br><br>

즉, polls application의 detail은 polls:detail, blog application의 detail은
blog:detail로 표기해서 구분하게 됩니다.
<br><br>

우리의 예제는 파일을 나누어서 URL과 View의 mapping관리를 하는 방법을 사용하도록
하겠습니다.

---

## Template 작성

위에서 URL과 View에 대한 mapping설정을 했으니 이제 View와 Template을 작성할 차례입니다.
이 둘은 서로 서로에게 영향을 미치기 때문에 보통 같이 작업하는게 일반적이지만 UI화면을
먼저 생각하면서 로직처리를 하는것이 쉬울 수 있기 때문에 View보다는 Template을 먼저 작성하면
조금 더 쉬운 경향이 있습니다.
<br><br>

자 그럼 Template 파일 index.html을 작성해보도록 하죠. 이 Template 파일 역시 관리상의
이유로 하나의 폴더에서 모든 Template을 관리하는 것이 아니라 application별로 따로 관리합니다.
<br><br>

> **C:/python-Django/MyFirstWebPoll/polls> mkdir templates**

> **C:/python-Django/MyFirstWebPoll/polls> mkdir templates/polls**

> **C:/python-Django/MyFirstWebPoll/polls> cd templates/polls**

<br>

이 폴더 내에서 `index.html`을 아래처럼 작성합니다.

~~~html

{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    {% if latest_question_list %}
        <ul>
        {% for question in latest_question_list %}
            <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
        {% endfor %}
        </ul>
    {% else %}
        <p>No Polls are available</p>
    {% endif %}
</body>
</html>
{% endraw %}

~~~ 

여기서 주의해서 보셔야 하는 건 `latest_question_list` 입니다. View 함수인 index()에서
넘겨주는 인자입니다. 코드로 유추해 보건대 설문에 대한 번호와 질문내용을 가지고 있는 Question
객체로 구성된 list 일 듯 합니다.
<br><br>

사실 구현할 때는 역으로 생각하셔야 합니다. 즉, Template을 만들면서 어떤 데이터를 넘겨줘야
Template이 잘 표현될지를 생각해서 이 데이터를 View 함수에서 `context` 변수로 정의한 후
Template으로 넘겨줘야 하기 때문입니다.

---

## View 작성

`C:/python-Django/MyFirstWebPoll/polls` 폴더 하단에 있는 `view.py`을 다음과 같이 수정합니다.

~~~python

from django.shortcuts import render
from polls.models import Question


def index(request):
    latest_question_list = Question.objects.all().order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)

~~~

기억해야 할 사항은 다음과 같습니다.
<br><br>

* `render`
: 단축함수 중 하나로 Template 코드를 로딩한 후 context 변수를 적용하고 그 결과를 HttpResponse 객체에 담아 반환하는 
작업을 수행합니다. 이런 복잡한 과정을 Django 에서는 `render()` 내장함수로 제공하고 있습니다.

* `Question`
: Question Table에 access하기 위해 polls.models.Question class를 사용합니다.

* View 함수를 정의한 후 Template에게 넘겨줄 데이터를 생성합니다.

* Template에게 넘겨줄 때는 dict 형태로 넘겨주며 context 객체를 생성해서 render()함수에
  전달합니다.

* index() 함수는 최종적으로 클라이언트에게 응답할 데이터인 HttpResponse 객체를 반환합니다.

* 한가지 의문이 드는것은 render() 함수에서 Template 파일을 어떻게 찾느냐 하는 것입니다.
  Django에서 Template 파일을 찾을 때 `settings.py` 파일에 정의된 `TEMPLATES` 그리고
  `INSTALLED_APPS`에서 지정된 디렉토리를 검색하게 됩니다.

---

## 지금까지의 작업 확인

지금까지 작성한 내용으로 서버를 기동시켜서 `/polls/`로 접속하려 합니다. 하지만 아직 작성하지
않은 파일들이 존재하기 때문에 바로 동작되지는 않을테고 아직 미구현인 부분들은 주석처리 후
서버를 기동시켜서 접속을 해보도록 하죠
<br><br>

당연히 `No Polls are available`가 출력되게 됩니다. 아직 어떠한 질문도 등록하지 않았거든요.
<br><br>

그럼 Admin site로 접속한 후 데이터를 입력하고 다시 접속해 보도록 하겠습니다.
질문은 3개만 등록하도록 하겠습니다.
<br><br>

* 취미가 무엇인가요?
* 가장 좋아하는 선수는 누구인가요?
* 어디에 살고 있나요?

<br>

이제 화면이 정상적으로 보이나요?

{% include image.html
file='python-django-first-page.png'
%}
<br>

End.

{% include links.html %}
