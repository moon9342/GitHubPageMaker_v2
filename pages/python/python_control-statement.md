---
title: Python Control Statement
sidebar: python_sidebar
summary: "Python의 기본 제어문에 대해서 알아보겠습니다."
permalink: python_control-statement.html
folder: python
---

## if

Python의 조건문은 두가지 방식으로 사용이 가능합니다. 첫번째는 전통적인
`if ~ elif ~ else` 형태이고 다른 하나는 `in` 구문을 사용하는 형태입니다.
<br><br>

또한 코드 block을 표현하기 위해서 다른 언어처럼 `{ }`를 이용하지 않고
들여쓰기(indentation)를 이용합니다.
<br><br>

다음의 예제를 통해서 알아보도록 하겠습니다.

~~~python

# python control statement if

a = 20

if a % 3 == 0:
    print("{}은(는) 3의 배수입니다.".format(a))
elif a % 5 == 0:
    print("{}은(는) 5의 배수입니다.".format(a))
else:
    print("{}은(는) 3과 5의 배수가 아닙니다.".format(a))  
    
~~~

아래의 코드는 `in` 연산자를 이용하는 경우입니다.

~~~python

# python control statement if

area = ["서울", "부산", "제주"]
region = "수원"

if region in area:
    pass
else:
    print("{} 지역은 포함되지 않습니다.".format(region))   


mydict = { "서울" : 100, "광주" : 200 }
region = "부산"

if region in mydict:
    print("키 값이 dict안에 있습니다.")   
else:
    print("키 값이 dict안에 없습니다.")   
 
~~~

---

## for

Python의 `for`구문은 두가지 방식으로 사용이 가능합니다.
<br><br>

`for ~ in range()` 형태와 `for ~ in dict,list` 형태입니다.
<br><br>

~~~python

# python control statement for

mySum = 0

for tmp in range(1,10,2):
    mySum += tmp
    print("현재 tmp값은 {}이고 현재 누적값은 {}입니다.".format(tmp,mySum))

print("총 누적값은 {}입니다.".format(mySum))
    
~~~

아래의 예제는 `for ~ in dict,list` 형태입니다.

~~~python

# python control statement for

myList = [1, 2, 3, 4, 5]
mySum = 0

for tmp in myList:
    mySum += tmp

print("리스트의 합은 : {0}".format(mySum))

####################################

myList = [(1, 2), (3, 4), (5, 6)]
mySum = 0

for (tmp1, tmp2) in myList:
    mySum += (tmp1 + tmp2)

print("리스트의 합은 : {0}".format(mySum))

####################################

myDict = { "홍길동" : 24, "신사임당" : 21 }

for (key,value) in myDict.items():
    print("Key : {} , Value : {}".format(key,value))

####################################


score = [100, 50, 80, 90, 70, 60]
mySum = 0

for i in range(len(score)):
    mySum += score[i]

print("score의 합은 %d" % mySum)

~~~

추가적으로,
<br><br>

리스트의 `[...]` 괄호 안에 for 구문을 통한 반복적인 표현식을 이용해서
리스트의 요소들을 정의할 수 있는데 이를 `List Comprehension`이라고 합니다.
<br><br>

아래의 예제처럼 사용됩니다.

~~~python

# List Comprehension

myList = [1, 2, 3, 4, 5, 6, 7, 8, 9]

score1 = [tmp * 2 for tmp in myList]
score2 = [tmp * 2 for tmp in myList if tmp % 2 == 0]

print("score1 리스트 : {0}".format(score1))
print("score2 리스트 : {0}".format(score2))

# dictionay에도 사용가능
my_dict = {"stu" + str(x): x ** 2 for x in range(1, 10)}
print("my_dict : {0}".format(my_dict))

~~~

---

## while

Python의 `while` 구문은 다른 언어의 while 사용과 유사합니다.
<br><br>

단, Python은 `do~while` 구문이 존재하지 않는다는 점만 기억하시면 될 듯 합니다.

~~~python

# python control statement while

idx = 0
mySum = 0

while idx < 10:
    mySum += idx
    idx += 1

print("0~9까지의 합은 : {}".format(mySum))

~~~


End.

{% include links.html %}
