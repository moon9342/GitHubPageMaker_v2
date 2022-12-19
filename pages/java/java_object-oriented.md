---
title: Java Object Oriented
sidebar: java_sidebar
summary: "Java 객체지향에 대한 내용입니다."
permalink: java_object-oriented.html
folder: java
---

## Java 객체지향

이번에는 Java에서 나오는 객체지향 개념에 대해서 하나하나 알아보도록 하겠습니다. 익숙치 않은 용어와 개념이
많이 나오니 각 용어에 대한 개념을 잘 이해해 보시기 바랍니다.

---

## class

class는 현실세계의 설계도와 같은 개념입니다. 
<br>

{% include image.html
file='java-real-world-car.png'
%}
<br>

프로그램세계에서 class의 의미는 다음 그림과 같습니다.
<br>

{% include image.html
file='java-program-world-car-class.png'
%}
<br>

> `class`는 객체 모델링의 수단입니다.
<br>

관점을 바꿔서 보면 `class`는 `ADT`라고 볼 수 있습니다.
<br>

* `ADT`(Abstract Data Type)
* 사용자 정의 데이터 타입
* 기존 데이터 타입을 이용해 새로운 데이터 타입을 만드는 것.

---

## instance

class에 정의된 field와 method를 사용하기 위해서는 메모리에 일정 공간을 확보 해야 합니다. 이렇게 메모리상에 
생성된 일정 공간을 class의 `instance`,혹은 객체(object)라고 합니다.
<br><br>

Java program은 class와 그 class에서 파생된 instance를 가지고 동작합니다.
<br>

{% include image.html
file='java-instance-create.png'
%}

---

## constructor

Java의 모든 class는 적어도 하나 이상의 constructor를 가집니다. 
<br><br>

method와 비슷한 구조를 가지지만 method는 아닙니다. 리턴타입이 존재하지 않아요.
<br><br>

constructor의 이름은 class의 이름과 같아야 합니다.
<br><br>

객체가 생성될 때 `new` 키워드와 함께 호출되며 객체가 가지는 필드에 대한 초기화 등의 작업을 주로 합니다.
<br><br>

만약 생성자를 명시적으로 작성하지 않았다면 javac compiler에 의해서 default 생성자가 자동으로 추가됩니다. 
(default 생성자 : 인자를 가지지 않고 어떠한 일도 하지 않는 생성자를 의미합니다.)
<br><br>

* 실습: constructor를 이용하여 instance 생성(new keyword 이용)

* 생성된 instance를 `. operator`를 이용해서 사용.

* instance가 생성되고 사용되는 과정을 코드와 그림으로 이해.

---

## JVM의 메모리 구조

JVM은 메모리를 용도에 따라 다음의 5개의 부분으로 나누어서 관리합니다.
<br>

* Register
* Runtime Constant Pool
* Method Area
* Heap
* Call Stack( 일반적으로 Stack이라고 함 )
<br>

---

## class member & instance member

`class member`는 static keyword로 선언합니다.
<br><br>

class member는 모든 instance(객체)가 공유하는 field와 method로 class명으로 사용이 가능합니다. 
당연히 instance를 이용해서 사용하는 것도 가능하구요.
<br><br>

`instance member`는 각 instance가 독립적으로 메모리 공간을 확보하는 field와 method로 
instance를 이용해야 사용가능합니다. 
<br><br>

class variable( static으로 지정된 field )의 사용은 객체의 존재 유무와 관련이 없습니다. class가 메모리에 load될 때
`Method Area`에 단 한번 class variable를 위한 메모리가 할당되고 초기화됩니다. 따라서, 객체를 생성하지 않더라도
class variable에 대한 접근이 가능합니다.
<br>

{% include image.html
file='java-instance-variable.png'
%}

{% include image.html
file='java-instance-variable-example.png'
%}

---

## method overloading

같은 이름의 method가 여러 개 존재하는 것을 의미합니다.
<br><br>

method가 갖는 매개변수의 개수와 데이터 타입을 이용하여 method의 이름은 같지만 서로 다른 method로 구분하는
개념입니다. 
<br><br>

method에 대한 overloading이 constructor에도 적용된다. (constructor overloading)

---

## package & import

source code를 효율적으로 관리하고 재사용하는 방법입니다.
<br><br>

기존에 사용했던 Java Code를 재 사용하는 가장 원시적인 방법은 class file이나 source file을
우리 program이 있는 folder에 위치하는 거죠. 하지만 결국 여러 개의 copy본을 유지해야 하므로 
결과적으로 maintenance에 문제가 발생합니다.
<br><br>

가장 효과적인 방법은 class의 source code나 class file을 package로 묶어서 재사용하는 방법입니다. 
<br><br>

Java의 모든 class는 특정 package에 반드시 포함됩니다. 우리가 package를 지정하지 않으면 자동으로
`default package`에 포함되게 됩니다. 
<br><br>

package명은 folder명과 같아야 합니다. 결국 package라는 논리적인 개념은 폴더라는 물리적 형태로 
나타나게 됩니다. 
<br><br>

package는 중첩되어 사용할 수 있고 package안에는 bytecode만을 포함시킬 수 도 있습니다. 
<br><br>

class를 package로 묶을 때에는 source code안에 package구문을 추가하면 됩니다. 다른 class를 사용할 때는 
반드시 class의 package를 명시해야 class를 사용할 수 있는데 이를 편하게 사용하기 위한 구문이 바로
`import`입니다.
<br><br>

---

## access modifier (접근제어자)

* `public` : 어디에서나 접근 가능한 field, method, constructor, class 선언에 사용
<br>

* `protected` : 기본적으로 default와 같지만 상속관계에 있는 class에서 field, method, constructor를 사용할 수 있도록 선언
<br>

* `default` : 같은 package내의 다른 class에서  접근 가능한 field, method, constructor, class 선언
<br>

* `private` : 다른 class에서는 접근할 수 없는 field, method, constructor 선언
<br>

---

## static block

static block은 class가 로드된 후 main thread에 의해서 main method가 호출되기 이전에 실행됩니다. 
주로 JNI 같은것을 이용할 때 사용됩니다. 

~~~java

package com.kakao.lecture;

class MyTest {

	int aaa;
	static int bbb = statciCall();
	
	public static int statciCall() {
		System.out.println("static method call()");
		return 100;
	}

	static {
		System.out.println("이것은 Static block의 연습입니다. bbb의 값은 : " + bbb);
	}

	public static void main(String args[]) {
		System.out.println("main() 호출");
	}

}

~~~

---

## inheritance

* 상속은 부모 class가 가진 내용을 자식 class가 물려받는 것이다.
<br>

* 상속이 필요한 이유

{% include image.html
file='java-not-inheritance-example.png'
%}

{% include image.html
file='java-inheritance-example.png'
%}
<br>

상속은 어떻게 표현되나?
<br><br>

* 자식 클래스 선언 시 extends keyword를 이용하여 부모 클래스를 상속
* Java에서의 상속은 단일 상속만 지원
* 상속은 항상 좋은가?

---

## 상속과 관련된 constructor의 특징

* constructor는 상속되지 않는다.
* 프로그래머가 명시하지 않아도 constructor가 호출될 때 자동적으로 상위 class의 constructor를 호출한다.
* sub class의 객체를 생성할 경우 먼저 super class의 객체가 만들어 져야 하기 때문에 super class의 constructor가  호출
* 주의할 것은 이때 자동적으로 호출되는 상위 class의 constructor는 인자가 없는 constructor가 호출된다는 것이다.
<br>

{% include image.html
file='java-inheritance-create-order.png'
%}

{% include image.html
file='java-inheritance-create-order-object.png'
%}

---

## method overriding

* overriding은 부모로부터 물려받은 method의 내용을 하위 class에서 다시 정의하는 것.
* method overriding의 조건
* method의 이름이 같아야 한다.
* method의 return type이 같아야 한다.
* method의 parameter개수와 type이 같아야 한다.
* access modifier는 부모의 것과 같거나 더 넓어야 한다.

---

## is-a Relationship

* `Sub class is a Super class`
* sub class type을 써야 하는 자리에 super class의 type을 사용해도 된다.
* Type casting

---

## this, super, this(), super()

* `this` : this keyword가 사용된 instance에 대한 reference
* `super` : this와 같은 의미이지만 실제로 가리키는 객체는 상위 클래스의 객체
* `this()` : constructor 내에서 overloading된 다른 constructor를 호출하기 위해 사용
* `super()` : constructor 내에서 상위 class의 constructor를 호출하기 위해 사용

{% include image.html
file='java-inheritance-create-order-example-1.png'
%}

{% include image.html
file='java-inheritance-create-order-example-2.png'
%}

---

## Abstraction

* abstraction은 객체들의 공통적인 특징(속성과 기능)을 뽑아내서 이름을 붙이는 것.
* C의 구조체 역시 추상화 ( 자료의 추상화 )
* C의 function, Java의 method 역시 추상화 ( 기능의 추상화 )
* class를 정의하는 과정 그 자체가 추상화.

---

## Encapsulation

* 데이터 구조와 데이터를 다루는 방법을 결합시켜 묶은 것을 지칭.
* class로 부터 파생된 객체가 독립적으로 역할을 수행하기 위해 데이터(field)와 기능(method)을 묶어서 관리
* Encapsulation을 하는 이유: class의 재활용성을 높이고 에러발생을 최소화

---

## Information Hiding

* class는 외부에서 알 필요가 없는 field나 method를 외부에 노출 시킬 필요가 없다.
* 불필요한 정보는 숨기고 최소한의 정보만을 이용하여 class를 쉽게 사용할 수 있어야 한다.
* Java에서는 access modifier를 이용하여 선언된 class의 외부에서의 접근을 제어하면서 정보를 은폐하는 방식을 이용한다.
* Encapsulation된 코드를 제공할 때 외부에서 함부로 수정, 호출해서는 안 되는 field, method를 보호하고
  프로그램의 안정적인 수행을 위해 필요.

---

## final keyword

* class 앞에 final이 붙었을 경우 : 해당 class를 상속할 수 없음을 의미
* field 앞에 final이 붙었을 경우 : 해당 field의 값을 변경할 수 없음을 의미 ( 상수화 )
* method 앞에 final이 붙었을 경우 : 해당 method를 하위 class에서 overriding할 수 없음을 의미

---

## abstract class

* abstract class의 특징 : abstract class는 instance를 생성할 수 없다
* abstract class를 상속받은 하위 class에서 abstract method를 overriding 한 후 instance 생성

---

## polymorphism & dynamic binding

다형성은 부모의 객체 타입으로 다양한 형태의 자식 객체를 생성,사용할 수 있는 것을 의미.

{% include image.html
file='java-polymorphism-1.png'
%}
<br>

다형성을 이용하여 상위 데이터 타입으로 객체의 타입을 통일해서 사용

{% include image.html
file='java-polymorphism-2.png'
%}
<br>

다형성을 이용해 객체들을 상위 데이터 타입으로 통일하면 프로그램의 처리가 쉬워짐.

{% include image.html
file='java-polymorphism-3.png'
%}
<br>

다음은 동적 바인딩에 대한 내용입니다.

{% include image.html
file='java-dynamic-binding-1.png'
%}

{% include image.html
file='java-dynamic-binding-2.png'
%}
<br>

다음과 같은 경우 에러가 발생하기 때문에 조심해야 합니다.

{% include image.html
file='java-dynamic-binding-error-1.png'
%}

{% include image.html
file='java-dynamic-binding-error-2.png'
%}

---

## interface

{% include image.html
file='java-interface-simple-mean.png'
%}

* 동일한 기능에 대해서는 같은 method 명을 사용하도록 강제
* method명은 지정해 주지만, 내부 구현은 각자에 맞게 구현

{% include image.html
file='java-interface-simple-mean-1.png'
%}

* interface는 abstract method와 public static final field로 구성되어 있다.
* interface 내부에 선언하는 field는 static final 이다. ( 상수 )
* interface 내부에 선언하는 method는 모두 abstract method 이다.
* interface를 구현하는 class에서 `implements` keyword를 이용하여 구현
* interface간의 상속도 존재하며 `extends` keyword로 상속을 구현
* interface와 interface를 구현한 class 간에도 `is-a relationship`이 성립한다.
* interface는 다중 구현을 허용

{% include image.html
file='java-interface-shape.png'
%}

{% include image.html
file='java-interface-implements.png'
%}


End.

{% include links.html %}
