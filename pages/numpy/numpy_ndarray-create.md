---
title: NumPy ndarray 생성과 dtype
sidebar: numpy_sidebar
summary: "NumPy의 ndarray 생성과 dtype에 대한 내용을 알아보도록 하겠습니다."
permalink: numpy_ndarray-create.html
folder: numpy
---

## ndarray 생성 및 type 확인

~~~python


# ndarray 생성 및 type 확인

import numpy as np

a = [1,2,3,4]
print("list => {}".format(a))       # [1,2,3,4]  
print("type => {}".format(type(a))) # <class 'list'>


b = np.array([1,2,3,4])
print("array => {}".format(b))         # [1 2 3 4]
print("type => {}".format(type(b)))    # <class 'numpy.ndarray'>
print("b.dtype => {}".format(b.dtype)) # dtype : 배열 데이터 타입 속성
                                       # int32
print("b[0] type => {}".format(type(b[0]))) # <class 'numpy.int32'>


c = np.array([100,"Hello",3.141592])
print("array => {}".format(c))         # array => ['100' 'Hello' '3.141592'],  
print("type => {}".format(type(c)))    # type => <class 'numpy.ndarray'>  
print("c.dtype => {}".format(c.dtype)) # b.dtype => <U11 
print("c[0] type => {}".format(type(c[0]))) # <class 'numpy.str_'>
      
~~~

---

## 다차원 ndarray와 data type 지정

~~~python
# 다차원 ndarray와 data type 지정

import numpy as np
 
my_list = [[1,2,3], [4,5,6]]
arr = np.array(my_list)

print("{}".format(arr))  # [[1 2 3]
                         #  [4 5 6]]
    
print("1행 2열의 값은 : {}".format(arr[1,2])) # 1행 2열의 값은 : 6
print("1행의 값은 : {}".format(arr[1])) # 1행의 값은 : [4 5 6]
print("1행 2열의 값은 : {}".format(arr[1][2])) # 1행 2열의 값은 : 6

# ndarray 생성시 dtype 명시

# ndarray 생성 시 명시적으로 타입을 지정하지 않으면
# 데이터를 보고 적절한 타입을 알아서 지정

# 혹은 ndarray 생성 시 data type을 지정할 수 있다.

my_list = [[1,2,3], [4,5,6]]
arr = np.array(my_list, dtype=np.float64)

print("{}".format(arr))  # [[1. 2. 3.]
                         #  [4. 5. 6.]]

~~~

End.

{% include links.html %}
