---
title: NumPy ndarray iterator
sidebar: numpy_sidebar
summary: "NumPy ndarray의 전치행렬과 iterator에 대해 알아보도록 하겠습니다."
permalink: numpy_ndarray-iterator.html
folder: numpy
---

## ndarray transpose

~~~python

# ndarray transpose

# 일반적으로 전치행렬이라고 불리는 transpose에 대해서 알아보자.
# 전치행렬은 원본 행렬의 행은 열로, 열은 행으로 바꾼 행렬을 의미
# 전치행렬의 수학적 표현은 윗첨자 T를 이용해서 표현한다. 
# ndarray의 T 속성을 이용하면 전치행렬을 구할 수 있다.(View)

import numpy as np

arr = np.array([[1,2,3],[4,5,6]])   # 2 x 3 ndarray

arr_transpose = arr.T

print(arr)
print(arr_transpose)

arr[0,0] = 100

print(arr)
print(arr_transpose)   # 전치행렬 또한 View

# Vector에 대한 transpose

arr = np.array([1, 2, 3, 4])
arr_transpose = arr.T    

print(arr)
print(arr_transpose)   # vector에 대한 전치행렬은 의미없음.

# 만약 전치행렬을 구하고 싶으면 2차원 matrix로 변환한 후 수행해야 한다.

arr_transpose = arr.reshape(1,4).T
print(arr_transpose)

~~~

---

## iterator

~~~python

# ndarray iterator

# ndarray의 모든 원소를 access하는 경우에 일반적으로 iterator를 이용.
# iternext()와 finished 속성을 이용하여 ndarray의 모든 요소들을 순차적으로
# access 할 수 있다.

import numpy as np

# 1차원 ndarray에 대한 요소 출력
arr = np.array([1, 2, 3, 4, 5])

for tmp in arr:
    print(tmp, end=' ')
    
####################################    

# 1차원 ndarray에 대한 iterator

arr = np.array([1, 2, 3, 4, 5])

it = np.nditer(arr, flags=['c_index'])

while not it.finished:
    
    idx = it.index
    
    print(arr[idx], end=' ')
    
    it.iternext()

####################################

# 2차원 ndarray에 대한 요소 출력
arr = np.array([[1,2,3], [4,5,6]])

for tmp1 in range(arr.shape[0]):
    for tmp2 in range(arr.shape[1]):
        print(arr[tmp1,tmp2], end=' ')
        
####################################    

# 2차원 ndarray에 대한 iterator

arr = np.array([[1,2,3], [4,5,6]])

it = np.nditer(arr, flags=['multi_index'])

while not it.finished:
    
    idx = it.multi_index
    
    print(arr[idx], end=' ')
    
    it.iternext()        

~~~


End.

{% include links.html %}
