---
title: Python Django Poll Project(5)
sidebar: python_sidebar
summary: "Python Django Poll Project에 관한 내용입니다."
permalink: python_django-poll-5.html
folder: python
---

## 마지막 화면 작성

아래의 그림은 우리의 Poll Project의 전체 처리 흐름을 도식화 한 것이죠.

{% include image.html
file='python-django-polls-view-template.png'
%}
<br>

이전 절에서 첫화면에 있는 3개의 질문 중 하나를 선택했을 때, 질문에 대한 답변 항목을
보여주고 투표하도록 하는 화면을 작성해 보았습니다.
<br><br>

이번에는 답변항목을 선택한 후 `Vote` 버튼을 클릭했을 때 처리되는 부분에 과정과 최종
화면을 작성해 보겠습니다.
<br><br>

먼저 View 함수인 vote() 부터 알아봐야 할 듯 합니다.
<br><br>

View 함수인 vote()의 호출과 연관된 URL은 detail.html안의 form tag의 action에서
나타납니다.

~~~html

{% raw %}
<form action="{% url 'polls:vote' question.id %}"
          method="post">
{% endraw %}

~~~

이 부분은 결국 `polls/urls.py` 파일안에 다음과 같은 부분과 mapping되게 됩니다.

~~~python

app_name = "polls"

urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
    path('<int:question_id>/results/', views.results, name='results'),
]

~~~

그럼 vote() 함수를 작성해 보도록 하죠.

~~~python

from django.shortcuts import render, get_object_or_404
from polls.models import Question, Choice
from django.http import HttpResponseRedirect, HttpResponse
from django.urls import reverse


def vote(request,question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "아무것도 선택하지 않았어요!!"
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))

~~~ 

이번에도 상당히 복잡할 듯 보이네요. 기억해야 하는 부분들을 하나씩 살펴보겠습니다.
<br><br>

* 이번에는 `redirect` 라는 기능이 필요합니다. 이를 위해 `HttpResponseRedirect`를 import 합니다.
<br><br>

* url 처리를 위해 `reverse()` 함수를 역시 import 처리 합니다.
<br><br>

* vote() 함수의 request 객체는 필수 인자이고 detail() 처럼 question_id 인자를 더 받습니다.
<br><br>

* `get_object_or_404()` 단축함수를 사용하였고 이번에는 Choice Table을 검색하는 용도로 사용되고 있습니다. 
검색 조건은 `pk=request.POST['choice']`로 되어 있네요. `request.POST`는 form의 데이터를 담고 있는 객체입니다. 
`request.POST['choice']`는 form의 데이터 중 key값이 choice에 해당하는 값인 choice.id를 의미하겠네요.
<br><br>

* 만약 `choice`라는 key가 `request.POST`에 존재하지 않으면 `KeyError` exception이 발생합니다. 또한 검색 조건에 맞는 
객체가 없으면 `Choice.DoesNotExist` exception이 발생합니다.
<br><br>

* 만약 exception이 발생하면 render() 함수를 이용해 question과 error_message 변수를 detail.html로 전달합니다. 
사용자에게 질문 항목을 다시 보여주기 위함이죠.
<br><br>

* exception이 발생하지 않으면 Choice 객체의 votes 속성의 값을 1증가시킵니다. 그리고 변경사항을 Choice Table에 적용하게 됩니다.
<br><br>

* 주의해야 할 점은 이번에 vote()함수가 반환하는 객체가 HttpResponse가 아닌 `HttpResponseRedirect` 라는 것입니다. 
`HttpResponseRedirect` 객체는 redirect할 URL을 인자로 가지며 reverse() 함수를 이용하여 생성합니다. 
이 reverse() 함수는 URL pattern으로부터 URL string을 구하기 위해서 사용합니다. 당연히 URLConf에 이미 정의된 
URL pattern을 활용해서 URL string을 추출하게 됩니다.

---

## View 함수 results()와 Template 작성

View 함수인 vote() 함수의 redirect로 results() 함수가 호출되게 됩니다. 정확하게는
서버로 부터 redirect 하라는 내용을 결과로 받은 클라이언트 웹 브라우져가 redirect URL로
다시 요청을 보내는 것이죠.
<br><br>

이번에는 먼저 `results()` 함수를 작성한 후 결과를 보여주는 `result.html` Template 파일을 작성하도록
하겠습니다.
<br><br>

아래와 같이 results() 함수를 작성합니다.

~~~python

def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {
        'question': question
    })

~~~

앞에서 다 설명했던 부분들이니 바로 `results.html` 파일을 작성하도록 하겠습니다.

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

    <ul>
    {% for choice in question.choice_set.all %}
        <li>{{ choice.choice_text }} - {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
    {% endfor %}
    </ul>

    <a href="{% url 'polls:detail' question.id %}">다시 투표</a>
</body>
</html>
{% endraw %}

~~~

음.. 대부분의 내용은 이제 어느정도 이해가 갈 듯 합니다. 한가지 특이한 코드가 있는데
`choice.votes|pluralize`입니다. `choice.votes`의 값에 따라서 복수 접미사(s)를
붙여주는 역할을 합니다.
<br><br>

이제 모든 작업을 완료했습니다. 서버를 기동하고 `/polls`로 접속해서 잘 동작하는지
확인해보도록 하죠.

{% include image.html
file='python-django-third-page.png'
%}
<br>

End.

{% include links.html %}
