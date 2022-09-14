---
title: NumPy ndarray operation
sidebar: numpy_sidebar
summary: "NumPy ndarray의 사칙연산과 행렬곱연산에 대해 알아보도록 하겠습니다."
permalink: numpy_ndarray-operation.html
folder: numpy
---

## ndarray 사칙연산과 행렬곱

~~~python

# ndarray 사칙연산과 행렬곱

import numpy as np

arr1 = np.array([[1,2,3],[4,5,6]])            # 2 x 3 ndarray
arr2 = np.arange(10,16,1).reshape(2,3).copy() # 2 x 3 ndarray
arr3 = np.arange(10,16,1).reshape(3,2).copy() # 3 x 2 ndarray

# 같은 크기의 배열 간의 연산은 
# 같은 위치에 있는 원소 간의 연산으로 결과가 계산

print(arr1 + arr2)  # np.add(arr1,arr2)
print(arr1 - arr2)  # np.subtract(arr1,arr2)
print(arr1 * arr2)  # np.multiply(arr1,arr2)
print(arr1 / arr2)  # np.divide(arr1,arr2)

# 두 행렬간의 행렬곱은 np.matmul() 혹은 np.dot()으로 수행가능
# np.dot(A,B)에서 A 행렬의 열 vector와 B 행렬의 행 vector의 size가 같아야 한다.
# 그렇지 않으면 이전에 배운 reshape이나 전치행렬을 이용하여 형 변환 후 크기를
# 맞추고 연산을 수행해야 한다.

print("행렬곱 : ", np.matmul(arr1,arr3))  # np.dot(arr1,arr3)


# 이런 행렬곱을 왜 알아야 할까?
# 행렬곱이 없다면 matrix연산은 무조건 같은 크기의 사칙연산만을 수행할 수 있다.
# 하지만 행렬곱을 이용하면 
# 행렬곱 조건을 만족하는 다양한 크기의 행렬을 이용하여 연속적으로
# 행렬곱을 수행시킬 수 있기 때문.
# 이러한 특성이 Machine Learning과 Image processing에서 자주 사용된다.

# 예) 입력 : 32 x 32 matrix (image라고 가정)
#     출력 : 32 x 10 matrix (다양한 처리가 적용된 image)
#     행렬곱 : (32 x 32) dot (32 x 128) dot (128,64) dot (64 x 10) => 32 x 10

# 위의 예처럼 행렬곱 특성을 이용하면 다양한 크기의 행렬을 이용하여 원본 데이터를
# 변경시키는 것이 가능. 만약 행렬곱이 없고 사칙연산만 수행할 수 있다면
# 32 x 32 형태의 크기를 가지는 특성(행렬)만 이용할 수 있기 때문에 
# 다양한 특성을 가지는 필터 개발이 불가능하다.

~~~

---

## ndarray broadcasting

~~~python

# ndarray broadcasting

# shape이 다른 경우 두 배열에 대한 이항연산은 두 배열간의 shape을
# 맞추는 broadcasting과정을 거친 후 수행된다.
# 가장 일반적인 경우는 배열과 scalar의 연산

import numpy as np

arr1 = np.array([[1,2,3],[4,5,6]])   # 2 x 3 ndarray
arr2 = np.array([7,8,9])             # 1차원 ndarray 
print(arr1)
print(arr2)
print(arr1 + arr2)  # arr2를 2차배열로 broadcasting

arr1 = np.array([[1,2,3],[4,5,6]])
arr2 = np.array([[1,2],[4,5]])
# print(arr1 + arr2)  # broadcasting이 일어날 수 없다. Error 발생

# 주의!!
# 이런 ndarray의 broadcasting은 사칙연산에 한해서 일어나게 된다.
# 즉, 행렬곱 연산에 대해서는 broadcasting이 발생하지 않는다.

~~~




End.

{% include links.html %}
