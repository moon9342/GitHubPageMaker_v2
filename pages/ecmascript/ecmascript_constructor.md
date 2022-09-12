---
title: ECMAScript 6.0 constructor
sidebar: ecmascript_sidebar
summary: "이전에는 객체 literal을 이용한 객체 생성에 대해서 알아봤습니다. 객체 literal은 가장 일반적이고 간단한 객체 생성방식입니다.
객체는 객체 literal 이외에도 다양한 방법으로 생성할 수 있는데 그 중 하나가 생성자 함수입니다."
permalink: ecmascript_constructor.html
folder: ecmascript
---

## Object 생성자 함수

`new` 연산자와 함께 `Object()` 생성자 함수를 호출하면 빈 객체를 만들어 반환합니다. 그 후에
`property`나 `method`를 추가하여 객체를 완성하는 방법이죠.
<br><br>

~~~javascript

const person = new Object();

person.name = '홍길동'
person.age = 20;

console.log(person);   // { name: '홍길동', age: 20 }

~~~

위의 코드에서 보는 것처럼 생성자 함수란 `new` 연산자와 함께 호출하여 객체를 생성하는 함수를
말합니다. 이렇게 생성자 함수에 의해 생성된 객체를 `instance`라고 부릅니다.
<br><br>

JavaScript는 `Object` 생성자 함수 이외에도 `String`, `Number`, `Boolean`, `Function`,
`Array`, `Date`, `Promise`등의 `built-in` 생성자 함수를 제공합니다. (이 외에도 더 있습니다.)

~~~javascript

const strObj = new String('Lee');

console.log(typeof strObj);  // object
console.log(strObj);  // [String: 'Lee']

const strObj1 = {
    name: 'Lee'
}

console.log(typeof strObj1);  // object
console.log(strObj1);   // { name: 'Lee' }

const numObj = new Number(100);
console.log(numObj);   // [Number: 100]

const dateObj = new Date();
console.log(dateObj);   // 2021-05-30T09:43:35.049Z
console.log(dateObj.toLocaleString());

~~~

---

## 생성자 함수

객체 literal을 이용해 객체를 생성하는 것은 직관적이지만 한번에 하나의 객체만 생성할 수 있습니다.
만약 동일한 `property`를 가지는 객체를 여러개 만들어야 한다면 이는 매우 비효율적인 방법이 되겠죠.
<br><br>

Java의 `class`처럼 동일한 property를 가지는 여러개의 instance를 쉽게 만들 수 있는 방법이
바로 생성자 함수에 의한 객체 생성 방식입니다.
<br><br>

먼저 생성자 함수를 이용해 생성한 객체(instance)와 객체 literal을 이용해 생성한 객체를 출력해서
비교해 보도록 하겠습니다.

~~~javascript

function Person() {

}

const person1 = new Person();

const person2 = {}


console.log(person1); // Person {}
console.log(person2); // {}

~~~

출력 자체가 다르게 됩니다. 특히 browser에서 출력해 객체의 내부를 들여다 보면
같은 형식의 객체가 아님을 알 수 있습니다. 자세한 사항은 뒤에서 알아보기로 하고
일단 생성자 함수로 생성한 instance와 객체 literal로 생성한 객체가 같은 구조를
가지지 않는다는 것만 기억하도록 하죠.
<br><br>

그럼 일반적인 형태의 생성자 함수를 보도록 하죠.

~~~javascript

// 생성자 함수
// 생성자 함수는 대문자로 시작
function Circle(radius) {
    this.radius = radius;
    this.getDiameter = function() {
        return 2 * this.radius;
    }
}

// instance의 생성
const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter());  // 10
console.log(circle2.getDiameter());  // 20

~~~

코드 자체는 어렵지 않습니다. 우리가 알고 있는 함수를 생성자 함수로 이용하는 것입니다. 단,
`this` 키워드가 나오는데 이 `this` 키워드는 사용하는 곳에 따라 그 의미가 달라집니다.
<br><br>

{% include image.html
file='es6-javascript-this-various-use.png'
%}

간단한 예를 보기로 하죠.

~~~javascript

// 해당 코드는 browser 환경에서 실행시켜야 합니다.

// 함수는 다양한 방식으로 호출될 수 있습니다.
function foo() {
    console.log(this);
}

foo();   // browser 환경의 전역객체인 window 출력

const obj = { foo };  // ES6 축약 표현

// const obj1 = {
//     foo: function() {
//         console.log(this);
//     }
// }

obj.foo();   // {foo: ƒ}

const inst = new foo();  // foo {}

~~~

위에서 보는 것처럼 생성자 함수는 객체(인스턴스)를 생성하는 함수를 말합니다. Java와 `class`기반의
객체지향언어와는 다르게 그 형식이 일반 함수와 동일한 방법으로 생성자 함수를 정의하고

{% include callout.html
type="danger"
content="`new` 연산자와 함께 호출하면 해당 함수는 생성자 함수로 동작합니다."
%}

만약 `new` 연산자와 함께 생성자 함수를 호출하지 않으면 일반 함수로 동작합니다. 이 경우 함수의 return값이
없기 때문에 `undefined`가 리턴됩니다.

~~~javascript

// 함수는 다양한 방식으로 호출될 수 있습니다.
function foo() {
    console.log(this);
}

const inst = new foo();  

const result = foo();
console.log(result);  // undefined

~~~

생성자 함수로 사용될 때 JavaScript Engine은 암묵적인 처리를 통해 `instance`를 생성하고 반환합니다.
즉, `this` 키워드로 `binding`(바인딩)된 생성된 `instance`가 암묵적으로 반환되기 때문에 `return` 키워드가
필요없습니다. 만약 `return` keyword를 사용한다면 `instance`에 바인딩된 `this`가 아닌 리턴값이 대신
전달됩니다.

~~~javascript

// 함수는 다양한 방식으로 호출될 수 있습니다.
function Circle(radius) {
    this.radius = radius;
    // return {};

    // new로 호출 시 명시적으로 원시값을 반환하면 무시되고 this를 반환
    // return 10;
}

const inst = new Circle(100);
console.log(inst);  // Circle { radius: 100 }

const result = Circle(100);
console.log(result);  // undefined

~~~

위의 예와 같이 혼동되는 경우가 생기게 됩니다. 따라서 생성자 함수로 사용할 때는 반드시
`return`구문을 생략해야 합니다.
<br><br>

조금 더 알아보도록 하죠.
<br><br>

함수는 그 자체로 객체입니다. 따라서 다음과 같은 코드가 가능합니다.

~~~javascript

function foo() {}

foo.myName = '홍길동';
foo.myFunc = function() {
    console.log(this);
}

console.log(foo.myName);  // 홍길동
foo.myFunc();  // { [Function: foo] myName: '홍길동', myFunc: [Function] }

~~~

일반 객체는 호출할수 없지만 함수는 호출할 수 있습니다. 따라서 함수는 일반객체가 가지고 있는
내부 슬롯과 내부 메소드는 물론 함수로 동작하기 위한 내부 슬롯과 내부 메소드를 별도로 가지고
있습니다.
<br><br>

대표적인 내부 슬롯은 `[[Environment]]`, `[[FormalParameters]]`이고 대표적인
내부 메소드는 `[[Call]]`, `[[Construct]]`입니다.
<br><br>

함수가 일반 함수로 호출되면 내부에 있는 `[[Call]]`이 호출되고 `new` 연산자와 함께
생성자 함수로 호출되면 내부 메소드 `[[Construct]]`가 호출됩니다.
<br><br>

참고로 내부 메소드 `[[Call]]`을 가지는 함수 객체를 `callable`이라고 하며, 내부 메소드
`[[Construct]]`를 가지는 함수 객체를 `constructor`, `[[Construct]]`를 가지지 않는
함수 객체를 `non-constructor`라고 부릅니다.
<br><br>

모든 함수 객체는 반드시 `callable`이어야 합니다. 내부 메소드 `[[Call]]`을 가지고 있으므로
호출할 수 있는 거지요. 하지만 모든 함수가 `[[Construct]]`를 가지는 것은 아닙니다. 다시말해
함수는 `constructor`일 수도 있고 `non-constructor`일 수도 있습니다.
<br><br>
그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-callable-constructor.png'
%}

그러면 `constructor`와 `non-constructor`는 어떻게 구별할까요?
<br><br>

* `constructor`: 함수 선언문, 함수 표현식, 클래스
* `non-constructor`: 메소드(`ES6` 메소드 축약 표현), `arraow function`
<br>

여기서 주의해야 할 점은 `ECMAScript`에서 `method`로 인정하는 범위가 일반적인 의미의
`method`보다 상당히 좁다는 것입니다.
<br><br>

예를 들어보죠.

~~~javascript

// 함수 선언문
function foo() {}

// 함수 표현식
const bar = function() {};

// property x의 값으로 할당된것은
// 일반 함수로 정의된 함수이기 때문에
// method로 인정하지 않습니다.
const barx = {
    x: function() {}
};

// 일반 함수로 정의된 함수만이 constructor
new foo();
new bar();
new barx.x();

// 화살표 함수 정의
const arrow = () => {};
new arrow();  // TypeError: arrow is not a constructor

// 메소드 정의(ES6 축약 표현만 메소드로 인정)
const obj = {
    x() {}
}
new obj.x();  // TypeError: obj.x is not a constructor

~~~

지금까지의 내용을 잘 이해하셨으면 확인하는 차원에서 다음의 코드를 한번 확인해
보시면 될 듯 합니다.

~~~javascript


function add(x, y) {
    return x + y;
}

let inst = new add();

console.log(inst);  // add {}

// 객체를 반환하는 일반함수
function createUser(name, role) {
    return {name, role};
}

inst = new createUser('Lee', 'admin');
console.log(inst);  // { name: 'Lee', role: 'admin' }

// 생성자 함수
function Circle(radius) {
    this.radius = radius;
    this.getDiameter = function() {
        return 2 * this.radius;
    };
}

// new 연산자 없이 생성자 함수 호출
const circle = Circle(5);
console.log(circle);  // undefined (리턴구문이 없기때문에)

console.log(radius);  // 5 
// (일반함수의 this는 전역객체인 window나 global을 지칭하기 때문)

console.log(getDiameter()); // 10

circle.getDiameter() // TypeError: Cannot read property 'getDiameter' of undefined

~~~


End.

{% include links.html %}
