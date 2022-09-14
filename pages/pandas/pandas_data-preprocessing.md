---
title: Pandas Data pre-processing
sidebar: pandas_sidebar
summary: "Pandas를 이용한 데이터 전처리에 대해 살펴보도록 하겠습니다."
permalink: pandas_data-preprocessing.html
folder: pandas
---

## Data pre-processing

데이터 분석 및 머신러닝의 정확도는 분석 데이터의 품질에 좌우됩니다. 데이터 품질을 높이기 위해서는
누락데이터(결치값), 비정상데이터(이상치), 중복 데이터 등의 오류를 수정하고 분석 목적에 맞게
변형하는 과정이 필수입니다. 이를 `data pre-processing`(데이터 전처리)라고 합니다.
<br><br>

코드로 살펴보기 위해 우리는 유명한 `titanic` data set을 이용하겠습니다. `Kaggle`에서도 데이터를
얻을 수 있지만 쉽게 사용하기 위해 `seaborn` module에서 제공하는 `titanic` data set을 사용하도록
하겠습니다. 참고로 `Kaggle`의 데이터셋과 약간 상이합니다.
<br><br>

하나하나 살펴보도록 하죠.

---

## Missing Value

DataFrame에는 데이터 값이 종종 누락되는 경우가 있습니다. 데이터를 파일로 입력할 때
빠뜨리거나 파일 형식을 변환하는 과정에서 데이터가 소실되는 경우가 대부분이죠. 이런 값들을
`Missing Value`(결치값)라고 하는데 이런 누락데이터는 `NaN`(Not a Number)으로 표시합니다.
<br><br>

이런 결치값이 많아지면 데이터의 품질이 떨어지고 머신러닝 알고리즘을 왜곡하는 현상이 발생하기 때문에
제거하거나 적절한 값으로 대체하는 과정이 필요합니다. 데이터가 충분히 많다면 삭제가 가장 좋은 방법입니다.
하지만 일반적으로 충분히 많은 데이터를 확보하기가 쉽지 않습니다. 그래서 다른 값으로 대체해서 사용하는게
일반적입니다. 어떻게 대체하는가냐 관건입니다.

---

## 결치값 확인

먼저 데이터에 존재하는 결치값을 확인해보도록 하죠. 코드로 확인해 보겠습니다.

~~~python

import seaborn as sns

# titanic data set 가져옵니다.
df = sns.load_dataset('titanic')

display(df)

# DataFrame의 요약정보를 출력합니다.
# RangeIndex: 891이기 때문에 각 열에 891개의 데이터가 있습니다.
# deck 열은 203개의 유효한 범주형 데이터가 있음을 확인할 수 있습니다.
# deck 열은 688개의 결치값이 존재(891-203=688)
print(df.info())

# value_counts()를 사용하면 deck에 688개의 결치값이 존재하는것을 바로
# 확인할 수 있습니다. 이때 반드시 dropna=False 옵션을 사용해야 합니다.
print(df['deck'].value_counts(dropna=False))   # NaN    688

# 결치값을 직접 찾는 방법으로 isnull() 메소드와 notnull() 메소드가 있습니다.
display(df.head().isnull())

# python에서 False는 0, True는 1이므로 합을 구함으로 결치값의 개수를
# 알 수 있습니다. 이 때 합을 구할 때 axis=0을 이용해야 겠죠.
print(df.head().isnull().sum(axis=0))

~~~

---

## 결치값 제거

결치값이 존재하는지를 확인했으면 이제 이 결치값을 제거하는 방법에 대해서 알아보죠.
결치값이 들어있는 행 혹은 열을 삭제하면 될 듯 합니다.
<br><br>

코드로 살펴보겠습니다.

~~~python

import seaborn as sns

# titanic data set 가져옵니다.
df = sns.load_dataset('titanic')

# isnull()을 이용하여 새로운 DataFrame을 생성합니다.
missing_df = df.isnull()

# 각 열에 value_counts()를 수행해 
# Series 객체를 결과로 얻을 수 있습니다.
print(missing_df['deck'].value_counts())
print(missing_df['sex'].value_counts())

# for문을 이용해 각 column의 NaN개수를 출력합니다.
for col in missing_df.columns:
    missing_count = missing_df[col].value_counts()
    try :
        print(col, ' : ', missing_count[True])
    except:
        print(col, ' : ', 0)

# deck 열에는 688개의 결치값이 있고 결치값의 비율이 굉장히
# 높기 때문에 해당 열은 분석에서 제외하는게 맞습니다. 
# dropna() 메소드를 이용해서 삭제해보죠.

thresh_df = df.dropna(axis=1, thresh=500) 
# thresh 옵션을 주지 않으면 NaN이 존재하는 모든 열을 삭제합니다.
# NaN값이 500개 이상인 column만 삭제하기 위해서 thresh 옵션을 사용합니다.

display(thresh_df)  # column이 삭제된 것을 확인할 수 있습니다.

# 이번에는 행을 제거해 보죠.
# age column에는 177개의 결치값이 존재합니다. column 자체를 삭제하는것보다는
# NaN을 포함하는 행을 삭제하는게 더 좋을 듯 합니다. 
# subset을 이용해 특정 column으로 한정할 수 있습니다.
age_df = df.dropna(subset=['age'], axis=0, how='any')

display(len(age_df['age']))  # 714

~~~

---

## 결치값 치환

데이터의 품질을 높이기 위해 결치값을 무조건 삭제한다면 데이터의 수가 부족해져 활용하지
못하는 상황이 발생할 수 있습니다. 데이터의 분석은 데이터의 품질에도 영향을 받지만 데이터의
양에 의해서 역시 상당히 영향을 받습니다.
<br><br>

따라서 일부가 누락되었더라도 나머지 데이터를 최대한 살려서 분석에 활용하는게 맞습니다.
<br><br>

결치값을 대체할 값으로는 데이터의 분포와 특성을 잘 나타내는 평균값, 최빈값, 중위값등을
일반적으로 사용합니다. 또한 항상 사용할 수 있는건 아니지만 머신러닝 기법을 이용해
결치값을 예측해서 대체해서 사용할 수 있습니다.
<br><br>

가끔 결치값이 `NaN`이 아닌 다른 값(예를 들면, ?, - 등)으로 입력되기도 합니다. 이런경우
`replace()` 메소드를 이용해서 `np.nan`값으로 변경해주고 처리하는 것이 좋습니다.

~~~python

df.replace('?', np.nan, inplace=True)

~~~

코드를 이용해서 알아보도록 하겠습니다.

~~~python

import seaborn as sns

# titanic data set 가져옵니다.
df = sns.load_dataset('titanic')

# age 열의 첫 10개의 데이터를 살펴보죠.
# 결치값이 존재합니다.
print(df['age'].head(10))

# age 열의 NaN 값을 나이 데이터의 평균으로 치환
mean_age = df['age'].mean(axis=0)         # NaN값을 제외하고 평균 계산
df['age'].fillna(mean_age, inplace=True)  

print(df['age'].head(10))

##############################

# 이번에는 승선도시를 나타내는 embark_town 열에 있는 NaN을 다른 값으로
# 치환하죠. 승객들이 가장 많이 승선한 도시의 이름을 찾아내 NaN을 치환합니다.

print(df['embark_town'][825:830])

# idxmax() 메소드를 이용해서 가장 큰값의 index를 얻어온다.
most_freq = df['embark_town'].value_counts(dropna=True).idxmax()
print(most_freq)

df['embark_town'].fillna(most_freq, inplace=True)

print(df['embark_town'][825:830])

~~~

데이터의 특성 상 서로 이웃하고 있는 데이터끼리 유사성을 가질 가능성이 높습니다. 이럴 때는
앞이나 뒤로 이웃하고 있는 값으로 치환해 주는 것도 하나의 방법입니다.
<br><br>

아래의 코드를 보시죠.

~~~python

import seaborn as sns

# titanic data set 가져옵니다.
df = sns.load_dataset('titanic')

print(df['embark_town'][825:830])

# method='ffill' 옵션을 주면 앞 행에 있는 값으로 치환하고
# method='bfill' 옵션을 주면 뒤 행에 있는 값으로 치환합니다.
df['embark_town'].fillna(method='ffill', inplace=True)

print(df['embark_town'][825:830])

~~~

---

## 중복 데이터 처리

하나의 데이터셋에서 동일한 관측값(행)이 2개 이상 중복되는 경우 중복 데이터를
찾아서 삭제해야 합니다. 분석 결과가 왜곡되기 때문입니다. 그러나 정말 중복인지를
확인하는 과정도 거쳐야 하겠죠.

---

## 중복데이터 확인 및 삭제

행의 데이터가 중복되었는지를 확인하기 위해서는 `duplicated()` 메소드를 이용합니다.
전에 나온 행들과 비교하여 중복되는 행이면 `True`를 반환하고, 처음 나오는 행이면
`False`를 반환합니다.
<br><br>

중복된 행을 삭제하기 위해서는 `drop_duplicates()` 메소드를 이용합니다. subset
옵션을 이용하면 모든 열이 아닌 지정된 열을 기준으로 중복을 체크해서 삭제합니다.
<br><br>

다음의 코드를 보시죠.

~~~python

import pandas as pd

df = pd.DataFrame({'c1': ['a', 'a', 'b', 'a', 'b'],
                   'c2': [1, 1, 1, 2, 2],
                   'c3': [1, 1, 2, 2, 2]})
display(df)

dup_df = df.duplicated()
print(dup_df)

# Series에도 적용됩니다.
col_dup = df['c2'].duplicated()
print(col_dup)

# 중복데이터 제거
df2 = df.drop_duplicates()
display(df2)

# 중복데이터를 제거할 때 subset 옵션에 열 이름의 리스트를
# 줄 수 있습니다. 중복 여부를 판단할 때 subset 옵션의 
# 열을 기준으로 판단합니다. 
df3 = df.drop_duplicates(subset=['c2', 'c3'])
display(df3)

~~~

---

## 데이터의 표준화

우리가 사용하는 데이터셋은 다양한 사람들이 다양한 형태로 가공해서 표현된
것입니다. 그러다보니 서로 다른 단위가 섞여 있거나 같은 대상을 다른 형식으로
표현하는 경우가 의외로 많이 발생합니다.
<br><br>

동일 대상을 표현하는 방법에 차이가 있다면 분석의 정확도는 당연히 현저하게
낮아지게 됩니다. 따라서 데이터 포맷을 일관성 있게 표준화하는 작업이 필요합니다.

---

## 단위 환산

같은 데이터셋 안에서 서로 다른 측정 단위를 사용한다면, 전체 데이터의 일관성에 문제가
발생합니다. 따라서 측정 단위를 동일하게 맞출 필요가 있습니다.
<br><br>

특히 외국데이터를 가져오면 이런 경우가 많이 발생하는데 외국은 국내에서 잘 사용하지
않는 마일, 야드, 온스와 같은 단위를 사용하기 때문입니다. 우리나라의 미터, 평, 그램등으로
변경해서 사용하는게 필요할 수 있습니다.
<br><br>

일전에 사용한 `MPG data set`을 이용해 보죠. 여기서 `mpg`는 `mile per gallon`의 약자로
갤런당 마일수(연비)를 나타내고 있습니다. 우리나라에서는 연비를 표현할 때 `km/L`단위를
사용하죠. 찾아보면 `1mpg=0.425km/L` 라는 것을 알 수 있습니다.
<br><br>

간단하게 예를 들어보겠습니다.

~~~python

import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']

display(df.head(3))

mpg_to_kpl = 1.60934 / 3.78541 # 0.425

df['kpl'] = df['mpg'] * mpg_to_kpl
display(df.head(3))

# kpl 열을 소수점 3째자리에서 반올림
df['kpl'] = df['kpl'].round(2)
display(df.head(3))

~~~

---

## 자료형 변환

또 하나 생각해야 할 점은 `data type`의 변환입니다. 숫자가 문자열(pandas의 object)로
저장된 경우 숫자형(int, float)으로 변환해야 합니다. `dtypes` 속성을 이용하면
DataFrame을 구성하는 각 열의 data type을 확인할 수 있습니다.
<br><br>

`MPG data set`을 예로 들면 `horsepower`열은 숫자형이 적절할 것이고 출시연도를 나타내는
`model year`와 출시국가를 표현하는 `origin`은 `category`(범주)형이 적절할 듯 싶습니다.
<br><br>

다음의 코드를 보시죠.

~~~python

import numpy as np
import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']

print(df.dtypes)

# horsepower는 object형임을 알 수 있습니다. 이를 숫자형으로 변환해보죠
# 일단 먼저 변환이 가능한지부터 살펴보고 변환작업을 진행하겠습니다.

print(df['horsepower'].unique())   # 중간에 '?' 글자가 있습니다. 잘못된 값이죠.

df['horsepower'].replace('?', np.nan, inplace=True)

df.dropna(subset=['horsepower'], axis=0, inplace=True)

df['horsepower'] = df['horsepower'].astype('float')

print(df.dtypes)

# origin 열에는 정수값(1,2,3)이 들어 있지만 실제로는 국가이름인
# 'USA', 'EU', 'JPN'을 뜻합니다. replace() 메소드를 이용하여 
# 국가이름을 문자열로 바꿔준 후 범주형 데이터로 변환합니다.

print(df['origin'].unique())

df['origin'].replace({1: 'USA',
                      2: 'EU',
                      3: 'JPN'}, inplace=True)

print(df['origin'].unique())  # ['USA' 'JPN' 'EU']
print(df['origin'].dtypes)    # object

df['origin'] = df['origin'].astype('category')
print(df['origin'].dtypes)    # category

# 범주형을 다시 문자열로 다시 변환
# df['origin'] = df['origin'].astype('str')
# print(df['origin'].dtypes)    # object

# model year 열의 자료형도 숫자로 되어 있는데 이를 category형으로 변환해보죠
# 사실 연도를 뜻하기 때문에 숫자(int64)로 처리해도 되지만 연도는 시간적인
# 순서의 의미는 있으나 숫자의 상대적인 크기는 별 의미가 없기때문에 범주형으로
# 처리하는게 적합합니다.

print(df['model year'].sample(3))   # sample() 메소드는 무작위로 행을 추출하는 method
df['model year'] = df['model year'].astype('category')

display(df.dtypes)

~~~

---

## 범주형(Category) 데이터 처리

데이터 분석 알고리즘에 따라 연속 데이터를 그대로 사용하기 보다는 범주형 데이터로
변환해서 처리하는게 효율적인 경우가 많습니다. 이번에는 이런 범주형 데이터를
어떻게 처리해야 하는지에 대해서 알아보도록 하겠습니다.

---

## 구간 분할

연속 데이터를 일정한 구간(`bin`)으로 나눠서 분석하는 방법입니다. 가격, 비용, 효율등
연속적인 값을 일정한 수준이나 정도를 나타내는 이산적인 값으로 나타내에 구간별 차이를
드러내는 것입니다.
<br><br>

이처럼 연속 변수를 일정한 구간으로 나누고, 각 구간을 범주형 이산 변수로 변환하는 과정을
`binning`(구간 분할)이라고 합니다. `Pandas`는 `cut()` 함수를 이용해 연속 데이터를
여러 구간으로 나누고 범주형 데이터로 변환할 수 있습니다.
<br><br>

간단하게 그림으로 표현하면 다음과 같습니다.

{% include image.html
file='pandas-data-binning.png'
%}

`MPG data set`에서 `horsepower` 열은 엔진 출력을 나타냅니다. 경우에 따라서는
엔진 출력을 숫자가 아닌 저출력, 보통출력, 고출력 등 구간으로 나누어서 표시하는 것이
효과적일 수 있습니다. 이렇게 3개의 구간으로 구분하려면 위의 그림처럼 총 4개의 경계값이
필요합니다.
<br><br>

경계값을 구하는 방법 중 `Numpy`의 `histogram()` 함수를 활용하는 방법을 써 보도록
하겠습니다. 아래의 코드를 보시죠

~~~python

import numpy as np
import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']


df['horsepower'].replace('?', np.nan, inplace=True)
df.dropna(subset=['horsepower'], axis=0, inplace=True)
df['horsepower'] = df['horsepower'].astype('float')

count, bin_dividers = np.histogram(df['horsepower'], bins=3)

print(count)         # [257 103  32]
print(bin_dividers)  # [ 46. 107.33333333 168.66666667 230.] 

# 이제 pandas의 cut() 함수를 이용해보죠.
bin_names = ['저출력', '보통출력', '고출력']

df['hp_cut'] = pd.cut(x = df['horsepower'],    # 데이터 배열
                      bins=bin_dividers,        # 경계값 리스트
                      labels=bin_names,         # bin 이름
                      include_lowest=True)     # 첫 경계값 포함여부

display(df[['horsepower', 'hp_cut']].head(10))

~~~

---

## dummy variable

위의 예에서 `horsepower` 열의 숫자형 연속 데이터를 `hp_cut` 열의 범주형 데이터로
변환할 수 있음을 확인했습니다. 하지만 이처럼 category를 나타내는 범주형 데이터를
머신러닝 알고리즘에 바로 사용할 수 없는 경우가 많은데 그 이유는 컴퓨터가 인식 가능한
값으로 표현해야 하기 때문입니다.
<br><br>

이럴 때 숫자 0 혹은 1로 표현되는 `dummy variable`(더미변수)를 사용합니다. 여기서
0과 1은 수의 크고 작음을 의미하지 않고 어떤 특성이 있는지 없는지 여부만을 나타냅니다.
즉, 해당 특성이 존재하면 1로 표현하고 존재하지 않으면 0으로 표현하는 방식입니다.
<br><br>

이처럼 범주형 데이터를 컴퓨터가 인식할 수 있도록 숫자 0과 1로만 구성되는 `one hot vector`
(원 핫 벡터)로 변환한다고 해서 `one-hot-encoding`(원 핫 인코딩)이라고 부르기도 합니다.
<br><br>

`Pandas`가 제공하는 `get_dummies()` 함수를 이용하면 범주형 변수의 모든 고유값을 각각
새로운 더미 변수로 쉽게 변환할 수 있습니다.
<br><br>

아래의 코드를 보시죠.

~~~python

import numpy as np
import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']


df['horsepower'].replace('?', np.nan, inplace=True)
df.dropna(subset=['horsepower'], axis=0, inplace=True)
df['horsepower'] = df['horsepower'].astype('float')

count, bin_dividers = np.histogram(df['horsepower'], bins=3)

bin_names = ['저출력', '보통출력', '고출력']

df['hp_cut'] = pd.cut(x = df['horsepower'],    # 데이터 배열
                      bins=bin_dividers,        # 경계값 리스트
                      labels=bin_names,         # bin 이름
                      include_lowest=True)     # 첫 경계값 포함여부

display(df[['horsepower', 'hp_cut']].head(10))

horsepower_dummies = pd.get_dummies(df['hp_cut'])
display(horsepower_dummies.head(10))

~~~

---

## Normalization(정규화)

각 변수(DataFrame의 각 열)에 들어있는 숫자 데이터의 상대적 크기 차이 때문에 머신러닝 결과가
달라질 수 있습니다. 예를 들어 A변수는 0~1000 범위의 값을 가지고 B변수는 0~1 범위의 값을 갖는다고
하면 상대적으로 큰 숫자 값을 가지는 A 변수의 영향이 더 커지게 됩니다.
<br><br>

따라서 숫자 데이터의 상대적인 크기 차이를 제거할 필요가 있습니다. 각 열에 속하는 데이터 값을
동일한 크기 기준으로 나눈 비율로 나타내는 것을 `Normalization`(정규화)라고 합니다. 정규화 과정을
거치면 각 열의 데이터 범위는 0~1 혹은 -1~1이 되게 됩니다.
<br><br>

가장 쉬운 방법 중 하나는 `Min-Max Scaling`이라고 불리는 방법입니다. 수식으로 표현하면
다음과 같습니다. 정규화에 대한 조금 더 자세한 내용은 머신러닝쪽에서 다시한번 다루기로 하죠.

{% include image.html
file='machine-learning-min-max-scale.png'
%}

다음의 코드를 보시죠.

~~~python

import numpy as np
import pandas as pd

df = pd.read_csv('./data/auto-mpg.csv', header=None)

df.columns = ['mpg', 'cylinders', 'displacement', 'horsepower',
              'weight', 'acceleration', 'model year', 'origin', 'name']


df['horsepower'].replace('?', np.nan, inplace=True)
df.dropna(subset=['horsepower'], axis=0, inplace=True)
df['horsepower'] = df['horsepower'].astype('float')

min_max = df['horsepower'].max() - df['horsepower'].min()

df['horsepower'] = (df['horsepower'] - df['horsepower'].min()) / min_max

print(df['horsepower'])

~~~

---

## 시계열 데이터

주식, 환율 등 금융 데이터를 다루기 위해서는 `time series`(시계열) 데이터를 잘
다루어야 합니다. 특히 시계열 데이터를 DataFrame의 행 index로 사용하면 시간으로
기록된 데이터를 분석하는 것이 매우 편리해 집니다.
<br><br>

`Pandas`의 시간 표시 방식 중에서 시계열 데이터 표현에 자주 이용되는 두 가지 유형을
살펴보도록 하겠습니다. 특정한 시점을 기록하는 `Timestamp`와 두 시점 사이의 일정한
기간을 나타내는 `Period`가 그것입니다.
<br><br>

다음 그림을 보시죠.

{% include image.html
file='pandas-time-series.png'
%}

---

## 다른 자료형을 시계열 데이터로 변환

우리가 사용하는 많은 시간 데이터들은 별도의 시간 자료형으로 기록되지 않고 문자열 혹은
숫자로 저장되는 경우가 많습니다. `Pandas`는 다른 자료형으로 저장된 시간 데이터를
시계열 객체인 `Timestamp`로 변환하는 함수를 제공합니다.
<br><br>

예제를 위해 주식데이터를 사용하도록 하겠습니다. 아래의 링크를 이용해 `CSV` 파일을
다운로드 한 후 이용해보도록 하죠.
<br><br>

[실습 CSV 파일(stock-data.csv)](/assets/downloads/stock-data.zip){: target="_blank" }

~~~python

import numpy as np
import pandas as pd

df = pd.read_csv('./data/stock-data.csv')

display(df.head())
print(df.info())   # Data 열이 object(문자열)로 되어있다.

# Data 열의 날짜 데이터를 Pandas의 Timestamp 객체로 변환합니다.
df['new_Date'] = pd.to_datetime(df['Date'])
display(df.head())
print(df.info())  # new_Date열은 datetime64[ns]

# new_Date 열을 DataFrame의 index로 설정하고 Date 열 삭제
df.set_index('new_Date', inplace=True)
df.drop('Date', axis=1, inplace=True)
display(df.head())
print(df.info())  # DatetimeIndex: 20 entries, 2018-07-02 to 2018-06-01

~~~

이번에는 `Pandas`의 `to_period()` 함수를 이용해 일정한 기간을 나타내는 `Period`객체로
`Timestamp` 객체를 변환해 보겠습니다. `freq` 옵션으로 기준이 되는 기간을 설정합니다.
<br><br>

코드로 알아보겠습니다.

~~~python

import numpy as np
import pandas as pd

dates = ['2019-01-01', '2020-03-01', '2021-06-01']

# 문자열의 날짜 데이터를 Timestamp 객체로 변환
ts_dates = pd.to_datetime(dates)
print(ts_dates)

# Timestamp를 Period로 변환
pr_day = ts_dates.to_period(freq='D')
print(pr_day)

pr_month = ts_dates.to_period(freq='M')
print(pr_month)

pr_year = ts_dates.to_period(freq='A')
print(pr_year)

~~~

참고로 `freq` 옵션의 종류는 다음과 같습니다.

{% include image.html
file='pandas-period-freq-option.png'
%}

---

## 시계열 데이터 만들기

`Pandas`는 `date_range()` 함수를 이용하여 여러 개의 날짜(Timestamp)가 들어있는
배열 형태의 시계열 데이터를 만들 수 있습니다. `Python`의 `range()`와 유사하다고
생각하시면 됩니다.
<br><br>

코드로 살펴보죠

~~~python

import numpy as np
import pandas as pd

ts_ms = pd.date_range(start='2019-01-01',   # 날짜 범위 시작
                      end=None,             # 날짜 범위 끝
                      periods=6,            # 생성한 Timestamp 개수
                      freq='MS',            # 시간 간격(MS : 월의 시작일)
                      tz='Asia/Seoul')      # 시간대(타임존)
print(ts_ms)

# 시간 간격을 다르게 설정해보죠.
ts_me = pd.date_range(start='2019-01-01',   # 날짜 범위 시작
                      end=None,             # 날짜 범위 끝
                      periods=6,            # 생성한 Timestamp 개수
                      freq='M',             # 시간 간격(M : 월의 말일)
                      tz='Asia/Seoul')      # 시간대(타임존)
print(ts_me)

ts_3m = pd.date_range(start='2019-01-01',   # 날짜 범위 시작
                      end=None,             # 날짜 범위 끝
                      periods=6,            # 생성한 Timestamp 개수
                      freq='3M',            # 시간 간격(3M : 3개월 말일)
                      tz='Asia/Seoul')      # 시간대(타임존)
print(ts_3m)


# DatetimeIndex(['2019-01-01 00:00:00+09:00', '2019-02-01 00:00:00+09:00',
#                '2019-03-01 00:00:00+09:00', '2019-04-01 00:00:00+09:00',
#                '2019-05-01 00:00:00+09:00', '2019-06-01 00:00:00+09:00'],
#               dtype='datetime64[ns, Asia/Seoul]', freq='MS')
# DatetimeIndex(['2019-01-31 00:00:00+09:00', '2019-02-28 00:00:00+09:00',
#                '2019-03-31 00:00:00+09:00', '2019-04-30 00:00:00+09:00',
#                '2019-05-31 00:00:00+09:00', '2019-06-30 00:00:00+09:00'],
#               dtype='datetime64[ns, Asia/Seoul]', freq='M')
# DatetimeIndex(['2019-01-31 00:00:00+09:00', '2019-04-30 00:00:00+09:00',
#                '2019-07-31 00:00:00+09:00', '2019-10-31 00:00:00+09:00',
#                '2020-01-31 00:00:00+09:00', '2020-04-30 00:00:00+09:00'],
#               dtype='datetime64[ns, Asia/Seoul]', freq='3M')

~~~

이번에는 `period_range()` 함수를 이용하여 여러 개의 기간(Period)이 들어 있는 시계열 데이터를
만들어보죠.

~~~python

import numpy as np
import pandas as pd

pr_m = pd.period_range(start='2019-01-01',
                       end=None,
                       periods=3,
                       freq='M')

print(pr_m)
# '2019-01'은 2019년 1월 전체 기간을 나타내고 '2019-02'는 2월 전체
# 기간을 나타냅니다.

pr_h = pd.period_range(start='2019-01-01',
                       end=None,
                       periods=3,
                       freq='H')

print(pr_h)
# '2019-01-01 00:00'은 2019년 1월 1일 0시부터 1시까지의 기간을
# 나타냅니다.

pr_2h = pd.period_range(start='2019-01-01',
                        end=None,
                        periods=3,
                        freq='2H')

print(pr_2h)
# '2019-01-01 00:00'은 2019년 1월 1일 0시부터 2시까지의 기간을
# 나타냅니다.

# PeriodIndex(['2019-01', '2019-02', '2019-03'], dtype='period[M]')
# PeriodIndex(['2019-01-01 00:00', '2019-01-01 01:00', '2019-01-01 02:00'], dtype='period[H]')
# PeriodIndex(['2019-01-01 00:00', '2019-01-01 02:00', '2019-01-01 04:00'], dtype='period[2H]')

~~~

---

## 시계열 데이터 활용

`Timestamp` 객체로 되어 있는 시간에서 연, 월, 일 정보를 따로 분리할 수 있습니다.
주식데이터를 이용해 실습을 해보죠.

~~~python

import numpy as np
import pandas as pd

df = pd.read_csv('./data/stock-data.csv')

display(df.head())
print(df.info())   # Data 열이 object(문자열)로 되어있다.

# Data 열의 날짜 데이터를 Pandas의 Timestamp 객체로 변환합니다.
df['new_Date'] = pd.to_datetime(df['Date'])
display(df.head())

# new_Date 열의 dt 속성을 이용해 연, 월, 일 정보를 분리
df['Year'] = df['new_Date'].dt.year
df['Month'] = df['new_Date'].dt.month
df['Day'] = df['new_Date'].dt.day

####################################

# to_period() 함수를 이용
df['Date_year'] = df['new_Date'].dt.to_period(freq='A')
df['Date_m'] = df['new_Date'].dt.to_period(freq='M')

display(df.head())

~~~

`Timestamp`로 구성된 열을 행 index로 지정하면 `DatetimeIndex`라는
고유 속성으로 변환되고 `Period`로 구성된 열을 행 index로 지정하면
`PeriodIndex` 속성으로 변환됩니다.
<br><br>

날짜 index를 사용하는 이유는 시계열 데이터에 대한 `indexing`과 `slicing`이
편리하기 때문입니다.
<br><br>

예제를 보시죠.

~~~python

import numpy as np
import pandas as pd

df = pd.read_csv('./data/stock-data.csv')

# Data 열의 날짜 데이터를 Pandas의 Timestamp 객체로 변환합니다.
df['new_Date'] = pd.to_datetime(df['Date'])

# new_Date 열의 dt 속성을 이용해 연, 월, 일 정보를 분리
df['Year'] = df['new_Date'].dt.year
df['Month'] = df['new_Date'].dt.month
df['Day'] = df['new_Date'].dt.day

####################################

# to_period() 함수를 이용
df['Date_year'] = df['new_Date'].dt.to_period(freq='A')
df['Date_m'] = df['new_Date'].dt.to_period(freq='M')

# new_Date 열을 행 index로 지정
df.set_index('new_Date', inplace=True)

display(df)

print(df.loc['2018-07-02'])

display(df.loc['2018-07'])

display(df.loc['2018-06-20':'2018-06-25'])

# 시간 간격 계산
today = pd.to_datetime('2018-12-25')      # 기준일
df['time_delta'] = today - df.index       # 날짜 차이 계산
df.set_index('time_delta', inplace=True)  # 행 index로 설정

display(df)

display(df.loc['180 days':'182 days'])

~~~


End.

{% include links.html %}
