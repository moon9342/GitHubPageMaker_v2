---
title: ECMAScript 6.0 function basic
sidebar: ecmascript_sidebar
summary: "function(함수)는 JavaScript에서 가장 중요한 개념입니다. 특히 JavaScript의 다른 중요한 개념들과
묶여 있는 내용이니 잘 이해해 보도록 하죠."
permalink: ecmascript_function-basic.html
folder: ecmascript
---

## function

프로그래밍의 함수를 한마디로 정의하자면 다음과 같습니다.

{% include callout.html
type="danger"
content="함수는 일련의 과정을 statement(문)으로 구현하고 `code block`(코드 블록)으로 감싸서 하나의
실행단위로 적용한 것"
%}

함수는 함수의 정의(`definition`)를 통해 생성되고 실행을 명시적으로 지시해야 합니다. 즉 함수 호출
(`function call/invoke`)이 일어나야 실행됩니다.
<br><br>

아래 그림으로 3가지 용어(`parameter`, `argument`, `return value`)에 대해 알아보고 가죠.

{% include image.html
file='es6-javascript-parameter-argument-return-value.png'
%}

<br>
왜 함수를 사용할까요? 함수는 코드의 중복을 억제하고 재활용성을 높여서 코드의 유지보수성을 높일 수 있습니다.
또한 실수를 줄여 코드의 신뢰성을 높이는 효과도 있기 때문에 우리는 함수를 이용하는 것입니다.

---

## function literal

JavaScript 함수는 object type입니다. 따라서 숫자를 숫자 literal로, 객체를 객체 literal로 생성했던
것처럼 함수도 함수 literal로 생성할 수 있습니다.
<br><br>

아래의 그림은 함수 literal을 변수에 할당하는 코드입니다.

{% include image.html
file='es6-javascript-function-literal-variable-assign.png'
%}

<br><br>
**함수이름은 식별자입니다.**

{% include callout.html
type="danger"
content="이 함수이름은 함수 몸체내부에서만 참조할 수 있는 식별자입니다."
%}

이런 함수이름은 생략할 수 있는데 함수이름이 있는 함수를 `named function`(기명 함수), 이름이 없는 함수를
`anonymous function`(익명 함수)라고 합니다.
<br><br>

함수 literal은 다른 literal처럼 평가되어 값을 생성합니다. 이 함수 literal의 평가 값은 `object`(객체)
입니다. 즉, 함수는 객체라는 말이죠.
<br><br>

함수는 객체지만 다른 객체와 차이가 있습니다. 일반 객체는 `call`(호출)을 할 수 없지만 함수는 `call`(호출)을
할 수 있습니다. 그리고 일반 객체에는 없는 고유한 `property`를 가지게 됩니다.

---

## function definition

JavaScript는 함수를 정의하는 방법이 4가지 있습니다. 함수 선언문, 함수 표현식, Function 생성자 함수,
그리고 `ES6`에서 도입된 `arrow function`이 있습니다. 아래 그림으로 설명이 됩니다.

{% include image.html
file='es6-javascript-function-definition-case.png'
%}

<br>
위의 그림처럼 **함수 선언문은 함수 literal과 형태가 같습니다.** 단, 함수 literal은 함수이름을 생략할 수 있지만
함수 선언문은 함수이름을 생략할 수 없습니다.
<br><br>

여기서 약간 혼동되는 사항이 있는데 우리가 알다시피 `statement`(문)은 변수에 할당할 수 없습니다. 하지만 아래의
코드를 보면 함수 선언문을 변수에 할당하는 것처럼 보입니다.

~~~javascript

let myFunc = function add(x, y) {
    return x + y;
}

~~~

사실 이 경우 변수에 할당되는 함수는 JavaScript Engine에 의해서 함수 선언문인지 함수 literal인지가 결정됩니다.
즉, 위와 같은 경우는 함수 literal을 이용해서 함수를 생성한 경우라고 보시면 됩니다. 그래서 이와 같은 혼란을 없애고자
일반적으로 **함수 literal을 사용하는 경우 기명 함수가 아닌 익명 함수를 사용**하게 됩니다.
<br><br>

또 다른 경우를 하나 더 보도록 하죠.

~~~javascript

function foo() {
    console.log('foo');
}

foo();

(function bar() {
    console.log('bar');
});

bar();  // bar is not defined

~~~

위의 예에서 `foo`은 함수 선언문으로 해석됩니다. 하지만 그룹 연산자인 `()` 내에 있는
`bar`는 함수 선언문이 아닌 함수 literal로 해석됩니다. 왜냐하면 그룹 연산자인 `()`의
피 연산자는 반드시 값으로 평가될 수 있는 표현식이 되어야 하기 때문입니다. 따라서 `bar`는
함수 literal이고 변수에 저장되기 않았기 때문에 호출을 할 수 없게 됩니다.
<br><br>

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-function-literal-memory.png'
%}

<br>
이전에 언급했던 것처럼 함수이름은 함수 몸체 내에서만 참조할 수 있는 식별자라는걸 기억하도록 하죠.
<br><br>

그러면 위의 `foo` 함수는 함수 선언문인데 이건 어떻게 해서 함수를 호출 할 수 있었던 걸까요? 함수이름은
방금 설명했듯이 함수 몸체 내에서만 참조할 수 있을 텐데 말이죠.

{% include callout.html
type="danger"
content="결론적으로 말하자면 `foo`는 JavaScript Engine이 암묵적으로 만든 식별자 입니다."
%}

{% include image.html
file='es6-javascript-function-definition-memory.png'
%}

{% include callout.html
type="danger"
content="결국 함수는 함수 이름으로 호출하는 것이 아니라 함수 객체를 가리키는 식별자로 호출하게 됩니다."
%}

{% include image.html
file='es6-javascript-function-call-by-identifier.png'
%}

---

## function expression(함수 표현식)

JavaScript의 함수는 객체입니다. 객체가 값의 의미로 사용될 때 우리는 일급 객체라는 표현을 사용하는데
JavaScript의 함수는 일급 객체입니다.
<br><br>

함수는 일급 객체이기 때문에 함수 literal로 생성한 함수 객체를 변수에 할당 할 수 있습니다. 이러한
함수 정의 방식을 `function expression`(함수 표현식)이라고 합니다. 일반적으로 함수 표현식을 이용해
함수를 정의할때는 익명 함수를 이용하는것이 일반적입니다.

---

## function hoisting

`variable hoisting`처럼 `function`역시 `hoisting`이 발생합니다. 하지만 약간 차이가 있습니다.
<br><br>

코드로 알아보죠.

~~~javascript

console.log(add(2, 5));  // 7
console.log(sub(2, 5));  // sub is not a function

function add(x, y) {
    return x + y;
}

var sub = function(x, y) {
    return x - y;
}

~~~

위의 예제에서 알 수 있듯이 함수 선언문으로 정의한 함수는 함수 선언문 이전에 호출이 가능합니다. 하지만
함수 표현식으로 정의한 함수는 함수 표현식 이전에 호출이 불가능 합니다. 그 이유는 함수의 생성 시점이
다르기 때문입니다.
<br><br>

모든 선언문이 그러하듯이 함수 선언문도 `runtime`이전에 JavaScript Engine에 의해서 먼저 실행됩니다.
따라서 함수 선언문으로 함수를 정의하면 `runtime`이전에 함수 객체가 먼저 생성되고 JavaScript Engine은
함수 이름과 동일한 이름의 식별자를 암묵적으로 생성하고 생성된 함수 객체를 할당하게 되는 거죠.
<br><br>

즉, `function hoisting`이 발생하게 됩니다. 단 `var`과 `function expression`을 이용해 만든 함수는
그 생성시점이 다릅니다. 식별자는 `variable hoisting`에 의해 먼저 생성되고 `undefined`로 초기화 되지만
실제 함수객체는 `runtime`에 생성되게 됩니다. 변수에 assign될 때 생성된다는 것이죠. 그렇기 때문에 위의
코드에서 오류가 발생하게 됩니다.
<br><br>

여하간 이런 `function hoisting`은 어찌 보면 함수를 호출하기 전에 반드시 함수를 선언해야 한다는
당연한 규칙을 무시합니다. 그래서 `JSON`의 창시자인 더글라스 크라포드를 포함한 몇몇 사람들은 함수 선언식
보다는 함수 표현식 사용을 더 권장합니다.

---

## Function 생성자 함수

JavaScript가 기본적으로 제공하는 `built-in function`인 Function 생성자 함수에 매개변수와
함수몸체를 문자열 형태로 전달하면 함수 객체를 생성해서 반환해 줍니다.
<br><br>

다음의 예제 하나만 보고 가죠.

~~~javascript

let add = new Function('x','y', 'return x + y');

console.log(add(2,5));  // 7

~~~

단적으로 말해 이 방식은 일반적이지도 않고 바람직하지도 않습니다. 기존에 얘기했던 함수와 다르게
동작하게 됩니다. 그냥 이렇게도 할 수 있다 정도로 알아두고 가급적 사용은 자제하는게 좋습니다.

---

## arrow function

`ES6`에 도입된 `arrow function`(화살표 함수)는 function keyword 대신 화살표를 사용해
좀 더 간략한 방법으로 함수를 선언할 수 있습니다. 화살표 함수는 항상 `anonymous function`(익명 함수)로
정의합니다.
<br><br>

단순히 축약표현이 아니라 이렇게 `arrow function`을 이용하면 **기존의 함수와 조금 다른 특성**을 가지게 됩니다.
이 부분은 내용이 조금 더 진행되어야 하기 때문에 나중에 다시 한번 알아보도록 하고 여기서는 형태만 확인하고
넘어가도록 하겠습니다.

~~~javascript

const add = (x,y) => x + y;

console.log(add(2,5));  //  7

~~~

---

## function call(함수 호출)

함수 호출은 우리가 알고 있는 것과 그리 차이가 없습니다. 여기서는 몇가지 JavaScript에서 특이하다고
생각되는 것들만 코드로 알아보도록 하겠습니다.
<br><br>

* JavaScript 함수는 인자의 개수가 맞지 않아도 함수 호출이 가능합니다.

~~~javascript

function add(x, y) {
    return x + y;
}

console.log(add(2));  // NaN
// Error가 아니라 호출이 가능.
// 이 경우 x에는 2가 y에는 undefined가 전달되며
// 연산결과는 NaN임

~~~

* 인자의 개수를 더 많이 해서 호출하는 경우 역시 호출이 가능하고 남는 인자는 버려집니다.

~~~javascript

// 남는 인자는 버려집니다. 
// 대신 argements라는 객체 요소로 들어갑니다.

function add(x, y) {
    console.log(arguments);  // [Arguments] { '0': 2, '1': 5, '2': 10 }
    return x + y;
}

console.log(add(2,5,10));


~~~

원칙적으로 함수의 매개변수의 개수는 제한이 없습니다. 하지만 일반적으로 코드의 명확성을 위해
매개변수는 최대 3개 이상 넘지 않는 것을 권장하고 있습니다. 그럼 매개변수가 많은 경우는 어떻게
할까요? `object`를 넘기는 방법을 이용합니다.
<br><br>

`object`를 인자로 사용하는 경우 `call-by-value`가 아닌 `call-by-reference`형태가 되기
때문에 함수 내부에서 변경한 객체가 함수 외부에 영향을 미치는 `side-effect`가 발생할 여지는
있습니다. 다음의 예제처럼 말이죠.

~~~javascript

function changeVal(primitive, obj) {
    primitive += 100;
    obj.name = 'Kim';
}

let num = 100;
let person = { name: 'Lee' };

console.log(num);    // 100
console.log(person); // { name: 'Lee' }

changeVal(num, person);

console.log(num);    // 100
console.log(person); // { name: 'Kim' }

~~~

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-function-call-by-reference.png'
%}

함수의 결과값을 호출한 곳으로 돌려주기 위해서는 `return`이라는 키워드를 사용합니다.
만약 `return`이라는 키워드 없이 함수가 종료되거나 혹은 `return;` 행태로 돌려주는 값을 명시하지
않는경우는 어떤값이 함수를 호출한 곳으로 돌아갈까요?

{% include callout.html
type="danger"
content="이런경우는 `undefined`값이 돌아가게 됩니다."
%}

---

## 즉시 실행 함수

`Immediately InVoked Function Expression`(IIFE-즉시 실행 함수)라는 함수 호출 기법이 있습니다.
함수의 정의와 동시에 즉시 호출되는 함수를 말하며 즉시 실행 함수는 단 한번만 호출되며
다시 호출할 수 없습니다. 그렇기 때문에 대부분 `anonymous function`(익명 함수)행태로 사용됩니다.

~~~javascript

(function add() {
    let x = 10;
    let y = 20;
    
    console.log(x + y);

    return x + y;
}());   

~~~

굳이 왜 이런 방법을 사용할까요? 한번만 쓰고 버릴용도면 함수를 굳이 사용할 이유는 없어보이는데 말이죠.
즉시 실행 함수내에 코드를 모아두면 혹시 있을수도 있는 변수나 함수이름의 충돌을 막을 수 있습니다.
`scope`가 달라지기 때문입니다. 전역변수의 사용은 자제하는 것이 좋은데 이런 경우 즉시 실행 함수를 이용하면
전역변수의 사용을 줄일 수 있습니다.

---

## 중첩 함수

함수 내부에 정의된 함수를 `nested function`(중첩 함수) 혹은 `inner function`(내부 함수)라고 합니다.
그리고 중첩 함수를 포함하는 함수는 `outer function`(외부 함수)라는 표현을 사용합니다.
<br><br>

중첩 함수는 외부 함수에서만 호출될 수 있습니다. 그리고 일반적으로 자신을 포함하는 외부 함수를 돕는
`helper function`(헬퍼 함수)의 역할을 수행합니다.

~~~javascript

function outer() {
    let x = 1;

    function inner() {
        let y = 2;

        console.log(x + y);
    }

    inner();
}

outer();

~~~

---

## callback function(콜백 함수)

프로그래밍을 하다 보면 아래의 경우와 같은 일이 자주 발생합니다.
<br><br>

어떤 일을 반복하는 repeat() 함수를 정의해서 사용하고 있었습니다.

~~~javascript

function repeat(n) {

    for(let i=0; i< n; i++) {
        console.log(i);
    }
}

repeat(5);  // 0 ~ 4 출력

~~~

그런데 repeat() 함수와 유사하지만 약간 기능이 다른 함수가 필요하게 됬습니다.
입력으로 들어온 `n`까지 출력하는 기능을 동일하지만 이번에는 홀수만 출력하게끔 해야 합니다.
<br><br>

이런 경우 우리는 로직을 이용해서 함수의 코드를 변경하거나 아니면 아예 함수를 또 만들어서 사용합니다.
이미 잘 동작하고 있는 기능을 가지는 함수를 변경하는건 상당한 부담을 안게 됩니다. 그래서 아예
새롭게 만드는 선택을 하는 거죠.

~~~javascript

function repeat1(n) {

    for(let i=0; i< n; i++) {
        console.log(i);
    }
}

repeat1(5);  // 0 ~ 4 출력

function repeat2(n) {

    for(let i=0; i< n; i++) {
        if (i % 2) {
            console.log(i);
        }
    }
}

repeat2(5);  // 1, 3 출력

~~~

위의 두 함수는 반복하는 일은 변하지 않고 공통적으로 수행하지만 반복하면서
하는 일은 다릅니다. 즉, 함수의 일부분만이 다르기 때문에 함수를 새롭게 정의한 것이죠.
<br><br>

이 문제는 함수를 합성하는 것으로 해결할 수 있습니다.
<br><br>

함수의 변하지 않는 공통 로직은 미리 정해 두고 경우에 따라 변경되는 로직은 추상화해서
함수 외부에서 함수 내부로 전달받는 것입니다.

~~~javascript

function repeat(n, f) {

    for(let i=0; i<n; i++) {
        f(i);
    }
}

let logAll = function(i) {
    console.log(i);
}

let logOdd = function(i) {
    if (i % 2) console.log(i);
}

repeat(5,logAll); // 0 ~ 4까지 출력
repeat(5,logOdd); // 1, 3 출력

~~~

위의 repeat() 함수는 경우에 따라 변경되는 일을 함수 f로 추상화 했고 이를
외부에서 전달받아 사용합니다. JavaScript의 함수는 일급 객체이기 때문에
함수를 다른 함수의 인자로 사용하는게 가능하기 때문에 이런 구조가 가능한 것입니다.
<br><br>

이처럼 함수의 매개변수를 통해 다른 함수의 내부로 전달되는 함수를 `callback`(콜백 함수)
라고 하며, 매개 변수를 통해 외부에서 `callback function`을 전달받은 함수를
`Higher-Order function`(고차 함수)라고 합니다.
<br><br>

즉, 고차 함수는 콜백 함수를 자신의 일부분으로 합성해서 사용하게 됩니다.
<br><br>

콜백 함수는 고차 함수에 의해 호출되며 이 때 고차함수는 필요에 따라 콜백 함수에 인자를
전달할 수 있습니다. 우리 예제 처럼 말이죠.
<br><br>

만약 콜백 함수가 고차 함수 내부에서만 사용된다면 콜백 함수를 익명 함수 literal로 정의하면서
고차 함수에 전달하는 것이 일반적인 방법입니다.
<br><br>

다음과 같이 말이죠.

~~~javascript


function repeat(n, f) {

    for(let i=0; i<n; i++) {
        f(i);
    }
}

repeat(5,function(i) {
    console.log(i);
}); // 0 ~ 4까지 출력

repeat(5,function(i) {
    if (i % 2) console.log(i);
}); // 1, 3 출력

~~~

이런 `callback function`은 일반적으로 비동기 처리(`Event` 처리, `AJAX`)에 활용되는
아주 중요한 프로그래밍 패턴입니다.

~~~javascript

document.getElementById('myBtn').addEventListener('click',function() {
    console.log('Button Clicked!!');
});

~~~


End.

{% include links.html %}
