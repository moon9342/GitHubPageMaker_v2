---
title: NumPy ndarray를 생성하는 다양한 생성함수
sidebar: numpy_sidebar
summary: "ndarray를 생성하는 다양한 생성함수에 대해 알아보도록 하겠습니다."
permalink: numpy_ndarray-create-other.html
folder: numpy
---

## zeros,ones,empty,full

~~~python

# ndarray 다양한 생성 함수-zeros,ones,empty,full

import numpy as np

arr = np.zeros((3,4)) # shape을 지정
                      # default data type은 float64
print(arr)

arr = np.ones((2,4), dtype=np.int32)
print(arr)

arr = np.empty((3,3)) # 공간만 할당, 초기화 하지 않는다. 
print(arr)

arr = np.full((3,4), 7, dtype=np.float64)
print(arr)   # 지정된 shape으로 array를 생성한 후
             # parameter값으로 초기화

arr = np.array([(1,2,3),(4,5,6)]) # 2 x 3 array

# 지정된 배열과 shape이 같은 array를 생성.
arr_like = np.ones_like(arr, dtype=np.float64)
arr_like = np.zeros_like(arr, dtype=np.float64)
arr_like = np.empty_like(arr, dtype=np.float64)
arr_like = np.full_like(arr, 9, dtype=np.float64)

print(arr_like)

~~~

---

## arange

~~~python

# ndarray 다양한 생성 함수-arange

import numpy as np

# python의 range()와 유사
# 주어진 범위 내에서 지정한 간격으로 
# 연속적인 원소를 가진 배열을 생성
# np.arange(시작,끝,간격)
# 시작은 inclusive, 끝은 exclusive 

arr = np.arange(0,10,2)
print("arr의 크기 : {}".format(arr.size))
print("arr : ",arr)    # [0 2 4 6 8]

arr = np.arange(10)
print("arr의 크기 : {}".format(arr.size))
print("arr : ",arr)    # [0 1 2 3 4 5 6 7 8 9]

arr = np.arange(0.1,5.3)
print("arr의 크기 : {}".format(arr.size))
print("arr : ",arr)    # [0.1 1.1 2.1 3.1 4.1 5.1]

~~~

---

## linspace

~~~python

# ndarray 다양한 생성 함수-linspace

import numpy as np
import matplotlib.pyplot as plt

# np.linspace(start,stop,num)
# start와 stop의 값은 고정(inclusive).
# start부터 stop의 범위에서 num개를 균일한 간격으로
# 데이터를 생성하고 배열을 만드는 함수
# 원소간 간격은 (stop-start)/(num - 1).
# num의 default값은 50

arr = np.linspace(0,10,11)
print("arr의 크기 : {}".format(arr.size))   # 11
print(arr)          # [ 0.  1.  2.  3.  4.  5.  6.  7.  8.  9. 10.]
plt.plot(arr,"*")   # 그래프를 그려준다. 
plt.show()

arr = np.linspace(1,121,31)
print(arr)
plt.plot(arr,"o")
plt.show()

~~~


End.

{% include links.html %}
