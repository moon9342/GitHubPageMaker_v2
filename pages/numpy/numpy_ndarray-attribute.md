---
title: NumPy ndarray 주요 속성
sidebar: numpy_sidebar
summary: "NumPy의 ndarray 주요 속성에 대해 알아보도록 하겠습니다."
permalink: numpy_ndarray-attribute.html
folder: numpy
---

## 차원 관련 속성 : ndim, shape

~~~python

# ndarray의 차원 관련 속성 : ndim, shape

import numpy as np

# 1차원
my_list = [1,2,3,4]
arr = np.array(my_list)

print("arr.ndim => {}".format(arr.ndim))   # arr.ndim => 1
print("arr.shape => {}".format(arr.shape)) # arr.shape => (4,)


# 2차원
list = [[1,2],[3,4],[5,6],[7,8]]
arr = np.array(list)

print("arr.ndim => {}".format(arr.ndim))   # arr.ndim => 2
print("arr.shape => {}".format(arr.shape)) # arr.shape => (4, 2)


# 3차원 (2,2,3) 형태를 list로 만들어보자!
list = [[[1,2,3],[4,5,6]],[[7,8,9],[10,11,12]]]
arr = np.array(list)
print("arr.ndim => {}".format(arr.ndim))   # arr.ndim => 3
print("arr.shape => {}".format(arr.shape)) # arr.shape => (2, 2, 3)

~~~

---

## ndarray의 크기 속성과 shape 조절

~~~python

# ndarray의 크기 속성과 shape 조절

import numpy as np

list = [1,2,3,4]
arr = np.array(list)

print("arr.shape => {}".format(arr.shape))   # arr.shape => (4,)
print("크기(size) : {}".format(arr.size))    # 배열 요소의 수 : 4 
print("크기(len) : {}".format(len(arr)))     # 1차원배열길이 : 4


arr.shape = 2,2    # shape 변경
print("arr.shape => {}".format(arr.shape))   # arr.shape => (2, 2)
print("크기(size) : {}".format(arr.size))    # 배열 요소의 수 : 4
print("크기(len) : {}".format(len(arr)))     # 1차원배열길이 : 2


arr.shape = 4,1,1  # shape 변경
print("arr.shape => {}".format(arr.shape))   # arr.shape => (4, 1, 1)
print(arr)

~~~

---

## astype()을 이용한 ndarray data type 변경

~~~python

 # astype()을 이용한 ndarray data type 변경

import numpy as np

arr = np.array([1.2, 2.5, 3.8, 4.2, 5.3])
print("arr.dtype : {}".format(arr.dtype))  # float64

arr = arr.astype(np.int32)
print("arr.dtype : {}".format(arr.dtype))  # int32
print(arr)  # [1 2 3 4 5]  소수점 이하 버림 처리

~~~


End.

{% include links.html %}
