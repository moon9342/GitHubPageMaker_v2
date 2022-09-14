---
title: Pandas DataFrame
sidebar: pandas_sidebar
summary: "Pandas의 자료구조인 DataFrame에 대해서 살펴보도록 하겠습니다."
permalink: pandas_dataframe.html
folder: pandas
---

## DataFrame 생성

`DataFrame`은 2차원 형태입니다. 우리가 흔히 사용하는 Excel과 같은 형태라고 생각
하시면 됩니다. 즉, 행과 열을 가지고 Table구조라고 생각하시면 됩니다.
<br><br>

Series를 열벡터(vector)라고 한다면 DataFrame은 여러 개의 열백터들이 같은
행 index를 기준으로 줄지어 결합된 2차원 matrix입니다.
<br><br>

말이 좀 어려웠는데 그림으로 보면 이해를 해 보죠.

{% include image.html
file='pandas-dataframe-architecture.png'
%}

`DataFrame`의 구조가 위와 같기 때문에 DataFrame을 생성하기 위해서는 같은 길이(원소의 개수가 동일한)
1차원 배열 여러 개가 필요합니다.
<br><br>

dictionary를 이용해서 DataFrame을 생성할 경우 아래의 그림과 같은 형태가 되어야 합니다.

{% include image.html
file='pandas-dictionary-to-dataframe.png'
%}

~~~python

# pandas DataFrame 생성 (Dictionary이용)

# 데이터의 개수가 맞지 않으면 DataFrame생성 시 Error
# dictionary의 key가 DataFrame의 column명으로 사용
# DataFrame은 Series의 집합으로 구성(Series가 DataFrame의 각각의 column에 해당)

import numpy as np
import pandas as pd

data = {"names" : ["아이유", "김연아", "홍길동", "강감찬", "이순신"],
        "year" : [2015, 2016, 2015, 2017, 2016],
        "point" : [1.5, 2.0, 3.1, 1.1, 2.7] }

df = pd.DataFrame(data)

print("shape : {}".format(df.shape)) # shape : (5, 3)
print("size : {}".format(df.size)) # size : 15
print("ndim : {}".format(df.ndim)) # dimension : 2

display(df) # print(df)보다는 display(df)를 사용

~~~

---

## DataFrame의 index, columns, values

~~~python

# DataFrame의 index, columns, values

import numpy as np
import pandas as pd

data = {"names" : ["아이유", "김연아", "홍길동", "강감찬", "이순신"],
        "year" : [2015, 2016, 2015, 2017, 2016],
        "point" : [1.5, 2.0, 3.1, 1.1, 2.7] }

df = pd.DataFrame(data)

display(df)

print("index : {}".format(df.index))     # RangeIndex(start=0, stop=5, step=1)
print("index type : {}".format(type(df.index)))  #  <class 'pandas.core.indexes.range.RangeIndex'>
print("columns : {}".format(df.columns)) # Index(['names', 'year', 'point'], dtype='object')
print("columns type : {}".format(type(df.columns))) # <class 'pandas.core.indexes.base.Index'>

print(df.values)     # 2차원 numpy array

print(type(df.values)) # <class 'numpy.ndarray'>

~~~

---

## DataFrame의 index와 column에 이름 지정

~~~python

# DataFrame의 index와 column에 이름 지정

import numpy as np
import pandas as pd

data = {"names" : ["아이유", "김연아", "홍길동", "강감찬", "이순신"],
        "year" : [2015, 2016, 2015, 2017, 2016],
        "point" : [1.5, 2.0, 3.1, 1.1, 2.7] }

df = pd.DataFrame(data)

df.index.name = "sNum"
df.columns.name = "sInfo"

display(df)

~~~

---

## DataFrame의 index와 column 이름 변경

~~~python

# rename()을 이용하면 행 index 또는 열 이름의 일부를
# 변경할 수 있다.

import numpy as np
import pandas as pd

data = {"names" : ["아이유", "김연아", "홍길동", "강감찬", "이순신"],
        "year" : [2015, 2016, 2015, 2017, 2016],
        "point" : [1.5, 2.0, 3.1, 1.1, 2.7] }

df = pd.DataFrame(data)

display(df)

new_df = df.rename(columns={'year':'입학연도', 'point':'학점'}, 
                   index={0:'one'},
                   inplace=False)

display(new_df)

~~~

---

## DataFrame의 특정 column을 index로 설정

~~~python

# set_index() 함수를 적용하면 특정 column을 새로운 행 index로
# 설정할 수 있다. 

import numpy as np
import pandas as pd

data = {"names" : ["아이유", "김연아", "홍길동", "강감찬", "이순신"],
        "year" : [2015, 2016, 2015, 2017, 2016],
        "point" : [1.5, 2.0, 3.1, 1.1, 2.7] }

df = pd.DataFrame(data)

display(df)

new_df = df.set_index('names', inplace=False)

display(new_df)

~~~

---

## DataFrame의 행, 열의 위치 바꾸기

전치행렬과 같은 개념입니다. DataFrame의 행과 열을 서로 맞바꾸는 방법입니다.

~~~python

import numpy as np
import pandas as pd

data = {"names" : ["아이유", "김연아", "홍길동", "강감찬", "이순신"],
        "year" : [2015, 2016, 2015, 2017, 2016],
        "point" : [1.5, 2.0, 3.1, 1.1, 2.7] }

df = pd.DataFrame(data)

display(df)

new_df = df.set_index('names', inplace=False)

display(new_df)

t_df = new_df.transpose()

display(t_df)

~~~

---

## CSV 파일을 이용한 DataFrame 생성 (1)

[실습 CSV 파일(student.csv)](/assets/downloads/student.zip){: target="_blank" }

~~~python

# CSV 파일을 이용한 DataFrame 생성

# ","로 구분된 CSV파일을 이용하여 DataFrame을 생성
# 파일의 1행의 내용으로 column 설정

# student.csv
"""
이름,입학연도,성적
아이유,2015,1.5
김연아,2016,2.0
홍길동,2015,3.1
강감찬,2017,1.1
이순신,2016,2.7
"""
import numpy as np
import pandas as pd

df = pd.read_csv("./data/student.csv")

display(df)
~~~

---

## CSV 파일을 이용한 DataFrame 생성 (2)

[실습 CSV 파일(movies.csv)](/assets/downloads/movies.zip){: target="_blank" }

~~~python

# CSV 파일을 이용한 DataFrame 생성

# 제공된 MovieLens Data Set의 movies.csv파일을 읽어 DataFrame으로 생성
# pandas는 문자열처리 시 numpy보다 효율적인 방법을 제공

import numpy as np
import pandas as pd

df = pd.read_csv("./data/movies.csv")

display(df.head())   # 상위 5개의 행을 출력
print(df.shape)

~~~

---

## Database에서 data를 읽어와 DataFrame으로 생성

`MySQL 8.0` 버전을 이용하여 다음 링크의 Script를 실행해 테이블을 생성합니다.
데이터베이스명은 자유롭게 설정하셔도 됩니다. 단, 코드에서 데이터베이스명을 정확하게 입력해야 합니다.
<br><br>

아래의 코드를 실행시키기 위해서는 `pymysql` module이 필요합니다. 아래의 명령으로
module을 설치합니다.
<br><br>

{% include callout.html
type="danger"
content="**conda install pymysql**"
%}

[실습 SQL 파일(BookTableDump.zip)](/assets/downloads/BookTableDump.zip){: target="_blank" }

~~~python

# MySQL Database에서 data를 읽어와 DataFrame으로 생성
# MySQL 8.0버전 설치 후 데이터베이스 생성
# 제공된 Script를 이용하여 Database 구축(Table 생성)
  
import pymysql.cursors
import numpy as np
import pandas as pd

# 데이터베이스 connection
conn = pymysql.connect(host="localhost",
                       user="root",
                       password="test1234",
                       db="lecture_db",
                       charset="utf8")
# 데이터 selection
keyword = "java"
sql = "SELECT bisbn,btitle,bauthor,bprice FROM book WHERE btitle like '%{}%'".format(keyword)
try:
    df = pd.read_sql(sql, con=conn)
    display(df)
except Exception as err:
    print(err)
finally:
    conn.close()

~~~

{% include image.html
file='pandas-sql-to-dataframe.png'
%}

---

## DataFrame을 JSON 파일로 저장

~~~python

# DataFrame을 JSON 파일로 저장

# Database에서 data를 읽어와 DataFrame으로 생성 한 후
# 생성된 DataFrame을 JSON형태로 파일에 저장

import pymysql.cursors
import numpy as np
import pandas as pd

# 데이터베이스 connection
conn = pymysql.connect(host="localhost",
                       user="root",
                       password="test1234",
                       db="lecture_db",
                       charset="utf8")
# 데이터 selection
keyword = "java"
sql = "SELECT bisbn,btitle,bauthor,bprice FROM book WHERE btitle like '%{}%'".format(keyword)
df = pd.read_sql(sql, con=conn)
conn.close()

# DataFrame을 json형식으로 파일에 저장
# 총 4가지 각기 다른 형식으로 저장이 가능.
# Unicode로 파일을 생성한 후 저장해야 한글이 정상 처리됨.
# 저장할 폴더까지는 생성이 되어 있어야 함.

with open('./data/json/books_orient_columns.json', 'w', encoding='utf-8') as file:
    df.to_json(file, force_ascii=False, orient="columns") # default
    # json이 생성되고 column명이 json의 key값

with open('./data/json/books_orient_record.json', 'w', encoding='utf-8') as file:
    df.to_json(file, force_ascii=False, orient="records") 
    # json배열이 생성되고 배열의 요소가 json
    # 각 행이 배열 요소의 json으로 생성

with open('./data/json/books_orient_index.json', 'w', encoding='utf-8') as file:
    df.to_json(file, force_ascii=False, orient="index") 
    # json이 생성되고 각 행의 index가 key값, 각 행의 데이터가 value값.

with open('./data/json/books_orient_values.json', 'w', encoding='utf-8') as file:
    df.to_json(file, force_ascii=False, orient="values") 
    # json 배열이 생성되고 배열의 요소가 다시 각 행의 value로 구성된 json 배열
    
~~~

---

## JSON 파일을 읽어 DataFrame 생성

~~~python

# JSON 파일을 읽어서 DataFrame 생성

import numpy as np
import pandas as pd
import json

# 기본적인 파일처리
# file = open("./data/json/books_orient_columns.json","r", encoding='utf-8')
# dict_books = json.load(file)
# file.close()

with open("./data/json/books_orient_columns.json","r", encoding='utf-8') as file:
    dict_books = json.load(file)   # json을 읽어서 python의 dictionary 생성

print(type(dict_books))  # <class 'dict'>
print(dict_books)

df = pd.DataFrame.from_dict(dict_books, orient='columns') # orient=index
                                                          # orient=columns(default)
display(df)

~~~

---

## Open API(JSON)를 이용한 DataFrame 생성

~~~python

# 결과를 JSON으로 return하는 open API를 이용한 DataFrame 생성

# 결과를 JSON으로 return하는 open API를 이용한 DataFrame 생성

import numpy as np
import pandas as pd
import json
import urllib

# 영화진흥위원회 일일 Box Office 순위 Open API 이용
movie_url = "http://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json?key=452ebde09792c086e5d37811e590d162&targetDt=20220101"
load_page = urllib.request.urlopen(movie_url)

print(load_page)  # <http.client.HTTPResponse object at 0x00000209A35AED48>

json_page = json.loads(load_page.read())

print(type(json_page)) # <class 'dict'>
print(json_page)  

movie_data = dict()
rank_list = list(); 
title_list = list(); 
sales_list = list()

for tmp_dict in json_page["boxOfficeResult"]["dailyBoxOfficeList"]:
    rank_list.append(tmp_dict["rank"])
    title_list.append(tmp_dict["movieNm"])
    sales_list.append(tmp_dict["salesAmt"])
        
movie_data["제목"] = title_list
movie_data["당일매출액"] = sales_list

df = pd.DataFrame(movie_data)
df.index = rank_list
df.index.name="순위"

display(df)

~~~

{% include image.html
file='pandas-openapi-to-dataframe.png'
%}

---

## DataFrame 생성 시 index와 column 지정

~~~python

# DataFrame을 생성하는 시점에 column과 index를 지정할 수 있다.

# index의 개수와 데이터의 개수가 일치하지 않은 경우 Error
# column의 경우 dictionary의 key와 일치하지 않는 경우
# NaN(Not a Number)로 설정, NaN은 연산가능

# 참고(R언어와 비교)
# R에서 NA는 결치값으로 잘못된 값을 의미, NULL은 존재하지 않는 객체를 지정할 때 사용
# R에서 NaN은 Not Available Number의 의미로 수학적 연산이 불가능한 수를 의미
# Pandas에서는 NaN은 missing data in pandas의 의미(즉, R의 NA의 의미)
# python에서 R의 NULL을 None으로 표현한다.(None의 비교는 is 연산자 이용)

# NaN의 literal -> np.nan으로 표현

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","평균학점","학년","등급"],
                  index = ["one","two","three","four","five"])

display(df)

~~~

---

## DataFrame의 기본 분석 함수

~~~python

# DataFrame은 기본 분석 함수를 제공

# count, mean, std, min, 25%, 50%, 75%, max 제공

import numpy as np
import pandas as pd

data = {"이름" : ["이지은", "박동훈", "홍길동", "강감찬", "오혜영"],
        "학과" : ["컴퓨터", "기계", "철학", "컴퓨터", "철학"],
        "학년" : [1, 2, 2, 4, 3],
        "학점" : [1.5, 2.0, 3.1, 1.1, 2.7]}

df = pd.DataFrame(data, 
                  columns=["학과","이름","학점","학년","등급"],
                  index = ["one","two","three","four","five"])

display(df.describe())  # 수치연산이 가능한 column에 대해 통계치를 출력

import numpy as np
arr = np.arange(1,8)  # [1 2 3 4 5 6 7]
print(np.percentile(arr,[50]))   # 2사분위
      
arr = np.arange(1,10)  # [1 2 3 4 5 6 7 8 9]
print(np.percentile(arr,[50, 75])) # 1,3 사분위

~~~


End.

{% include links.html %}
