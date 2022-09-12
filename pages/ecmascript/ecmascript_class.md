---
title: ECMAScript 6.0 class
sidebar: ecmascript_sidebar
summary: "ES6에서 도입된 class는 기존 prototype방식보다 더 쉽고 빠르게 새로운 객체를 생성할 수 있는 메커니즘을 제공합니다. 
자세히 이해해 보도록 하죠."
permalink: ecmascript_class.html
folder: ecmascript
---

## 객체지향의 기본적인 개념

먼저 프로그래밍 패러다임에 대해서 알아보도록 하죠. 프로그래밍 패러다임은 아주쉽게 생각하면
프로그래밍 방법론 이라고 생각하시면 됩니다. 어떤 방법으로 프로그래밍을 하느냐 하는 것이죠.
<br><br>

많은 방법론이 있겠지만 대표적인 것으로는 다음과 같은 것들이 있습니다.
<br><br>

* 함수형 프로그래밍 -> 구조적 프로그래밍, 절차적 프로그래밍
* 객체지향 프로그래밍
* 선언적 프로그래밍
<br><br>

이런 방법론이 있는데 가장 대표적인 두가지 방법에 대해 알아보도록 하겠습니다.

---

## 절차적 프로그래밍

절차적 프로그래밍의 특징은 다음과 같습니다.
<br><br>

* 프로그램을 모듈(함수)이라는 실행단위로 분할.
* 상위 모듈에서 하위 모듈을 호출하는 방식으로 프로그램을 구성
* 프로그램 작성 시 기능으로 세분화 한 다음 각각의 기능을 모듈로 제작
* 프로그램의 구조를 이해하기 쉽고 빠르게 프로그램을 작성할 수 있다.
* 프로그램 규모가 커지게 되면 유지보수와 기존 코드의 재사용에 한계가 나타나게 된다.

---

## 객체지향 프로그래밍

객체지향 프로그래밍의 특징은 다음과 같습니다.
<br><br>

* 현실세계의 해결해야 하는 문제를 그대로 프로그램으로 구현.
* 즉, 프로그램을 기능으로 세분화 하지 않고 프로그램을 구성하는 주체(객체)들을 파악하고
  그 객체들간의 data 흐름에 초점을 맞추어서 프로그램 작성
* 프로그램의 설계, 작성이 상대적으로 어려움.
* 이렇게 작성된 객체지향 프로그램은 유지보수와 재사용성에 이점이 있다.
<br><br>

간단하게 은행 프로그램을 예로 들어보면 될 듯 합니다.
<br><br>

객체지향 프로그램은 다음과 같은 방법을 이용합니다.
<br><br>

* 현실세계의 해결해야 하는 문제를 그대로 프로그램으로 표현.
* 해결해야 하는 문제들을 구성하고 있는 객체들을 파악
* 해당 객체의 `상태`와 `행위` 규정
* 이렇게 규정된 해당 객체의 내용을 프로그램적으로 표현
* 이때 사용되는 것이 class.
<br><br>

결국 class는 객체 모델링의 수단이라고 할 수 있습니다. 또한 instance를 만들어내는 수단이기도
하며 추상 데이터 타입(`ADT`)이기도 합니다.
<br><br>

추상 데이터 타입은 다음과 같이 설명할 수 있습니다.
<br><br>

* 사용자 정의 data type으로 사용.
* 추상 데이터 타입, reference data type 이라고도 한다.
* 기존 data type을 이용하여 새로운 data type을 만드는 작업.

---

## class

JavaScript는 `prototype` 기반의 객체지향 언어입니다. Java나 C++과 비교하여 여러 논쟁들이 존재하지만
객체지향 개념을 잘 따르고 있는 언어라고 할 수 있습니다.
<br><br>

`Prototype` 기반의 객체지향 언어이기 때문에 `class`가 필요없습니다. 우리가 알고 있는 것처럼 생성자 함수와
`prototype`을 통해 상속같은 기능을 구현할 수 있습니다.
<br><br>

기억을 상기하기 위해 코드를 보도록 하죠.

~~~javascript

var Person = (function() {

    function Person(name) {
        this.name = name
    }

    Person.prototype.sayHello = function() {
        console.log(`Hello ${this.name}`);
    }

    return Person;
}());

var me = new Person('Lee');

me.sayHello(); // Hello Lee

~~~

하지만 우리가 익숙한건 `class` 기반의 객체지향 개념이기 때문에 이 방식이 익숙치않고
JavaScript를 어렵게 만드는 요인으로 인식되고 있습니다.
<br><br>

`ES6`에서 도입된 `class`는 기존 `prototype`방식보다 더 쉽고 빠르게 새로운 객체를
생성할 수 있는 메커니즘을 제공합니다. 기존에 Java나 C++에 익숙한 사람들에게는 더더욱
그렇습니다.
<br><br>

**당연히 `ES6`가 기존의 `prototype`기반의 객체지향 모델을 폐지하고 새롭게 `class`기반의
객체지향 모델을 제공하는 것은 아닙니다.** 사실 `class`역시 함수이며 객체이고 기존의 방식을
`class`를 이용하는 것으로 바꾼 `syntactic sugar`라고 볼 수 있습니다.
<br><br>

`syntactic sugar`(문법적 설탕)이라는 표현은 JavaScript에 국한된 것이 아니라 일반적인
프로그래밍 언어에서 통용되는 표현입니다. 읽는 사람 혹은 작성하는 사람이 편하게 디자인된
문법이라는 뜻입니다.
<br><br>

`syntactic sugar`는 직관적이고 간결한 문법을 가지고 있습니다. 번거롭게 처리했던 코드가
짧아지고 가독성이 높아지는 효과가 있습니다. 모든 상황에 적용하는 것은 아니지만 명확히 쉽게
작성할 여지가 있는 코드라면 이 방식을 이용하는게 당연히 좋습니다.
<br><br>

`class`는 생성자 함수와 매우 유사하게 동작하지만 다음과 같은 차이점이 있습니다.
<br><br>

1. `class`를 `new` 연산자 없이 호출하면 에러가 발생합니다. 하지만 생성자 함수는 new 없이
   호출하면 일반 함수로 호출되죠.
2. `class`는 상속을 지원하는 `extends`와 `super` 키워드를 제공합니다. 하지만 생성자 함수는
   이런 키워드를 사용할 수 없습니다.
3. `class`는 `hoisting`이 발생하지 않는 것처럼 동작합니다. 하지만 함수 선언문으로 정의된
   생성자 함수는 함수 `hoisting`이, 함수 표현식으로 정의한 생성자 함수는 변수 `hoisting`이
   발생합니다.
4. `class`내의 모든 코드에는 암묵적으로 `strict mode`가 지정되어 실행됩니다. 하지만 생성자
   함수는 암묵적으로 `strict mode`가 적용되지 않습니다.
5. `class`의 `constructor`, `prototype method`, `static method`는 모두 property attribute값이
   false입니다. 다시말해 열거되지 않습니다.
<br><br>

이처럼 생성자 함수와 `class`간에는 차이가 있습니다. 따라서 단순한 `syntactic sugar`라고 보기보다는
객체를 생성하기 위한 또 다른 메커니즘으로 인식하는 것이 타당합니다.

---

## class의 정의

클래스는 `class` 키워드를 사용하여 정의합니다. 클래스 이름은 생성자 함수처럼 파스칼 케이스를
사용하는 것이 일반적이나 파스칼 케이스를 사용하지 않아도 에러가 발생하지는 않습니다.
<br><br>

또한 일반적이지 않지만 표현식으로 클래스를 정의할 수 있습니다. 이때 함수와 마찬가지로 이름을 가질 수
도 있고 갖지 않을 수도 있습니다.

~~~javascript

// 클래스 선언문
class Person {}

// 익명 클래스 표현식
const Person1 = class {};

// 기명 클래스 표현식
const Person2 = class MyClass {};

~~~

클래스를 표현식으로 정의할 수 있다는 말은 클래스가 값으로 사용할 수 있는 일급 객체라는 것을
의미합니다. 조금 더 자세히 말하자만 클래스는 함수입니다.
<br><br>

**클래스의 몸체에는 0개 이상의 메소드만 정의할 수 있습니다.** 클래스 몸체에 정의할 수 있는 메소드는
<br><br>

1. `constructor`(생성자)
2. `prototype method`(프로토타입 메소드)
3. `static method`(정적 메소드)
<br><br>

이렇게 3가지가 종류가 있습니다.

~~~javascript

// 클래스 선언문

class Person {

    // 생성자
    constructor(name) {
        // 인스턴스 초기화
        this.name = name;
    }

    // 프로토타입 메소드
    sayHello() {
        console.log(`Hello ${this.name}`);
    }

    // 정적 메소드
    static sayHi() {
        console.log('안녕하세요!');
    }
}

// 인스턴스 생성
const me = new Person('Lee');

console.log(me.name);  // Lee
me.sayHello();  // Hello Lee
Person.sayHi(); // 안녕하세요!

~~~

문법적인 부분만 다를 뿐이지 우리가 기존에 알던 개념과 상당히 유사합니다.

---

## class의 hoisting

클래스는 함수로 평가됩니다.
<br><br>

조금 자세히 들여다보면 클래스 선언문으로 정의한 클래스는 함수 선언문과 마찬가지로
소스코드가 실행되기 전, 즉 `runtime`전에 먼저 평가되어 함수 객체를 생성합니다.
<br><br>

이때 클래스가 평가되어 생성한 함수 객체는 생성자 함수로서 호출할 수 있는 함수 즉,
instance를 생성할 수 있는 `constructor`입니다. 이 시점에 `prototype 객체` 역시 같이 생성됩니다.
<br><br>

하지만 클래스는 클래스 정의 이전에 사용할 수 없습니다.
<br><br>

`hoisting`이 되지 않는 것은 아닙니다. 단지 `let`이나 `const`처럼 일시적 사각지대
(`Temporal Dead Zone`)가 생성되어 마치 `hoisting`이 되지 않는 것처럼 보일 뿐입니다.
<br><br>

다음의 코드를 보면 좀 더 분명히 알 수 있죠.

~~~javascript

const Person = '안녕하세요';

{
                          // 만약 호이스팅이 일어나지 않는다면 
                          // '안녕하세요'가 출력되어야 한다.
                          
    console.log(Person);  // '안녕하세요'가 출력되지 않는다.
                          // ReferenceError: Person is not defined

    class Person {}       
}

~~~

---

## instance 생성

클래스는 생성자 함수이며 `new` 연산자와 함께 호출되어 instance를 생성합니다.
클래스 자체는 함수이지만 반드시 `new` 연산자를 이용해야 합니다.
<br><br>

**만약 클래스 표현식으로 정의한 클래스인 경우 기명 클래스 표현식 이름으로 인스턴스를
생성하면 오류가 발생합니다. 반드시 클래스를 가리키는 식별자를 이용해야 합니다.**
<br><br>

클래스 표현식에서 사용한 클래스 이름은 외부 코드에서 접근이 불가능하기 때문입니다.
<br><br>

예를 보시죠

~~~javascript

class Person {}

const me = new Person();  // instance 생성


const Person1 = class MyClass {}

const a = new Person1()  // OK!

const b = new MyClass()  // Error!

~~~

---

## constructor

클래스의 `constructor`는 인스턴스를 생성하고 초기화 하는 특수한 메소드입니다.
`constructor`는 이름을 변경할 수 없습니다.
<br><br>

다음의 코드를 실행시키고 `chrome` browser에서 확인해보도록 하죠.

~~~javascript

class Person {

    // 생성자
    constructor(name) {
        // 인스턴스 생성 및 초기화
        this.name = name;
    }
}

console.dir(Person);

~~~

{% include image.html
file='es6-javascript-class-constructor.png'
%}

위의 결과에서 보는 것처럼 클래스는 평가되어 함수 객체가 됩니다. 따라서 함수 객체 고유의
property도 모두 가지고 있습니다. 함수와 동일하게 `prototype 객체` 와 연결되어 있으며
자신의 `scope chain`도 구성하고 있습니다.
<br><br>

모든 `constructor` 함수가 가지고 있는 `prototype property`가 가리키는 `prototype 객체`의 `constructor
property`는 클래스 자신을 가리키고 있습니다. 이는 **클래스가 생성자 함수**라는 것을 의미합니다.
<br><br>

즉, new 연산자와 함께 클래스를 호출하면 `instance`를 생성한다는 의미입니다.
<br><br>

다음의 코드를 실행해 보죠.

~~~javascript


class Person {

    // 생성자
    constructor(name) {
        // 인스턴스 생성 및 초기화
        this.name = name;
    }
}

const me = new Person('Lee');
console.log(me);

~~~

{% include image.html
file='es6-javascript-class-constructor-create-instance.png'
%}

`constructor`에서 `this`에 추가한 name property가 class가 생성한 instance의 property로
추가한 것을 볼 수 있습니다. 생성자 함수와 마찬가지로 `this`는 클래스가 생성한 instance를 가리킵니다.
그리고 `this`에 추가한 property는 `instance property`가 됩니다
<br><br>

**`ECMAScript` 표준에는 `instance property`는 반드시 constructor 내부에서 정의해야 한다고 명시하고
있습니다.** 하지만 현재 다른 메소드나 클래스 몸체 내에서 property를 정의할 수 있도록 표준안이 제안되어
있습니다. (사실 chrome과 같은 browser에서는 이미 적용중입니다. ) 하지만 현재 표준안대로 `constructor`안에서
`instance property`를 정의하는 것이 좋을 듯 합니다.
<br><br>

또 하나 `class`의 `constructor`와 `prototype 객체`의 `constructor` property는 다릅니다. 이름이 같아서
혼동하기 쉬우나 직접적인 연관성이 없습니다.
<br><br>

`constructor`는 다음과 같은 특징이 있습니다.
<br><br>

* `constructor`는 클래스 내에 최대 한 개만 존재할 수 있습니다. 만약 2개 이상 있으면 문법 오류입니다.
* `constructor`는 생략할 수 있습니다. 만약 생략하면 빈 constructor가 암묵적으로 정의됩니다.
* property를 초기화 하여 instance를 생성하려면 constructor안에서 `this`에 instance property를 추가합니다.
* `constructor`는 `return`문을 가지지 않습니다. 암묵적으로 `this`를 반환하기 때문입니다.

---

## prototype method

생성자 함수를 사용할 경우 `prototype method`를 추가할 경우 우리는 반드시 명시적으로 정의했어야 합니다.

~~~javascript

function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log(`안녕하세요 ${this.name}`);
}

~~~

하지만 클래스 몸체에서 정의한 메소드는 기본적으로 `prototype method`가 됩니다.
<br><br>

다음의 코드와 그림을 보시죠.

~~~javascript


class Person {

    constructor(name) {
        this.name = name;
    }

    sayHi() {
        console.log(`Hello ${this.name}`);
    }
}

const me = new Person('Lee');
me.sayHi();  // Hello Lee

console.log(Object.getPrototypeOf(me) == Person.prototype); // true
console.log(me instanceof Person);  // true

console.log(Object.getPrototypeOf(Person.prototype) == Object.prototype); // true
console.log(me instanceof Object);  // true

console.log(me.constructor === Person);  // true

~~~

{% include image.html
file='es6-javascript-class-prototype-method.png'
%}

---

## static method

`static method`는 `instance`를 생성하지 않아도 호출할 수 있는 메소드입니다.
<br><br>

생성자 함수의 경우 정적 메소드를 추가하기 위해서는 명시적으로 생성자 함수에 메소드를
추가했어야 했습니다.

~~~javascript

function Person(name) {
    this.name = name;
}

Person.sayHi = function () {
    console.log('안녕하세요!');
}

Person.sayHi();  // 안녕하세요!

~~~

하지만 클래스에서는 `static` 키워드를 붙이면 정적 메소드(클래스 메소드)가 됩니다.

~~~javascript

class Person {

    constructor(name) {
        this.name = name;
    }

    static sayHi() {
        console.log('안녕하세요');
    }
}

Person.sayHi();  // 안녕하세요

~~~

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-class-static-method.png'
%}

당연한 말이지만 이런 정적 메소드는 `instance`를 이용해 호출할 수 없습니다. 정적 메소드가
바인딩된 클래스는 `instance`의 `prototype chain`상에 존재하지 않기 때문입니다. 그 말은
`instance`로 정적 메소드(클래스 메소드)는 상속할 수 없다는 말과 동일합니다.
<br><br>

이렇게 정적 메소드는 `prototype 메소드`와 다릅니다.
<br><br>

* 정적 메소드와 프로토타입 메소드는 자신이 속해있는 `prototype chain`이 다릅니다.
* 정적 메소드는 클래스로 호출하고 프로토타입 메소드는 `instance`로 호출합니다.
* 정적 메소드는 `instance`가 가진 property를 참조할 수 없지만 프로토타입 메소드는 `instance`가 가진
  property를 참조할 수 있습니다.

---

## clsss에서 정의한 메소드의 특징

클래스 안에서 정의한 메소드들은 다음과 같은 특징을 가집니다.
<br><br>

* function 키워드를 생략한 축약 표현을 사용합니다.(`ES6` 메소드 축약표현)
* 객체 literal과 다르게 클래스에서 메소드를 정의할 때는 `,`를 사용하지 않습니다.
* 암묵적으로 `strict mode`가 적용됩니다.
* `for..in`이나 `Object.keys()`를 이용한 열거가 안됩니다. property attribute의 모든 값이
  `false`로 지정되어 있습니다.
* 내부 메소드 `[[Construct]]`를 가지지 않는 `non-constructor`입니다. 따라서 `new`연산자와 함께
  호출할 수 없습니다.

---

## instance property

`instance property`는 `constructor`내부에서 정의해야 합니다.

~~~javascript

class Person {

    constructor(name) {        
        this.name = name;  // public
    }
}

const me = new Person('Lee');
console.log(me.name);  // Lee

~~~

`ES6`의 클래스는 다른 객체지향 언어처럼 `private`, `public`, `protected`와 같은 접근 제어자를 지원하지
않습니다. 따라서 모든 instance property는 기본적으로 `public`입니다. 너무 불편하죠. 하지만 현재
`private`한 property를 정의할 수 있는 사양이 제안중에 있습니다. 조금 있으면 JavaScript에서도 편하게 접근 제어자를
사용할 날이 올듯 합니다.

---

## 접근자 property

접근자 property는 자체적으로 값을 가지지 않고 다른 데이터 property의 값을 읽거나 저장할 때
사용하는 접근자 함수로 구성된 property입니다.
<br><br>

먼저 객체 literal을 이용한 접근자 property부터 살펴보죠.

~~~javascript

const person = {

    firstName: '길동',
    lastName: '홍',

    // fullName은 접근자 함수로 구성된 접근자 property
    // getter 함수
    get fullName() {
        return `${this.firstName} ${this.lastName}`
    },

    // setter 함수
    set fullName(name) {
        [this.firstName, this.lastName] = name.split(' ');
    }   
};

console.log(person.firstName, person.lastName);  // 길동 홍

// 접근자 property를 통한 property값 참조
// 값을 조회하면 getter 함수가 호출
console.log(person.fullName);  // 길동 홍

// 접근자 property를 통한 property값 저장
// 값을 저장하면 setter 함수가 호출
person.fullName = '연아 김';

console.log(person.fullName);  // 연아 김

~~~

이런 접근자 property는 클래스에서도 사용가능합니다.

~~~javascript

class Person {

    constructor(firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    // getter 함수
    get fullName() {
        return `${this.firstName} ${this.lastName}`
    }

    // setter 함수
    set fullName(name) {
        [this.firstName, this.lastName] = name.split(' ');
    }   
}

const me = new Person('길동', '홍');

console.log(`${me.firstName} ${me.lastName}`);  // 길동 홍

me.fullName = '순신 이';
console.log(me);  // Person { firstName: '순신', lastName: '이' }

console.log(me.fullName);  // 순신 이

~~~

접근자 property는 자체적으로 값을 가지고 있지 않습니다. 다른 데이터 property의 값을 읽거나
저장할 때 사용하는 접근자 함수, 즉 `getter`와 `setter`로 구성되어 있습니다.
<br><br>

`getter`는 메소드 이름앞에 `get` keyword를 사용해 정의하고 `setter`는 메소드 이름앞에
`set` keyword를 사용해 정의합니다.
<br><br>

이때 getter와 setter 이름은 `instance property`처럼 사용하고 함수호출이 아닌
참조형태로 사용하며 내부적으로 함수가 호출됩니다.
<br><br>

`getter`는 반드시 `return` 구문이 있어야 합니다. `setter`는 반드시 매개변수가 있어야 한다는
점을 기억해두시면 됩니다.

---

## 상속에 따른 class의 확장

상속에 따른 class의 확장은 `prototype`기반 상속과는 다릅니다.

{% include callout.html
type="danger"
content="`prototype` 기반의 상속은 `prototype chain`을 통해 다른 객체의 자산을 상속 받는
개념이었지만 상속에 의한 클래스의 확장은 기존 class를 상속받아 새로운 class를 확장하여
정의하는 개념입니다."
%}


{% include image.html
file='es6-javascript-class-inheritance.png'
%}

위에서 살펴본것처럼 `class`와 생성자 함수는 인스턴스를 생성할 수 있다는 점에서 매우 유사합니다.
하지만 class는 상속을 통해 기존 class를 확장할 수 있는 문법이 기본적으로 제공되지만 생성자 함수는
그렇지 않습니다.
<br><br>

`class`를 통한 상속은 기존 다른 객체지향 언어의 상속의 개념과 같습니다. 예를 들어보죠
<br><br>

동물을 추상화한 `Animal` class와 새와 사자를 추상화한 `Bird`, `Lion` class를 각각 정의한다고
생각해 보죠. 새와 사자는 동물에 속하기 때문에 동물의 속성을 가집니다. 하지만 새와 사자는 자신만의
고유한 속성도 가집니다. 따라서 상속을 통해 이 클래스를 확장시켜 사용할 수 있습니다.
<br><br>

class의 상속관계에서 상위 class를 부르는 용어로는 다음과 같은 것들이 있습니다.
<br><br>

* `super class`(슈퍼 클래스)
* `parent class`(부모 클래스)
* `upper class`(상위 클래스)
* `base class`(기반 클래스)
<br><br>

하위 class를 부르는 용어로는 다음과 같은 것들이 있습니다.
<br><br>

* `sub class`(서브 클래스, 하위 클래스)
* `derived class`(파생 클래스)
* `child class`(자식 클래스)

{% include image.html
file='es6-javascript-class-inheritance-animal-example.png'
%}

class를 상속하기 위해서 사용하는 키워드는 `extends` keyword입니다. 위의 그림에서 확인할 수
있듯이 class도 prototype을 통해 상속을 구현합니다. 즉, 하위 class의 `[[Prototype]]` 내부슬롯이
상위 class를 reference하고 있습니다.
<br><br>

따라서 super class와 sub class는 `instance`의 `prototype chain`뿐 아니라 `class`간의
`prototype chain`도 생성합니다. 이를 통해 `prototype method` 및 `static method` 모두 상속이
가능합니다.
<br><br>

이처럼 상속을 통한 class의 확장은 코드 재사용관점에서 매우 유용하다고 할 수 있습니다. 이제
코드로 표현해 보죠.

~~~javascript

class Animal {
    constructor(age, weight) {
        this.age = age;
        this.weight = weight;
    }

    eat() {
        return 'eat';
    }

    move() {
        return 'move';
    }
}

class Bird extends Animal {
    fly() {
        return 'fly';
    }
}

const bird = new Bird(1,5);

console.log(bird);  // Bird { age: 1, weight: 5 }
console.log(bird instanceof Bird);  // true
console.log(bird instanceof Animal);  // true

console.log(bird.eat());  // eat
console.log(bird.fly());  // fly

~~~

위의 코드에 대한 instance prototype chain은 다음 그림과 같습니다.

{% include image.html
file='es6-javascript-class-inheritance-prototype-chain.png'
%}

---

## 생성자 함수로부터의 상속

`extends` keyword를 사용해 `class`를 상속할 때 class가 아닌 생성자 함수를 이용할 수도
있습니다. 간단한 예를 보죠.

~~~javascript

function Base(name) {
    this.name = name;
}

class Derived extends Base {

}

const derived = new Derived('홍길동');

console.log(derived);  // Derived { name: '홍길동' }

~~~

또한 `extends` keyword 뒤에는 `class`, 생성자 함수 이외에 `constructor`로 평가될
수 있는 모든 표현식이 나올 수 있습니다. 이를 이용해서 동적 상속을 구현할 수 있습니다.
<br><br>

간단한 예로 설명해보죠.

~~~javascript

function Base1() {
}

class Base2 {}

let condition = true;

class Derived extends (condition ? Base1 : Base2) {

}

const derived = new Derived();

console.log(derived instanceof Base1); // true

~~~

---

## sub class의 constructor

앞서 설명했듯이 `class`에서 constructor를 생략하면 parameter도 가지지 않고
내부 코드도 가지지 않는 비어 있는 `constructor`가 암묵적으로 정의됩니다.

{% include callout.html
type="danger"
content="`constructor() {}`"
%}

위와 같은 형태가 되겠죠.
<br><br>

그런데 만약 `sub class`에서 constructor를 생략하면 다음과 같은 constructor가
암묵적으로 정의됩니다.

{% include callout.html
type="danger"
content="`constructor(...args) { super(...args) }`"
%}

이 코드에서 `super()`는 상위 클래스의 constructor를 호출하여 인스턴스를 생성하게 됩니다.

~~~javascript

class Base {
    // constructor() {}  // 생략하면 이 코드가 삽입
}

class Derived extends Base {
    // 생략하면 아래 코드가 삽입
    // constructor(...args) {
    //     super(...args);
    // }
}

const derived = new Derived();

console.log(derived instanceof Base); // true

~~~

---

## super keyword

`super`는 함수처럼 호출할 수도 있고 this 처럼 참조식별자로 이용할 수 도
있습니다.
<br><br>

* `super()` : 상위 class의 constructor를 호출합니다.
* `super` : 슈퍼 클래스의 메소드를 호출할 수 있습니다.
<br><br>

`super()` 사용에 대해서는 몇가지 유의해야 할 상황이 있습니다.
<br><br>

각 상황에 대해 하나하나 코드로 살펴보기로 하겠습니다.

{% include callout.html
type="danger"
content="super class의 constructor에서 선언한 property만을 그대로 갖는 경우
sub class의 constructor는 생략이 가능합니다."
%}

~~~javascript

class Base {
    constructor(a,b) {
        this.a = a;
        this.b = b;
    }
}

class Derived extends Base {
    // 생략하면 아래 코드가 삽입
    // constructor(...args) {
    //     super(...args);
    // }
}

const derived = new Derived(1,2);

console.log(derived); // Derived { a: 1, b: 2 }

~~~

{% include callout.html
type="danger"
content="super class의 constructor에서 선언한 property와 sub class에서 추가한
property를 갖는 instance를 생성하는 경우 constructor를 생략할 수 없습니다."
%}

~~~javascript

class Base {
    constructor(a,b) {
        this.a = a;
        this.b = b;
    }
}

class Derived extends Base {
    constructor(a,b,c) {
        super(a,b);
        this.c = c
    }
}

const derived = new Derived(1,2,3);

console.log(derived); // Derived { a: 1, b: 2, c: 3 }

~~~

메소드 내에서 `super` keyword를 참조하면 super class의 메소드를 참조할 수 있습니다.

~~~javascript

class Base {
    constructor(name) {
        this.name = name;
    }

    sayHello() {
        return '안녕하세요!';
    }
}

class Derived extends Base {
    sayHello() {
        return super.sayHello() + this.name;
    }
}

const derived = new Derived('홍길동');

console.log(derived.sayHello()); // 안녕하세요!홍길동

~~~

---

## 상속을 통한 instance의 생성과정

상속관계에 있는 sub class에 대해 new를 이용해 instance를 생성하면 어떤 일이 벌어지는 것일까요?
<br><br>

여기서 조금 주의해야 하는것이 객체의 생성은 super class가 담당한다는 것입니다. 즉, instance의 생성의
주체는 super class이기 때문에 sub class에서 반드시 `super()`를 호출해야 하는 것입니다. 이렇게 생성된
super class의 constructor에 의해 초기화된 instance는 sub class의 constructor에게
인계되어 property 추가 작업이 진행되게 됩니다.
<br><br>

따라서 `super()`가 호출되기 이전에는 `this` reference도 존재하지 않기 때문에 `super()`를 사용하기
이전에는 `this` reference를 사용할 수 없습니다.
<br><br>

이렇게 sub class의 초기화가 끝난 객체가 비로소 `this`에 binding되어 instance가 리턴되게 됩니다.

End.

{% include links.html %}
