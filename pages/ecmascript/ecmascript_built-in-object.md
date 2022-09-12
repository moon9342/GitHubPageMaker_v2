---
title: ECMAScript 6.0 built-in objects
sidebar: ecmascript_sidebar
summary: "JavaScript에 존재하는 built-in objects에 대해서 알아보겠습니다."
permalink: ecmascript_built-in-object.html
folder: ecmascript
---

## built-in objects

JavaScript의 객체는 다음과 같이 크게 3가지로 구분됩니다.
<br><br>

* `standard built-in objects`(표준 빌트인 객체)
* `host objects`
* `user-defined objects`
<br><br>

---

## standard built-in objects

JavaScript는 Object, String, Number, Boolean, Symbol, ... 등 총 40여개의
표준 built-in objects를 제공합니다. 이 중 `Math`, `JSON`, `Reflect`를 제외한
표준 built-in objects는 모두 `instance`를 생성할 수 있는 생성자 함수 입니다.
<br><br>

생성자 함수인 표준 built-in objects는 prototype 메소드와 정적 메소드를 제공하고
생성자 함수가 아닌 표준 built-in objects는 정적 메소드만 제공합니다.
<br><br>

간단한 예를 보죠.

~~~javascript

const strObj = new String('Lee');
console.log(typeof strObj);  // object

const numObj = new Number(123);
console.log(typeof numObj);  // object

const boolObj = new Boolean(true);
console.log(typeof boolObj);  // object

const arr = new Array(1,2,3);
console.log(typeof arr);  // object

console.log(Object.getPrototypeOf(strObj) === String.prototype);  // true
console.log(Object.getPrototypeOf(numObj) === Number.prototype);  // true
console.log(Object.getPrototypeOf(boolObj) === Boolean.prototype);  // true
console.log(Object.getPrototypeOf(arr) === Array.prototype);  // true

~~~

string, number, boolean과 같은 `primitive value`가 존재하는데 String, Number, Boolean이
존재하는 이유는 무엇일까요? 우리가 자주 보는 것처럼 원시값은 객체가 아님에도 마치 객체처럼 동작하는
경우가 있습니다.

~~~javascript

const str = 'hello';

console.log(str.length);  // 5
console.log(str.toUpperCase());  // HELLO

~~~

위의 예처럼 `primitive value`에 `dot notation`이나 `bracket notation`로 접근하면 JavaScript Engine이
일시적으로 `primitive value`를 연관된 객체로 변환해 줍니다. 일시적으로 변경된 후 사용되고 다시 `primitive value`로
되돌리게됩니다.
<br><br>

이처럼 string, number, boolean 값에 대해 객체처럼 접근하면 생성되는 객체를 `wrapper object`(래퍼 객체)라고 부릅니다.
문자열 같은 경우 `dot notation`으로 접근 시 `wrapper object`인 String 생성자 함수의 instance가 생성되고 문자열은
`[[StringData]]` 내부 슬롯에 할당됩니다.
<br><br>

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-string-wrapper-object.png'
%}

`wrapper object`의 처리가 종료되면 `primitive value`로 되돌리고 해당 객체는 `garbage collection`의 대상이
됩니다. 코드로 확인해보면 다음과 같습니다.

~~~javascript

const str = 'hello';

str.name = 'Lee';

console.log(str.name);  // undefined

const num = 1.5;

// 고정 소수점 표기로 바꾸는 함수. 숫자는 반올림 처리
console.log(num.toFixed(0));  // 2

console.log(typeof num, num);  // number 1.5

~~~

---

## global object

`global object`(전역 객체)는 코드가 실행되기 전에 JavaScript Engine에 의해 어떤 객체보다도
먼저 생성되는 특수한 객체입니다. 따라서 어떤 객체에도 속하지 않는 최상위 객체입니다.
<br><br>

전역 객체는 JavaScript 환경에 따라 지칭하는 이름이 다릅니다. browser 환경에서는 `window`
(또는 `self`, `this`, `frames`)가 전역 객체를 가지키지만 `Node.js`환경에서는 `global`이
전역 객체를 가리킵니다.
<br><br>

**이런 전역 객체는 JavaScript의 표준 built-in objects와 Host 객체(client web API 혹은 node.js Host API)
그리고 전역 변수와 전역 함수를 property로 가집니다.**
<br><br>

**전역 객체가 최상위 객체라고 말하는 것은 `prototype`상속 관계에서 최상위에 위치한다는 말은 아닙니다.** 단지 전역
객체 자신은 다른 어떤 객체의 property도 아니며 객체의 계층 구조상 가장 위에 있다는 의미입니다.
<br><br>

전역 객체의 특징은 다음과 같습니다.
<br><br>

* 전역 객체는 개발자가 의도적으로 생성할 수 없습니다.
* 전역 객체의 property를 참조할 때 `window`(또는 `global`)을 생략할 수 있습니다.
* 모든 standard built-in objects를 property로 가집니다.
* browser환경에서는 `DOM`, `BOM`, `XMLHttpRequest`, `Web Storage`와 같은 client side
  web API를 property로 가집니다.
* var 키워드로 선언한 전역 변수와 선언하지 않은 변수에 대한 암묵적 전역, 그리고 전역 함수를
  property로 가집니다.

~~~javascript


// browser 환경에서 실행하세요.

console.log(window.parseInt(3.14));  // 3

console.log(parseInt(3.14));  // 3

console.log(window.parseInt == parseInt); // true

var foo = 1;
console.log(window.foo);  // 1

// let이나 const로 선언한 전역 변수는 window객체의
// property가 아닙니다. 
// 이 전역변수는 보이지 않는 개념적인 block내에 존재합니다.
let myName = '홍길동';
console.log(window.myName);  // undefined

bar = 2;
console.log(window.bar);  // 2

function myFunc() {}
console.log(window.myFunc);  // ƒ myFunc() {}

~~~

---

## built-in global property

기억해야 하는 몇가지 `built-in global property`에 대해서 알아보겠습니다.
<br><br>

* `Infinity`<br>
  `Infinity` property는 무한대를 나타내는 숫자 값 `Infinity`를 가집니다.
<br><br>

* `NaN`<br>
  `NaN` property는 숫자가 아님을 나타내는 숫자값 `NaN`을 가집니다. `NaN` property는
  `Number.NaN`과 같습니다.
<br><br>

* `undefined`<br>
  `undefined` property는 `primitive value`인 `undefined`를 값으로 가집니다.

~~~javascript

// browser 환경에서 실행하세요.

console.log(window.Infinity === Infinity); // true
console.log(typeof Infinity);  // number

console.log(window.NaN);  // NaN
console.log(window.NaN === NaN);  // false(NaN은 비교가 안된다.)
console.log(window.isNaN(NaN));  // true

console.log(window.undefined === undefined);  // true

~~~

---

## built-in 전역 함수

기억해야 하는 몇가지 `built-in` 전역 함수에 대해서 알아보겠습니다.
<br><br>

* `isNaN()`<br>
  전달받은 인수가 `NaN`인지 검사하여 그 결과를 boolean으로 리턴합니다.
<br><br>

* `parseInt()`, `parseFloat()`<br>
  전달받은 문자열 인수를 정수, 부동소수점 숫자로 해석해서 리턴합니다.
<br><br>

* `encodeURI()`, `decodeURI()`<br>
  `encodeURI()` 함수는 완전한 `URI`(Uniform Resource Identifier)를 문자열로
  전달받아 이스케이프 처리를 위해 인코딩합니다. 참고로 `URI`는 인터넷에 있는 자원을
  나타내는 유일한 주소를 말하며 하위 개념으로 `URL`, `URN`이 있습니다. 이스케이프 처리한다는
  의미는 네트워크를 통해 정보를 공유할 때 어떤 시스템에서도 읽을 수 있는 아스키 문자셋으로
  변환하는 것을 의미합니다. 특수문자인 공백 같은 경우 `%20`으로 한글같은 경우 1문자당 3byte로
  인코딩 됩니다.

{% include image.html
file='es6-javascript-uri-url-urn.png'
%}

~~~javascript

// browser환경에서 실행하세요.

console.log(isNaN(NaN));  // true
console.log(window.isNaN(NaN));  // true
console.log(isNaN('Hello'));  // true
console.log(isNaN(3.1415));  // false 
console.log(isNaN('10'));  // false (숫자 10으로 변경 가능)
console.log(isNaN(true));  // false (숫자 1로 변환 가능)
console.log(isNaN(null));  // false (숫자 0으로 변환 가능)
console.log(isNaN(undefined));  // true

console.log(parseFloat('3.1415'));  // 3.1415
console.log(parseInt('3.76'));  // 3 (버림처리)

const uri = 'http://example.com?name=홍길동&job=programmer';
const enc = encodeURI(uri);

console.log(enc);
// http://example.com?name=%ED%99%8D%EA%B8%B8%EB%8F%99&job=programmer


~~~



End.

{% include links.html %}
