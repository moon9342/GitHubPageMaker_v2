---
title: Java Data Type
sidebar: java_sidebar
summary: "Java언어의 Data Type에 대한 내용입니다."
permalink: java_data-type.html
folder: java
---

## variable

프로그램을 잘 하기 위해서는 `data`(값)를 잘 다루어야 합니다. 이런 데이터를 안전하게 그리고 공간적인 효율성을
추구하기 위해 우리는 `data structure`를 배우는 것이고 역시 이런 데이터 사용에 시간적인 효율성을 추구하기 위해
`algoirthm`을 공부하는 것이지요.
<br><br>

여하간 이 데이터를 다루는 것은 매우 중요한 일이며 이런 데이터를 저장하는 가장 기본적인 메모리 공간을 우리는
`variable`이라고 합니다.
<br><br>

Java언어는 `strong type language`입니다. 강타입언어라고 하는데 변수를 선언할 때 변수의 데이터 타입을 지정해서
변수를 선언합니다. `JavaScript`는 아시다시피 `weak type language`입니다. 변수 선언시 타입을 명시하지 않고
변수에 값이 할당될 때 `type inference`(타입 추론)을 통해 변수의 타입을 동적으로 결정합니다.
<br><br>

하지만 Java역시 버전이 올라가면서 함수형 언어의 장점을 받아들이기 시작했고 타입 추론 방식의 `var` keyword를 이용한
동적 타입 변수를 제공하기 시작했습니다. 이건 참고로 알아두도록 하죠.
<br><br>

`variable`은 선언한 이후에 반드시 초기화 과정을 거친 후에 사용해야 합니다. 메모리는 여러 프로그램이 공유하는 자원이기
때문에 다른 프로그램에 의한 `garbage value`가 선언된 변수 안에 남아 있기 때문입니다. 그래서 초기화가 반드시 필요합니다.
<br>

---

## 식별자 명명규칙

Java의 식별자는 다음과 같은 특징을 가집니다.
<br>

* 대소문자가 구별되며 길이의 제한은 없습니다.
* 당연히 예약어를 사용하면 안됩니다
* 숫자로 시작하면 안됩니다.
* 특수문자는 `_`와 `$`만 허용됩니다.
<br>

그 외 필수적이지는 않지만 권장하는 naming convention은 다음과 같습니다.
<br>

* 클래스 이름의 첫 글자는 항상 대문자를 사용합니다. (`Pascal case`)
* 여러 단어로 이루어진 이름은 단어의 첫 글자를 대문자로 합니다. (`Camel case`)
* 상수의 이름은 모두 대문자로 합니다. 여러 단어로 이루어진 경우 `_`로 구분합니다.(`Snake case`)
<br>

---

## 데이터 타입

`data type`은 크게 `primitive type`과 `reference type`으로 나눠집니다.
<br><br>

`primitive type`은 총 8개가 있으며 각 타입에 따른 크기는 아래의 그림에 정리되어 있습니다.
<br>

{% include image.html
file='java-primitive-data-type-size.png'
%}

문자형인 `char`는 문자를 내부적으로 정수(유니코드)로 저장하기 때문에 정수형과 별반 다르지 않고, 정수형
또는 실수형과 연산도 가능합니다. 하지만 `boolean`은 다른 기본형과의 연산이 불가능합니다. 묵시적인 형 변환이
일어나지 않습니다.

---

## 상수(constant)

상수는 변수와 마찬가지로 값을 저장할 수 있는 공간이지만 변수와 달리 한번 값을 저장하면 다른 값으로 변경할
수 없습니다. 상수를 선언하는 방법은 변수와 동일하면 단지 변수의 타입 앞에 `final`을 붙여주면 됩니다.
<br>

> final int MAX_SPEED = 100;
<br>

당연히 상수는 선언과 동시에 초기화가 이루어져야 합니다.

---

## 형변환(Type Casting)

모든 변수와 값에는 데이터 타입이 있습니다. 이런 데이터 타입을 다른 데이터 타입으로 강제로 변환시킬 수 있는데
이를 `type casting`(형 변환)이라고 합니다.
<br><br>

형변환 방법은 아주 간단합니다. `()`를 이용하면 됩니다.
<br>

> (타입)피연산자


End.

{% include links.html %}
