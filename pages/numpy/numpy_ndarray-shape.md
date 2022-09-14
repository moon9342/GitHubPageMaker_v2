---
title: NumPy ndarray shape
sidebar: numpy_sidebar
summary: "NumPy의 ndarray shape을 변경하는 함수에 대해 알아보도록 하겠습니다."
permalink: numpy_ndarray-shape.html
folder: numpy
---

## ndarray shape 조절 함수 - reshape

~~~python

# ndarray shape 조절 함수 - reshape

import numpy as np

arr = np.arange(0,12,1)
print(arr)

# 배열의 데이터는 공유하지만 shape이 다른 뷰(View)를 생성
arr1 = arr.reshape(4,3)
print(arr1)

# 데이터가 공유되기 때문에 배열을 변경하면 다른 View에도
# 영향을 미침
# 데이터를 공유하는지 확인

arr[4] = 100
print(arr)
print(arr1)

#######################

# base 속성을 이용하면 현재의 View의 데이터가 어떤 객체의 
# 데이터 인지를 알 수 있다.

print(arr1.base)

if arr1.base is arr:
    print("데이터 공유!!")
    
#######################

# reshape()을 사용할 때 차원 하나를 -1로 설정하면 
# 배열의 전체 원소 개수와 확정된 차원 크기로 부터 
# 남은 차원의 크기를 추론하여 배열을 생성

arr = np.arange(0,12,1)

arr1 = arr.reshape(4,-1)
print(arr1)

#######################

# View를 생성하지 않으려면 copy()를 이용하여 새로운
# array 생성

arr2 = arr.reshape(4,3).copy()

~~~

---

## ndarray shape 조절 함수 - ravel

~~~python

# ndarray shape 조절 함수 - ravel

import numpy as np

# ravel() : 배열의 모든 원소가 포함된 1차원 배열을 리턴
# ravel() 역시 View를 return

arr = np.arange(0,100,1).reshape(5,-1).copy()
print(arr)


arr1 = arr.ravel() 
print(arr1)

~~~


---

## ndarray shape 조절 함수 - resize

~~~python

# ndarray shape 조절 함수 - resize

# resize()는 reshape()과 유사한 기능을 수행.
# 단, reshape()는 배열 요소 수를 변경하지 않는반면 resize()는
# shape을 변경하는 과정에서 배열 요소 수가 변할 수 있다.

import numpy as np

np.random.seed(1)

arr = np.random.randint(0,10,(3,4))
print(arr)

# resize()를 호출하는 방식에 따라서 원본 변경 혹은
# 결과 배열이 리턴된다.
# resize()는 reshape()과는 다르게 View를 생성하지 않는다.

print(np.resize(arr,(2,6))) # 새로운 배열 생성
                            # View 생성이 아님
print(arr)

print(arr.resize(2,6)) # return 없음. 원본 변경
print(arr)

arr.resize(3,5)  # 요소수가 늘어나면 0으로 세팅
print(arr)

arr.resize(2,2)  # 요소수가 줄면 기존 데이터를 버린다.
print(arr)

~~~


End.

{% include links.html %}
