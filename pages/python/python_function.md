---
title: Python Function
sidebar: python_sidebar
summary: "Python 함수의 정의와 사용에 대해서 알아보겠습니다."
permalink: python_function.html
folder: python
---

## function

python 함수의 body에는 최소한 한 개 이상의 문장이 필요합니다. 만약 내용이 없는 함수를
만들 경우 `pass` keyword를 사용해야 합니다.
<br><br>

함수명은 가독성을 높이기 위해 필요에 따라 밑줄로 구분 된 단어와 함께 소문자를 이용하는것을 원칙으로 합니다.
(`Snake Case`)
<br><br>

Python의 함수는 내장함수와 사용자 정의 함수로 구분할 수 있습니다.

---

## 사용자 정의 함수

먼저 user define 함수를 정의하고 사용하는 몇가지 예를 살펴보도록 하겠습니다.

### 함수 정의

함수를 정의할때는 `def` 키워드를 이용해서 함수를 정의합니다. 
<br><br>

함수의 인자를 명시할 때는 당연히 데이터 타입을 명시하지 않습니다. python은 하나의
함수가 다양한 데이터 타입의 입력인자를 받아서 처리할 수 있도록 유연하게 설계되어 있습니다.

~~~python

# python user define function

def my_sum(a, b, c):
    return a + b + c

result = my_sum(1, 2, 3.0)

print("result의 값은 : {0}".format(result))

~~~

### 다수의 인자 전달

함수로 전달되는 인자의 수가 가변적일 때 tuple형태로 인자를 전달할 수 있습니다.

~~~python

def my_sum(*args):          # tuple형태로 전달
    result = 0
    for i in range(len(args)):
        result += i
    return result


print("결과값은 : {0}".format(my_sum(1, 2, 3, 4, 5, 6)))

~~~

### 여러개의 리턴값 사용

Python은 함수의 리턴값을 여러 개 사용할 수 있습니다. 정확히 말하자면,
여러개의 값을 tuple로 만들어서 리턴하는 형태로 함수의 리턴값은 사실 한 개 입니다.

~~~python

def multi_return(a, b):
    result1 = a + b
    result2 = a * b
    return result1, result2


data1 = multi_return(10, 20)
print(type(data1))
print(data1)

data1, data2 = multi_return(10, 20)
print("두수의 합 : {0}, 두수의 곱 : {1}".format(data1, data2))

~~~

### default parameter

함수의 입력 인자에 기본값을 지정할 수 있습니다. 입력 인자에 명시적인
값이 전달되지 않았을 경우 기본값으로 지정된 값을 사용한다는 의미이고
당연히 맨 마지막 인자에만 기본값을 지정할 수 있습니다.

~~~python

def default_param(a, b, c=True):
    data = a + b
    if data > 10 and c:
        return data
    else:
        return 0


result1 = default_param(10, 20)
result2 = default_param(30, 40, False)

~~~

### call-by-value & call-by-reference

함수의 입력인자가 list, dict 등인 경우 함수내에서 해당 인자의 내용을 변경하면 
원래 데이터인 list, dict의 내용이 변경됩니다. 이런 특징을 
`call-by-reference`라고 합니다.
<br><br>

반대로 숫자, 문자열, tuple 등은 함수내에서 해당 인자의 내용을 변경하여도 원래의 
데이터에는 변형이 이루어지지 않습니다. 이런 특징을 `call-by-value`이라고 합니다.
<br><br>

`mutable`과 `immutable`이라는 표현이 있는데 이는 메모리에 있는 값이 변경이 될 수 있는가
그렇지 않은가를 나타내는 용어입니다. 

~~~python

# call-by-value & call-by-reference

def my_func(tmp_number, tmp_list):
    tmp_number = tmp_number + 100
    tmp_list.append(100)

data_x = 10
data_list = [10]

my_func(data_x,data_list)

print("data_x의 값은 : {}".format(data_x))
print("data_list의 값은 : {}".format(data_list))

~~~

### local variable vs global variable

~~~python

# local variable vs global variable

tmp = 100


def my_func(x):
    global tmp     # tmp를 global scope에서 찾는다.
    tmp += x       # 만약 tmp를 global로 선언하지 않았다면
                   # local variable ( 초기화 x -> error )
                   # 즉, global scope와 local scope가 자동으로 연결되지 않는다.
                   # JavaScript같은 함수형 언어와 다른점이라고 할 수 있다.
    return tmp


print(my_func(100))
print(tmp)

~~~

---

## 내장 함수

Python은 상당히 많은 내장 함수를 제공합니다. 이를 통해 데이터 처리를
간편하게 할 수 있습니다. 간단한 연산부터 데이터 타입의 변환같은 처리를
쉽게 할 수 있습니다.
<br><br>

다음과 같은 기본적인 내장 함수는 알아 두어야 할 필요가 있습니다.
<br><br>

* `abs(x)` : 절대값을 return
* `all(x)` : 반복 가능한 자료형 x에 대해 x가 모두 참이면 True, 그렇지 않으면 False
* `any(x)` : 반복 가능한 자료형 x에 대해 x중 하나라도 참이면 True, 그렇지 않으면 False
* `eval(expression)` : 실행 가능한 문자열을 입력 받아서 문자열을 실행한 결과값을 return
* `int(x)` : 입력값을 정수형태로 return
* `len(x)` : 입력값 x의 길이를 return
* `list(x)` : 반복 가능한 자료형 x를 입력 받아 list로 만들어서 return
* `str(x)` : 문자열 형태로 변환하여 return
* `tuple(x)` : 반복 가능한 자료형을 입력받아 tuple로 바꾸어 return
* `type(x)` : 입력값의 데이터 타입을 return

~~~python

# python built-in functions

# python의 내장함수

# dir() : 사용가능한 변수와 함수를 리스트 형태로 return
print(dir({"key" : 100}))   # dictionary에서 사용가능한 변수와 함수
print(dir([]))              # list에서 사용가능한 변수와 함수

# divmod(a,b) : a를 b로 나눈 몫과 나머지를 tuple로 반환
print(divmod(8,3))

# enumerate() : 순서가 있는 자료형을 입력으로 받아
#               index값을 포함하는 enumerate 객체 return

result = enumerate(["Show","me", "the", "money"])
myList = list(result)
myList[0]

for idx,item in enumerate(["Show","me", "the", "money"]):
    print(idx, item)
    
# id() : 객체의 고유 주소값(reference) return
a = 100
print(id(a))

# join() : 일반적으로 리스트를 특정 구분자를 포함하여 
#          문자열로 변환할 때 많이 사용
animal = ["멍멍이","사자","호랑이","개"]
print(",".join(animal))
print("-".join(animal))

# map() : 특정함수에 입력값을 주어 반복 호출
def my_func(x):
    return x**2

a = list(map(my_func,range(1,10)))
print(a)

# max(), min() : 최대값, 최소값 return
print(max([7,4,9,2,3,1]))
print(max("This is a sample Text"))

# pow(x,y) : x를 y 제곱한 결과 return
print(pow(2,4))

# sorted() : 입력값을 정렬한 후 그 결과를 list로 리턴

# sorted()는 기본 내장함수로 정렬된 list를 return하는 반면
# list의 sort() 함수는 list를 정렬하고 결과를 return하지 않음
a = [4,7,9,2,3,6]
result = a.sort()
print(result)   # None
print(a)        # 정렬되어 있음

a = [4,7,9,2,3,6]
result = sorted(a)
print(result)   # 정렬된 결과
print(a)        # 원본은 변함 없음.

# zip() : 동일한 개수로 이루어진 자료형을 묶어 주는 역할
#         일반적으로 list나 tuple형태로 변환해서 사용
a = [1,2,3]
b = ["a","b","c"]
c = list(zip(a,b))

print(c)

str=list(zip("Hello","World"))
print(str)

str=list(zip("Hello1234","World"))
print(str)

~~~

---

## lambda

`lambda`(람다)는 한 줄로 함수를 정의하는 방법입니다. 단, 함수의 이름이
명시되지 않기 때문에 `anonymous function`이라고도 합니다.
<br><br>

함수명이 없기 때문에 일반적으로 람다식(lambda expression)은 변수에 할당되어서
사용됩니다. 
<br><br>

`lambda`는 입력 값을 대체 표현식으로 대체해주는 역할을 합니다. 따라서 일반적인 함수와는
약간 다릅니다.
<br><br>

사용하는 형식은 다음과 같습니다.

{% include callout.html
type="danger"
content="변수 = lambda 입력변수1, 입력변수2, ... : 대체 표현식"
%}

~~~python

# labmda

# lambda의 모든 입력인자를 다 이용하는 
# 대체식을 사용하지 않아도 된다.

f = lambda x1,x2,x3 : x1 + x2    # x3은 대체식에서 사용되지 않았다.

# 일반적인 함수의 형태로 인식하면 
# 다음과 같이 사용해야 될 것이라고 생각하기 쉽다.
# f = lambda x1,x2,x3 : return (x1 + x2)
# 하지만 표현식이 대체되는 개념이기 때문에 
# return 구문을 사용하면 안된다.

print(f(10,20,30))

~~~ 

End.

{% include links.html %}
