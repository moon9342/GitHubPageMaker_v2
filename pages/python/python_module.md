---
title: Python module
sidebar: python_sidebar
summary: "module의 개념과 사용에 대해 살펴보도록 하겠습니다."
permalink: python_module.html
folder: python
---

## module

python의 `module`은 함수나 변수 또는 class들을 모아 놓은 파일을 지칭합니다.
<br><br>

즉, 다른 Python 프로그램에서 불러와 사용할 수 있도록 만들어진 Python 파일을 의미합니다.
<br><br>

module을 사용해야 하는 이유는 간단합니다. 코드의 재활용성을 높이고 관리를 더 쉽게 하기 위해서
입니다. Python의 스탠다드 라이브러리는 크게 두 가지로 구성됩니다.
<br><br>

* C언어로 구현된 binary module
* Python언어로 구현된 일반 module
<br><br>

우리가 많이 사용하는 import keyword는 Python module을 사용할 수 있도록 만들어 주는 keyword입니다.
여기서 한가지 기억하셔야 할 점은 Python의 module은 객체라는 것입니다.

~~~ python

import sys

sys.path.append("C:/Python_Lib");

# 환경변수 PYTHONPATH에 모듈을 저장한 폴더를 설정해도
# import 할 수 있다.

# module1.py를 C:/Python_Lib에 작성하자!

##########################
# module1.py
def sum(a,b):
    return a+b

module1_PI = 3.141592
##########################


# import 모듈이름
# import 모듈이름 as alias
# from 모듈이름 import 모듈함수(모듈변수,모듈class)

import module1

print(module1.module1_PI)
print(module1.sum(10,20))

import module1 as m1

print(m1.module1_PI)
print(m1.sum(10,20))

from module1 import module1_PI
print(module1_PI)

from module1 import *
print(sum(100,200))

~~~

---

## package

Python에서 package는 `.`을 이용하여 Python module을 계층적으로 관리합니다.
단적으로 말하면 package는 module을 담고 있는 폴더를 지칭합니다.
<br><br>

예를 들어, 만약 module명이 `A.B`인 경우 A는 package, B는 module을 의미합니다.
<br><br>

특정 디렉토리에 `__init__.py` 파일이 존재하면 해당 디렉토리가 package임을 의미합니다.
( Python 3.3 이후부터는 `__init__.py`가 없어도 package로 인식됨 )

~~~python

import myFolder.myModule.module1
print(myFolder.myModule.module1.sum(10,20))


from myFolder.myModule import module1
print(module1.module1_PI)


from myFolder.myModule.module1 import sum
print(sum(100,200))


from myFolder.myModule.module1 import *
print(module1_PI)

~~~


End.

{% include links.html %}
