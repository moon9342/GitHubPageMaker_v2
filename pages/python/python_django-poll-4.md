---
title: Python Django Poll Project(4)
sidebar: python_sidebar
summary: "Python Django Poll Project에 관한 내용입니다."
permalink: python_django-poll-4.html
folder: python
---

## 두번째 화면 작성

아래의 그림은 우리의 Poll Project의 전체 처리 흐름을 도식화 한 것이죠.

{% include image.html
file='python-django-polls-view-template.png'
%}
<br>

이전 절에서 `/polls/` URL로 접속했을 때 View 함수와 Template 파일을 작성해서
첫 화면을 browser에 출력해보았습니다.
<br><br>

이번에는 첫화면에 있는 3개의 질문 중 하나를 선택했을 때, 질문에 대한 답변 항목을
보여주고 투표하도록 하는 화면을 작성해 보도록 하겠습니다.
<br><br>

먼저 Template 화면부터 생성하면서 어떠한 데이터가 View 함수로부터 넘어오면 되는지를
생각해보면 될 듯 합니다.
<br><br>

Template 파일의 이름은 `detail.html` 이고 아래와 같이 입력합니다.

~~~html

{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>{{ question.question_text }}</h1>
    {% if error_message %}
    <p><strong>{{ error_message }}</strong></p>
    {% endif %}

    <form action="{% url 'polls:vote' question.id %}"
          method="post">
    {% csrf_token %}
    {% for choice in question.choice_set.all %}
        <input type="radio" name="choice"
               id="choice{{ forloop.counter }}"
               value="{{ choice.id }}">
        <label for="choice{{ forloop.counter }}">
            {{ choice.choice_text }}
        </label><br>
    {% endfor %}
        <br>
        <input type="submit" value="Vote">
    </form>
</body>
</html>
{% endraw %}

~~~

상당히 복잡하네요. 하나씩 알아보도록 하겠습니다.
<br><br>

* `question.question_text` : 질문을 제목으로 사용하는 것입니다. 크게 표현하기 위해서 `<h1>` tag를 이용하고 있습니다.
<br><br>

* `error_message` : 만약 에러가 있으면 에러 메시지를 화면에 보여주기 위해서 if 처리를 했습니다. 
에러 체크는 View의 vote() 함수에 구현될 것입니다. 만약 vote() 함수에서 exception이 발생하면 error_message를 담아서 
detail.html Template을 rendering하게 됩니다.
<br><br>

* `form`에 입력된 데이터는 `POST` 방식으로 서버측에 전달됩니다. REST 환경이 아닐때는 GET과 POST방식을 사용하는데 
데이터를 읽어올때는 일반적으로 GET, 서버측의 데이터를 변경할때는 POST 방식을 이용합니다. (반드시 그런것은 아닙니다.) 
그러면 데이터를 어디로 보낼까요? `action` 속성에 해당 URL을 명시합니다. 여기서는 `url` template tag를 이용하여 받는 곳의
URL을 `polls:vote`로 설정했습니다. polls:vote에서 polls는 namespace를 의미하고 vote는 URL의 이름을 의미합니다. 
이 부분은 `polls/urls.py`에 명시되어 있습니다. 결과적으로 받는곳의 URL은 `/polls/1/vote/` 과 같은 형식이 될 것입니다.
<br><br>

* `form`을 처리하는데 보안상의 issue를 해결하기 위해 `csrf_token` template tag를 사용했습니다. 
CSRF(Cross Site Request Forgery) 공격은 특정 웹사이트에서 이미 인증받은 사용자를 이용하여 웹 사이트를 공격하는 방법입니다.
<br><br>

* `for` 구문을 이용하여 Question 객체가 가지고 있는 데이터를 이용하여 radio button을 구현하고 있습니다.
<br><br>

* `forloop.counter` : for loop를 실행한 횟수를 담고 있는 template 변수 입니다. 여기서 label tag의 `for` 속성과 
radio button의 `id` 속성 값이 같아야 서로 바인딩 된다는것도 추가로 기억해야 합니다.
<br><br>

* `Vote` 버튼을 누르면 사용자가 선택한 form data가 POST 방식으로 `polls:vote` URL로 전송됩니다. 이렇게 전송된 데이터는 
View 함수 vote()에서 access 할 수 있습니다.
<br><br>

* 마지막으로 Question 객체의 `choose_set` 속성에 대해서 알아보겠습니다. Question Table과 Choice Table은 1:N 의 관계를 
가지고 있고 Foreign Key로 연결되어 있습니다. 이렇게 1:N의 관계에서 1 Table에 N Table의 항목이라는 의미로 `xxx_set` 속성을 
default로 제공합니다. 즉, question.choice_set.all()이라고 하면 Question Table의 question record에 연결된 Choice Table의
모든 record를 의미하게 됩니다. 단, Template 문법상 method 호출을 표시하는 `()`는 사용하지 않기때문에 Template 파일에서는 
`question.choice_set.all` 이라고 사용된 것입니다.
<br><br>

상당히 복잡했는데 몇번을 다시 보면서 이해하시면 좋을 듯 합니다.
<br><br>

이제 이 Template을 사용하는 View 함수인 detail() 함수를 작성하고 실행해서 투표항목이 정상적으로
출력이 되는지를 확인해 보도록 하겠습니다.
<br><br>

`views.py` 파일을 다시 열고 기존의 index() 함수 다음에 아래의 코드를 입력합니다.

~~~python

from django.shortcuts import render, get_object_or_404
from polls.models import Question


def index(request):
    latest_question_list = Question.objects.all().order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)


def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})

~~~  

View 함수를 정의했습니다. detail()이라는 함수를 정의했고 추가적으로 URL로 부터 추출한
`question_id` 인자를 View 함수의 인자로 넘겨 받았습니다.

~~~python

path('<int:question_id>/', views.detail, name='detail')

~~~

위의 부분에서 `<int:question_id>` 부분을 의미합니다.
<br><br>

* `get_object_or_404()` : Django에서 제공되는 단축함수입니다. 이 함수의 첫번째 인자는 Model class이고 두번째 인자부터는 
검색 조건을 여러 개 사용할 수 있습니다. 우리예제에서는 검색조건이 `pk=question_id` 이기 때문에 primary key가 question_id와 
일치하는 객체를 조회하게 됩니다. 만약 조회된 객체가 없을 시 `Http404` exception이 발생하게 됩니다.
<br><br>

* 추가적으로 `get_list_or_404()` 단축함수도 있습니다. 이것은 대상객체의 집합을 list로 가져올 때 사용합니다.

---

## 지금까지의 작업 확인

지금까지 작성한 내용으로 서버를 기동시켜서 `/polls/`로 접속한 후  
3개의 질문 중 하나를 선택했을 때, 질문에 대한 답변 항목을 보여주고
투표하도록 하는 화면이 나오는지를 확인하도록 하겠습니다.
아직 미구현인 부분들은 주석처리 후 서버를 기동시켜서 접속을 해보도록 하죠
<br><br>

특히 form tag `action` 속성부분은 일단 주석처리해야 할 듯 합니다.

~~~html
{% raw %}
{% comment %}
    <form action="{% url 'polls:vote' question.id %}"
          method="post">
{% endcomment %}
    <form action=""
          method="post">
{% endraw %}
~~~

그리고 데이터베이스에 각 질문에 대한 선택항목을 저장하지 않았기 때문에
Admin site에 접속해서 질문에 대한 선택항목을 저장한 후 실행하면 다음과 같은 화면을
보실 수 있습니다.

{% include image.html
file='python-django-second-page.png'
%}
<br>

End.

{% include links.html %}
