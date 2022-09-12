---
title: ECMAScript 6.0 Terminology & Data Type
sidebar: ecmascript_sidebar
summary: "이번에는 기본적인 용어 몇가지만 살펴보고 data type으로 넘어가도록 하겠습니다.
용어에 대한 명확한 이해가 있으면 개념을 더 잘 이해할 수 있기 때문입니다. "
permalink: ecmascript_data-type.html
folder: ecmascript
---

## literal

{% include callout.html
type="danger"
content="**`literal`(리터럴)이란 사람이 이해할 수 있는 문자 또는 약속된 기호를 사용하여 value(값)을 생성하는
`notation`(표기법)을 말합니다.**"
%}

코드에서 숫자 `3`은 단순히 아라비아 숫자 3이 아니라 숫자 literal 입니다. 사람이 이해할 수 있는
아라비아 숫자를 이용해 숫자 literal 3을 코드에 기술하면 JavaScript Engine은 이를 평가하여
숫자값 3을 생성해 내는 것이죠.

{% include image.html
file='es6-javascript-literal.png'
%}
<br>

즉, `literal`은 값을 생성하기 위해 미리 약속해 놓은 표기법이라고 할 수 있습니다. 이런
`literal`을 사용하면 아래 그림처럼 다양한 종류(data type)의 값을 생성할 수 있습니다.

{% include image.html
file='es6-javascript-literal-sample.png'
%}

---

## Statement

{% include callout.html
type="danger"
content="`statement`(문)는 프로그램을 구성하는 기본 단위이자 최소 실행단위를 말합니다.**"
%}

이런 `statement`의 집합이 바로 프로그램입니다. `statement`를 작성하고 순서에 맞게 구성하는
것을 우리는 프로그래밍이라고 합니다.
<br><br>

이런 `statement`는 `token`으로 구성됩니다. `token`은 문법적인 의미를 가지며, 문법적으로
더 이상 나눌 수 없는 코드의 기본 요소를 의미합니다. 다음 그림처럼 말이죠.

{% include image.html
file='es6-javascript-statement-token.png'
%}
<br>

`statement`와 관련되서 특이한 기호를 하나 사용하는데 바로 `;` 기호입니다. `;` 기호는
`statement`의 종료를 나타내는 기호입니다. 따라서 statement를 끝낼 때에는 세미콜론을 붙여야
합니다. 한가지 주의해야 할 점은 중괄호로 묶은 `code block`은 뒤에 세미콜론을 붙이지 않습니다.
<br><br>

`code block`은 statement의 종료를 의미하는 `self closing`(자체 종결성)을 가지기 때문입니다.
<br><br>

{% include callout.html
type="danger"
content="**JavaScript에서 이런 `;`(세미콜론)의 사용은 사실은 옵션입니다.**"
%}
<br>

즉, 생략가능하다는 것이죠. 만약 우리가 생략하면 JavaScript Engine이 `statement`의 종료지점이라고
예측되는 곳에 세미콜론을 자동으로 삽입하게 됩니다. 이를 `Automatic Semicolon Insertion`(ASI)라고 합니다. 
이 문제에 대해 생략하자와 꼭 써야한다는 의견이 분분한데 가능하면 개발자가 명확히 `statement`의 종료를 명시해서 불필요한
오류를 방지하는 방법을 사용하는게 좋을 듯 합니다.
<br><br>

한마디로 쓰라는 말이죠!!!

---

## data type

JavaScript의 모든 값은 `data type`을 가집니다. 변수가 아닌 값입니다. JavaScript의 변수는
type을 명시하지 않습니다. `ES6`는 총 7개의 데이터 타입을 제공하는데
크게 `primitive type`(원시타입)과 `reference type`(참조타입)으로 분류할 수 있습니다.

{% include image.html
file='es6-javascript-es6-data-type.png'
%}

---

## data type - number

JavaScript는 다른 언어와는 다르게 정수와 실수를 위한 다양한 type이 존재하지 않습니다. 즉, JavaScript는
숫자를 위해 하나의 `number` type만 존재합니다. 모든 숫자는 내부적으로는 64-bit 실수형식으로 처리합니다.
<br><br>

그래서 다음과 같은 코드 실행 결과가 나오는 것이죠.

~~~javascript

let myVar = 1.0

console.log(1 === myVar);   // true

console.log(4 / 2);  // 2

console.log(3 / 2);  // 1.5 (다른언어에서는 1)  

~~~

number type은 추가적으로 다음의 3가지 특별한 값도 표현할 수 있습니다.
<br><br>

* `Infinity` : 양의 무한대
* `-Infinity` : 음의 무한대
* `NaN` : 산술 연산 불가(Not a Number)

~~~javascript

console.log(10 / 0);   // Infinity

console.log(10 / -0);  // -Infinity

console.log(10 * 'Hello');  // NaN

let myVar = NaN;

console.log(myVar);  // NaN

~~~

---

## data type - string

`string type`은 문자열을 나타내기 위해 사용합니다. JavaScript에는 `char`(문자) 개념이
없습니다. 모두 문자열입니다. 문자열은 0개 이상의 16bit Unicode(UTF-16)의 집합으로 다국어를
표현할 수 있습니다.
<br><br>

문자열의 literal은 `''`(작은 따옴표), `""`(큰 따옴표), <code>``</code>(백틱)으로 텍스트를 감싸면 됩니다.
가장 일반적인 형태는 `''`(작은 따옴표)를 사용하는 것입니다.
<br><br>

이 중 백틱(<code>``</code>)은 `ES6`에서 도입된 것입니다. `ES6`에는 `template literal`이라고 하는 새로운
문자열 표기법이 도입되었습니다. 몇가지 편리한 문자열 처리 기능을 제공하는데 이런 `template literal`은
`runtime`에 일반문자열로 변환되어 처리됩니다. 많이 사용하는 두가지 정도만 알아보도록 하겠습니다.

* `multi-line string`<br>
  당연히 일반문자열에서는 줄바꿈(개행)이 허용되지 않습니다. 그래서 우리는 특수문자(`\n`)를 이용해야 했죠. 하지만
  `template literal`을 이용하면 줄바꿈과 공백을 그대로 표현할 수 있습니다.

~~~javascript

let myHTML1 = '<body>\n    <h1>소리없는 아우성</h1>\n</body>';

console.log(myHTML1);
// <body>
//     <h1>소리없는 아우성</h1>
// </body>

let myHTML2 = `<body>
    <h1>소리없는 아우성</h1>
</body>`

console.log(myHTML2);
// <body>
//     <h1>소리없는 아우성</h1>
// </body>


~~~

* `expression interpolation`(표현식 삽입)<br>
  일반적으로 변수를 출력하거나 문자열화 시킬 때 문자열과 `+` 연산자를 이용하여 문자열 형태로 변형해서
  사용합니다. 하지만 `ES6`의 `template literal`을 이용하면 조금 더 직관적으로 사용할 수 있습니다.

~~~javascript

let last1 = '홍';
let first1 = '길동';

console.log('내 이름은 ' + last1 + first1 + '입니다.');

let last2 = '김';
let first2 = '연아';

console.log(`내 이름은 ${last2}${first2} 입니다.`);

console.log(`계산 결과는 ${10 + 20} 입니다.`);

~~~

---

## data type - boolean

`boolean type`의 값은 `true`, `false` 두 가지만 제공합니다. 우리가 흔히 알고 있는
논리값입니다.

---

## data type - undefined

`undefiend type`의 값은 `undefined`가 유일합니다. `var` keyword로 선언한 변수는
암묵적으로 `undefined`로 초기화되는 것을 앞에서 설명한적이 있습니다.
<br><br>

{% include callout.html
type="info"
content="**`undefined`는 개발자가 의도적으로 할당하는 값이 아닌 JavaScript Engine이 변수를
초기화 할 때 사용하는 값입니다.**"
%}
<br>

그래서 만약 변수의 참조값이 `undefined`라면 참조한 변수가 선언한 후 값이 할당된 적이
없는 다시말해 초기화되지 않은 변수라는 것을 알 수 있습니다.
<br><br>

따라서 `undefined`라는 값은 개발자가 의도적으로 사용하는 것을 권장하지 않습니다. 그렇다면
변수에 값이 없다는 것을 명시하고 싶은 경우 어떻게 해야 할까요? 이런 경우 `null`을 이용합니다.

---

## data type - null

`null type`의 값은 `null`이 유일합니다. 위에서 언급했듯이 `null`은 변수에 값이 없다는 것을
의도적으로 명시할 때 사용합니다. 이는 할당되어 있던 값에 대한 참조를 명시적으로 제거해서
`garbage collection`이 발생되게끔 하겠죠.
<br><br>

또 `null`이 사용되는 다른 경우는 함수가 유효한 값을 반환할 수 없는 경우입니다. 이런 경우 `null`을
리턴하기도 합니다. 예를 들어 `document.getElementById()`와 같은 함수를 사용할 경우 해당 Element를
찾을 수 없으면 `null`을 반환합니다.

---

## data type - symbol

`symbol type`은 `ES6`에 추가된 type으로 변경 불가능한 원시타입의 값입니다.
<br><br>

{% include callout.html
type="info"
content="**이 symbol type의 값은 다른 값과 중복되지 않는 `unique`한 값입니다.**"
%}
<br>

그래서 일반적으로 객체의 property key를 만들기 위해 많이 사용합니다.
<br><br>

이 `symbol`값은 다른 값과 다르게 literal을 이용해 생성할 수 없습니다. 오직 `Symbol()`이라는 함수를 호출해야만
생성할 수 있습니다. 또한 생성된 `symbol`값은 외부로 노출되지 않으며 위에서 언급한 것처럼 `unique`한 값이 됩니다.
<br><br>

다음의 코드를 보죠.

~~~javascript

const mySymbol = Symbol();

console.log(typeof mySymbol);  // symbol

console.log(mySymbol);  // Symbol(), 값이 노출되지 않습니다.

// Symbol() 함수에는 문자열로 인자를 전달할 수 있습니다.
// 이 문자열은 생성된 symbol 값에 대한 description의 용도로만 사용할 수 있고
// symbol값 생성에는 어떠한 영향도 주지 않습니다. 
// 이 문자열은 global symbol registry에서 symbol을 찾을 때 사용할 수 있습니다.

const mySymbol1 = Symbol('소리없는 아우성');
const mySymbol2 = Symbol('소리없는 아우성');

console.log(mySymbol1 === mySymbol2);  // false

// description property를 이용하면 문자열을 얻을 수 있습니다.

console.log(mySymbol1.description);   // 소리없는 아우성

// symbol은 다른 type으로 변환이 안됩니다. 하지만 다음과 같이
// 조건문에서 논리값으로 사용할 수는 있습니다. 

if (mySymbol2) {
    console.log('mySymbol2가 존재합니다.');
}

~~~

`symbol`은 `Symbol.for()`와 `Symbol.forKey()` 메소드를 제공합니다.
<br><br>

`Symbol.for()` 메소드는 인자로 전달받은 문자열을 key로 사용하여 key와 symbol값들이 저장되어 있는
`global symbol registry`(전역 심볼 레지스트리)에서 해당 key와 일치하는 symbol값을 검색합니다.
<br><br>

다음의 코드를 보시죠.

~~~javascript

// global symbol registry에 mySymbol이라는 key로 저장된
// symbol값이 없으면 새로운 symbol을 생성

const s1 = Symbol.for('mySymbol');

// global symbol registry에 mySymbol이라는 key로 저장된
// symbol값이 있으면 해당 symbol값을 반환합니다.

const s2 = Symbol.for('mySymbol');

if (s1 === s2) {
    console.log('같은 symbol입니다.');
}

~~~

한가지 주의해야 할 점은 `Symbol()`함수(`.for()`함수를 사용하지 않는)는 유일무이한
symbol값을 생성하지만 symbol값을 저장하는 key를 지정하지 않기 때문에
`global symbol registry`에 저장되에 관리되지 않는다는 점입니다.
<br><br>

`Symbol.keyFor()` 메소드는 global symbol registry에 저장된 symbol값의
key를 추출할 수 있습니다.

~~~javascript

const s1 = Symbol.for('mySymbol')  // key : mySymbol

console.log(Symbol.keyFor(s1));  // mySymbol

const s2 = Symbol('testSymbol')

console.log(Symbol.keyFor(s2));  // undefined

~~~

이런 `Symbol`이 가장 흔하게 사용되는 곳 중 하나가 바로 아래와 같은 경우입니다. 의미없는 상수를 대체
하는 용도로 사용됩니다.

~~~javascript

// 상,하,좌,우를 나타내는 상수를 정의
// 이때 1~4의 숫자는 의미가 없음.

const Direction = {
    'UP': 1,
    'DOWN': 2,
    'LEFT': 3,
    'RIGHT': 4
}

let myDirection = Direction.UP;

if (myDirection == Direction.UP) {
    console.log('직진중입니다.!');
}

~~~

위와 같은 경우 숫자값에는 특별한 의미가 없고 상수 이름 자체에 의미가 있는 경우입니다.
이런 경우 숫자가 변경된다거나 중복될 수 있는 여지가 있기 때문에 무의미한 상수대신에
`Symbol`을 이용하면 좋습니다.

~~~javascript

const Direction = {
    'UP': Symbol('up'),
    'DOWN': Symbol('down'),
    'LEFT': Symbol('left'),
    'RIGHT': Symbol('right')
}

let myDirection = Direction.UP;

if (myDirection == Direction.UP) {
    console.log('직진중입니다.!');
}

~~~

아직 객체에 대해 자세히 다루지는 않았지만 기본적인 객체 literal은 알고 있을 테니
객체의 `property key`로 symbol을 사용하는 경우도 살펴보겠습니다.
<br><br>

객체의 property key는 빈 문자열을 포함하여 모든 문자열 또는 symbol값으로 만들 수
있습니다. 만약 symbol값을 property의 key로 사용하려면 `[]`(대괄호)를 사용해야 합니다.
property에 접근할 때도 마찬가지로 대괄호를 이용해야 합니다.
<br><br>

이때 한가지 조심해야 하는 점이 있습니다. `symbol`값을 property key로 사용한 property는
`for...in` 구문이나 `Object.keys()` 메소드로 해당 키를 당연히 찾을 수 없습니다.
(키값을 실제로 우리가 알지 못하기 때문에 그렇습니다.)

~~~javascript

const obj = {
    'myName': '김연아',
    [Symbol.for('mySymbol')]: 100
}

console.log(obj['myName']);    // 김연아

console.log(obj[Symbol.for('mySymbol')]);  // 100

// symbol을 property key로 사용하면
// Object.keys()나 for...in 구문에서 나타나지 않습니다.

console.log(Object.keys(obj));  // [ 'myName' ]

~~~

`Symbol`의 특성상 같은 값이 생성되지 않기 때문에 동적으로 객체에 property를 추가할 때
property key가 중복, 충돌할 일이 발생하지 않게 됩니다.
<br><br>

이렇게 symbol을 이용해 property의 key로 사용한 경우 `ES6`에서 도입된
`Object.getOwnPropertySymbols()` 메소드를 이용해야 property를 이용할 수 있습니다.

~~~javascript


const obj = {
    'myName': '김연아',
    [Symbol.for('mySymbol1')]: 100,
    [Symbol.for('mySymbol2')]: 200,
}

console.log(obj['myName']);    // 김연아

console.log(obj[Symbol.for('mySymbol')]);  // 100

// symbol을 property key로 사용하면
// Object.keys()나 for...in 구문에서 나타나지 않습니다.

console.log(Object.keys(obj));  // [ 'myName' ]

console.log(Object.getOwnPropertySymbols(obj));
// [ Symbol(mySymbol1), Symbol(mySymbol2) ]

let myVar = Object.getOwnPropertySymbols(obj)[0];

console.log(obj[myVar]);  // 100

~~~

---

## data type - reference

마지막으로 객체 타입이 있습니다. JavaScript는 객체기반 언어이며 JavaScript를 이루고 있는
거의 모든 것들은 객체입니다. 내용이 많기 때문에 이 부분은 뒤쪽에서 다시 다루도록 하죠.

---

## data type 정리

지금까지 **JavaScript value(값)**의 data type에 대해서 알아봤습니다. 이런 데이터 타입은
메모리 공간의 확보와 참조를 하기 위해서 그리고 값의 해석을 위해 사용됩니다.
<br><br>

즉, 데이터 타입을 알아야 해당 값을 저장할 메모리 공간을 알맞게 확보할 수 있고
이진수로 저장된 값을 어떠한 형태로 해석하느냐를 결정할 수 있다는 말이죠.
<br><br>

JavaScript 값의 데이터 타입에 대해서 알아봤는데 그럼 **변수도 데이터 타입을 가지고
있을까요?**
<br><br>

우리가 잘 알고있는 `C`, `Java`와 같은 언어는 변수를 선언할 때 변수에 data type을
선언해야 합니다. 이를 `explicit type declaration`(명시적 타입 선언)이라하고 이러한
언어를 우리는 정적 타입 언어(static/strong type language)라고 부릅니다.
<br><br>

하지만 JavaScript는 그렇지 않습니다. JavaScript의 변수는 선언이 아닌 할당에 의해
data type이 결정됩니다. 이를 `type inference`(타입 추론)이라고 부르고 당연히
재할당에 의해 변수의 data type은 언제든지 동적으로 변경될 수 있습니다. 이러한
특징을 우리는 `dynamic typing`(동적 타이핑)이라고 부릅니다.
<br><br>

정리하자면 JavaScript는 정적 타입 언어가 아닌 동적 타이핑을 지원하는
동적 타입 언어(dynamic/weak type language)라고 할 수 있습니다. 대표적인
동적 타입 언어에는 `python`이 있습니다.
<br><br>

JavaScript는 이런 동적 타입 언어의 특징 때문에 JavaScript Engine에 의해 값이
동적으로 casting되는 경우가 발생합니다. 이 때문에 예상치 못한 오류를 발생시키기도 하죠.
하지만 프로그램을 상당히 유연하게 끌고 갈 수 있고 코드의 양도 줄일 수 있다는 장점이 있습니다. 물론
그로인한 신뢰성은 떨어진다고 할 수 있습니다.
<br><br>

코드는 명확하게 작성해야 합니다. 가장 좋은 코드는 가독성이 높은 코드라는 걸 항상 상기하면서
명확한 표현이 되도록 그리고 사람이 쉽게 이해할 수 있도록 코드가 되도록 노력과 연습을 해야 합니다.

End.

{% include links.html %}
