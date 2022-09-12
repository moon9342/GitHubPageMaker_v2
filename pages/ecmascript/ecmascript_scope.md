---
title: ECMAScript 6.0 scope
sidebar: ecmascript_sidebar
summary: "scope는 비단 JavaScript뿐 아니라 모든 프로그래밍 언어에서 중요한 개념입니다. 이 또한 
JavaScript의 다른 중요한 개념들과 묶여 있는 내용이니 잘 이해해 보도록 하죠."
permalink: ecmascript_scope.html
folder: ecmascript
---

## scope

변수는 자신이 선언된 위치에 의해 자신이 유효한 범위, 즉 다른 코드가 변수 자신을 참조할 수
있는 범위가 결정됩니다. 변수뿐 아니라 모든 식별자가 사실 그렇습니다. 이런 유효범위를
우리는 `scope`라고 부릅니다.

{% include callout.html
type="danger"
content="`scope`는 식별자가 유효한 범위를 지칭하는 용어입니다."
%}

아래의 코드를 예로 들어보죠.

~~~javascript

var x = 'global';

function foo() {
    var x = 'local';

    console.log(x);  // local
}

foo();

console.log(x);  // global

~~~

위의 코드에서 JavaScript Engine은 이름이 같은 식별자 x에 대해 어떤 식별자를
사용해야 할 것인가를 결정해야 합니다. 이를 `identifier resolution`(식별자 결정)이라고
합니다. 
<br><br>

이 때 JavaScript Engine은 `scope`를 이용해 어떤 변수를 참조할지를 결정하게
되는 것이죠. 따라서 `scope`는 JavaScript Engine이 식별자를 검색할 때 사용하는 규칙이라고도
할 수 있습니다.
<br><br>

조금 어려운 얘기인데 JavaScript Engine은 코드를 실행할 때 코드의 `context`(문맥)을 고려합니다.
조금 정확히 말하면 "코드가 어디서 실행되며 주변에 어떤 코드가 있는가"를 `lexical environment`(렉시컬 환경)
이라고 부릅니다. 즉, 코드의 `context`(문맥)은 이 `lexical environment`로 이루어집니다. 이것을
구현해 놓은 것이 바로 `execution context`이며 모든 코드는 이 `execution context`에서 실행됩니다.
<br><br>

아래 그림처럼 위 예제 코드의 변수 x는 다른 변수 입니다. `scope`가 다르기 때문인데 이런 의미로 `scope`를
`namespace`라고 합니다.

{% include image.html
file='es6-javascript-scope-namespace.png'
%}
<br>

이전에 `var`과 `let`, `const`의 차이점이 기억나시나요? `var`은 같은 `scope`내에서 중복선언이 허용됩니다.
하지만 `let`과 `const`는 같은 `scope`내에서 중복 선언을 할 수 없습니다.

---

## scope의 종류

`code`는 `global`(전역)과 `local`(지역)으로 나눌 수 있습니다.

{% include image.html
file='es6-javascript-scope-global-local.png'
%}
<br>

그리고 변수는 자신이 선언된 위치에 의해 자신이 유효한 범위인 `scope`가 결정됩니다. 아래 그림을 보시죠.

{% include image.html
file='es6-javascript-scope-global-local-code.png'
%}
<br>

전역이란 코드의 가장 바깥부분을 의미합니다. 전역은 `global scope`를 만들고 전역에 변수를 선언하면
`global scope`를 가지는 `global variable`이 됩니다. 알다시피 전역변수는 어디에서든 참조가
가능합니다. 위의 그림에서 `x`와 `y`는 전역변수입니다.
<br><br>

지역이란 함수 몸체 내부를 의미합니다. 지역은 `local scope`를 만듭니다. 지역에 변수를 선언하면
`local scope`를 가지는 `local variable`이 됩니다. 지역변수는 자신의 `local scope`와
하위 `local scope`에서 유효합니다.
<br><br>

우리가 함수를 배울 때 함수는 중첩이 가능하다고 배웠습니다. 함수가 중첩이 가능하니 당연히 함수의
`local scope`도 중첩이 될 수 있습니다. 즉, `scope`가 함수의 중첩에 의해 계층적 구조를 가진다는
말입니다.
<br><br>

그림으로 표현하면 다음 그림처럼 표현할 수 있습니다.

{% include image.html
file='es6-javascript-scope-chain.png'
%}
<br>

이렇게 모든 `scope`는 하나의 계층적 구조로 연결되며 모든 `local scope`의 최상위 scope는 당연히
`global scope`가 됩니다.

{% include callout.html
type="danger"
content="이렇게 `scope`가 계층적으로 연결된 것을 `scope chain`이라고 부릅니다."
%}

---

## function-level scope(함수 레벨 scope)

지역은 함수 몸체 내부를 말하고 지역은 `local scope`를 만든다고 했죠. 이는 `code block`이 아닌
함수에 의해서만 `local scope`가 생성된다는 의미입니다.
<br><br>

`C`나 `Java`를 포함한 대부분의 프로그래밍 언어는 함수 몸체뿐 아니라 모든 `code block`이 `local scope`를
만듭니다. 이러한 특성을 `block-level scope`라고 합니다. 하지만 우리가 알고 있듯이 `var` 키워드로 변수를
선언하면 오로지 `함수의 code block`만을 `local scope`로 인정합니다. 이러한 특성을 `function-level scope`라고
하며 JavaScript의 특이한 점 중 하나입니다.
<br><br>

따라서 다음과 같은 코드는 오류의 여지가 있습니다.

~~~javascript

var i = 10;

for (var i=0; i<5; i++) {
    console.log(i);
}

console.log(i);  // 5

~~~

이런 문제를 해결하고자 `ES6`에서 `let`과 `const`를 제공하는 것이죠. 이 두가지 키워드로 변수를
선언하면 `block-level scope`를 지원합니다.

---

## lexical scope(정적 scope)

일단 다음의 코드를 보고 실행결과를 유추해보도록 하죠.

~~~javascript

var x = 1;

function foo() {
    var x = 10;
    bar();
}

function bar() {
    console.log(x);   
}

foo();   // ?? => 1
bar();   // ?? => 1

~~~

위 코드의 실행결과는 bar() 함수의 상위 `scope`가 무엇인지에 따라 결정됩니다. 다음의
두가지 경우를 생각해 볼 수 있습니다.
<br><br>

1. 함수를 어디서 호출했는지에 따라 함수의 상위 `scope`를 결정한다.
2. 함수를 어디서 정의했는지에 따라 함수의 상위 `scope`를 결정한다.
<br><br>

첫번째 방식을 우리는 `dynamic scope`(동적 스코프)라고 합니다. 함수가 호출되는 시점에
`scope`가 결정되는 것이죠.
<br><br>

두번째 방식은 `lexical scope`혹은 `static scope`(정적 스코프)라고 합니다. 함수가 정의
되는 시범에 상위 `scope`가 결정되는 방식입니다.
<br><br>

{% include callout.html
type="danger"
content="JavaScript를 포함한 대부분의 언어는 `lexical scope`를 따릅니다. 즉, 함수를 어디서 호출했는지가
아니라 함수를 어디서 정의했는지에 따라 `scope`가 결정된다는 것이죠."
%}

---

## 전역 변수의 문제점

변수는 선언에 의해 생성되고 사용되다가 소멸합니다. 이를 변수의 `life cycle`이라고 하는데 변수는 자신이
선언된 위치에서 생성되고 소멸됩니다.
<br><br>

그래서 전역 변수의 생명주기는 `application`의 생명주기와 같습니다. 하지만 함수 내부에서 선언한 지역 변수는
함수가 호출되면 생성되고 함수가 종료되면 소멸합니다.
<br><br>

함수와는 다르게 전역 코드는 명시적인 호출 없이 실행됩니다. 즉, 특별한 진입점이 없고 코드가 로드되자 마자 곧바로
실행되게 됩니다. 따라서 `var` 키워드로 선언한 전역변수는 전역 객체의 `property`가 되고 이는 전역 변수의
생명 주기가 전역 객체의 생명 주기와 동일하다는 말이 됩니다.
<br><br>

여기서 전역 객체란 `browser`환경에서는 `window`객체, `Node.js`환경에서는 `global`객체가 됩니다. 환경에
따라 전역 객체가 다양했으나 `ES11`에 들어와서는 `globalThis`로 통일되었습니다. 하지만 아직 사용하기에는
무리가 있겠죠.(최신 버전의 browser만 지원하기 때문입니다.)
<br><br>

`browser`환경에서 전역 객체는 `window`이므로 `var`로 선언한 전역 변수는 전역 객체 `window`의 `property`가
됩니다. 전역 객체인 `window`는 웹페이지를 닫을 때 까지 유효하며 당연히 전역 변수도 웹 페이지를 닫을 때 까지
유효합니다.
<br><br>

이런 전역변수는 다음과 같은 몇가지 문제점을 가지고 있습니다.
<br><br>

1. 코드 어디서나 참조할 수 있기 때문에 코드의 가독성이 나빠지고 의도치 않게 변수 상태가 변경될 수 있습니다.
2. 생명 주기가 길기 때문에 메모리 리소스를 오랜 기간 소모합니다.
3. `scope chain`상 최상위에 존재하므로 전역 변수의 검색 속도가 가장 느립니다.
4. 웹 환경에서 JavaScript는 파일이 분리되어 있다하더라도 하나의 `global scope`를 공유한다는 것입니다. 따라서
   다른 파일에서 동일한 이름의 변수를 사용할 경우 문제가 발생할 여지가 있습니다.
<br><br>

이처럼 전역변수는 몇가지 문제를 가지고 있는데 전역변수를 사용하지 말라는게 아니라 가능한 최소한으로 유지해야 한다는
것입니다.
<br><br>

전역변수를 대체할 수 있는 방법이 몇가지 있는데 그 중 다음의 두가지만 기억하도록 하죠.
<br><br>

1. 이전에 한번 소개한 즉시 실행 함수를 이용하는 것입니다. 이 경우 지역 변수로 인식되기 때문에 전역 변수의 사용을
   제한할 수 있습니다.
2. `ES6`의 `module`을 사용하는 방법입니다. 이 방법은 뒤쪽에서 다시 다루도록 하죠.
<br><br>

End.

{% include links.html %}
