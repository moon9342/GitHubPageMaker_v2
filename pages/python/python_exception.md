---
title: Python exception
sidebar: python_sidebar
summary: "exception의 개념과 사용에 대해 살펴보도록 하겠습니다."
permalink: python_exception.html
folder: python
---

## exception

python의 exception처리는 `try ~ except` 구문을 사용합니다. `Java`언어의
예외처리와 아주 흡사하게 동작하며 예제를 통해 다양한 처리를 살펴보도록 하겠습니다.

~~~ python

# Python Exception
"""
try:
    (예외 발생 가능) statements
except Exception:
    예외가 발생했을 때 실행되는 문장
else:
    예외가 발생하지 않았을 때 실행되는 문장
finally:
    예외 발생 유무에 상관없이 실행되는 문장
"""    

def my_func(list_data):

    sum = 0

    try:
        sum = list_data[0] + list_data[1] + list_data[2]

        if sum < 0:
            raise Exception("User Define Exception")

    except ZeroDivisionError as err:
        print("0으로 나눌수 없습니다." + str(err))
    except Exception as err:
        print("Exception 발생 : " + str(err))         
    else:
        print("Exception이 없습니다.")
    finally:
        print("무조건 수행됩니다.")

# my_list = [1,2,3,4]    
# my_func(my_list)       # Exception이 없습니다.
#                        # 무조건 수행됩니다.

# my_list = [1,2]    
# my_func(my_list)       # Exception 발생 : list index out of range
#                        # 무조건 수행됩니다.

my_list = [1,2,-10]    
my_func(my_list)       # Exception 발생 : User Define Exception
                       # 무조건 수행됩니다.

~~~

---

## file 처리

Python의 기본 파일 처리과정에 대해서 알아보겠습니다. 조금 더 자세한 파일처리 과정은 pandas에서
살펴보도록 하겠습니다.

~~~ python

file1 = open("student_score.txt","r")
file2 = open("backup.txt","w")

while True:
    line = file1.readline()
    print("읽어들인 라인 : {}".format(line))
    if not line:
        break;
    file2.write(line)

file1.close()
file2.close()

~~~

---

## with 구문

일반적으로 file처리를 하거나 session처리를 할 때 기본적으로 다음과 같은
순서를 거치게 됩니다.
<br><br>

**open() -> CRUD -> close()**
<br><br>

Python에서 `with` 구문을 이용하면 명시적으로 resouce를 close() 해 주지 않아도
자동으로 close()를 해 줄 수 있습니다. with block을 벗어나는 순간 자동으로 close()처리가
일어나게 됩니다.
<br><br>

위에서 살펴본 파일 처리를 with 구문으로 바꾸어보면 다음과 같습니다.

~~~python

with open("student_score.txt","r") as file1, open("backup.txt","w") as file2:
    while True:
        line = file1.readline()
        print("읽어들인 라인 : {}".format(line))
        if not line:
            break;
        file2.write(line)

~~~

End.

{% include links.html %}
