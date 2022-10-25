---
title: Python Django Poll Project(2)
sidebar: python_sidebar
summary: "Python Django Poll Project에 관한 내용입니다."
permalink: python_django-poll-2.html
folder: python
---

## Model

Model 작업은 우리가 사용하는 Database에 Table을 생성하는 작업입니다.
<br><br>

우리의 polls application은 `Question`과 `Choice` 두 개의 테이블이 필요합니다.
Table은 `models.py` 파일에 정의합니다.
<br><br>

`C:/python-Django/MyFirstWebPoll/polls` 폴더에 존재하는 `models.py` 파일의 내용을
다음과 같이 수정합니다.

~~~python

from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

    def __str__(self):
        return self.question_text


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

    def __str__(self):
        return self.choice_text

~~~

Django에서는 Table을 하나의 class로 정의합니다. 또한 Table의 column은 class의
속성으로 mapping합니다.
<br><br>

위에서 표현이 안된 부분이 있는데 바로 Table의 `id` column입니다. Table의 id column은
Primary key로 지정되며 정수(integer)형태입니다. 또한 Django가 자동으로 생성해
주기 때문에 class 정의에서는 표현되지 않았습니다.
<br><br>

참고로 PK(primary key)는 `Not Null` 속성을 가지며 `AUTO_ICREMENT`로 지정됩니다.
<br><br>

조심해야 될 점은 Choice class의 FK로 지정되어 있는 question 입니다. FK이기 때문에
어떤 reference할 class만 명시하면 되고 실제 Table이 생성될 때 `_id` 접미어가 붙어서
column이 생성됩니다. 즉, column명이 `question` 이 아닌 `question_id` 가 되게 됩니다.

---

## Admin site에 테이블 반영

이전에 살펴본 Admin site에는 Users와 Groups Table만 존재합니다. 방금 `models.py` 에서
정의한 Table도 Admin site에서 보이도록 등록해야 할 듯 합니다.
<br><br>

`admin.py` 파일을 아래의 내용으로 수정합니다.

~~~python

from django.contrib import admin
from polls.models import Question, Choice

admin.site.register(Question)
admin.site.register(Choice)

~~~ 

---

## Database 변경 사항 반영

당연한 말이지만 Database에 변경사항이 있으면 이를 반영해 주어야 합니다.
class로 Table만 정의한 단계이지만 실제로 Database에 반영을 시켜줘야 합니다.
<br><br>

> **C:/python-Django/MyFirstWebPoll> python manage.py makemigrations**

> **C:/python-Django/MyFirstWebPoll> python manage.py migrate**

마이그레이션은 테이블 및 필드의 생성, 삭제, 변경 등과 같이 데이터베이스에 대한
변경사항을 알려주는 정보입니다. 물리적으로는 application 디렉토리별로 마이그레이션
파일이 존재합니다. `makemigrations` 명령에 의해 polls/migrations 디렉토리 하위에
마이그레이션 파일들이 생성되고 이 마이그레이션 파일들을 이용해 `migrate` 명령으로
데이터베이스에 대한 테이블을 만들어줍니다.

---

## Admin site에서 변경사항 확인

지금까지 작업한 내용이 잘 적용되었나를 Admin site에서 확인해보도록 하겠습니다.
서버를 기동하고 Admin site에 접속합니다.

{% include image.html
file='python-django-admin-database.png'
%}
<br>

이번 절에서는 Model을 생성하는 방법에 대해서 알아보았습니다. 그럼 다음에는 View와 Template에
대해서 알아보면서 우리의 간단한 Web Project를 완성해 보도록 하죠.

End.

{% include links.html %}
