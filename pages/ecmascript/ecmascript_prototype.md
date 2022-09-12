---
title: ECMAScript 6.0 prototype
sidebar: ecmascript_sidebar
summary: "JavaScript는 prototype을 기반으로 상속을 구현합니다. 어떻게 구현하는지에 대해 알아보도록 하겠습니다."
permalink: ecmascript_prototype.html
folder: ecmascript
---

## prototype

Javascript는 함수형, 프로토타입기반, 객체지향 프로그래밍을 지원하는 멀티 패러다임 프로그래밍
언어입니다.
<br><br>

C++이나 Java와 같은 class기반 객체지향 언어의 특징인 class와 상속, 캡슐화를 위한 키워드가 없어서
객체 지향언어라기 보다는 객체기반 언어라고 많이 생각하는데 사실 JavaScript는 프로토타입을 기반으로 한
객체지향 언어이고 class기반의 객체지향 언어와 다른 구현을 사용할 뿐이지 같은 객체지향 개념을 충분히
잘 반영하고 있는 언어라고 할 수 있습니다.
<br><br>

물론 `ES6`에서 `class`가 도입되었으나 이는 새로운 객체 생성 메커니즘으로 보는 시각이 더 타당하지
기존 프로토타입 객체지향에 문제가 있어서 도입된 것은 아니라는 것입니다.
<br><br>

객체지향 개념에 대한 기본적인 설명은 제외하고 설명을 시작하겠습니다.

---

## 상속과 prototype

`inheritance`(상속)은 객체지향의 꽃이라고 불릴 정도로 객체지향 프로그래밍의 핵심적인 요소입니다.
<br><br>

JavaScript는 `prototype`을 기반으로 상속을 구현하여 불필요한 중복을 제거합니다. 기존 코드를
적극적으로 재 사용하는 개념이라고 보시면 됩니다.
<br><br>

우리가 지금까지 봐왔던 생성자 함수를 이용해서 객체를 생성하는 코드입니다.

~~~javascript

function Circle(radius) {
    this.radius = radius;
    this.getArea = function() {
        return Math.PI * this.radius ** 2;
    }
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle() 생성자 함수는 instance가 생성될 때마다
// 동일한 기능을 하는 getArea() 메소드를 생성하고
// 각각의 instance가 따로따로 소유하게 됩니다.

console.log(circle1.getArea === circle2.getArea);  // false

~~~

따라서 지금까지 우리가 사용한 생성자 함수는 문제가 약간 있습니다. 위의 예에서 `radius` property는
각 instance가 따로 소유하는게 맞지만 같은 역할을 하는 `getArea()` 메소드 역시 각 instance가
따로 소유하고 있어서 메모리의 낭비를 가져오게 됩니다. 그림으로 보자면 다음과 같습니다.
<br><br>

{% include image.html
file='es6-javascript-constructor-function-method-each.png'
%}

상속을 통해 불필요한 중복을 제거해 보도록 하죠. 다시한번 말하지만 JavaScript는 `prototype`을
기반으로 상속을 구현합니다.

~~~javascript

function Circle(radius) {
    this.radius = radius;

    // 생성자 함수로 생성되는 모든 instance가 getArea() 
    // 메소드를 공유할 수 있도록 prototype 객체에 추가한다.
    // prototype 객체는 Circle 생성자 함수의 prototype property에
    // 바인딩 되어 있다.
    Circle.prototype.getArea = function() {
        return Math.PI * this.radius ** 2;
    }
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);

console.log(circle1.getArea === circle2.getArea);  // true

~~~

그림으로 보자면 다음과 같습니다.

{% include image.html
file='es6-javascript-constructor-function-prototype.png'
%}

위의 그림에서 알수 있듯이 각 `instance`는 자신의 상태를 나타내는 `radius` property만 개별적으로
소유하고 내용이 같은 메소드는 상속을 통해 공유해서 사용하는 것을 알 수 있습니다. **여기서는 상속이라는
표현을 썻지만 정확히는 해당 객체에 대한 참조를 가지고 있는 형태입니다.**

---

## prototype 객체

`prototype 객체`는 객체간 상속을 구현하기 위해 사용됩니다. `prototype 객체`는 어떤 객체의 상위 객체의 역할을
하는 객체로서 다른 객체에 `공유 property`(메소드 포함)를 제공합니다. `prototype 객체`를 상속받은 하위
객체는 상위 객체의 `property`를 자신의 `property`처럼 자유롭게 사용할 수 있습니다. 메소드가 아닌
`property`는 모든 instance가 공유하는 공유 property가 됩니다.
<br><br>

~~~javascript

function Circle(radius) {
    this.radius = radius;

    // 생성자 함수로 생성되는 모든 instance가 getArea() 
    // 메소드를 공유할 수 있도록 prototype 객체에 추가한다.
    // prototype 객체는 Circle 생성자 함수의 prototype property에
    // 바인딩 되어 있다.
    Circle.prototype.getArea = function() {
        return Math.PI * this.radius ** 2;
    }

    Circle.prototype.name = '홍길동';
    
}

const circle1 = new Circle(1);
const circle2 = new Circle(2);

console.log(circle1.getArea === circle2.getArea);  // true

console.log(circle1.name, circle2.name);  // 홍길동 홍길동

circle1.name = '아이유';

console.log(circle1.name, circle2.name);  // 아이유 홍길동 
// 이건 왜 이럴까요? javascript는 property의 추가가 가능합니다. 
// 따라서 circle1 객체에 새로운 name property가 생성되는 것이죠.

delete circle1.name;

circle1.__proto__.name = '김연아';
console.log(circle1.name, circle2.name);  // 김연아 김연아

~~~

모든 객체는 `[[Prototype]]` 이라는 내부 슬롯을 가지고 있으며 이 내부 슬롯의 값은 자신의 `상위
prototype 객체`에 대한 참조입니다. 그리고 이 `[[Prototype]]`이 참조하는 `(상위) prototype 객체`는
객체 생성 방식에 의해서 그 종류가 결정됩니다.
<br><br>

즉, 객체가 생성될 때 객체 생성 방식에 따라 `(상위) prototype 객체`가 결정되고 `[[Prototype]]` 내부 슬롯을
이용하여 참조합니다.
<br><br>

간단한 예로, 객체 literal에 의해서 생성된 객체의 `(상위) prototype 객체`는 `Object.prototype`이고 생성자 함수에
의해 생성된 객체의 `(상위) prototype 객체`는 생성자 함수의 `prototype property`에 바인딩 되어 있는 객체입니다.
<br><br>

모든 객체는 하나의 `(상위) prototype 객체`를 가지며 모든 `(상위) prototype 객체`는 생성자 함수와 연결되어 있습니다.
그림으로 보면 다음과 같습니다.

{% include image.html
file='es6-javascript-constructor-function-prototype-connect.png'
%}

몇번 설명했다시피 `[[Prototype]]` 내부 슬롯에는 직접 접근할 수 없지만 위 그림처럼 `__proto__ 접근자 property`를
통해 자신의 `(상위) prototype 객체`, 즉, 자신의 `[[Prototype]]` 내부 슬롯이 가리키는 `(상위) prototype 객체`에
간접적으로 접근할 수 있습니다.
<br><br>

간단한 예를 보죠.

~~~javascript

// browser 환경에서 실행해야 합니다.

const person = {
    name: 'Lee'
};

console.log(person);

~~~

{% include image.html
file='es6-javascript-prototype-inner-slot.png'
%}

**`__proto__`는 접근자 property입니다.** 이 property는 자체적으로 값(`[[Value]]` property
attribute)를 가지지 않고 `[[Get]]`, `[[Set]]` property attribute로 구성됩니다.
즉, `(상위) prototype 객체`을 취득하거나 할당하는데 사용하게 된다는 말입니다.
<br><br>

다음의 코드를 보고 이해해보도록 하죠.

~~~javascript

const obj = {};

const parent = { x: 1 };

obj.__proto__ = parent;

// parent 객체를 prototype객체(상위객체)로 지정
// 결국 parent 객체의 property를 상속하게 된다.
console.log(obj.x);  // 1

~~~

**이 `__proto__` 접근자 property는 객체가 직접 소유하는 property가 아니라
`Object.prototype`의 property입니다.** 즉,
이 property 자체가 상속을 통해 내려온 것이라는 말이죠. 모든 객체는 상속을 통해
`Object.prototype.__proto__` 접근자 property를 사용할 수 있는 것입니다.
<br><br>

단, `Object.create(null)`를 이용해서 객체를 생성한 경우 `상위 prototype 객체`가
존재하지 않기 때문에 이 경우에는 `__proto__`를 사용할 수 없습니다.

~~~javascript

const person = { name: 'Lee' };

console.log(person.hasOwnProperty('__proto__'));  // false

console.log(Object.getOwnPropertyDescriptor(Object.prototype, '__proto__'));
// { get: [Function: get __proto__],
//     set: [Function: set __proto__],
//     enumerable: false,
//     configurable: true }

console.log({}.__proto__ === Object.prototype);  // true

~~~

참고로 모든 객체는 `prototype 객체`의 계층 구조인 `prototype chain`에 묶여 있습니다.
JavaScript Engine은 객체의 property에 접근할 때 해당 객체에 접근하려는 property가
존재하지 않으면 `__proto__` 접근자 property가 가리키는 참조를 따라 자신의 부모 역할을
하는 `(상위) prototype 객체`의 property를 순차적으로 검색합니다.
<br><br>

이제 `__proto__` 접근자 property에 대한 이해가 어느정도 되셨을 거라 생각됩니다.
주의해야 할 점은 이런 **`__proto__` 접근자 property를 코드내에서 직접 사용하는 것은 권장되지
않는다는 것**입니다.
<br><br>

왜냐하면 모든 객체가 `__proto__` 접근자 property를 사용할 수 있는 것은
아니기 때문입니다. 직접 상속이라는 방법이 있는데 이 경우 `Object.prototype`을 상속받지
않는 객체를 생성할 수 도 있기 때문입니다. 이 경우 `__proto__` 접근자 property를 사용할 수
없습니다.

~~~javascript

// browser에서 실행시켜야 합니다.

const myObj = Object.create(null);

console.log(myObj.__proto__);  // undefined

// 이런 경우 직접적인 __proto__ 접근자 property를 사용하는 것보단
// getPrototypeOf method를 사용하는 것이 좋습니다.
console.log(Object.getPrototypeOf(myObj));  // null

// 일반적인 사용법
const obj = {};
const parent = { x: 1 };

console.log(Object.getPrototypeOf(obj));

Object.setPrototypeOf(obj, parent);

console.log(obj.x);  // 1

~~~

---

## 함수 객체의 prototype property

함수 객체(정확히 말하자면 `constructor`)가 가지고 있는 `prototype property`는 생성자 함수가 생성할
instance의 `(상위) prototype 객체`를 가리킵니다. 앞에서 언급했다시피 **일반 객체는 `prototype property`을
가지고 있지 않습니다.** `constructor`만이 가지고 있는 property이기 때문입니다.

~~~javascript

// 함수 객체는 prototype property를 가집니다.
console.log((function() {}).hasOwnProperty('prototype'));  // true

// 일반 객체는 prototype property가 없습니다.
console.log({}.hasOwnProperty('prototype'));  // false

~~~

`prototype property`는 생성자 함수가 생성할 instance의 `(상위) prototype 객체`를 가리키기 때문에
생성자 함수로서 호출할 수 없는 함수, 즉, `non-constructor`인 arrow function이나 `ES6`
메소드 축약 표현으로 정의한 메소드는 **`prototype property`를 가지지 않고 `prototype 객체`도
생성하지 않습니다.**

~~~javascript

const Person = name => {
    this.name = name
};

console.log(Person.hasOwnProperty('prototype'));  // false

console.log(Person.prototype);  // undefined

const obj = {
    foo() {}
};

console.log(obj.foo.hasOwnProperty('prototype'));  // false

console.log(obj.foo.prototype);  // undefined

~~~

생성자 함수가 아닌 일반 함수 역시 `prototype property`를 가지지만
instance를 생성하는것이 아니기 때문에 아무런 의미가 없습니다.
<br><br>

정리를 하자면 모든 객체가 가지고 있는(정확히 표현하자면 `Object.prototype`으로 부터 상속받은)
`__proto__ 접근자 property`와 함수 객체만이 가지고 있는 `prototype property`는 결국
동일한 `prototype 객체`를 가리킵니다. 하지만 이들 property를 사용하는 주체가 다른거죠.

{% include image.html
file='es6-javascript-proto-prototype-difference.png'
%}

예를 들어보기로 하죠. 생성자 함수로 instance를 생성한 후 `__proto__ 접근자 property`와
`prototype property`로 `prototype 객체`에 접근해 보도록 하겠습니다.

~~~javascript

function Person(name) {
    this.name = name;
}

const me = new Person('Lee');

console.log(Person.prototype === me.__proto__);  // true

~~~

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-proto-prototype-same-object.png'
%}

모든 `prototype 객체`는 `constructor property`를 가집니다. 이 `constructor property`는
자신을 참조하고 있는 생성자 함수를 가리킵니다. 다음의 코드와 그림을 보시죠.

~~~javascript

function Person(name) {
    this.name = name;
}

const me = new Person('Lee');

console.log(me.constructor === Person);  // true

~~~

{% include image.html
file='es6-javascript-constructor-property.png'
%}

---

## literal 표기법에 의해 생성된 객체

앞서 살펴본 바와 같이 생성자 함수에 의해서 생성된 `instance`는 `(상위) prototype 객체`의 `constructor property`에
의해 생성자 함수와 연결됩니다. 당연히 이 때 `consturctor property`가 가리키는 생성자 함수는 `instance`를 생성한
생성자 함수입니다. 다음의 코드를 보죠.

~~~javascript

const obj = new Object();
console.log(obj.constructor === Object);  // true
console.log(Object.prototype === obj.__proto__);  // true
console.log(Object.prototype === Object.getPrototypeOf(obj));  // true

const add = new Function('x', 'y', 'x + y');
console.log(add.constructor === Function);  // true

function Person(name) {
    this.name = name;
}

const me = new Person('Lee');
console.log(me.constructor === Person);  // true

~~~

literal 표기법으로 생성된 객체도 `(상위) prototype 객체`가 존재합니다. 하지만 literal 표기법으로
생성한 객체는 `(상위) prototype 객체`의 `constructor property`가 가리키는 생성자 함수는
무엇이 될까요?
<br><br>

예제 코드를 보시죠.

~~~javascript

const obj = {};

console.log(obj.prototype);  // undefine(non-constructor)

console.log(obj.__proto__ === Object.prototype);  // true

console.log(obj.constructor === Object);  // true

~~~

위의 코드를 보면 마치 객체 literal은 결국 `Object` 생성자 함수를 통해서 만들어지는 것처럼 보입니다.
내부로직에 의해 객체 literal 형태로 객체를 생성하면 `Object.prototype`객체를 `(상위) prototype 객체`로
가지는 빈 객체를 생성해줍니다. 하지만 엄밀히 말해 `Object` 생성자 함수를 이용해 만든 것은 아닙니다.
<br><br>

이번에는 함수 literal에 대해서 다시 알아보죠.

~~~javascript

var foo = function() {}

console.dir(foo.__proto__);  // ƒ anonymous()
                             // Function 생성자 함수의 prototype

// foo 함수 객체의 상위 prototype 객체는 Object.prototype가 아닙니다.
console.dir(foo.__proto__ === Function.prototype);  // true

console.dir(Function.prototype.__proto__ === Object.prototype);  // true

console.dir(Object.prototype.__proto__);  // null

console.log(foo.constructor === Object);  // false

console.log(foo.constructor === Function);  // true

console.log(foo.prototype);  // {constructor: ƒ}


~~~

literal 표기법에 의해 생성된 객체도 상속을 위해 `(상위) prototype 객체`가 필요합니다.
따라서 **literal 표기법에 의해 생성된 객체도 가상적인 생성자 함수를 가지게 됩니다.**
<br><br>

정리해보면 다음과 같습니다.

{% include image.html
file='es6-javascript-literal-constructor-function-prototype.png'
%}

---

## prototype 객체의 생성 시점

**`prototype 객체`는 생성자 함수가 생성되는 시점에 더불어 생성됩니다.**
생성자 함수는 사용자가 직접 정의한 사용자 정의 생성자 함수와 JavaScript가 기본적으로
제공하는 `built-in` 생성자 함수로 구분되는데 먼저 사용자 정의 생성자 함수와 `prototype 객체`의
생성 시점에 대해 그림으로 알아보죠.

생성자 함수로서 호출할 수 있는 함수, 즉 `constructor`는 함수 정의가 평가되어 함수 객체를 생성하는
시점에 `prototype 객체`도 더불어 생성됩니다.

~~~javascript

// browser환경에서 실행해야 합니다.

console.log(Person.prototype); // {constructor: ƒ}

function Person(name) {
    this.name = name;
}

~~~

생성자 함수로서 호출할 수 없는 함수, 즉, `non-constructor`는 `prototype 객체`가 생성되지
않습니다.

~~~javascript

// browser환경에서 실행해야 합니다.

const Person = (name) => {
    this.name = name;
}

console.log(Person.prototype); // undefined

~~~

여하간 이렇게 생성자 함수와 함께 생성된 `prototype 객체`는 오직 `constructor property`만을 가지는 객체입니다.
그런데 `prototype 객체` 역시 객체이고 모든 객체는 `(상위) prototype 객체`를 가지기 때문에
`prototype 객체`도 자신의 `(상위) prototype 객체`를 가집니다. **이 때 `prototype 객체`의
`(상위) prototype 객체`는 `Object.prototype`입니다.**
<br><br>

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-prototype-of-prototype.png'
%}

~~~javascript

function Person(name) {
    this.name = name;
}

console.log(Person.prototype); // {constructor: ƒ}

console.log(Person.prototype.__proto__ == Object.prototype); // true

~~~

그럼 `built-in` 생성자 함수(Object, Function, Array, String, Number, etc)는 어느시점에
`prototype 객체`가 생성될까요? 모든 `built-in` 생성자 함수는 전역객체가 생성되는 시점에
생성자 함수가 생성되고 바로 따라서 `prototype 객체`도 생성됩니다. 전역객체는 browser환경에서
`window`객체를 의미하며 프로그램 상 모든 전역 변수와 전역 함수는 이 `window` 전역객체의 property로 등록됩니다.

~~~javascript

// browser환경에서 실행해야 합니다.
console.log(Object === window.Object);  // true

~~~

그림으로 보면 다음과 같습니다.

{% include image.html
file='es6-javascript-object-constructor-function-prototype.png'
%}

다음의 코드를 보면서 `built-in` 생성자 함수에 대해서도 한번 생각해 보도록 하죠.

~~~javascript

console.log(Object.__proto__);  // ƒ () { [native code] }

console.log(Object.prototype.__proto__);  // null

console.log(Function.__proto__);  // ƒ () { [native code] }

console.log(Function.prototype.__proto__);  // Object.prototype

~~~

**객체생성 방식과 prototype객체 결정**{: class="subtitle2_fontAwesome" }

객체를 생성하는 방법에는 다양한 방법이 있습니다.
<br><br>

* 객체 literal
* Object 생성자 함수
* 생성자 함수
* Object.create() 메소드
* 클래스(`ES6`)
<br><br>

이 중 3가지 경우에 대해서 객체 생성과 `prototype 객체`의 연관성에 대해서 그림으로 알아보겠습니다.
<br><br>

* 객체 literal을 사용해 객체를 생성하면 다음과 같습니다.

~~~javascript

const obj = {
    x: 1
};

~~~

{% include image.html
file='es6-javascript-object-literal-prototype.png'
%}

~~~javascript

const obj = {
    x: 1
};

console.log(obj.constructor == Object); // true 

console.log(obj.hasOwnProperty('x')); // true

~~~

* Object 생성자 함수를 사용해 객체를 생성하면 다음과 같습니다.

~~~javascript

const obj = new Object();
obj.x = 1;

~~~

{% include image.html
file='es6-javascript-object-constructor-function-create-prototype.png'
%}

~~~javascript

const obj = new Object();
obj.x = 1;

console.log(obj.constructor == Object); // true 

console.log(obj.hasOwnProperty('x')); // true

~~~

* 생성자 함수를 사용해 객체를 생성하면 다음과 같습니다.

~~~javascript

function Person(name) {
    this.name = name;
}

const me = new Person('Lee');

~~~

{% include image.html
file='es6-javascript-constructor-function-create-prototype.png'
%}

`prototype 객체`인 `Person.prototype`에 property를 추가하여 자식 객체가 상속받을 수 있도록하려면
다음과 같이 하면 되겠죠.

~~~javascript

function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log('Hello!' + this.name);
}

const me = new Person('Lee');
const you = new Person('Kim');

me.sayHello();   // Hello!Lee
you.sayHello();  // Hello!Kim

~~~

{% include image.html
file='es6-javascript-constructor-function-create-prototype-inheritance.png'
%}

---

## prototype chain

JavaScript는 **객체의 property(메소드 포함)에 접근하려 할 때** 해당 객체에 접근하려는 property가
없으면 `[[Prototype]]` 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 `(상위) prototype 객체`의 property를
순차적으로 검색합니다.
<br><br>

이를 `prototype chain`이라고 하며 이 `prototype chain`은 JavaScript가 객체지향의 상속을 구현하는
메커니즘입니다.
<br><br>

다음의 코드를 보고 그림을 이해해보도록 하죠.

~~~javascript

function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log(`Hello ${this.name}`);
}

const me = new Person('Lee');

console.log(me.hasOwnProperty('name'));  // true

console.log(Object.getPrototypeOf(me) === Person.prototype);  // true

console.log(Object.getPrototypeOf(Person.prototype) === Object.prototype); // true

~~~

{% include image.html
file='es6-javascript-prototype-chain.png'
%}

---

## overriding과 property shadowing

먼저 코드를 보죠

~~~javascript

function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log(`Hello ${this.name}`);
}

const me = new Person('Lee');

// instance method overriding
me.sayHello = function() {
    console.log(`안녕하세요. ${this.name}`);
}

me.sayHello();  // 안녕하세요. Lee

~~~

`(상위) prototype 객체`가 가지고 있는 property와 같은 이름의 property를 instance에 추가하면
`prototype chain`을 따라 올라가 덮어 쓰는게 아니라 instance property로 추가하게 됩니다.
<br><br>

이때 overriding이 발생하고 가리워진 property는 `property shadowing`이 발생했다고 합니다.
<br><br>

{% include image.html
file='es6-javascript-overriding-shadowing.png'
%}

삭제의 경우도 마찬가지 입니다. 단, 하위 객체를 통해 `(상위) prototype 객체`의 property를 변경하거나
삭제하는 것은 허용되지 않습니다. 어찌보면 당연하죠. 이 작업을 하려면 `(상위) prototype 객체`에 직접 접근해야 합니다.

~~~javascript


function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log(`Hello ${this.name}`);
}

const me = new Person('Lee');

// instance method overriding
me.sayHello = function() {
    console.log(`안녕하세요. ${this.name}`);
}

me.sayHello();  // 안녕하세요. Lee

delete me.sayHello;

me.sayHello();  // Hello Lee

// 아래의 코드는 아무런 영향을 주지 않습니다. 
delete me.sayHello;

me.sayHello();  // Hello Lee

// delete me.__proto__.sayHello;
delete Person.prototype.sayHello;

me.sayHello();  // TypeError: me.sayHello is not a function

~~~

---

## prototype 객체의 교체

`prototype 객체`는 임의의 다른 객체로 변경이 가능합니다. 즉, 부모 객체인 `(상위) prototype 객체`를
동적으로 바꿀 수 있다는 의미이고 이 특징을 이용해 객체간의 상속관계를 동적으로 변경할 수 있습니다.
<br><br>

하지만 상당히 복잡하고 예기치 못한 상황이 발생할 수 있기 때문에 상속관계를 동적으로 변경하는 것은
추천되지 않습니다.
<br><br>

간단한 예를 통해 `prototype 객체`를 교체해 보도록 하겠습니다.

~~~javascript

function Person(name) {
    this.name = name;
}

Person.prototype = {
    sayHello() {
        console.log(`Hello ${this.name}`);
    }
}

const me = new Person('Lee');

me.sayHello();  // Hello Lee

console.log(me.constructor === Person);  // false
console.log(me.constructor === Object);  // true

~~~

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-prototype-change.png'
%}

`prototype 객체`로 교체한 객체 literal에는 `constructor property`가 없습니다. 그래서
위와 같은 형태가 나오게 되는 것이죠.
<br><br>

다음과 같은 코드를 이용하면 끊어진 링크를 다시 살릴 수 있습니다.

~~~javascript

function Person(name) {
    this.name = name;
}

Person.prototype = {
    constructor: Person,
    sayHello() {
        console.log(`Hello ${this.name}`);
    }
}

const me = new Person('Lee');

me.sayHello();  // Hello Lee

console.log(me.constructor === Person);  // true
console.log(me.constructor === Object);  // false

~~~

이번에는 조금 다른 형태로 isntance를 이용해 `prototype 객체`를 교체해 보도록 하겠습니다.

~~~javascript


function Person(name) {
    this.name = name;
}

const me = new Person('Lee');

const parent = {
    sayHello() {
        console.log(`Hello ${this.name}`);
    }
};

Object.setPrototypeOf(me, parent);

me.sayHello();  // Hello Lee

console.log(me.constructor === Person);  // false
console.log(me.constructor === Object);  // true

// 연결이 끊어진다.
console.log(Person.prototype === me.__proto__); // false

~~~

그림으로 보면 다음과 같습니다.

{% include image.html
file='es6-javascript-prototype-change-instance.png'
%}

---

## Object.create()를 이용한 직접 상속

`Object.create()` 메소드는 명시적으로 **prototype을 지정**하여 새로운 객체를 생성합니다.
<br><br>

`Object.create()` 메소드의 **첫번째 매개변수에는 생성할 객체의 prototype으로 지정할 객체**를
전달하고 두번째 매개변수에는 생성할 객체의 key와 property descriptor객체로 구성됩니다. 두번째
인자는 생략이 가능합니다.
<br><br>

간단한 예를 보죠.

~~~javascript


let obj = Object.create(null);

console.log(Object.getPrototypeOf(obj)); // null

// console.log(obj.toString()); // TypeError: obj.toString is not a function

// ------

obj = Object.create(Object.prototype);

console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// -------

obj = Object.create(Object.prototype, {
    x: { value: 'Hello World', writable: true}
});

console.log(obj.x); // Hello World

// -------

const myProto = { x: 10 }

obj = Object.create(myProto);
console.log(obj.x); // 10

// -------

function Person(name) {
    this.name = name;
}

obj = Object.create(Person.prototype)
obj.name = 'Lee';

console.log(obj.name);
console.log(Object.getPrototypeOf(obj) === Person.prototype);  // true

~~~

위의 방법도 가능하지만 조금 더 깔끔한 방법이 있습니다. 아래의 코드를 보시죠.

~~~javascript

const myProto = { x: 10 };

// prototype을 직접 지정
const obj = {
    y: 20,
    __proto__: myProto
}

console.log(obj.x, obj.y);  // 10 20
console.log(Object.getPrototypeOf(obj) === myProto);  // true

~~~

---

## static property/method

`static` property와 메소드는 생성자 함수로 instance를 생성하지 않아도 참조 호출할
수 있는 property와 메소드를 의미합니다.
<br><br>

다음의 코드를 보시죠.

~~~javascript


function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log(`Hello ${this.name}`);
}

Person.staticProp = 'static property';

Person.staticMethod = function() {
    console.log('static method');
}

const me = new Person('Lee');

Person.staticMethod();  // static method

me.staticMethod();  // TypeError: me.staticMethod is not a function

~~~

그림으로 보면 다음과 같습니다. 지금까지의 내용을 잘 이해하셨다면 어렵지 않은 내용입니다.

{% include image.html
file='es6-javascript-static-property-method.png'
%}


End.

{% include links.html %}
