---
title: ECMAScript 6.0 Operator & Type 변환
sidebar: ecmascript_sidebar
summary: "기본적인 연산자들은 제외하고 특별히 기억해야 하는 연산자 그리고 JavaScript에서 
특이하다고 생각되는 몇가지 사항들에 대해서만 알아보도록 하겠습니다."
permalink: ecmascript_operator.html
folder: ecmascript
---

## + 연산자와 묵시적 형변환

가장 대표적인 연산자 중 하나인 `+` 연산자입니다. 피 연산자 중 하나 이상이 문자열인 경우 문자열 연결 연산자로
동작하며 그 외에는 산술 연산자로 사용됩니다. 그 외의 경우도 다음의 코드로 알아보죠.

{% include callout.html
type="danger"
content="**기본적으로 알아두어야 할 사항은 모든 연산자는 같은 데이터 타입에서만 연산이 수행된다는 점입니다. 그로 인해
묵시적 형 변환이 일어나게 되는것이구요.**"
%}

~~~javascript

// 산술 연산자
console.log(1 + 2);   // 3

// 문자열 연결 연산자
console.log(1 + '2');   // '12'

// true는 1, false는 0으로 간주합니다.
console.log(1 + true);  // 2
console.log(1 + false);  // 1

// null은 0으로 간주합니다.
console.log(1 + null); // 1

// undefined는 숫자로 변환되지 않습니다.
console.log(1 + undefined); // NaN

~~~

---

## Comparison Operator(비교 연산자)

비교 연산자는 크게 2가지가 있습니다. 하나는 `loose equality`(동등 비교)연산자이고
다른 하나는 `strict equality`(일치 비교)입니다.
<br><br>

동등 비교는 `==` 기호를 이용하며 값이 같은지를 체크합니다. 좌항과 우항을 비교할 때
당연히 같은 데이터 타입이어야 비교가 되기 때문에 먼저 묵시적 형 변환을 통해 데이터
타입을 통일시킨 후 같은 값인지를 비교합니다. 묵시적 형 변환이 일어나기 때문에 잘못하면
예기치 못한 오류를 발생할 여지가 있습니다.
<br><br>

반면 일치 비교는 `===` 기호를 이용합니다. 좌항과 우항의 피 연산자가 타입도 같고
값도 같은 경우에만 `true`를 반환합니다. 즉, 묵시적 형 변환이 일어나지 않습니다.
<br><br>

아래 코드를 통해 주의해야 할 몇가지를 살펴보도록 하겠습니다.

~~~javascript

console.log(`5 == 5 ${5 == 5}`);  // true

console.log(`5 == '5' ${5 == '5'}`);  // true
console.log(`5 === '5' ${5 === '5'}`);  // false

console.log(`0 == '' ${0 == ''}`); // true

console.log(`false == 0 ${false == 0}`); // true

console.log(`null == 0 ${null == 0}`); // false

console.log(`false == null ${false == null}`); // false
console.log(`false == undefined ${false == undefined}`); // false

console.log(`null == undefined ${null == undefined}`); // true

// NaN의 비교는 연산자를 이용해서 할 수 없습니다.
// Number.isNaN() 메소드를 이용해서 비교합니다.

console.log(`NaN == NaN ${NaN == NaN}`); // false
console.log(`Number.isNaN(NaN) ${Number.isNaN(NaN)}`); // true


~~~

추가적으로 `!=`은 `==`의 반대개념이고 `!==`은 `===`의 반대개념의 연산자입니다.

---

## 삼항 조건 연산자

삼항 조건 연산자는 조건식의 평가에 따라 반환할 값을 결정하는 연산자입니다. 다음 그림으로
설명이 될 듯 합니다.

{% include image.html
file='es6-javascript-ternary-operator.png'
%}

---

## 그 외의 연산자

`typeof` 연산자는 피 연산자의 데이터 타입을 문자열 형태로 반환합니다. `typeof`연산자는
`string`, `number`, `boolean`, `undefined`, `symbol`, `object`, `function` 7개의
값 중 하나를 반환하는데 `null`을 반환하지는 않습니다. 그리고 함수인 경우 `function`을 반환한다는
것도 기억해 두셔야 합니다.
<br><br>

~~~javascript

console.log(typeof 100);        // number
console.log(typeof 'Hello');    // string
console.log(typeof undefined);  // undefined
console.log(typeof true);       // boolean 
console.log(typeof Symbol());   // symbol
console.log(typeof [1,2,3]);    // object

console.log(typeof null);       // 주의! object

let myFunc = function() {
    console.log('Hello World');
}

console.log(typeof myFunc);       // 주의! function

~~~

이처럼 `typeof` 연산자가 반환하는 문자열은 Data Type과 정확히 일치하지 않는다는 것에 주의해야 합니다.

{% include callout.html
type="danger"
content="**`null`값에 typeof 연산자를 적용하면 `object`가 나옵니다. 버그의 일종이지만 기존코드에 영향을 줄 수
있다는 이유로 아직 수정되지 않고 있습니다. 따라서 값이 `null`인지를 확인할 때는 `===` 연산자를 이용해야
합니다.**"
%}

또한 선언하지 않은 식별자를 typeof 연산자로 연산해보면 `undefined`값이 리턴되는 것도 알아두도록 하죠.
<br><br>

특이한 연산자가 있는데 이는 `ES11(ECMAScript 2020)`에서 도입된 `optional chaining` 연산자인
`?.`과 null 병합 연산자인 `??`입니다.
<br><br>

`?.` 연산자는 좌항의 피 연산자가 null 또는 undefined인 경우 undefined를 반환하고 그렇지 않으면
우항의 property를 참조하는 연산자 입니다.
<br><br>

`??` 연산자는 좌항의 피 연산자가 null 또는 undefined인 경우 우항의 피 연산자를 반환하고 그렇지 않으면
좌항의 피 연산자를 반환하는 연산자 입니다.
<br><br>

이 구문은 `ES11` 구문이기 때문에 버전이 맞지 않으면 `VS Code`에서 실행시킬 수 없습니다. 나중에 코드로
확인해 보도록 하죠.

---

## type 변환

JavaScript의 모든 값은 type이 있습니다. 값의 type은 개발자의 의도에 따라 혹은
JavaScript Engine에 의해 다른 type으로 변환이 가능합니다.
<br><br>
개발자의 의도에 따라 값의 type을 변환하는 것을 `explicit coercion`(명시적 타입 변환) 혹은
`type casting`(타입 캐스팅)이라고 하며 JavaScript Engine에 의해 자동으로 type이 변경되는
것을 `implicit coercion`(묵시적 타입 변환) 혹은 `type coercion`(타입 강제 변환)이라고 합니다.
<br><br>
상당히 많은 경우의 수가 존재하는데 기억해야 하는 몇가지만 살펴보도록 하겠습니다.
<br><br>
묵시적 타입 변환에서 가장 많이 사용하는 건 `boolean` type으로의 변환입니다.
JavaScript Engine은 boolean type이 아닌 값들을 `Truthy` 값(참으로 평가되는 값)과 `Falsy` 값으로 분류합니다.
<br><br>
제어문과 같은 조건식에서 `Truthy` 값은 당연히 true로, `Falsy` 값은 false로 묵시적 타입 변환이
일어납니다. 다음의 값들은 `false`로 평가되는 `Falsy` 값입니다.
<br><br>
* `false`
* `undefined`
* `null`
* `0`
* `NaN`
* `''`(empty string)
<br><br>

위의 값 이외의 모든 값들은 `Truthy` 값으로 간주됩니다.
<br><br>
`explicit coercion`(명시적 타입 변환) 혹은 `type casting`(타입 캐스팅)을 하는 방법은
여러가지 방법이 있습니다. 그 중 대표적인 것 몇 가지만 알아보고 가도록 하겠습니다.

---

## string type으로 변환

일반적으로 크게 3가지 방법이 있는데

* `String` 생성자 함수를 new 연산자 없이 호출하는 방법
* `Object.prototype.toString()` 메소드를 사용하는 방법
* 문자열 연결 연산자(`+`)를 사용하는 방법

이렇게 있습니다. 코드로 알아보죠

~~~javascript

// String 생성자 함수를 new 없이 호출하는 방법
console.log(typeof String(1));  // string
console.log(typeof String(NaN)); // string
console.log(typeof String(Infinity)); // string

// Object.prototype.toString() 메소드 호출
console.log(typeof (1).toString());  // string
console.log(typeof (NaN).toString()); // string
console.log(typeof (Infinity).toString()); // string

// 문자열 연결 연산자를 사용
console.log(1 + '');  // 1
console.log(NaN + '');  // NaN
console.log(Infinity + '');  // Infinity

~~~

---

## number type으로 변환

number type이 아닌값을 number type으로 변환하는 방법은 다음과 같습니다.

* `Number` 생성자 함수를 new 연산자 없이 호출하는 방법
* `parseInt()`, `parseFloat()` 함수를 사용하는 방법(문자열에 대해서만 사용가능)
* `+`(단항 연산자), `*`(산술 연산자)를 사용하는 방법

이것 역시 코드로 알아보죠.

~~~javascript

console.log(typeof Number('0'));
console.log(typeof Number('3.1415'));
console.log(Number(true));  // 1

console.log(typeof parseInt('100'));  // number
console.log(typeof parseFloat('3.1415'));  // number

console.log(typeof +'0'); 
console.log(typeof +false); 

console.log(typeof('0' * 1)); // number
console.log(typeof(true * 1)); // number

~~~

---

## boolean type으로 변환

boolean type이 아닌값을 boolean type으로 변환하는 방법은 다음과 같습니다.

* `Boolean` 생성자 함수를 new 연산자 없이 호출하는 방법
* 부정논리연산자(`!`)를 두번 연속 사용하는 방법

이것 역시 코드로 알아보죠.

~~~javascript

console.log(Boolean('x'));  // true
console.log(Boolean(0));  // false
console.log(Boolean(NaN));  // false
console.log(Boolean(null));  // false
console.log(Boolean({}));  // true (빈 객체는 true로 변환)

console.log(!!'x');  // true 
console.log(!!0);  // false
console.log(!!NaN);  // false
console.log(!!null);  // false
console.log(!!{});  // true 

~~~


End.

{% include links.html %}
