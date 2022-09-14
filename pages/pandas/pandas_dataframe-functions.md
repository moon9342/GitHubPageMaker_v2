---
title: Pandas DataFrame functions
sidebar: pandas_sidebar
summary: "DataFrame의 기본함수에 대해서 알아보겠습니다."
permalink: pandas_dataframe-functions.html
folder: pandas
---

## DataFrame 기본 함수

`DataFrame`의 기본함수에 대해서 알아보겠습니다. 예제를 위해 `UCI Machine Learning Repository`에서
제공하는 자동차 연비 데이터셋을 사용하도록 하겠습니다. 총 398개의 데이터로 구성되며
자동차의 연비, 실린더 수, 배기량, 출력, 차중, 가속능력등의 컬럼을 가지고 있습니다.
<br><br>

`MPG Data Set`에 대한 링크는 다음과 같습니다.
<br><br>

[UCI Machine Learning Repository - MPG Data Set](https://archive.ics.uci.edu/ml/datasets/auto+mpg){: target="_blank" }
<br><br>

아래의 링크를 통해 예제 `CSV`파일을 다운로드 받아서 사용합니다.
<br><br>

[실습 CSV 파일(auto-mpg.csv)](/assets/downloads/auto-mpg.zip){: target="_blank" }

~~~python

import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']

display(df)

~~~

`MPG Data Set`에 대한 간단한 명세는 다음과 같습니다.

{% include image.html
file='pandas-dataframe-mpg-data-set.png'
%}

---

## head(), tail()

`head()`메소드는 DataFrame의 앞부분 일부내용을 출력하는 메소드 입니다. 반대로 `tail()`은 마지막 부분의
내용을 출력하는 메소드입니다. 만약 정수 `n`의 인자를 주지 않는다면 기본값은 5입니다. 즉, 5개의 행을 보여준다는
의미입니다.

---

## shape, info() 

`shape`속성은 행과 열의 개수를 tuple로 보여줍니다. DataFrame의 크기를 알아볼 때 사용하면 좋겠죠.
<br><br>

`info()` 메소드를 DataFrame에 적용하면 DataFrame의 기본 정보를 화면에 출력할 수 있습니다. 클래스 타입,
열 이름의 종류와 개수, 각 열의 데이터 타입과 개수등이 포함됩니다.
<br><br>

코드로 확인해보죠.

~~~python

import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']

display(df.head(3))  # 상위 3개의 행을 출력

display(df.tail())   # 하위 5개의 행을 출력

print(df.shape)      # DataFrame의 행과 열을 tuple로 출력

print(df.info())

~~~

---

## count(), value_counts()

`count()` 메소드는 DataFrame의 각 열이 가지고 있는 데이터 개수를 Series 객체로 리턴합니다.
주의할 점은 <span class='highlighter_red'>유효한 값의 개수</span>만을 계산합니다.
<br><br>

`value_counts()` 메소드는 Series 객체의 `unique value`(고유값) 개수를 세는 데 사용합니다.
`dropna=True` option을 이용하면 데이터값 중 `NaN`을 제외하고 개수를 계산합니다. 옵션을 따로
지정하지 않으면 `dropna=False` option이 적용되고 이때는 `NaN`이 포함됩니다.

~~~python

import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']

print(df.count())   # 유효한 값의 개수를 출력 (NaN이 있는 경우 제외)

print(df['origin'].value_counts())  # value_counts는 Series에 대해서 적용.
                                    # unique value의 개수를 count.

# 1    249
# 3     79
# 2     70
# Name: origin, dtype: int64


# count() 함수는 DataFrame의 각 열이 가지고 있는 데이터의 개수를
# Series로 return한다. 단, 유효한 값의 개수만을 계산한다.

data = [[2, np.nan],
        [7, -3],
        [np.nan, np.nan],
        [1, -2]
       ]

df = pd.DataFrame(data, 
                  columns=["one","two"],
                  index=["a","b","c","d"])

display(df)

print(df.count())

# one    3
# two    2
# dtype: int64

~~~

---

## unique(), isin()

`unique()` 메소드는 Series에 대해서 중복을 제거하는 메소드입니다. `isin()` 메소드는
`boolean mask`를 만들 때 종종 사용됩니다. 특정 값이 포함되어 있는지를 bool값으로 리턴하는 메소드입니다.
<br><br>

코드를 이용해서 살펴보도록 하겠습니다.

~~~python

import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']

print(df['model year'].unique())   # Series에 대해 중복제거
print(df['origin'].unique())       # Series에 대해 중복제거

print(df['origin'].isin([1, 2]))   # 제조국이 1(미국), 2(EU)인 차량만 True

~~~

---

## sort() 

가장 흔하게 사용하는 메소드 중 하나가 정렬을하는 `sort()` 메소드입니다.
<br><br>

다음의 코드를 이용해서 `sort()` 메소드의 사용법을 익혀두도록 하죠.

~~~python

# DataFrame index와 column 재 설정

import numpy as np
import pandas as pd

np.random.seed(1)
df = pd.DataFrame(np.random.randint(0,10,(6,4)))
display(df)   # 숫자 index사용, column명도 숫자 index처리

df.columns = ["A", "B", "C", "D"]
df.index = pd.date_range("20200101", periods=6)
display(df)

# 순열 랜덤 치환
random_date = np.random.permutation(df.index)  
print(random_date)
# np.random.shuffle(df.index)
# shuffle()을 못쓰는 이유는 Index는 mutable opertion을 지원하지 않기때문.

# index와 column이 재 설정된 DataFrame 생성
df2 = df.reindex(index=random_date, columns=["B","A","D","C"])
display(df2)

## axis 기준으로 정렬
display(df2.sort_index(axis=1, ascending=True))
# axis=0 : 행(row) 기준 정렬
# axis=1 : 열(column) 기준 정렬
# ascending=Flase : 내림차순 정렬

## 특정 column의 값으로 행 정렬
display(df2.sort_values(by="B"))

# "B"값이 같을 때 "A"로 정렬(2차정렬)
display(df2.sort_values(by=["B","A"]))   


~~~

---

## DataFrame 분석용 함수

`Numpy`와 `Pandas`는 `Descriptive Statistics`(기술통계)를 위한 다양한 함수를 제공합니다.
평균, 편차, 분산, 표준편차, 사분위, 공분산, 상관계수등을 쉽게 계산할 수 있습니다.
<br><br>

기본이 되는 `sum()`과 `mean()`의 사용법과 `axis`의 개념에 대해서 알아보고
나머지 기술통계를 위한 데이터 분석용 메소드의 사용은 추후에 기술통계부분에서 다루도록 하겠습니다.

---

## sum(), mean()

~~~python

# DataFrame 분석용 함수 (Sample DataFrame 생성)

import numpy as np
import pandas as pd

data = [[2, np.nan],
        [7, -3],
        [np.nan, np.nan],
        [1, -2]
       ]

df = pd.DataFrame(data, 
                  columns=["one","two"],
                  index=["a","b","c","d"])

display(df)
print(df.sum())   # df.sum(axis=0)
                  # skipna=True (default) => NaN은 제외
                  # Series return
                  
print(df.sum(axis=1))

print(df["two"].sum())    # 특정 열에 대한 합
print(df.loc["b"].sum())  # 특정 행에 대한 합

# skipna=False, NaN을 연산에 포함(실수처리)
print(df.mean(axis=0, skipna=False))
             
# skipna=True, NaN을 연산에서 배제    
print(df.mean(axis=1, skipna=True))
                                                    
# "one" column의 결측값은 "one" column의 평균으로
# "two" column의 결측값은 "two" column의 최소값으로 대체
one_avg = df["one"].mean() # df.mean(axis=0)["one"]
two_min = df["two"].min()  # df.min(axis=0)["two"]

df["one"] = df["one"].fillna(value=one_avg)
df["two"] = df["two"].fillna(value=two_min)

display(df)       

~~~

---

## Pandas의 내장 그래프 도구

`Pandas`는 그래프 module인 `matplotlib`의 기능 중 일부를 포함하고 있기 때문에
별도의 모듈 import 없이 DataFrame의 데이터를 그래프로 표현할 수 있습니다.

기본적인 선그래프, 막대그래프, 박스 plot, scatter 등을 그릴 수 있는데 시각화 모듈은
추후에 `matplotlib` module을 설명하면서 그쪽에서 자세히 설명하고 Pandas의 시각화
기능은 따로 설명하지 않도록 하겠습니다.


End.

{% include links.html %}
