---
title: ECMAScript 6.0 strict mode
sidebar: ecmascript_sidebar
summary: "strict mode에 대해서 알아보겠습니다."
permalink: ecmascript_strict-mode.html
folder: ecmascript
---

## strict mode

`strict mode`에 대해 알아보기 전에 다음 코드의 실행결과를 한번 보도록 하겠습니다.

~~~javascript

function foo() {
    x = 10;
}

foo();

console.log(x);  // 10

~~~

위의 `foo()`함수 내부에 선언되지 않은 변수 x에 값을 할당했습니다. 이때 변수 x를 찾아야 하기에
`scope chain`을 통해서 해당 변수를 검색하게 됩니다. 하지만 있을리가 없죠.
<br><br>

당연히 `ReferenceError`가 발생할 상황인데도 JavaScript Engine은 전역객체에 `x` property를
동적으로 생성합니다. 이를 `implicit global`(암묵적 전역)이라고 합니다.
<br><br>

이러한 현상이 과연 좋은 현상일까요? 이런 코드는 여러가지 예기치 않은 오류를 발생시킬 수 있습니다.
따라서 변수는 반드시 `var`, `let`, `const` 키워드를 이용하여 선언한 다음 사용해야 합니다.
<br><br>

하지만 JavaScript는 이런 오류 발생 여지가 있는 코드에 대해서 따로 제제를 가하지 않고 있습니다.
그래서 등장한 것이 바로 `strict mode`입니다.
<br><br>

`strict mode`는 JavaScript 언어의 문법을 조금 더 엄격히 적용시켜서 오류를 발생시킬 가능성을
줄이는데 사용됩니다.
<br><br>

`strict mode`를 사용하는 방법은 간단합니다. 전역의 선두 혹은 함수 몸체의 선두에 `'use strict';`를
추가하면 됩니다.

~~~javascript

'use strict';

function foo() {
    x = 10;  // ReferenceError: x is not defined
}

foo();

console.log(x);  

~~~

몇가지 주의해야 할 점이 있는데 전역에 `strict mode`를 사용하는건 지양해야 합니다. 왜냐하면
외부 서드파티 라이브러리를 사용할 경우 해당 라이브러리가 `non-strict mode`인 경우도 있기 때문입니다.
<br><br>

일반적으로 `strict mode`는 즉시 실행 함수로 감싼 스크립트 실행 단위로 적용하는것이 안정적입니다.

~~~javascript

(function() {
    // non-strict mode
    var let = 10;  // error가 발생하지 않는다.
 
    function foo() {
        'use strict';
                 
        let = 20;  // SyntaxError: Unexpected strict mode reserved word(예약어 사용)
    }
    foo();
}());

~~~

---

## strict mode로 인한 변화

다음은 `strict mode`가 에러를 발생시키는 대표적인 예입니다.
<br><br>

* 암묵적 전역 : 선언되지 않은 변수를 사용하면 에러로 처리합니다.

~~~javascript

(function() {
    'use strict';

    x = 1; // ReferenceError: x is not defined
    console.log(x);
}());

~~~

* 변수, 함수, 매개변수의 삭제

~~~javascript

(function() {
    'use strict';

    var x = 1; 
    delete x;

    function foo(a) {
        delete a;
    }

    delete foo;
}());

~~~

또한 오류는 아니지만 약간 주의해야 할 점도 있습니다. 대표적인 것이 바로 `this`의 사용입니다.
<br><br>

`strict mode`에서 함수를 일반 함수로 호출하면 `this`에 `undefined`가 바인딩 됩니다.
`strict mode`가 아닌 경우에는 전역 객체(`window`)가 바인딩 됩니다.

~~~javascript

(function() {
    'use strict';

    function foo() {
        console.log(this);  // undefined
    }
    foo();
}());

~~~


End.

{% include links.html %}
