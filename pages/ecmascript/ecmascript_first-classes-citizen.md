---
title: ECMAScript 6.0 일급 객체
sidebar: ecmascript_sidebar
summary: "이번장에서는 일급 객체와 함수객체의 property에 대해서 알아보도록 하겠습니다."
permalink: ecmascript_first-classes-citizen.html
folder: ecmascript
---

## 일급 객체

다음의 조건을 만족하는 객체를 우리는 `first-class object`(일급 객체)라고 부릅니다.
<br><br>

* 무명의 literal로 생성할 수 있다. 즉, `runtime`에 생성이 가능하다.
* 변수나 자료구조(배열, 객체)에 저장할 수 있다. (값으로 사용이 가능하다.)
* 함수의 매개변수에 전달할 수 있다.
* 함수의 리턴값으로 사용할 수 있다.
<br><br>

JavaScript의 함수는 위의 조건을 모두 만족하기 때문에 일급 객체입니다. 특별히 일급 함수라는
표현을 쓰기도 합니다.
<br><br>

일급 객체로서 함수가 가지는 가장 큰 특징은 일반 객체와 마찬가지로 함수의 매개변수로 사용할 수
있으며 함수의 리턴값으로 사용할 수 있다는 점입니다. 이는 함수형 프로그래밍을 가능케하는
JavaScript의 중요한 특징 중 하나입니다.

---

## 함수 객체의 property

함수는 객체이기 때문에 `property`를 가지고 있습니다. browser console에서 `console.dir()` 메소드를
이용해 함수 객체 내부를 들여다 보기로 하죠. 비교를 위해 객체 literal을 하나 생성해서 객체 literal의
내부도 같이 비교해보겠습니다.

~~~javascript

// browser에서 실행해야 합니다.

var obj = { name: '홍길동' }

console.dir(obj);


function squre(number) {
    return number * number;
}

console.dir(squre);

~~~

{% include image.html
file='es6-javascript-function-object-inner.png'
%}

{% include callout.html
type="danger"
content="결과에서 확인할 수 있는 것처럼 `arguments`, `caller`, `length`, `name`, `prototype`
property는 일반 객체에는 없는 `함수 객체 고유의 property`입니다."
%}

조심해야 할 것은 browser에서 출력된 내용을 보면 `[[Prototype]]`이 보이는데 모든 객체가 가지고
있는 property입니다. 직접 사용이 안되기 때문에 `__proto__` property로 간접적으로 사용할 수
있습니다.
<br><br>

하나씩 살펴보도록 하죠.

---

## arguments property

함수 객체의 `arguments property`의 값은 `arguments 객체`입니다. 이 객체는 함수 호출시
전달되는 인자들을 담고 있는 순환가능한 유사 배열 객체이고 함수 내부에서 지역 변수처럼 사용됩니다.
즉, 함수 외부에서는 참조할 수 없다는 말이죠.
<br><br>

아시다시피 JavaScript는 함수의 매개변수 개수가 일치하지 않아도 호출이 가능합니다. 함수에 전달된
인자는 `arguments 객체`에 저장되게 됩니다. `arguments 객체`는 전달된 인수의 순서를 `property key`로
사용하고 전달된 인수를 `property 값`으로 사용합니다.
<br><br>

그럼 이런 `arguments 객체`를 어디에 사용할까요? 매개변수의 개수를 확정할 수 없는 가변 인자 함수를
구현할 때 유용합니다.
<br><br>

코드를 보시죠.

~~~javascript

function sum() {
    let res = 0;

    for(let i = 0; i < arguments.length; i++) {
        res += arguments[i];
    }

    return res;
}

console.log(sum(1,2));    // 3
console.log(sum(1,2,3));  // 6
console.log(sum(1,2,3,4,5));  // 15

~~~

`ES6`의 `Rest parameter`의 도입으로 `arguments 객체`의 활용이 줄어들긴 했지만 기본적으로
알아두어야 할 내용입니다.
<br><br>

`Rest parameter`는 매개변수의 이름앞에 `...` 점 3개를 붙여서 정의한 매개변수 입니다 `Rest parameter`는
함수에 전달된 인수들의 목록을 `Array`로 전달 받습니다. **유사 배열 객체가 아니라 배열로 전달받는게 다릅니다.**
<br><br>

아래 예에서 보겠지만 `Rest parameter`는 제일 마지막 parameter에만 적용할 수 있고 당연히 1개만 사용 가능합니다.
또한 선언한 매개변수의 개수를 나타내는 `length property`에 영향을 주지 않습니다.
<br><br>

`argmuments 객체`는 유사 배열 객체이기 때문에 직접적인 배열 메소드를 사용할 수 없습니다. 그래서 배열로 일단
변환한 다음 사용해야 합니다. 하지만 `Rest parameter`는 배열이기 때문에 배열 객체가 가지는 메소드를 직접 사용할 수
있습니다.
<br><br>

함수와 `ES6` 메소드는 `Rest parameter`와 `arguments 객체`를 모두 사용할 수 있습니다. 하지만
`arrow function`은 함수 자체에 `arguments property`를 가지고 있지 않습니다. 즉, `arguments 객체`를
사용할 수 없습니다. `arrow function`을 가변인자 함수를 구현할 때는 반드시 `Rest parameter`를 이용해야 합니다.

~~~javascript

function foo1(...args) {
    console.log(arguments);    
    console.log(args);
}

foo1(1,2,3,4,5)  
// [Arguments] { '0': 1, '1': 2, '2': 3, '3': 4, '4': 5 }
// [ 1, 2, 3, 4, 5 ]

function foo2(param, ...args) {
    console.log(param);
    console.log(args);
}

foo2(1,2,3,4,5)  
// 1
// [ 2, 3, 4, 5 ]

console.log(foo2.length);  // 1

function foo3(...args) {
    let res = args.pop();
    // let res = arguments.pop();  // TypeError
    return res;
}

console.log(foo3(5,6,7,8));  // 8

~~~

---

## caller property

`caller property`는 `ECMAScript` 사양에 포함되지 않는 비표준 property입니다. 따라서
사용하는것을 권장하지 않습니다.
<br><br>

`caller property`는 함수 자신을 호출한 함수를 가리킵니다.

~~~javascript

function foo(func) {
    return func();
}

function bar() {
    return 'caller: ' + bar.caller
}

console.log(bar());   // caller: null
console.log(foo(bar)); 
// caller: function foo(func) {
//     return func();
// }

~~~

---

## length property

함수 객체의 `lenght property`는 함수를 정의할 때 선언한 매개변수의 수를
가리킵니다. 
<br><br>

혼동하지 말아야 하는게 있는데 arguments 객체의 `length property`는
인자(argument)의 개수를 가리키는 것이고 함수 객체의 `length property`는 매개변수
(parameter)의 개수를 가리킵니다.

---

## name property

함수 객체의 `name property`는 함수 이름을 나타냅니다. `ES6` 표준에서 익명 함수의
표현식의 경우 함수 객체를 가리키는 식별자를 값으로 갖게 된다는 것에 주의해야 합니다.

~~~javascript

let namedFunc = function foo() {}

console.log(namedFunc.name);  // foo

let  anonymousFunc = function() {}

console.log(anonymousFunc.name);  // anonymousFunc

// 함수 선언문
function bar() {}

console.log(bar.name);  // bar

~~~

---

## prototype property

`prototype property`는 생성자 함수로 호출할 수 있는 함수 객체, 즉
`constructor`만이 소유하는 property입니다. 기억을 되살려 보자면
함수 선언문, 함수 표현식, 클래스만이 `constructor`이고 arrow function과
`ES6` 메소드(축약 표현식)은 `non-constructor`입니다.
<br><br>

일반 객체와 생성자 함수로 호출할 수 없는 `non-constructor`에는
`prototype property`가 없습니다.
<br><br>

이 `prototype property`는 함수가 객체를 생성하는 생성자 함수로 호출될 때
생성자 함수가 생성한 `instance`가 내부 슬롯 `[[Prototype]]`으로 참조하는
`(상위) prototype 객체`를 가리킵니다.

---

## `__proto__` property

모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 가집니다. 이 `[[Prototype]]` 내부 슬롯은
객체 지향의 상속을 구현하는 `(상위) prototype 객체`를 가리킵니다. `__proto__ property`는
내부 슬롯인 `[[Prototype]]`이 가리키는 `(상위) prototype 객체`에 접근하기 위한 `접근자 property`입니다.
원래 내부 슬롯은 직접적인 접근이 안되기 때문에 이와같은 간접적인 방법을 이용하여 접근을 가능하도록 허용해 줍니다.
<br><br>

`prototype`에 대해서는 조금 뒤에 자세히 알아보도록 하겠습니다.

~~~javascript

const obj = {
    a: 1
}

// 객체 literal 방식으로 생성한 객체의 (상위) 프로토타입 객체는
// Object.prototype 객체입니다.
console.log(obj.__proto__ == Object.prototype); // true

~~~


End.

{% include links.html %}
