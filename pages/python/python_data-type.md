---
title: Python Variable & Data Type
sidebar: python_sidebar
summary: "Python의 변수 및 데이터타입에 대해서 알아보겠습니다."
permalink: python_data-type.html
folder: python
---

## Jupyter Notebook 단축키

data type을 알아보기 이전에 `Jupyter Notebook`의 단축키부터 알아보도록 하죠. 
<br><br>

jupyter notebook을 PyCharm이 아닌 Web Application으로 사용할 때 마우스로 메뉴를 이용해도 되지만 단축키를 이용하는게
훨씬 편합니다. 아래내용은 기억해두면 사용하기 편한 단축키에 대한 설명입니다.

~~~javascript

Shift-Enter : run cell, select below
Ctrl-Enter : run cell
Alt-Enter : run cell, insert below
A : insert cell above
B : insert cell below
X : cut selected cell
C : copy selected cell
V : paste cell below
Shift-V : paste cell above
D, D : delete selected cell
Z : undo last cell deletion
L : toggle line number
O : toggle output

~~~

---

## python 주석, keyword, 변수

* 한줄 주석은 `#` 으로 처리합니다. 여러줄 주석은 `"""   """` 를 이용합니다.(혹은 `'''  '''`)

~~~python

# Python 주석

# 한줄 주석은 # 으로 처리

# 여러줄 주석은 """ """ 이용( 혹은 ''' ''' )

"""
이부분은 주석처리됩니다.!!
여러줄 주석처리할 경우 사용되며 따옴표(single quotation mark)를
사용해도 됩니다.
"""

# keyword module loading

import keyword
print(keyword.kwlist)


# 변수의 생성과 삭제

a = 100
print("생성된 a의 값은 : ", a)

del a
print("삭제된 a의 값은 : ", a)

~~~

~~~python

['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 
 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 
 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 
 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
 
생성된 a의 값은 :  100
---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
<ipython-input-1-9ac0e81add16> in <module>()
     25 print("생성된 a의 값은 : ", a)
     26 del a
---> 27 print("삭제된 a의 값은 : ", a)

NameError: name 'a' is not defined

~~~

---

## python built-in types

Python의 built-in data type은 다음과 같습니다.
<br><br>

* `Numeric`
* `Sequence`
* `Text Sequence`
* `Mapping`
* `Set`
* `Bool`
<br><br>

각각의 데이터 타입에 대해서 하나하나 알아보도록 하겠습니다.

---

## Numeric

~~~python

# python numeric type

# Numeric Type (숫자형)
# int (정수)
# float (실수)
# complex (복소수)

a = 123           # 정수
b = 3.141592      # 실수 
c = 3.14E10       # 실수 (지수형태)
d = 1 + 2j        # 복소수
e = 0o34          # 8진수
f = 0xAB          # 16진수

print(type(a))    # <class 'int'>
print(type(b))    # <class 'float'>
print(type(c))    # <class 'float'>
print(type(d))    # <class 'complex'>
print(type(e))    # <class 'int'>
print(type(f))    # <class 'int'>

div = 3 / 4       # 나누셈의 결과는?
print(div)        # 0.75

result = 3 ** 3   # 지수표현
print(result)     # 27

result = 10 % 3   # 나머지 연산
print(result)     # 1

result = 10 // 3  # 나눗셈 몫
print(result)     # 3

~~~

---

## Sequence

### list

Python의 `Sequence Type - list`에 대해서 코드로 알아보겠습니다.

~~~python

# python sequence type - list

# list
# 임의의 객체를 순서대로 저장하는 집합 자료형
# Java의 ArrayList와 유사 ( index 부여 및 값 변경 가능 )
# 대괄호 ( [ ] )를 이용하여 표시
# range()를 이용하여 리스트 생성 가능
a = list()
a = []
a = [1,2,3]
a = [1,2,"Hello",3.14]
a = [1,2,["Show","me", "the", "money!!"],3.14]

print(a)          # [1, 2, ['Show', 'me', 'the', 'money!!'], 3.14]
print(a[0]);      # 1
print(a[-1])      # 3.14
print(a[2][0])    # Show
print(a[0:2])     # [1, 2]
print(a[2][2:])   # ['the', 'money!!']

a = [1,2,3]
b = [4,5,6]
print(a + b)      # [1, 2, 3, 4, 5, 6] 

a = [1,2,3]
print( a * 3 )    # [1, 2, 3, 1, 2, 3, 1, 2, 3]
print(str(a[0]) + "Hello") # a[0] + "Hello" => Error

a = [1,2,3]
a[0] = 5          # [5, 2, 3]
a[0:1] = [7,8,9]  # [7, 8, 9, 2, 3]
a[1] = ["Hello", "World"]  # [7, ['Hello', 'World'], 9, 2, 3]
a[1:3] = []   # [7, 2, 3]
del a[0]      # [2, 3]
print(a)

a = [1,2,3]
a.append(4)          # [1, 2, 3, 4]
a.append([5,6,7])    # [1, 2, 3, 4, [5, 6, 7]]

#a = [7,3,1,8,2]
#a = ["Hello","World","kaka","hoho"]
a = ["홍길동","최길동","이순신","강감찬","박효신","아이유"]
result = a.sort()   
print(result)       # None
print(a)            # ['강감찬', '박효신', '아이유', '이순신', '최길동', '홍길동'] 
result = a.reverse()  
print(result)       # None
print(a)            # ['홍길동', '최길동', '이순신', '아이유', '박효신', '강감찬']


a = [7,3,1,8,2]
print(a.index(1))   # 1의 위치 반환 (2)

a = [1,2,3]
a.insert(0,-1)
print(a)            # [-1, 1, 2, 3]

a = [1,2,3,1,2,1,2,3]
a.remove(3)   # 첫번째로 나오는 3 제거 => [1, 2, 1, 2, 1, 2, 3]

a = [1,2,3,4]
print(a.pop())   # 마지막 index위치의 값을 pop
                 # 4
print(a)         # [1, 2, 3]

a = [1,2,3,4]
print(a.pop(1))  # index위치의 값을 pop
                 # 2
print(a)         # [1, 3, 4]

a = [1,2,3,1,2,1,2,3]
print(a.count(1))    # 1의 개수 counting => 3

a = [1,2,3]
a.extend([4,5,6])
print(a)            # [1, 2, 3, 4, 5, 6]

############################################
# is 연산자와 == 연산자
# is 연산자는 reference를 비교하는 연산자이고
# == 연산자는 value를 비교하는 연산자
# 변수의 reference(메모리 주소에 대한 int값)를 얻기위한
# 함수 => id()

a = 100;      
print(id(a))    # 1759493440
b = 100;
print(id(b))    # 1759493440

c = 256         
print(id(c))    # 1759498432
d = 256
print(id(d))    # 1759498432

e = 257         # 257 이상이면 다른 주소에 할당
                # console에서는 정상실행
                # pyCharm에서는 같은 주소가 할당(왜?)
                
print(id(e))    # 1134539404368
f = 257
print(id(f))    # 1134539405008


# 다른 데이터 타입은 매번 실행 시 다른 메모리 할당
list_1 = [1,2,3]
list_2 = [1,2,3]
print(id(list_1), id(list_2))    # 1134539686024 1134540817352

# 두 개의 list 비교
a = [1,2,3]
b = a;

print(a is b) # True
              # a와 b가 같은 객체 reference

b = a[:]      # 리스트 전체 복사의 의미
print(a is b) # False
              # a와 b가 서로 다른 객체 reference

from copy import copy

b = copy(a)  # 리스트 복사

print(a is b)  # False

~~~

### tuple

Python의 `Sequence Type - tuple`에 대해서 코드로 알아보겠습니다.

~~~python

# python sequence type - tuple

a = ()
b = (1,)     # 요소가 1개인 Tuple 표현
             # type(b) => <class 'tuple'>
c = (1)      # type(c) => <class 'int'>
d = (1,2,3)  # 일반적인 형태
e = 1,2,3    # () 생략가능
a,b,c = (10,20,30) # tuple의 각 값을 변수에 대입

f = (1,2,(3,4),5,(6,7,8))

print(f[0])    # indexing
print(f[1:3])  # slicing => (2, (3, 4))

a = (1,2,3)
b = (4,5,6)
print(a + b)   # (1, 2, 3, 4, 5, 6)

a = (1,2,3)
print(a * 3)   # (1, 2, 3, 1, 2, 3, 1, 2, 3)

# tuple과 list의 변환

my_list = [1,2,3]
my_tuple = tuple(my_list)

print(my_tuple)   # (1, 2, 3)

my_tuple = 10,20,30
my_list = list(my_tuple)

print(my_list)   # [10, 20, 30]

~~~

### range

Python의 `Sequence Type - range`에 대해서 코드로 알아보겠습니다.

~~~python

# python sequence type - range

# range는 숫자 sequence로 주로 for문에서 사용
# range의 장점은 표현하는 범위에 무관하게 항상
# 같은(작은)양의 메모리를 사용

range_1 = range(10)
print(range_1)    # range(0, 10)

range_2 = range(1,11,2)
print(range_2)    # range(1, 11, 2)

print(7 in range_2)    # True
print(10 in range_2)   # False

print(range_2.index(7)) # 7이 처음나오는 index  => 3
print(range_2[2])       # 5
print(range_2[2:])      # range(5, 11, 2)
print(range_2[-1])      # 9

~~~

---

## Text Sequence Type

Python의 `Text Sequence Type - str`에 대해서 코드로 알아보겠습니다.

~~~python

# python text sequence type - str

# 문자열 생성방법 (4가지)

a = "Hello"

b = 'python'

c = """this is a
multiline
sample text"""

d = '''이것은
소리없는
아우성'''

print(d)

# 문자열 연산 , Indexing, Slicing

first = "this is a"
middle = ' sample'
last = """ text"""
print(first + middle + last)  # this is a sample text

text = "Python"
print(text * 3)  # PythonPythonPython

a = "Talk is cheap. Show me the code"
print(a[3]);  # k
print(a[-1])  # e
print(a[0:4]);  # Talk
print(a[:20])  # Talk is cheap. Show
print(a[5:]);  # is cheap. Show me the code
print(a[:])  # Talk is cheap. Show me the code

# in, not in 연산

myStr = "This is a sample Text"

print("sample" in myStr)  # True
print("text" not in myStr)  # True
print("this" in myStr)  # False
print("this" in myStr.lower())  # True

# 문자열 formatting

a = "I have %d apples." % 3
b = "I have %s apples." % "five"

num = 10
c = "I have %d apples." % num

d = "I have %d apples and %s bananas" % (3, "three")

e = "Error Rate is %d%%." % 80

f = "I have %10s apples." % "five"
g = "I have %-10s apples." % "five"

h = "%0.4f" % 3.141592
i = "%10.4f" % 3.141592  # 전체 자리수 10자리,
# 오른쪽 정렬,
# 소수점 이하 4자리(반올림)

# 문자열 함수
a = "cocacola"

result = len(a)  # 문자열 길이 => 8
result = a.count("c")  # 문자 개수 count => 3
result = a.find("o")  # 문자가 처음나온 위치, 없으면 -1 => 1
result = a.index("a")  # 문자가 처음나온 위치, 없으면 Error => 3

a = "::"
b = "abcd"
result = a.join(b)
print(result)  # a::b::c::d

a = " hoBBy "
result = a.upper()
result = a.lower()
result = a.strip()
print(result)

a = "Show me the Code"
result = a.replace("Code", "Money")
print(result)        # Show me the Money

a = "Show me the Code"
result = a.split()
print(result)  # ['Show', 'me', 'the', 'Code']

a = "a:b:c:d:e"
result = a.split(":")
print(result)  # ['a', 'b', 'c', 'd', 'e']

a = "I have {0} apples".format(3)
a = "I have {0} apples and {1} bananas".format(3, "three")
a = "I have {num1} apples and {num2} bananas".format(num1=5, num2="two")
a = "{0:0.4f}".format(3.141592)
print(a)  # 3.1416

# 천 단위 comma 출력
num = 1234567899
print("{0:,}".format(num))

# 백분율 출력 ( 반올림 처리 )
num = 0.87657
print("{0:.0%}".format(num))
print("{0:.2%}".format(num))            
            
~~~

---

## Mapping Type

Python의 `Mapping Type - dict`에 대해서 코드로 알아보겠습니다.

~~~python

# python mapping type - dict

# dictionary는 key와 value의 대응관계 type
# Associative array 혹은 Hash와 유사한 구조
# {} 기호로 표현

a = { "name" : "홍길동", "age" : 30 }
print(type(a))   # <class 'dict'>

a[10] = "KK"
a["Hobby"] = "Game"
print(a)   # {'name': '홍길동', 'age': 30, 10: 'KK', 'Hobby': 'Game'}
del a[10]  # key값이 10인 쌍을 삭제
print(a)   # {'name': '홍길동', 'age': 30, 'Hobby': 'Game'}
print(a["name"])   # 홍길동

a = { "name" : "홍길동", "age" : 30, "age" : 40 }
# key가 중복되면 1개를 제외한 나머지 무시.
# key에 list는 올 수 없지만 tuple은 올 수 있다.
a = { "name" : "홍길동", "age" : 30, ("address",) : "서울" }
print(a[('address',)])     # 서울

a = { "name" : "홍길동", "age" : 30, "address" : "서울" }
print(a.keys())   # a.keys() => dict_keys 객체 리턴
                  # dict_keys(['name', 'age', 'address'])
                  # list와 유사하지만 list 함수 사용 불가
                  # list로 변환 => list(a.keys()) 
print(a.values())   # a.values() => dict_values 객체 리턴
                    # dict_values(['홍길동', 30, '서울'])
print(a.items())    # key와 value의 쌍을 tuple로 묶은 
                    # dict_items 객체 리턴
                    # dict_items([('name', '홍길동'), ('age', 30), ('address', '서울')])

for key in a.keys():
    #print(key + ", " + a[key]) => Type Error
    print("{0}, {1}".format(key,a[key]))

print(a.get("address"))          # 만약 찾지 못하면 None 리턴
                                 # 서울

print(a.get("address!!","지역")) # 만약 찾지 못하면 default 리턴

print("name" in a)  # key가 딕셔너리에 있는지 조사
                    # True

a.clear()               # Key:Value쌍 모두 삭제
print(a)                # {}

~~~

---

## Set Type

Python의 `Set Type - set`에 대해서 코드로 알아보겠습니다.

~~~python

# python Set Type - set

# Set 특징
# 중복이 없고, 순서가 없다.

a = {1,2,3}              # set 생성
a = set([1,2,3,1,2,3])   # set 생성 => {1, 2, 3}
print(type(a))           # <class 'set'>
print(a)                 # {1, 2, 3}

a = set("Hello")
print(a)           # {'o', 'e', 'l', 'H'}

s1 = set([1,2,3,4,5])
s2 = set([3,4,5,6,7])

print(s1 & s2)             # intersection
print(s1.intersection(s2)) # {3, 4, 5}

print(s1 | s2)             # union
print(s1.union(s2))        # {1, 2, 3, 4, 5, 6, 7} 

print(s1 - s2)             # difference
print(s1.difference(s2))   # {1, 2}

s1 = set([1,2,3,4,5])
s1.add(7)                  # set에 요소 추가
print(s1)                  # {1, 2, 3, 4, 5, 7}
s1.update([1,2,9,6,8])     # set에 값 여러개 추가(list이용)
print(s1)                  # {1, 2, 3, 4, 5, 6, 7, 8, 9}
s1.update({10,11,12})      # set에 값 여러개 추가(set이용)
print(s1)                  # {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}
s1.remove(1)               # set에서 특정값 삭제
print(s1)                  # {2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}

~~~

---

## Bool Type

Python의 `Bool Type - bool`은 다음과 같은 특징을 가집니다.
<br><br>

* True , False
* `not`, `and`, `or` 연산자 사용
* python에서 `and`, `or` ,`not`은 논리 연산(True,False 연산)
* python에서 `&`, `|`, `~`은 비교 연산(bitwise 연산)
<br><br>

다음의 경우 False로 간주합니다.
<br><br>

* 빈 문자열 (`""`)
* 빈 리스트 ( `[]` )
* 빈 튜플 ( `()` )
* 빈 딕셔너리 ( `{}` )
* 숫자 `0` ( 0 이외의 다른 숫자는 True )
* `None`

~~~python

# python Bool Type - bool

a = 5
b = 0

print(a & b)       # bitwise 연산 0101 & 0000 => 0000  => 0
print(bool(a & b)) # 0은 bool로 전환시 False           => False

print(a | b)       # bitwise 연산 0101 | 0000 => 0101  => 5
print(bool(a | b)) # 0이외의 숫자는 bool로 전환시 True  => True

a = True
b = False

print(int(a))    # True는 정수 전환시 1
print(int(b))    # False는 정수 전환시 0

print(a & b)     # False
print(a | b)     # True

print(a and b)   # False
print(a or b)    # True
print(not a)     # False

~~~

---

## date, timedelta

~~~python

# python date type

# 날짜
from datetime import date, datetime

today = date.today()
print(type(today))      # <class 'datetime.date'>
print(today)

print("연도 : {0}, 월 : {1}, 일 : {2}".format(today.year,today.month, today.day))

# 날짜 및 시간
my_datetime = datetime.today()
print(my_datetime)
print("현재 시 : {0}".format(my_datetime.hour))
print("현재 분 : {0}".format(my_datetime.minute))
print("현재 초 : {0}".format(my_datetime.second))
print("현재 마이크로초 : {0}".format(my_datetime.microsecond))


# pip install python-dateutil

from datetime import date, datetime, timedelta
from dateutil.relativedelta import relativedelta

today = date.today()
days = timedelta(days=-1)   # 일을 기준으로 하루 전
print("하루 전 날짜 : {}".format(today + days))
# weeks, days, hours, minutes, seconds 사용가능
# years와 months는 timedelta로 사용할 수 없다.
# days = timedelta(years=-2)   # 월을 기준으로 2개월 전 ( error )

# years와 months는 relativedelta 사용 ( 2019-03-31의 1달전은 2019-02-28 )
days = relativedelta(months=-2)
print("두달 전 날짜 : {}".format(today + days))

## 특정 날짜 객체를 생성
from dateutil.parser import parse
myDay = parse("2019-01-01")
print("parse()로 얻어온 날짜 : {}".format(myDay))
myDay = datetime(2019,1,1)
print("datetime()로 얻어온 날짜 : {}".format(myDay))


# 날짜 출력 형식
from datetime import datetime

today = datetime.today()

# strftime( 날짜를 문자열형태로 포맷 지정 )
print("{0}".format(today.strftime("%m-%d-%y"))) # year : 19
print("{0}".format(today.strftime("%m-%d-%Y"))) # year : 2019

# 문자열을 날짜 형식으로 변환 ( strptime )
str = "2019,01,03-13:30:29"
my_date = datetime.strptime(str, "%Y,%m,%d-%H:%M:%S")

print(type(my_date))
print(my_date)

~~~

---

## python console 입출력

~~~python
# python console IO

# console입력을 받기 위해서는 input()을 이용

input_val = input("숫자를 입력하세요!!")

print(type(input_val))  # input으로 입력받은 값은 str

# eval() : 입력받은 문자열을 숫자연산처리
print(eval("1+3*5"))   # 16

print(type(eval(input_val)))
~~~

---

## python print() 함수

~~~python

# python print function

# print() 함수

print("Hello" + "World") # + 연산자는 문자열 연결
print("Hello" "World")   # + 연산자가 없어도 문자열 연결
print("Hello","World")   # , 를 이용하면 공백추가하여 문자열 연결

# 기본적으로 print()는 한줄 출력한 후 line feed (줄바꿈)
# end="" 를 이용하여 출력 후 처리를 지정할 수 있다.

for tmp in range(1,10):
    print(tmp, end=" ")  # 개행 => 공백으로 변환
    
~~~


End.

{% include links.html %}
