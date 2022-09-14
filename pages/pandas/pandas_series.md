---
title: Pandas Series
sidebar: pandas_sidebar
summary: "Pandas의 자료구조인 Series에 대해서 살펴보도록 하겠습니다."
permalink: pandas_series.html
folder: pandas
---

## Series 생성

`Series`는 데이터가 순차적으로 나열된 1차원 배열의 형태를 가집니다. 우리가 앞서
배웠던 1차원 `ndarray`와 같은 형태라고 보시면 됩니다. 형태는 그렇지만 ndarray와
결정적으로 다른 점이 있는데 바로 index와 value가 1:1로 대응이 되는 python의
dictionary 형태로 구성된다는 것입니다.
<br><br>

아래의 그림을 보도록 하죠.

{% include image.html
file='pandas-series-architecture1.png'
%}

위의 그림에서 알 수 있듯이 dictionary와 Series의 구조가 비슷하기 때문에 Series를
생성할 때 dictionary를 많이 이용합니다. dictionary뿐 아니라
python list(혹은  Numpy ndarray)를 이용해서 Series를 생성하는 방법도 많이
이용됩니다.
<br><br>

아래의 그림처럼 dictionary가 Series로 변환됩니다.

{% include image.html
file='pandas-dictionary-to-series.png'
%}

코드를 통해서 실제 Seires를 생성하고 그 성질을 파악해 보도록 하죠.
먼저 python list를 이용한 Series 생성에 대한 코드를 보도록 하죠.

~~~python

# Series 생성(python list이용)
# numpy array와 비교해서 확인

import numpy as np
import pandas as pd

# ndarray (dtype=np.float64)
arr = np.array([-1,5,10,99], dtype=np.float64)
print(arr)
print(arr.dtype)

# ndarray (dtype=np.object)
arr = np.array([-1,5,3.14,"hello"], dtype=np.object)
print(arr)
print(arr.dtype)

###########################

# list를 이용한 Series 생성
s = pd.Series([-1,5,10,99], dtype="float64") # dtype=np.float64
print(s)         # Series 출력(index와 value를 같이 출력)

print("Series의 value : {}".format(s.values))  # [-1.  5. 10. 99.]
print("Series의 value type : {}".format(type(s.values))) # <class 'numpy.ndarray'>
print("Series의 value data type : {}".format(s.dtype)) # float64   

print("Series의 index data : {}".format(s.index))  # RangeIndex(start=0, stop=4, step=1)
print("Series의 index type : {}".format(type(s.index))) # <class 'pandas.core.indexes.range.RangeIndex'>

~~~

---

## Series index 지정

~~~python

# Series 생성 시 index를 지정 가능
# 숫자인덱스는 기본으로 사용가능

import numpy as np
import pandas as pd

s = pd.Series([1,5,-8,10],
              dtype=np.int32,
              index=['c','b','a','k'])
print(s)

print("s[1] : {}".format(s[1]))       # 5
print("s['c'] : {}".format(s['c']))   # 1
print("type(s['c']) : {}".format(type(s['c'])))   # <class 'numpy.int32'>

############################################

s = pd.Series([1,5,-8,10],
              dtype=np.int32,
              index=[0,2,100,6])
print(s)
print(s[0])   # 1 
# print(s[1])   # Key Error
print(s[100])   # -8

############################################

s = pd.Series([1,5,-8,10],
              dtype=np.int32,
              index=['c','b','c','k'])   # index가 같아도 되는가? (된다.)             
print(s)

print("s['c'] : {}".format(s['c']))    # c 1
                                       # c -8
# 만약 같은 index가 존재하고 해당 index로 값을 조회하면
# Series 형태로 return

print("type(s['c']) : {}".format(type(s['c'])))  # <class 'pandas.core.series.Series'>


############################################

s = pd.Series([1,5,-8,10],
              dtype=np.int32,
              index=['c','b','a','k'])
print(s)

# Slicing
print("s[0:3]\n{}".format(s[0:3])) # Series
print("s['c':'k']\n{}".format(s['c':'k'])) # Series
# s['c':'k'] 으로 slicing할 경우 'c'부터 'k'(포함)까지 slicing

# Fancy Indexing & Boolean Indexing
print("s[[0,2]]\n{}".format(s[[0,2]])) # Series(Fancy Indexing이용)
print("s[s%2==0]\n{}".format(s[s%2==0])) # Series(Boolean Indexing이용)


result = 0;
for x in range(len(s)):
    result += s[x]

print("for문을 이용한 합계 : {}".format(result)) 
print("sum() 함수를 이용한 합계 : {}".format(s.sum()))

~~~

---

## Dictionary로 Series 생성

~~~python

# Dictionary를 이용하여 Series 생성
# Dictionary의 key가 index로 사용된다.

# Series 자체의 이름과 index의 이름을 지정할 수 있다.
# Python의 list 혹은 numpy array를 이용해서 index 변경

import numpy as np
import pandas as pd

my_dict = { "서울" : 1000, "부산" : 2000, "인천": 3000 }

s = pd.Series(my_dict)

s.name = "지역별 가격데이터"
s.index.name = "지역명"

print(s)

s.index = ["SEOUL", "BUSAN", "INCHON"]
s.index.name = "REGION"
print(s)

~~~

---

## Series 연습

~~~python

# A 공장의 2019-01-01부터 10일간 생산량을 Series로 저장
# 생산량은 평균이 50이고 표준편차가 5인 정규분포에서 random하게 생성(정수)
# 형식) 2019-01-01 52

# B 공장의 2019-01-01부터 10일간 생산량을 Series로 저장
# 생산량은 평균이 70이고 표준편차가 8인 정규분포에서 random하게 생성(정수)
# 날짜별로 모든 공장의 생산량 합계를 구하세요!

import numpy as np
import pandas as pd
from datetime import date, datetime, timedelta
from dateutil.parser import parse

start_day = datetime(2019,1,1)
factory_A = pd.Series([int(x) for x in np.random.normal(50,5,(10,))],
                     index=[start_day + timedelta(days=x) for x in range(10)]) 

start_day = parse("2019-01-01")
factory_B = pd.Series([int(x) for x in np.random.normal(70,8,(10,))],
                     index=[start_day + timedelta(days=x) for x in range(10)]) 
# print(factory_A)
# print(factory_B)
print(factory_A + factory_B)

## 생각해볼 문제 - 만약 서로 날짜가 다르면 어떻게 될까?
## index가 같지 않을 경우 연산의 결과는 NaN
## 어떻게 하면 NaN이 나오지 않고 정상적으로 처리할 수 있을까?
## index를 맞춰보아요!

start_day = datetime(2019,1,5)
factory_A = pd.Series([int(x) for x in np.random.normal(50,5,(10,))],
                     index=[start_day + timedelta(days=x) for x in range(10)]) 

index_a = set(factory_A.index)
index_b = set(factory_B.index)

index_a_b = index_a - index_b  # Factory B에 추가
index_b_a = index_b - index_a  # Factory A에 추가 

for i in index_a_b:
    factory_B[i] = 0;
    
for i in index_b_a:
    factory_A[i] = 0;

print(factory_A + factory_B)
    
###################################

# 딕셔너리와 동일하게 추가할 key에 값을 대입해서 Series에 데이터를 추가
# drop(key)을 이용하여 키와 값을 삭제. (삭제된 Series return)


s = pd.Series([1,2,3,4])
print(s)

s[4] = 5        # 추가할 key와 value값을 대입해서 Series에 데이터 추가
print(s)

s = s.drop(2)   # index 2가 삭제
print(s)

~~~


End.

{% include links.html %}
