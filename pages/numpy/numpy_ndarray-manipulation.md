---
title: NumPy ndarray manipulation
sidebar: numpy_sidebar
summary: "NumPy ndarray의 정렬, 연결, 삭제에 대해 알아보도록 하겠습니다."
permalink: numpy_ndarray-manipulation.html
folder: numpy
---

## ndarray 정렬

~~~python

# ndarray 정렬

# NumPy의 array는 axis를 기준으로 정렬하는 sort() 제공
# 만약 axis를 지정하지 않으면 -1, 
# -1의 의미는 마지막 axis
# np.sort() : 정렬된 결과 배열을 return
# arr.sort() : 원본배열을 정렬. None return

import numpy as np

arr = np.arange(10)
np.random.shuffle(arr)      # shuffle 처리
print(arr)

print(np.sort(arr))         # 오름차순 정렬한 새로운 배열 return
print(np.sort(arr)[::-1])   # 내림차순 정렬, 특수한 indexing이용
print(arr)                  # 원본은 변함없음

arr = np.random.randint(0,10,(3,3))
print(arr)
print(np.sort(arr, axis=0))

#################################

# 2차원 배열 정렬
import numpy as np

arr = np.array([[10,2,12,4],
                [15,16,3,8],
                [9,1,11,7],
                [13,14,5,6]])

print(arr)
print(np.sort(arr,axis=0))    # 행 방향 정렬
print(np.sort(arr,axis=1))    # 열 방향 정렬

# 표준정규분포에서 
# 200개의 샘플을 추출한 후 
# 내림차순으로 상위 5%까지의 결과만 출력하세요!!

arr = np.random.randn(200)
result = np.sort(arr)[::-1][:int(0.05 * len(arr))]
print(result)

~~~

---

## NumPy concatenate() 함수

~~~python

# NumPy concatenate() 함수

# ndarray에 row(s) 또는 column(s)을 추가하기 위한 함수

import numpy as np

arr = np.array([[1,2,3], [4,5,6]])

new_row = np.array([7,8,9])

result = np.concatenate((arr,new_row.reshape(1,3)), axis=0)

print(result)

######################################

arr = np.array([[1,2,3], [4,5,6]])

new_col = np.array([7,8,9,10])

result = np.concatenate((arr,new_col.reshape(2,2)), axis=1)

print(result)


~~~

---

## ndarray delete() 함수

~~~python

# ndarray delete() 함수

# delete() 함수는 axis를 기준으로 행과 열을 삭제
# axis를 지정하지 않으면 1차배열로 변환 후 삭제
# 원본배열을 변경하지 않고 새로운 배열 return

import numpy as np

arr = np.random.randint(0,10,(3,4))
print(arr)

#####################

result = np.delete(arr,1)  # 1차 배열로 변환 후 1번 index 삭제
print(result)

#####################

result = np.delete(arr,1, axis=0)  # 1번 행 삭제
print(result)

#####################

result = np.delete(arr,3, axis=1)  # 3번 행 삭제
print(result)

~~~

---

## ndarray loadtxt() 함수

~~~python

# ndarray loadtxt() 함수

# numpy.loadtxt(fname, dtype='float', delimiter=' ', 
#  			    skiprows=0, usecols=None, unpack=False, ndmin=0, 
#               encoding='bytes', max_rows=None>
# 형식으로 사용됩니다. CSV 파일등으로부터 데이터를 읽어 ndarray를 생성하기 위한
# 함수라고 보시면 됩니다.

import numpy as np

arr = np.loadtxt("./data/seoul.csv", delimiter=",", dtype=np.object,
                 skiprows=1)

print(arr)

~~~


End.

{% include links.html %}
