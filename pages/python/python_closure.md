---
title: Python closure
sidebar: python_sidebar
summary: "closure의 개념과 사용에 대해 살펴보도록 하겠습니다."
permalink: python_closure.html
folder: python
---

## closure

다음은 `Closure`에 대한 위키피디아의 설명입니다.

{% include callout.html
type="danger"
content="컴퓨터 언어에서 클로저(Closure)는 일급 객체 함수(first-class functions)의 개념을
이용하여 스코프(scope)에 묶인 변수를 바인딩 하기 위한 일종의 기술이다.
기능상으로, 클로저는 함수를 저장한 레코드(record)이며, 스코프(scope)의 인수(Factor)들은
클로저가 만들어질 때 정의(define)되며, 스코프 내의 영역이 소멸(remove)되었어도
그에 대한 접근(access)은 독립된 복사본인 클로저를 통해 이루어질 수 있다."
%}

기존에 프로그래밍을 하신 분들이라면 위의 내용을 이해할 수 있겠지만 그렇지 않은 분들은
이게 무슨 소리야?? 라는게 정상적인 반응입니다.
<br><br>

이전에 first-class function에서 언급했듯이
`Closure`는 함수가 다른 함수를 리턴값으로 사용할 때 이용되는 기술입니다.
<br><br>

아주 간단한 예제를 통해서 알아보도록 하겠습니다.

~~~python

def my_outer_function(x):

    tmp = x     

    def my_inner_function():

        print(tmp *10)     # tmp : free variable

    return my_inner_function()


my_outer_function(10)     # 100

~~~

위의 코드는 이해하기 어렵지 않습니다.  `my_outer_function` 함수는 자신이 가지고 있는
내부 함수 `my_inner_function`를 실행해 그 결과값을 리턴하는 구조입니다.
<br><br>

다음의 코드는 위의 코드를 살짝 수정한 것입니다.

~~~python
def my_outer_function(x):

    tmp = x     

    def my_inner_function():

        print(tmp *10)     # tmp : free variable

    return my_inner_function     # 리턴값이 바뀌었다.


my_outer_function(10)     
~~~

`my_outer_function` 함수는 자신이 정의한 `my_inner_function` 함수 자체를 리턴합니다.
리턴값이 함수이기 때문에 다음과 같이 수행시키는 것이 가능할 것입니다.

~~~python

def my_outer_function(x):

    tmp = x     

    def my_inner_function():

        print(tmp *10)     # tmp : free variable

    return my_inner_function     # 리턴값이 바뀌었다.


my_outer_function(10)()     # 함수자체가 리턴되기 때문에 실행시키기 위해서 () 추가

~~~

자 여기까지 이해했다고 해도 한가지 더 이상한 점이 남습니다. 바로 `tmp`라는 지역변수의
값이 어떻게 유지되고 있냐는 것입니다.
<br><br>

우리가 알다시피 지역변수는 함수가 호출될 때 스택영역에 생성되게 되고 함수가 끝나면
해당 함수의 스택영역이 `POP`되기 때문에 그 안에 생성된 지역변수들은 당연히 사용할 수 없게
됩니다. 이게 우리가 알고있는 지역변수의 `scope` 입니다.
<br><br>

위의 예제를 다시 보면 tmp는 지역변수인데 함수의 호출이 끝나 내부의 함수가
리턴된 후 그 내부함수를 실행(호출)했을 때 tmp의 값을 참조할 수 있습니다.
<br><br>

이게 어떻게 가능할까? 라는 질문의 답이 바로 `closure` 입니다.
<br><br>

이를 알아보기 위해서 코드를 약간 수정해서 다시 살펴보도록 하겠습니다.

~~~python

def my_outer_function(x):

    tmp = x     

    def my_inner_function():

        print(tmp *10)     # tmp : free variable

    return my_inner_function     # 리턴값이 바뀌었다.


my_func = my_outer_function(10)

# <function my_outer_function.<locals>.my_inner_function at 0x7f94ddb43ea0>
print(my_func)                              

# 함수가 가지고 있는 속성
print(dir(my_func))                         

print(type(my_func.__closure__))      # <class 'tuple'>      

# 첫번째 인자의 type => class cell
print(type(my_func.__closure__[0]))         

# class cell의 속성과 함수
print(dir(my_func.__closure__[0]))          

# cell_contents 속성안에 tmp값을 저장해놓음.
print(my_func.__closure__[0].cell_contents) 

~~~ 

위의 코드에서 주석으로 달아놓은 것처럼 리턴되는 함수는 내부에 자료구조를 생성하여
`free variable`의 값을 저장해 놓고 있는 것을 알 수 있습니다. 여기서 `free variable`이란
코드블럭안에서 사용은 되었지만 그 코드블럭안에서 정의되지 않은 변수를 의미합니다. 즉,
우리 예제에서는 tmp가 free variable입니다.
<br><br>

자 이제 맨 처음 위키피디아의 정의를 다시 한번 읽어보도록 하죠. 아까보다는 약간 이해가
가지 않나요??


End.

{% include links.html %}
