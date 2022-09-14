---
title: NumPy 랜덤 기반의 함수
sidebar: numpy_sidebar
summary: "NumPy의 ndarray를 생성하는 랜덤 기반의 함수와 NumPy에서 제공하는 유용한 랜덤 관련 함수에 대해 알아보겠습니다."
permalink: numpy_ndarray-create-random.html
folder: numpy
---

## ndarray 다양한 생성 함수-random 기반

~~~python

# ndarray 다양한 생성 함수-random 기반

# np.random.normal() : 정규분포 확률밀도에서 실수 표본추출
# np.random.rand() : [0,1)의 균등분포 확률밀도에서 실수 표본추출
# np.random.randn() : 표준정규분포(평균:0, 표준편차:1) 확률밀도에서 실수 표본추출
# np.random.randint() : 주어진 범위에서 균등분포 확률밀도에서 정수 표본추출
# np.random.random() : [0,1)의 균등분포 확률밀도에서 실수 표본추출

import numpy as np
import matplotlib.pyplot as plt

# np.random.normal(정규 분포의 평균,표준편차,shape)
# 정규분포 확률밀도에서 실수 표본추출
# 추출된 난수는 정규분포의 형상을 가진다.

mean = 50
std = 2
arr = np.random.normal(mean,std,(10000,))
plt.hist(arr,bins=100)
plt.show()

########################

# np.random.rand(d0,d1,d2,...)
# 난수[0,1) 균등분포 확률 밀도에서 표본을 추출
# [](대괄호)는 이상, 이하의 의미, ()(소괄호)는 초과,미만의 의미
# 추출된 난수는 균등분포의 형상을 가진다.
arr = np.random.rand(10000)
plt.hist(arr,bins=100)
plt.show()

########################

# np.random.randn(d0,d1,d2,...)
# 표준 정규 분포 확률 밀도에서 표본을 추출
# 추출된 난수는 정규분포의 형상을 가진다.
arr = np.random.randn(10000)
plt.hist(arr,bins=100)
plt.show()

########################

# np.random.randint(low,high,shape)
# 균등 분포 확률 밀도에서 정수 표본을 추출
# 추출된 정수 난수는 해당 범위에서 균등 분포의 형상을 가진다.
arr = np.random.randint(-100,100,(1000,))
plt.hist(arr,bins=100)
plt.show()

########################

# np.random.random(shape)
# [0,1) 균등 분포 확률 밀도에서 표본을 추출
# 추출된 난수는 해당 범위에서 균등 분포의 형상을 가진다.
arr = np.random.random((10000,))
plt.hist(arr,bins=100)
plt.show()

~~~

---

## NumPy 난수관련 함수

~~~python

# random 기반의 배열 생성의 재현성을 확보해보자!!
# 난수는 특정 시작 숫자로부터 난수처럼 보이는 수열을 만드는
# 알고리즘의 결과물

# 시작점을 설정하면 같은 난수를 발생시킬 수 있다. ( 난수의 재현 )
# np.random.seed(x) : 난수의 시작점을 설정하는 함수

import numpy as np

np.random.seed(1)
arr = np.random.randint(0,100,(10,))
print(arr)
# 반복실행을 해도 똑같은 난수가 발생

##################

# 데이터의 순서를 바꾸려면 shuffle()을 이용합니다. 

arr = np.arange(10)
np.random.shuffle(arr)   # arr의 데이터 순서를 변경
print(arr)

##################

# 데이터 집합에서 일부를 무작위로 선택하는 샘플링(sampling)을 
# 수행하려면 choice()를 이용합니다. 

# numpy.random.choice(a, size=None, replace=True, p=None)

# a : 배열 혹은 정수
#     만약 정수면 arange(a) 명령으로 데이터 생성
# size : 정수. 샘플 숫자
# replace : True이면 한번 선택한 데이터를 다시 선택 할 수 있음.
# p : ndarray. 각 데이터가 선택될 수 있는 확률을 명시.

arr = np.random.choice(5, 3, replace=False)
arr = np.random.choice(5, 10)
arr = np.random.choice(5, 10, p=[0.1, 0, 0.3, 0.6, 0])

print(arr)

~~~


End.

{% include links.html %}
