---
title: Python decorator
sidebar: python_sidebar
summary: "decorator의 개념과 사용에 대해 살펴보도록 하겠습니다."
permalink: python_decorator.html
folder: python
---

## decorator

Python은 `decorator`라는 것을 가지고 있습니다. 사전적의미는 아시는 것처럼
장식가, 도배업자 라는 뜻을 가지고 있습니다. 장식가라는 의미처럼 `decorator`는
기존의 코드에 여러가지 기능을 추가하는 Python 구문이라고 생각하시면 일단 편합니다.
<br><br>

객체지향에서는 decorator pattern이라는 디자인 패턴이 있을 정도로 활용도가 높은 형태입니다.
<br><br>

`closure` 기억하시나요? Closure는 함수의 결과값으로 다른 함수를 리턴하는 형태에서
free variable의 값을 바인딩하기 위해서 적용되는 기술을 의미했었습니다.
<br><br>

`decorator`는 약간 형태가 다릅니다. Decorator는 함수의 인자로 다른 함수를 전달하는 과정에서
적용할 수 있는 기술을 의미합니다.
<br><br>

아래의 예를 보면서 알아도보록 하겠습니다.

~~~python

def my_outer_function(func): 

    def my_inner_function():          
        func()  

    return my_inner_function  


def my_func():  
    print("my_func함수가 수행되었습니다.!!")

decorated_my_func = my_outer_function(my_func)

my_func()              # my_func함수가 수행되었습니다.!!
decorated_my_func()    # my_func함수가 수행되었습니다.!!

~~~

`my_func` 함수를 그냥 이용하나 복잡하게 함수를 인자로 넘기고 결과로
함수를 다시 넘겨받아서 실행하나 결과는 똑같습니다.
<br><br>

위의 예제를 조금만 수정해보도록 하죠

~~~python

import time

def my_outer_function(func): 

    def my_inner_function():          
        print("{} 함수수행 시간을 계산합니다.".format(func.__name__))
        start = time.time()
        func()  
        end = time.time() - start 
        print("{} 함수수행 시간은 {} 입니다.".format(func.__name__,end))


    return my_inner_function  


def my_func():  
    print("my_func함수가 수행되었습니다.!!")

decorated_my_func = my_outer_function(my_func)

my_func()              # my_func함수가 수행되었습니다.!!

decorated_my_func()    # 추가적인 기능이 포함되어 있다.!!

~~~ 

위의 코드를 보면 살짝 감이 오시나요? 기존의 함수에 추가적인 기능을 부여하는 역할을
수행하고 있습니다. 만약 기존의 여러개의 함수에 동일한 추가 기능을 넣어야 한다면
모든 함수를 다 일일이 수정하는 것보다는 당연히 decorator를 이용하여 처리하는 것이 훨씬
좋은 방법이 될 것입니다.
<br><br>

자 그럼 일반적으로 표현하는 식으로 다시 살펴보도록 하겠습니다. 위의 코드는 decorator의
기능을 설명하기 위한 코드이고 실제는 아래와 같이 표현합니다.

~~~python

import time

def my_outer_function(func): 

    def my_inner_function():          
        print("{} 함수수행 시간을 계산합니다.".format(func.__name__))
        start = time.time()
        func()  
        end = time.time() - start 
        print("{} 함수수행 시간은 {} 입니다.".format(func.__name__,end))


    return my_inner_function  

@my_outer_function
def my_func():  
    print("my_func함수가 수행되었습니다.!!")

# decorated_my_func = my_outer_function(my_func) # 이 코드는 필요없습니다.

my_func()              # 추가적인 기능이 포함된 my_func() 호출

~~~

훨씬 간편하게 사용할 수 있게되었습니다. 이번에는 다른 함수를 추가하여
동일하게 함수의 실행시간을 측정하는 코드를 추가하여 적용해 보도록 하겠습니다.
단, 추가하는 함수는 인자를 받아 들이는 함수입니다.

~~~python

import time

def my_outer_function(func): 

    def my_inner_function():          
        print("{} 함수수행 시간을 계산합니다.".format(func.__name__))
        start = time.time()
        func()  
        end = time.time() - start 
        print("{} 함수수행 시간은 {} 입니다.".format(func.__name__,end))


    return my_inner_function  

@my_outer_function
def my_func():  
    print("my_func함수가 수행되었습니다.!!")

@my_outer_function
def my_adder(x,y):
    print("두 수를 더한 결과는 {}".format(x+y))    

# decorated_my_func = my_outer_function(my_func) # 이 코드는 필요없습니다.

my_func()              # 추가적인 기능이 포함된 my_func() 호출
my_adder(10,20)        # Error 발생 (인자 처리가 안되어 있음)

~~~   

인자를 처리하기 위한 코드가 추가되어야 할 것 같습니다.
여기서 사용되어 지는것이 `*args`, `**kwargs`입니다. 일단 이 두놈부터 알아야
할 듯 하니 간단하게 알고 넘어가도록 하겠습니다.
<br><br>

`*args`는 `*argments`의 줄임말입니다. 그리고 반드시 이 단어를 쓸 필요는 없습니다.
관용적으로 저렇게 표현합니다. 이 표현은 복수개의 인자를 함수로 받을 때 사용합니다.
그리고 이렇게 받은 복수개의 인자는 `tuple`로 함수에 전달되게 됩니다.

~~~python

def printUserName(*args):

    for name in args:
        print("입력된 사람은 : {}".format(name))

printUserName("홍길동","신사임당","강감찬")

~~~

`kwargs`는 `keyword argments`의 줄임말입니다. 만약 dictionary 형태로 함수에 인자가
전달되면 그 내용을 이 `**kwargs`가 받을 수 있습니다. 다음의 예를 보죠.

~~~python

def printUserName(**kwargs):

    if "korea" in kwargs:
        print("서울의 인구는 : {}".format(kwargs.get("korea")))
        
    if "USA" in kwargs:
        print("미국의 인구는 : {}".format(kwargs.get("USA")))


printUserName(korea="오천만", USA="2억")

~~~

이러한 `*args`와 `**kwargs`를 이용하면 우리의 문제를 해결할 수 있습니다.

~~~python

import time

def my_outer_function(func): 

    def my_inner_function(*args, **kwargs):          
        print("{} 함수수행 시간을 계산합니다.".format(func.__name__))
        start = time.time()
        func(*args, **kwargs)  
        end = time.time() - start 
        print("{} 함수수행 시간은 {} 입니다.".format(func.__name__,end))


    return my_inner_function  

@my_outer_function
def my_func():  
    print("my_func함수가 수행되었습니다.!!")

@my_outer_function
def my_adder(x,y):
    print("두 수를 더한 결과는 {}".format(x+y))    

# decorated_my_func = my_outer_function(my_func) # 이 코드는 필요없습니다.

my_func()              
my_adder(10,20)         

~~~

그러면 decorator는 한개만 적용가능한가? 라는 궁금증이 생길만도 한데 한번 알아보도록
하겠습니다. 위의 예에서 decorator함수를 2개로 분리했습니다. 하나는 어떠한 함수가
실행되는지를 출력하는 decorator함수, 다른 하나는 실행시간을 출력하는 함수로
분리했습니다. 잘못된 예를 먼저 보겠습니다.

~~~python

import time

def my_exec_function(func): 

    def my_inner_function(*args, **kwargs):          
        print("{} 함수가 실행됩니다.".format(func.__name__))
        return func

    return my_inner_function  


def my_timer_function(func): 

    def my_inner_function(*args, **kwargs):          
        print("{} 함수수행 시간을 계산합니다.".format(func.__name__))
        start = time.time()
        func(*args, **kwargs)  
        end = time.time() - start 
        print("{} 함수수행 시간은 {} 입니다.".format(func.__name__,end))

    return my_inner_function  

@my_exec_function
@my_timer_function     # decorator 순서에 주의해야 한다. 아래쪽이 먼저 실행  
def my_adder(x,y):
    print("두 수를 더한 결과는 {}".format(x+y))    

my_adder(10,20)        # 결과가 이상합니다.!!

~~~

왜 이런 결과가 나올까요? 이번에는 decorator의 순서를 바꾸어서 실행해보도록 하겠습니다.

~~~python

import time

def my_exec_function(func): 

    def my_inner_function(*args, **kwargs):          
        print("{} 함수가 실행됩니다.".format(func.__name__))
        return func

    return my_inner_function  


def my_timer_function(func): 

    def my_inner_function(*args, **kwargs):          
        print("{} 함수수행 시간을 계산합니다.".format(func.__name__))
        start = time.time()
        func(*args, **kwargs)  
        end = time.time() - start 
        print("{} 함수수행 시간은 {} 입니다.".format(func.__name__,end))

    return my_inner_function  

@my_timer_function     # decorator 순서에 주의해야 한다. 아래쪽이 먼저 실행  
@my_exec_function
def my_adder(x,y):
    print("두 수를 더한 결과는 {}".format(x+y))    

my_adder(10,20)        # 정상수행

~~~

아직 문제가 남아있습니다. 출력된 결과를 보니 `my_adder` 함수를 수행했음에도 불구하고
내부에서 리턴되는 `my_inner_function`이라는 함수이름을 사용하고 있네요. 이 문제는
python이 제공하는 `functools` 모듈의 `wraps` decorator를 이용하면 해결이 가능합니다.

~~~python

import time
from functools import wraps

def my_exec_function(func): 

    @wraps(func)
    def my_inner_function(*args, **kwargs):          
        print("{} 함수가 실행됩니다.".format(func.__name__))
        return func

    return my_inner_function  


def my_timer_function(func): 

    def my_inner_function(*args, **kwargs):          
        print("{} 함수수행 시간을 계산합니다.".format(func.__name__))
        start = time.time()
        func(*args, **kwargs)  
        end = time.time() - start 
        print("{} 함수수행 시간은 {} 입니다.".format(func.__name__,end))

    return my_inner_function  

@my_timer_function     # decorator 순서에 주의해야 한다. 아래쪽이 먼저 실행  
@my_exec_function
def my_adder(x,y):
    print("두 수를 더한 결과는 {}".format(x+y))    

my_adder(10,20)        # 정상수행

~~~ 

decorator의 이야기는 여기까지 입니다. 형태만 보고 이거 `Java`언어의 `annotation`
이랑 비슷한건가라고 생각하셨을 텐데 결론적으로는 아니었죠. 함수를 `wrapping`하는 기술이라고
간단하게 정리할 수 도 있을 듯 합니다.
<br><br>

이론적으로 더 파고들수는 있지만 이정도만 알고있어도 decorator가 무엇이고
우리의 코드에서 어떻게, 왜 적용해야 하는가에 대한 의문은 해결될 수 있다고
생각됩니다.


End.

{% include links.html %}
