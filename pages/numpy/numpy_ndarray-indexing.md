---
title: NumPy ndarray indexing
sidebar: numpy_sidebar
summary: "NumPy ndarray의 indexing과 slicing에 대해 알아보도록 하겠습니다."
permalink: numpy_ndarray-indexing.html
folder: numpy
---

## ndarray indexing & slicing

~~~python 

# ndarray indexing & slicing

import numpy as np

arr = np.arange(10,20,1)

# ndarray는 python list처럼 indexing과 slicing이 가능

for idx,data in enumerate(arr):
    print("index : {}, data : {}".format(idx,data))
    
# ndarray를 slicing한 결과는 View이기 때문에 
# 원본 데이터가 변경되면 View의 데이터도 변경됨을 기억하자.

arr = np.arange(0,5,1)
print(arr)
print(arr[0:2])
print(arr[1:-1])
print(arr[0::2])  # 첫번째 원소부터 2씩 건너띄며 원소를 슬라이싱
print(arr[1:4:2])


# 2차원 ndarray의 indexing & slicing

arr = np.array([[1,2,3,4],
                [5,6,7,8],
                [9,10,11,12],
                [13,14,15,16]])
print(arr[1,1])
print(arr[2,:])     # 2차원 이상인 경우 
                    # ","를 기준으로 인덱싱을 해야 한다.
print(arr[1:3,:]) 
print(arr[:,2])
print(arr[1:3,:2])

~~~

---

## ndarray Boolean indexing & Fancy indexing

~~~python

# ndarray Boolean indexing & Fancy indexing

# boolean indexing은 배열의 각 요소의 선택여부를 
# True,False로 구성된 boolean mask를 이용하여 
# 지정하는 방식으로 boolean mask의 True 요소에 해당하는 
# index만을 조회.

import numpy as np

np.random.seed(1)
arr = np.random.randint(0,10,(10,))

print(arr)
print(arr % 2 == 0)       # mask 생성
print(arr[arr % 2 == 0])  # boolean indexing

##################################

# Fancy Indexing

# 배열에 index 배열을 전달하여 배열요소를 참조하는 방식

import numpy as np

arr = np.arange(0,12,1).reshape(3,4).copy()
print(arr)

print(arr[2,2])       # indexing : 10
print(arr[1:2,2])     # slicing : [6]
print(arr[1:2,1:2])   # slicing : [[5]]

print(arr[[0,2],2])   # fancy indexing : [2 10]
print(arr[[0,2],2:3]) # [[ 2]
                      #  [10]]

print(arr[:,[0,2]])   # fancy indexing  
                      # [[ 0  2]
                      #  [ 4  6]
                      #  [ 8 10]]
    
print(arr[[0,2],[0,2]]) # ?? 생각처럼 나오지 않는다.
                        # 슬라이싱처럼 fancy indexing 적용 안됨
    
# 방법 1
# 행을 먼저 추출한 후 해당 행에 대해 fancy indexing을 적용

print(arr[[0,2]][:,[0,2]])  # [[ 0  2]
                            #  [ 8 10]]

# 방법 2
# numpy의 ix_() 함수를 이용

print(arr[np.ix_([0,2],[0,2])]) # [[ 0  2]
                                # [ 8 10]]
~~~


End.

{% include links.html %}
