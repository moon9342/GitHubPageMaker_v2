---
title: Pandas DataFrame Indexing
sidebar: pandas_sidebar
summary: "Pandas DataFrame의 Indexing방법에 대해서 살펴보도록 하겠습니다."
permalink: pandas_dataframe-indexing.html
folder: pandas
---

## DataFrame의 특정 column 추출 (1)

~~~python

# DataFrame에서 특정 column을 추출할 수 있다.

# 하나의 column을 추출한 경우 Series로 return

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

print(df["이름"])       
print(type(df["이름"])) # 결과는 Series로 return

print(df.이름)  # df["이름"] 방식을 더 많이 사용.

~~~

---

## DataFrame의 특정 column 추출 (2)

~~~python

# DataFrame에서 특정 column을 추출한 후 
# 데이터를 변경하면 원본 DataFrame에도 영향을 미친다.

# python에서 warning message를 on,off할 수 있다.

import warnings
import numpy as np
import pandas as pd

warnings.filterwarnings(action='ignore')   # warning off
# warnings.filterwarnings(action='default') # warning on

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

year = df["학년"]    # View가 아닌 복사본을 생성하려면 df["학년"].copy() 이용

year["one"] = 1000   # Series의 내용을 변경(View의 내용변경)
print(df["학년"])

display(df)          # DataFrame의 내용도 변경

~~~

---

## DataFrame에서 2개 이상의 column 추출

~~~python

# DataFrame에서 두개 이상의 column을 추출할 수 있다.
# Fancy Indexing을 사용.

# 두개 이상의 column을 추출한 경우 Series가 아닌
# DataFrame으로 결과 return

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

# print(df["names","year"])     # Error
display(df[["이름","학점"]])  # Fancy Indexing

~~~

---

## DataFrame에서 특정 column 값 수정

~~~python

# DataFrame에서 특정 column의 값 수정

# 단일값 혹은 numpy array 혹은 list를 이용하여 수정

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

display(df)

df["등급"] = "A"   
# df["등급"] = ["A","B","D","B","A"] 
# df["등급"] = np.array(["A","B","D","C","C"])
display(df)

~~~

---

## DataFrame에서 두 개 이상의 특정 column 값 수정

~~~python

# DataFrame에서 두개 이상의 특정 column 값 수정

# 단일값 혹은 numnpy array 혹은 list를 이용하여 수정

# 참고
# DataFrame의 column을 Fancy Indexing한 후 value들만
# ndarray로 추출하려면 to_numpy() 함수 사용.
# df[["grade","names"]].to_numpy() # numpy array로 변환

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

display(df)
df[["등급","이름"]] = "A"   
# df[["등급","이름"]] = [["A","최길동"],
#                        ["B","이길동"],
#                        ["D","박길동"],
#                        ["B","박길동"],
#                        ["A","문길동"]
#                       ]
display(df)

print(df[["학년","학점"]].to_numpy())

~~~

---

## DataFrame column 추가

~~~python

# 기존에 생성된 DataFrame에 column을 추가할 수 있다. 

# scalar, numpy array, list, Series 등을 이용해 추가

# Series로 추가할 경우 index를 기반으로 추가!!

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

df["등급"] = np.arange(1,10,2)

df["나이"] = [20,21,25,24,22]

# df["나이"] = [20,21,25,24] # Error(개수가 맞지 않음)

# age = pd.Series([20,21,25,24,22])
# df["나이"] = age    # 추가되는 내용 없음 (index 기반)

# age = pd.Series([20,21,25], index=["one","two","three"])
# df["나이"] = age  # matching되지 못한 index는 NaN

display(df)

~~~

---

## DataFrame에서 연산을 통한 column 추가

~~~python

# 기존에 생성된 DataFrame에 연산을 통해 column을 추가할 수 있다. 

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

df["학점"] = df["학점"] * 2
df["장학여부"] = df["학점"] > 3.0

display(df)

~~~

---

## DataFrame에서 column 삭제

~~~python

# DataFrame에서 특정 column 삭제

# drop() 이용

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])
display(df)

new_df = df.drop("등급", axis=1, inplace=False) # inplace=True 원본 변경
                                                 # return None
                                                 # inplace=False 원본 변경 X
                                                 # 삭제된 DataFrame return  
display(new_df)

display(df)

~~~

---

## DataFrame row indexing (1)

~~~python

# DataFrame의 Row(행) Indexing

# 숫자 index 이용

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

# Column indexing
# df["이름"]           # OK. Series return
# df["이름":"학년"]    # Error. Slicing 불가능
# df[["이름","학점"]]  # OK. Fancy indexing 가능
                       # 참고로 Boolean Indexing은 Row Indexing 처리

# Row indexing(index 숫자사용)   
# df[1]               # Error. 단일 indexing이 안된다.
# df[1:3]             # OK. slicing 가능 - slicing한 결과는 View
# df[2:]              # OK. 일반적인 slicing가능(row)
# df[[1,3]]           # Error. row에 대한 index숫자를 이용한 Fancy indexing은 안됨.

~~~

---

## DataFrame row indexing (2)

~~~python

# DataFrame의 row indexing

# 지정한 index 이용

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

# df["one"]           # Error. index를 이용해서 단일 row 추출 안됨
# df["one":"three"]   # OK. index를 이용한 row slicing가능
# df["one":]          # OK. 일반적인 slicing이기 때문에 가능  
# df["two":-1]        # Error. 숫자 index와 index를 혼합사용한 slicing안됨. 
# df[["one","two"]]   # Error. 

~~~

---

## DataFrame row indexing (3)

~~~python

# DataFrame의 row indexing

# loc[]를 이용 (일반적인 row indexing 방법)

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

# df.loc["one"]           # OK. loc와 index를 이용하면 단일 row 추출 가능. 
                          # 결과는 Series
# df.loc["one":"three"]   # OK. loc를 index를 이용하면 row slicing가능.
# df.loc["one":]          # OK. loc를 index를 이용하면 row slicing가능.
# df.loc["one":-1]        # Error. index 혼합 사용은 안됨.
# df.loc[["one","three"]] # OK. loc와 index를 이용한 fancy indexing 가능.

# loc와 숫자 index를 이용하는 방법은 모두 Error
# 숫자 index를 이용할때는 iloc를 이용
# df.loc[0]               # Error. loc와 숫자 index를 이용한 단일 row 추출 안됨.
# df.loc[0:3]             # Error. loc와 숫자 index를 이용한 slicing 안됨.
# df.loc[[0,3]]           # Error. loc와 숫자 index를 이용한 fancy indexing 안됨.

~~~

---

## DataFrame row indexing (4)

~~~python

# DataFrame의 row indexing

# loc[]를 이용, 특정 행과 열 slicing

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

# df.loc["one":"three"]                   # OK. row slicing
# df.loc["one":"three","이름"]            # OK. row slicing + column 선택 => Series 리턴
# df.loc["one":"three","이름":"학년"]     # OK. row slicing + column slicing
# df.loc["one":"three",:]                 # OK. row slicing + column slicing
# df.loc["one":"three",["이름","학점"]]   # OK. row slicing + column fancy slicing

~~~

---

## DataFrame boolean indexing

~~~python

# DataFrame에서 boolean mask 생성 후 

# loc를 이용한 boolean indexing 처리

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

# 학점이 1.5점을 초과하는 학생의 이름과 학점을 DataFrame으로 출력!

print(df["학점"] > 1.5)     # boolean MASK, Series로 return

display(df.loc[df["학점"] > 1.5, ["이름","학점"]]) 

~~~

---

## DataFrame boolean indexing 응용

~~~python

# DataFrame boolean indexing 응용

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

# 이름이 "박동훈"인 사람을 찾아 이름과 학점을 DataFrame으로 출력
display(df.loc[df["이름"] == "박동훈", ["이름","학점"]])

# 학점이 (1.5,2.5)인 사람을 찾아 학과,이름,학점을 DataFrame으로 출력
display(df.loc[(df["학점"] > 1.5) & (df["학점"] < 2.5) , "학과":"학점"])

# 학점이 3.0을 초과하는 사람을 찾아 등급을 "A"로 설정하세요.
df.loc[df["학점"] > 3.0, "등급"] = "A"
display(df)

~~~

---

## DataFrame iloc를 이용한 row indexing

~~~python

# DataFrame iloc를 이용한 row indexing

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

# df.iloc[1]           # OK. index 숫자를 이용해 단일 행 선택가능.
# df.iloc[1:4]         # OK. index 숫자를 이용해 row slicing가능
# df.iloc[1:4,0:2]     # OK. index 숫자를 이용해 row slicing + column slicing가능
# df.iloc[1:4,[0,2]]   # OK. index 숫자를 이용해 row slicing + column fancy indexing가능
# df.iloc[[1,4],[0,2]] # OK. index 숫자를 이용해 row fancy indexing +
                       #     column fancy indexing가능
# df.iloc[0,0]         # OK. 특정 위치의 값 도출

~~~

---

## DataFrame row 추가

~~~python

# loc를 이용한 DataFrame의 row 추가

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

df.loc["six",:] = ["컴퓨터","최길동",3.1,3,"A"] # index에 없기때문에 add
display(df)

df.loc["seven","이름":"학점"] = ["박길동",3.1] # index에 없기때문에 add
# df.loc["seven",["이름","등급"]] = ["박길동","A"] # Fancy indexing도 가능

df.loc["seven","이름":] = ["이길동",3.1,1,"A"]  # index에 존재하면 replace
display(df)

~~~

---

## DataFrame row 삭제

~~~python

# DataFrame row 삭제

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

new_df = df.drop("학년", axis=1) # inplace=False, 열 삭제, 원본 변경 X
display(new_df)

new_df = df.drop("one") # inplace=False, 행 삭제(axis=0), 원본 변경 X
                        # df.drop("one123") key를 못 찾으면 Error
display(new_df)

# Fancy indexing 사용 가능
new_df = df.drop(["one","three"]) # inplace=False, 행 삭제(axis=0), 원본 변경 X
display(new_df)

# slicing으로는 삭제 안됨.
# new_df = df.drop("one":"three") # slicing 삭제는 Error

~~~


End.

{% include links.html %}
