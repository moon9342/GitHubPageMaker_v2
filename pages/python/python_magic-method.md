---
title: Python magic method
sidebar: python_sidebar
summary: "magic method의 개념과 사용에 대해 살펴보도록 하겠습니다."
permalink: python_magic-method.html
folder: python
---

## magic method

* method 이름 앞뒤에 더블 언더스코어(`__`)가 붙어 있습니다. (대표적인 magic method : `__init__`)
* class안에 정의할 수 있는 특수한 형태의 method입니다.
* 일반적으로 연산자(+, -, >, <)가 동작할 때 각각의 데이터 타입에 맞는 method로
  오버로딩하여 백그라운드에서 연산을 수행합니다.
<br><br>

일반적으로 class의 method를 호출할 때 우리는 직접 magic method를 호출하지 않습니다. 특정 상황이 되면
내부적으로 수행되게 됩니다.

~~~python

# Magic Method

class Student(object):
    def __init__(self,name,dept):
        self.name = name
        self.dept = dept
        print("{1} 학과 {0} 학생이 생성되었습니다.".format(self.name,self.dept))

    def __del__(self):
        print("소멸자가 호출되었습니다.!!")


stu_1 = Student("홍길동","컴퓨터")    # __init__ 호출

del stu_1                             # __del__ 호출

~~~

우리가 흔히 사용하는 연산자(operator)는 내부적으로 magic method를 호출하게 됩니다. 다음의 예를 보죠.

~~~python

# Magic Method - __add__ method

class MyInt(int):
    pass

mynum = MyInt(100)

print(mynum + 200)         # + 연산자 이용

print(mynum.__add__(200))  # __add__ magic method 명시적 호출

~~~

어떤 method가 실행되는지 몰라도 결과값은 똑같은데
이렇게 자동으로 호출되는 매직 메소드에 대해서 알고있어야 할 필요가 있을까요?
<br><br>

다음의 예제를 한번 보면 magic method의 활용에 대해서 이해할 수 있을 듯 합니다.

~~~python

# Magic Method - 필요성

class Car(object):
    def __init__(self, model, price):
        self.model = model
        self.price = price

car_1 = Car("Genesis G70", 5000)

# 인스턴스 정보 출력
print(car_1)         # car_1 객체의 메모리 주소가 출력된다.

###########################################

class Car(object):
    def __init__(self, model, price):
        self.model = model
        self.price = price

    def __str__(self):
        return "차종은 {} 이고 차량 가격은 {} 입니다.(__str__ 이용)".format(self.model,self.price)

    # def __repr__(self):
    #     return "차종은 {} 이고 차량 가격은 {} 입니다.(__repr__ 이용)".format(self.model,self.price)


car_1 = Car("Genesis G70", 5000)

# 인스턴스 정보 출력
print(car_1)         # __str__ method 또는 __repr__ method가 호출되어서 객체의 정보가 출력된다.

~~~

이번에는 operator에 대해서 magic method를 활용하는 방법에 대해서 알아보도록 하겠습니다.
다음의 코드를 보도록하죠

~~~python

# Magic Method - operator 응용

class Car(object):
    def __init__(self, model, price):
        self.model = model
        self.price = price

car_1 = Car("Genesis G70", 5000)
car_2 = Car("Sonata", 3000)

print(car_1 < car_2)   # 어느차가 더 비싼차인지를 알아보자!!
                       # 이 코드는 당연히 Error

class Car(object):
    def __init__(self, model, price):
        self.model = model
        self.price = price
    def __lt__(self, other):
        if self.price < other.price:
            return "{} 가격이 더 낮습니다.".format(self.model)
        else:
            return "{} 가격이 같거나 더 높습니다.".format(self.model)    

car_1 = Car("Genesis G70", 5000)
car_2 = Car("Sonata", 3000)

print(car_1 < car_2)  # Magic Method 활용

~~~  

End.

{% include links.html %}
