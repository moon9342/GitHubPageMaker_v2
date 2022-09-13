---
title: Python first class citizen
sidebar: python_sidebar
summary: "first-class citizen 이라는 개념에 대해서 알아보겠습니다."
permalink: python_first-class.html
folder: python
---

## first class

`first-class citizen` 이라는 개념에 대해서 알아보겠습니다.
프로그램 개체가 다음의 특성을 만족할 때 first-class citizen 이라고 정의됩니다.
<br><br>

* 변수나 데이터 구조의 속성으로 저장될 수 있다
* 함수의 인자로 전달될 수 있다.
* 함수의 결과로 리턴될 수 있다.
<br><br>

아주 단순한 예로 우리가 프로그래밍에서 사용하는 숫자 타입의 데이터는
first-class citizen 입니다.
<br><br>

앞서 객체지향에 대한 이야기도 했었는데 위의 특성을 만족하는 객체를 우리는 1급 객체라고도
표현합니다.
<br><br>

우리의 python으로 돌아와서 다시 생각해보도록 하죠.
<br><br>

python의 함수는 first-class 특성을 가지는 객체입니다. 이런 함수를 `first-class function`이라고
하는데 first-class function은 다음의 두가지 특성을 추가로 가집니다.
<br><br>

* `runtime`으로 생성이 가능하다.
* 익명으로 생성이 가능하다.
<br><br>

그러면 이런 first-class function의 이점은 무엇일까요? 단적으로 얘기하면 프로그램의 유연성이
대폭 높아져서 효율적인 프로그래밍이 가능하게 합니다.
<br><br>

first-class function이란 용어를 처음 접하는 분들은 솔직히 아직 감이 잘 안 올텐데
예제를 이용해서 하나씩 살펴보도록 하겠습니다.

### 변수에 함수를 저장

~~~python

def my_add(x,y):
    return x + y

print("함수 호출 결과 : {}".format(my_add(10,20)))

print(my_add)        # my_add 라는 변수에 함수의 실행코드가 있는 메모리 시작 주소가 저장

f = my_add           # 다른 변수에 함수의 시작 주소를 저장(변수에 함수를 저장)

print(f)            

print(f(20,30))      # 당연히 함수 호출이 가능

~~~

Python은 first-class function을 지원하기 때문에 위의 예에서 본 것처럼 함수를
변수에 저장(할당)할 수 있습니다.

### 함수를 다른 함수의 인자로 전달

~~~python

def my_add(x,y):
    return x + y

def my_operation(func, arg_list):

    result = []

    for (tmp1,tmp2) in arg_list:
        result.append(func(tmp1,tmp2))

    return result
    
    
data = [(1,2), (3,4), (5,6)]

my_result = my_operation(my_add,data)

print("함수 호출 결과 : {}".format(my_result))

~~~

위의 예처럼 하나의 함수를 다른 함수의 인자로 전달할 수 있습니다. 오히려 더 복잡한거 아니냐? 라는
생각이 들 수 있지만 아래의 예처럼 여러개의 함수를 만들어 놓고 내가 사용할 함수와 인자를 같이
전달하는 방식을 이용하면 함수 자체의 기능을 수정하지 않고 여러 기능을 이용할 수 있다는 편리성이
생기게 됩니다.

~~~python

def my_add(x,y):
    return x + y

def my_minus(x,y):
    return x - y

def my_mul(x,y):
    return x * y


def my_operation(func, arg_list):

    result = []

    for (tmp1,tmp2) in arg_list:
        result.append(func(tmp1,tmp2))

    return result
    
    
data = [(1,2), (3,4), (5,6)]

my_result1 = my_operation(my_add,data)   # 합
my_result2 = my_operation(my_minus,data) # 차
my_result3 = my_operation(my_mul,data)   # 곱

print("함수 호출 결과 : {}".format(my_result1))
print("함수 호출 결과 : {}".format(my_result2))
print("함수 호출 결과 : {}".format(my_result3))

~~~

### 함수의 리턴값으로 다른 함수를 사용

~~~python

def addMaker(x):

    def my_add_maker(y):
        return x + y

    return my_add_maker


add_5 = addMaker(5);
add_10 = addMaker(10);

print("결과값은 : {}".format(add_5(1)))
print("결과값은 : {}".format(add_5(5)))
print("결과값은 : {}".format(add_10(20)))

~~~

위의 예제는 함수를 결과값으로 리턴하는 함수에 대한 예제입니다. 이 예제를 살펴보면
무언가 이상한 점을 발견할 수 있습니다. 로컬 변수의 `scope`가 유지되고 있다는 점이 이상하게
보입니다. 이 부분은 `Closure`(클로져)에서 다시 얘기하기로 하죠.
<br><br>

사실 first-class function의 개념은 그다지 어려운 개념은 아닙니다. 단, 이렇게 유연한
프로그래밍 기법을 어디서 어떻게 활용해야 생산성, 효율성을 높일 수 있는가 하는게 문제인 듯 합니다.
<br><br>

이런 기법들을 이용해서 기존의 코드보다 더 효율적인 코드를
만들어 낼 수 있다는걸 인식하고 어떻게 하면 활용할 수 있을까를 계속 고민하는 것도
좋은 학습방법이지 않을까 합니다.


End.

{% include links.html %}
