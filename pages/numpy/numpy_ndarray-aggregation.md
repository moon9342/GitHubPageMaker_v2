---
title: NumPy ndarray aggregation
sidebar: numpy_sidebar
summary: "NumPy ndarray의 boolean mask의 활용과 집계함수에 대해 알아보도록 하겠습니다."
permalink: numpy_ndarray-aggregation.html
folder: numpy
---

## ndarray 비교연산

~~~python

# ndarray 비교연산

# 사칙연산과 마찬가지로 비교연산도 같은 index의 
# 요소들끼리 수행된다.

import numpy as np

arr1 = np.random.randint(0,10,(2,3))
arr2 = np.random.randint(0,10,(2,3))

print(arr1)
print(arr2)


print(arr1 == arr2) # 논리 연산의 결과는 boolean
print(arr1 > arr2)

#######################

# 2개의 ndarray 자체가 같은 데이터를 가지고 있는지
# 비교할 때는 array_equal()을 사용한다.

arr1 = np.arange(10)
arr2 = np.arange(10)

print(np.array_equal(arr1,arr2)) # 두 배열 전체 비교

~~~

---

## NumPy 함수와 axis

~~~python

# NumPy 함수와 axis

import numpy as np
import matplotlib.pyplot as plt

arr = np.arange(1,7,1).reshape(2,3).copy()
print(arr)

print(np.sum(arr))        # 합, arr.sum()
print(np.cumsum(arr))     # 누적합, arr.cumsum()
print(np.mean(arr))       # 평균, arr.mean()
print(np.max(arr))        # 최대값, arr.max() 
print(np.min(arr))        # 최소값, arr.min()
print(np.argmax(arr))     # 최대값의 index => 5
print(np.argmin(arr))     # 최소값의 index => 0
print(np.std(arr))        # 표준편차, arr.std() 
print(np.sqrt(arr))       # 제곱근
print(np.exp(arr))        # 자연상수의 제곱값 (자연상수 e = 2.7182...)
print(np.log10(arr))      # 상용 log의 값
print(np.log(arr))        # 자연 log의 값 (자연상수 e = 2.7182...)

print(np.log10(100))      # 상용로그 
print(np.log(2.7182))     # 자연로그 

arr = np.arange(1,10000,1)
arr1 = np.log10(arr)
print(arr1)

plt.plot(arr1)
plt.show()


# NumPy의 모든 집계함수는 axis를 기준으로 계산.
# 만약 axis를 지정하지 않으면 axis는 None으로 간주하고
# 대상범위를 전체 행렬로 지정

import numpy as np

arr1 = np.array([1,2,3,4,5])
print(arr1.sum(axis=0)) # 1차원에서 axis=0은 열방향
#print(arr1.sum(axis=1)) # 1차원에서 axis=1은 error

arr1 = np.array([[1,2,3],[4,5,6],[7,8,9]])
print(arr1)
print(arr1.sum()) # axis=None, 전체배열대상 => 45
print(arr1.sum(axis=0)) # 2차원에서 axis=0은 행방향, np.sum(arr1,axis=0)
print(arr1.sum(axis=1)) # 2차원에서 axis=1은 열방향, np.sum(arr1,axis=1)
print(arr1.argmax(axis=1)) # 열방향으로 최대값의 index => [2 2 2]

arr1 = np.random.randint(0,5,(2,2,3))
print(arr1)
print(arr1.sum(axis=0)) # 3차원에서 axis=0은 depth방향

~~~

---

## NumPy 집계 함수의 처리속도

~~~python

# NumPy 집계 함수의 처리속도

import numpy as np

arr = np.arange(10000000, dtype=np.float64)

# 아래의 코드는 jupyter notebook의 cell을 나누어서 실행

%%time
# 해당 cell을 실행시키는데 걸리는 시간 출력출력

result = 0.0
for tmp in arr:
    result += tmp
print(result)    

##########################

%%time
print(arr.sum())

~~~

---

## Boolean Mask 활용

~~~python

# Boolean Mask 활용

import numpy as np

arr = np.array([[1,2,3,4],
                [5,6,7,8],
                [9,10,11,12],
                [13,14,15,16]])

# ndarray arr안에 10보다 큰 수가 몇개있는지 알아보려면
# 어떻게 해야 하는가?
# 여러가지 방법이 있지만 가장 쉽고 빠른 방법은 
# boolean indexing을 이용하는 방법

# arr > 10 => boolean mask
# True는 1로, False는 0으로 간주된다는 것을 기억하자.

(arr > 10).sum()    # 조건을 만족하는 개수(True의 개수)

~~~




End.

{% include links.html %}
