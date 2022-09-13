---
title: Python generator
sidebar: python_sidebar
summary: "generator의 개념과 사용에 대해 살펴보도록 하겠습니다."
permalink: python_generator.html
folder: python
---

## generator

이번에는 `generator`에 대해서 알아보겠습니다.
<br><br>

`generator`에 대한 내용을 살펴보면 다음과 같습니다.

{% include callout.html
type="danger"
content="제너레이터는 루프의 작용을 컨트롤하기 위해 쓰여지는 특별한 함수 또는 루틴이다.
사실 모든 제너레이터는 반복자이다. 한번에 모든 값을 포함한 배열을 만들어서 리턴하는
대신에 yield 구문을 이용해 한 번 호출될 때마다 하나의 값만을 리턴하고
일반 반복자에 비해 아주 작은 메모리를 필요로 한다."
%}

우리가 일반적으로 사용하는 `iterator`(반복자)와 유사한 개념입니다. 빠른 속도와 적은 메모리
사용으로 인해 더 좋은 성능을 낼 수 있습니다. 간단하게 예제를 보면서 알아보도록 하겠습니다.

~~~python

def my_func(data):

    result = []

    for tmp in data:
        result.append(tmp * 2)

    return result

twice_list = my_func([1, 2, 3, 4, 5])

print("처리된 결과는 : {}".format(twice_list))   # 일반적인 for문을 이용한 처리
                                                 # 처리된 결과는 : [2, 4, 6, 8, 10]

~~~

위의 예제를 generator를 이용하는 예제로 변경해보도록 하겠습니다.

~~~python

def my_func(data):

    for tmp in data:
        yield tmp * 2


twice_list = my_func([1, 2, 3])

print(type(twice_list))   # <class 'generator'>

print("첫번째 값 : {}".format(next(twice_list)))   # 2
print("두번째 값 : {}".format(next(twice_list)))   # 4
print("세번째 값 : {}".format(next(twice_list)))   # 6
print("네번째 값 : {}".format(next(twice_list)))   # Error 발생

~~~

마치 `my_func` 함수안의 `for` 구문이 일시정지 된 것처럼 보입니다. `next()`를 호출하는
순간 하나의 값이 return 되고 다시 `yield`에 의해서 다음 `next()`가 호출될 때 까지
일시정지 됩니다.
<br><br>

위의 코드는 다음과 같이 변경할 수 있습니다.

~~~python

def my_func(data):

    for tmp in data:
        yield tmp * 2


twice_list = my_func([1, 2, 3])

print(type(twice_list))   # <class 'generator'>

for t in twice_list:      # generator는 일반적으로 for문과 같이 사용됩니다. 
    print(t)

~~~

몇가지 generator 사용 용법을 알아보겠습니다. 가장 간단히 표현할 수 있는 방법은 `list comprehension`처럼
사용하는 것입니다.

~~~python

my_list = [tmp * 2 for tmp in [1,2,3]]        # list 생성

my_generator = (tmp * 2 for tmp in [1,2,3])   # generator 생성

print(type(my_list))
print(type(my_generator))

for k in my_list:
    print("리스트 안의 값은 : {}".format(k))

for k in my_generator:
    print("generator로 부터 발생된 값은 : {}".format(k))


convert_list = list(my_generator)
print("convert_list : {}".format(convert_list))   # 결과가 어떻게 나올까? 
                                                  # [] : 위쪽 for문에서 이미 다 소모했기때문

~~~


End.

{% include links.html %}
