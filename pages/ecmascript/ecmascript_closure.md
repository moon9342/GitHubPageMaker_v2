---
title: ECMAScript 6.0 closure
sidebar: ecmascript_sidebar
summary: "closure(클로저)는 어렵기로 유명한 JavaScript 개념 중 하나입니다. 이번 기회에 이해해 보도록 하죠."
permalink: ecmascript_closure.html
folder: ecmascript
---

## closure

`closure`(클로저)는 어렵기로 유명한 JavaScript 개념 중 하나입니다. 사실 JavaScript를 공부해봤으면
한번쯤은 들어보셨을 거라 생각됩니다. 하지만 우리가 앞서 얘기했던 내용을 잘 이해하고 있다면 그리 어려운
개념은 아닙니다. 이 개념을 언제 어떻게 활용해야 할지가 더 어려운 점이겠죠.
<br><br>

`closure`는 JavaScript 고유의 개념은 아닙니다. 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어에서
사용되는 중요한 개념입니다. 따라서 `ECMAScript`에는 별도록 `closure`에 대한 명세가 포함되어 있지
않습니다.
<br><br>

`closure`의 정의를 한마디로 말하자면 다음과 같습니다.

{% include callout.html
type="danger"
content="`closure`는 함수와 그 함수가 선언된 `lexcical` 환경의 조합이다."
%}

말이 어렵습니다. 함수가 선언된 `lexical` 환경이 무엇일까요?
<br><br>

일단 다음의 코드를 보죠.

~~~javascript

const x = 1;

function outerFunc() {
    const x = 10;

    function innerFunc() {
        console.log(x);   // 10
    }

    innerFunc();
}

outerFunc();

~~~

위의 코드는 `outerFunc()` 함수내부에서 중첩 함수 `innerFunc()`가 정의되고 호출되었습니다. 이때
중첩함수 `innerFunc()`의 상위 scope는 외부 함수 `outerFunc()`의 scope가 됩니다.
<br><br>

따라서 중첩 함수 `innerFunc()` 함수 내부에서 자신을 포함하고 있는 외부 함수 `outerFunc()`의 x변수에
접근할 수 있는 것입니다. 이건 당연한 거죠?
<br><br>

코드를 약간 바꿔보겠습니다.

~~~javascript

const x = 1;

function outerFunc() {
    const x = 10;
    innerFunc();
}

function innerFunc() {
    console.log(x);   // 1
}

outerFunc();

~~~

사실 이전에 한번 나왔던 내용입니다. 만약 `innerFunc()` 함수가 `outerFunc()` 함수 내부에
중첩된 함수가 아니라면 `innerFunc()`함수를 `outerFunc()`함수의 내부에서 호출한다 하더라도
`outerFunc()` 함수의 변수에는 접근할 수 없습니다.
<br><br>

**이러한 현상이 발생하는 이유를 우리는 JavaScript가 `lexical scope`를 따르는 프로그래밍 언어이기
때문이라고 배웠습니다.**
<br><br>

다시한번 정리하면 JavaScript Engine은 함수를 어디에서 호출했는지가 아니라 함수를 어디에서
정의했는지에 따라 상위 scope를 결정합니다. 이를 `lexical scope`(정적 스코프)라고 합니다.
<br><br>

여기서 조금 어려운 얘기가 나오는데 바로 `execution context`(실행 컨텍스트)라는 개념입니다.
<br><br>

`ECMAScript` 표준은 소스코드를 4가지 타입으로 분류합니다.
<br><br>

* `전역 코드` : 전역에 존재하는 소스코드입니다. 전역에 정의된 함수등의 내부코드는 포함되지 않습니다.
* `함수 코드` : 함수내부에 존재하는 소스코드입니다. 중첩함수등의 내부코드는 포함되지 않습니다.
* `eval 코드` : built-in 전역함수인 `eval()`함수에 인수로 전달되어 실행되는 소스코드입니다.
* `모듈 코드` : 모듈 내부에 존재하는 소스코드입니다. 모듈안의 함수등의 내부코드는 포함되지 않습니다.
<br><br>

이렇게 소스코드를 분류하는 이유는 이 소스코드 타입에 따라 실행 컨텍스트를 생성하는 과정과 관리하는
내용이 다르기 때문입니다.
<br><br>

간단하게 각 코드가 하는 일은 알아보죠.
<br><br>

* `전역 코드`<br>
  전역 코드는 전역변수를 관리하기 위해 최상위 scope인 전역 scope를 생성해야 합니다. 그리고 `var` 키워드로
  선언된 전역 변수와 함수 선언문으로 선언된 전역 함수를 전역객체(window나 global)의 property로 바인딩
  해야 합니다. 이러한 작업을 위해 `전역 실행 컨텍스트`가 생성되게 됩니다.

* `함수 코드`<br>
  함수 코드는 지역 scope를 생성하고 지역 변수, 매개변수, arguments 객체를 관리해야 합니다. 그리고 생성한
  지역 scope를 전역 scope에서 시작하는 scope chain의 일원으로 연결해야 합니다. 이를 위해 `함수 실행 컨텍스트`가
  생성되게 됩니다.

* `eval 코드`<br>
  `eval()`함수는 독립적인 scope를 가지게 되는데 이를 위해 `eval 실행 컨텍스트`가 생성됩니다.

* `모듈 코드`<br>
  모듈 코드는 모듈별로 독립적인 모듈 scope를 가집니다. 이를 위해 `모듈 실행 컨텍스트`가 생성됩니다.
<br><br>

그럼 조금 자세한 예를 가지고 `실행 컨텍스트`가 어떻게 동작하는지 알아보겠습니다.
<br><br>

알다시피 JavaScript의 소스코드는 2개의 과정을 거칩니다. `creation phase`와 `execution phase`입니다.
우리나라 말로는 소소코드의 평가와 소스코드의 실행이라고 표현하기도 합니다.
<br><br>

`creation phase`에서는 실행 컨텍스트를 생성하고 변수, 함수등의 선언문만 먼저 실행하여 생성된 변수나 함수
식별자를 key로 실행 컨텍스트가 관리하는 `scope`에 등록합니다. 이 `scope`는 `lexical scope`의 환경 레코드로
구성됩니다. (쉽게 말해 메모리 구조입니다.)
<br><br>

`execution phase`가 시작되면 즉, runtime이 되면 소스코드가 순차적으로 실행되게 됩니다. 이때 소스코드가 필요한
정보, 즉 변수나 참조의 정보를 실행 컨텍스트가 관리하는 scope에서 검색해서 이용합니다. 그리고 변수값의 변경등과 같은
실행결과가 다시 실행 컨텍스트가 관리하는 scope에 등록됩니다.
<br><br>

그림으로 표현하면 다음과 같습니다.

{% include image.html
file='es6-javascript-execution-context.png'
%}

한가지를 더 추가하자면 코드가 수행되면서 함수 호출이 일어나면 순차적으로 수행되고 있던 코드의 실행을 일시 중단하고
코드의 실행순서를 바꾸어 함수 내부로 진입해서 코드를 실행해야합니다. 또한 함수 호출이 종료되면 함수 호출 이전으로
돌아가기 위해 현재 실행중인 코드와 이전에 실행중인 코드 역시 구분해서 관리해야 합니다.
<br><br>

이처럼 코드가 실행되기 위해 `scope`관리나 식별자 관리, 코드 실행 순서 관리 같은 것들이 필요하게 됩니다.
이 모든 것을 관리하는 것이 바로 `execution context`(실행 컨텍스트)입니다.
<br><br>

쉽게 얘기해서 `execution context`(실행 컨텍스트)는 소스코드를 실행하는데 필요한 환경을 제공하고
코드의 실행결과를 실제로 관리하는 (메모리) 영역입니다.
<br><br>

조금 더 구체적으로 말하자면,
<br><br>

**식별자와 스코프는 실행 컨텍스트의 `lexical environment`(렉시컬 환경)으로 관리하고
코드 실행 순서는 실행 컨텍스트 스택으로 관리합니다.**
<br><br>

다음 코드에 대한 `execution context stack`은 아래쪽 그림처럼 동작합니다.

~~~javascript

const x = 1;

function foo() {
    const y = 2;    

    function bar() {
        const z = 3;
        console.log(x + y + z);
    }
    bar();
}

foo();

~~~

{% include image.html
file='es6-javascript-execution-context-stack.png'
%}

렉시컬 환경은 식별자와 식별자에 바인된 값, 그리고 상위 scope에 대한 참조를 기록하는
자료구조로 실행 컨텍스트를 구성하는 component입니다.

{% include image.html
file='es6-javascript-lexical-environment.png'
%}

`scope`의 실체는 실행 컨텍스트의 렉시컬 환경입니다. 이 렉시컬 환경은 자신의 외부 렉시컬 환경에
대한 참조를 통해 상위 렉시컬 환경과 연결되는 것이고 이것이 바로 `scope chain`인 것입니다.

---

## closure와 lexical 환경

아래의 코드를 살펴보죠.

~~~javascript

const x = 1;

function outer() {
    const x = 10;

    const inner = function() {
        console.log(x);
    }
    return inner;
}

const innerFunc = outer();
innerFunc();   // 10

~~~

위의 코드에서 `outer()` 함수를 호출하면 `outer()` 함수는 중첩 함수 `inner()`를
반환하고 `life cycle`을 마감합니다. 즉, `outer()` 함수의 실행이 종료되면 `outer()` 함수의
실행 컨텍스트는 실행 컨텍스트 스택에서 제거됩니다.
<br><br>

이때 `outer()` 함수의 지역변수 x와 변수 값 10을 저장하고 있던 `outer()` 함수의 실행 컨텍스트가
제거되었으므로 지역 변수 x 역시 생명주기를 마감하고 더이상 유효하지 않게 되어 x 변수에 접근할 수 있는
방법이 없게 됩니다.
<br><br>

하지만 위의 코드 결과는 그렇지 않습니다. 이미 생명 주기가 종료된 x가 아직 종료되지 않았기 때문입니다.

{% include callout.html
type="danger"
content="외부 함수보다 중첩함수가 더 오래 유지 되는 경우 중첩함수는 이미 생명 주기가 종료한 외부 함수의
변수를 사용할 수 있습니다. 이러한 중첩 함수를 `closure`라고 합니다."
%}

우리 예제에서 `outer()` 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거되지만 `outer()` 함수의
렉시컬 환경까지 소멸하는 것은 아닙니다.
<br><br>

`outer()` 함수의 렉시컬 환경은 `inner()` 함수의 `[[Environment]]` 내부 슬롯에 의해 참조되고 있고
`inner()` 함수는 전역 변수 `innerFunc()`에 의해 참조되고 있으므로 `garbage collection`의 대상이
아닙니다. 누군가 참조하고 있으면 `garbage collection`은 발생하지 않습니다.
<br><br>

위에서 설명한 내용대로라면 JavaScript의 모든 함수는 상위 scope를 기억하기 때문에 이론적으로는 모두 `closure`
입니다. 하지만 당연히 모든 함수를 `closure`라고 하지는 않습니다. 예를 보시죠.

~~~javascript

function foo() {
    const x = 1;
    const y = 2;

    // 일반적으로 closure라고 하지 않는다.
    function bar() {
        const z = 3;

        // 상위 스코프의 식별자를 참조하지 않는다.
        console.log(z);
    }
    return bar;
}

const bar = foo();
bar();   // 3

~~~

위의 예제에서 중첩 함수 `bar()`는 외부 함수 `foo()`보다 더 오래 유지되지만 상위 scope의
그 어떤 식별자도 참조하지 않습니다. 이처럼 상위 scope의 어떤 식별자도 참조하지 않는 경우
대부분의 모던 브라우져들은 최적화를 위해 상위 scope를 기억하지 않습니다. 메모리 낭비이기
때문이죠. 따라서 `bar()` 함수는 `closure`라고 할 수 없습니다.
<br><br>

조금 다른 예제를 보시죠.

~~~javascript

function foo() {
    const x = 1;

    // 일반적으로 closure라고 하지 않는다.
    function bar() {

        // 상위 스코프의 식별자를 참조한다.
        console.log(x);
    }
    bar();
}

foo();   // 1

~~~

위의 예에서 `bar()` 함수는 상위 scope의 식별자를 참조하고 있기 때문에 `closure` 입니다.
하지만 외부 함수 `foo()`의 외부로 중첩 함수 `bar()`가 반환되지 않습니다. 즉, 외부 함수보다
중첩 함수의 생명 주기가 짧은 경우입니다. 이런 경우 내부 함수가 더 일찍 종료되기 때문에 생명주기가
종료된 외부 함수의 식별자를 참조할 수 있다는 `closure`의 본질에 부합되지 않습니다. 따라서
이와 같은 경우도 `closure`라고 하지 않습니다.

---

## closure의 활용

그러면 이런 `closure`를 어디에 이용하는 걸까요? 어떤 용도로 이런 `closure`를 사용하는 걸까요?
<br><br>

`closure`는 상태를 안전하게 변경하고 유지하려는 목적으로 사용됩니다. 다시말해 상태가 의도치 않게
변경되지 않도록 상태를 안전하게 은닉하고 특정한 함수에게만 상태변경을 허용하도록 하기 위해서 사용됩니다.
객체지향의 `information hiding`개념입니다.
<br><br>

아래의 예를 한번 보죠.

~~~javascript

let num = 0;

const increase = function() {
    return ++num;
}

console.log(increase());  // 1
console.log(increase());  // 2
console.log(increase());  // 3

~~~

위의 코드는 잘 동작하지만 위험성을 내포하고 있는 코드입니다.
<br><br>

카운트의 상태는 전역 변수를 통해 관리되고 있기 때문에 언제든지 누구나 접근해서
변경할 수 있습니다. 즉, 의도치 않게 변경이 될 수 있다는 의미입니다.
<br><br>

따라서 카운트의 상태를 안전하게 변경하고 유지하려면 increase() 함수만이 num 변수를
참조하고 변경할 수 있게끔 하는 것이 바람직합니다.
<br><br>

이를 위해 `num` 변수를 지역변수로 바꿔보겠습니다.

~~~javascript

const increase = function() {
    let num = 0;

    return ++num;
}

console.log(increase());  // 1
console.log(increase());  // 1
console.log(increase());  // 1

~~~

`num` 변수를 지역변수로 바꿔어서 외부에서 의도치 않게 변경되는 것은 방지했습니다. 즉,
`num` 변수는 `increase()`함수만이 변경할 수 있습니다.
<br><br>

하지만 `increase()` 함수는 호출될 때 마다 지역 변수 `num`이 초기화되기 때문에 항상
결과값은 1이 됩니다. 다시말해 이전 상태를 유지하지 못하게 됩니다. 이전 상태를 유지하기
위해서 `closure`를 이용해 보도록 하죠.

~~~javascript

const increase = (function() {
    let num = 0;

    return function() {
        return ++num;
    };
}());

console.log(increase());  // 1
console.log(increase());  // 2
console.log(increase());  // 3

~~~

위의 코드가 실행되면 즉시 실행 함수가 호출되고 즉시 실행함수가 반환한 함수가
`increase` 변수에 할당됩니다.
<br><br>

`increase` 변수에 할당된 함수는 자신이 정의된 위치에 의해 결정된 상위 scope인
즉시 실행 함수의 lexical 환경을 기억하는 `closure`입니다.
<br><br>

즉시 실행 함수는 호출된 이후 소멸하지만 즉시 실행 함수가 반환한 `closure`는 `increase`
변수에 할당되어 호출됩니다. 이 때 `closure`는 자신이 정의된 위치에서 결정된 상위
scope인 즉시 실행 함수의 lexical 환경을 기억하고 있고 `num`을 사용할 수 있게 되는 것입니다.
<br><br>

즉시 실행 함수는 단 한번만 호출되기 때문에 `num` 변수는 초기화 되지 않으며 `num`변수는 외부에서
직접 접근할 수 없는 은닉된 `private` 변수이기 때문에 전역변수일때와 같이 의도치 않는 변경을
걱정할 필요가 없어서 안정적인 프로그래밍이 가능합니다.

{% include callout.html
type="danger"
content="이처럼 `closure`는 상태가 의도치 않게 변경되지 않도록 정보를 안전하게 은닉하고
특정 함수에게만 상태 변경을 허용하여 상태를 안전하게 변경하고 유지하기 위해서 사용됩니다."
%}

조금만 더 발전시켜 보죠.

~~~javascript

const counter = (function() {
    let num = 0;

    return {
        increase() {
            return ++num;
        },
        decrease() {
            return num > 0 ? --num : 0 
        }
    };
}());

console.log(counter.increase());  // 1
console.log(counter.increase());  // 2
console.log(counter.increase());  // 3

console.log(counter.decrease());  // 2
console.log(counter.decrease());  // 1
console.log(counter.decrease());  // 0
console.log(counter.decrease());  // 0

~~~

위의 코드를 우리가 알고 있는 생성자 함수를 이용하면 다음과 같이 표현할 수 도 있습니다.

~~~javascript

const Counter = (function() {
    let num = 0;

    function Counter() {

    }

    Counter.prototype.increase = function() {
        return ++num;
    };

    Counter.prototype.decrease = function() {
        return num > 0 ? --num : 0;
    };
    
    return Counter;

}());

const counter = new Counter();

console.log(counter.increase());  // 1
console.log(counter.increase());  // 2
console.log(counter.increase());  // 3

console.log(counter.decrease());  // 2
console.log(counter.decrease());  // 1
console.log(counter.decrease());  // 0
console.log(counter.decrease());  // 0

~~~

이전에 배웠던 내용을 이용해 간단한 샘플 코드를 한번 만들어 보죠. `closure`의 대표적인
활용예라고 보시면 됩니다.

~~~javascript

function makeCounter(func) {

    let counter = 0;

    return function() {
        counter = func(counter);
        return counter;
    };
}

// 보조 함수
function increase(n) {
    return ++n;
}

// 보조 함수
function decrease(n) {
    return --n;
}

// 함수로 함수를 생성합니다.
const increaser = makeCounter(increase);
console.log(increaser());  // 1
console.log(increaser());  // 2
console.log(increaser());  // 3

const decreaser = makeCounter(decrease);
console.log(decreaser());  // -1
console.log(decreaser());  // -2

~~~

End.

{% include links.html %}
