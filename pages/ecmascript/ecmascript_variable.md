---
title: ECMAScript 6.0 Variable
sidebar: ecmascript_sidebar
summary: "variable이 무었인지는 다 알고 있을거라 생각됩니다. 여기서는 JavaScript의 변수 특성 중
기억해야 할 몇 가지만 따로 설명해 보죠."
permalink: ecmascript_variable.html
folder: ecmascript
---

## variable

우리가 잘 알다시피 컴퓨터는 `CPU`를 이용해 연산하고 `memory`를 이용해 데이터를 저장합니다.
<br><br>

우리가 흔히 `memory`라고 부르는 것은 데이터를 저장할 수 있는 `memory cell`의 집합체를 의미합니다.
memory cell 하나의 크기는 `1 byte`(8 bit)이고 컴퓨터는 memory cell의 크기인 1 byte 단위로
데이터를 write하거나 read하게 됩니다.
<br><br>

그림으로 보면 다음과 같습니다.
<br>
{% include image.html
file='es6-javascript-memory-memory-cell.png'
%}

각 cell은 고유의 주소값(`address`)을 가집니다. 이 주소는 memory 공간의 위치를 나타내는
값이며 0부터 시작해서 메모리 크기만큼의 정수로 표현됩니다. (16진수로 표현되죠)
<br><br>

아래 그림과 같이 CPU는 10과 20의 값을 더하는 연산을 수행한 후 그 결과를 특정 메모리 주소에
저장할 수 있습니다. 하지만 우리가 메모리 셀에 10,20이라는 값을 저장하고 그 합한 결과를
이용하려면 결국 우리는 memory address를 알아야 합니다.
<br>

{% include image.html
file='es6-javascript-memory-memory-cell-variable.png'
%}
<br>

하지만 이런 `memory address`에 대한 직접적인 `access`는 시스템에 치명적인 문제를 발생시킬
여지가 많기 때문에 `JavaScript`는 개발자의 직접적인 메모리 제어를 허용하지 않습니다. `C`언어를
제외한 대부분의 언어가 그렇죠.
<br>

{% include callout.html 
type="danger" 
content="**따라서 하나의 값을 저장하기 위해 확보한 메모리 공간을 식별하기 위한 메커니즘이 필요한데
그게 바로 `variable`입니다.**" 
%}
<br>

변수이름을 `identifier`(식별자)라고 하는데 그림으로 표현하면 위와 같습니다.
<br><br>

당연한 말이지만 식별자는 자체는 메모리 주소를 저장하고 있습니다. 이 주소를 통해 메모리에 공간에
접근하여 저장된 값을 사용할 수 있다는 의미입니다.
<br><br>

주의해야 할 점은 이 `identifier`(식별자)는 변수이름에만 국한되서 사용되는 것은 아니라는
점입니다. 변수명, 함수명, 클래스명은 모두 `identifier`입니다.
<br><br>

이런 `identifier`는 `Naming Rule`(명명규칙)을 준수해야 하며 `declaration`(선언)에
의해 `JavaScript Engine`에 식별자의 존재를 알리게 됩니다.

---

## variable declaration

변수 선언은 변수를 생성하는 것을 의미합니다.
<br>

{% include callout.html
type="danger"
content="**조금 더 정확히는 값을 저장하기 위한 메모리 공간을 확보하고 변수 이름(식별자)과 확보된
메모리 공간의 주소를 연결해서 공간을 사용할 수 있도록 준비하는 것을 의미합니다.**"
%}
<br>

따라서 변수를 사용하려면 반드시 선언이 필요합니다. 변수를 사용할 때 사용하는 `keyword`는
`var`, `let`, `const` 3가지가 있습니다. `ES6`에서 `let`과 `const`가 도입되었고 그 이전에는
`var`을 이용해서 변수를 선언했었습니다.
<br><br>

`var` 키워드로 변수를 선언하면 해당 변수는 `block-level scope`를 지원하지 않고
`function-level scope`를 가지게 됩니다. 의도치 않게 전역변수로 선언되는 현상이 발생할 수
있으니 주의해야 합니다. `ES6`에서 제공되는 `let`과 `const`는 `block-level scope`를 지원합니다.
<br><br>

다음의 선언문을 이용하면 다음 그림과 같이 변수 공간이 할당됩니다.

~~~javascript

var score;

~~~

{% include image.html
file='es6-javascript-memory-variable-declaration.png'
%}
<br><br>

위의 그림에서 보는 것처럼 변수 선언에 의해 확보된 메모리 공간은 `undefined`라는 값으로
초기화 됩니다. `undefined`는 JavaScript에서 지원하는 `primitive value`(원시값)입니다.
<br><br>

이렇게 선언된 변수를 포함한 모든 identifier(식별자)들은 JavaScript Engine이
관리하는 `execution context`(실행 컨텍스트) 라는 곳에 `key/value`행태로 저장되고 관리됩니다.
이 부분은 아직 무슨말인지 모를테니 넘어가고 뒤쪽에서 `execution context`가 나오면 그 때 다시 
설명하기로 하겠습니다.
<br><br>

변수에 관련되서 한가지 주의해야 할 점이 있는데 바로 `hoisting`이라는 특성입니다.

---

## variable hoisting

다음의 코드를 한번 실행해보면 우리 생각과 조금 다른 결과를 얻을 수 있습니다.

~~~javascript

console.log(score);   // undefined

var score;

~~~
<br>

변수를 선언하지도 않았는데 score라는 변수를 사용했기 때문에 당연히 참조오류(`ReferenceError`)가
발생해야 합니다. 하지만 참조에러가 발생하지 않고 `undefined`가 출력되는 것을 확인할 수
있습니다.
<br>

{% include callout.html
type="danger"
content="**그 이유는 변수 선언이 소스코드가 한줄씩 실행되는 `runtime`이 아니라 그 이전단계에서 먼저
실행되기 때문입니다.**"
%}
<br>

JavaScript Engine은 소스코드를 한줄씩 순차적으로 실행시키기 이전에 먼저 소스코드의 평가과정을
거쳐 실행준비를 하게 됩니다. 이 평가과정에서 JavaScript Engine은 변수선언을 포함한 모든
선언문(변수, 함수, 클래스)을 찾아 먼저 실행하게 됩니다.
<br><br>

즉, JavaScript Engine은 변수 선언이 소스코드의 어디에 있든 상관없이 다른 코드보다 먼저
실행됩니다. 이처럼 변수 선언문이 코드의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의
특징을 `variable hoisting`(변수 호이스팅)이라고 합니다. 사실 변수뿐 아니라 모든 선언문에서
사용하는 identifier는 `hoisting`됩니다.
<br>

{% include callout.html
type="info"
content="**위의 설명처럼 JavaScript는 두 단계로 실행이 되는데 변수가 생성되고 hoisting이 발생하는 생성단계와
코드가 실행되는 실행단계입니다. 생성단계는 `Creation Phase`라고 하며 실행단계는 `Execution Phase`라고 합니다.**"
%}
<br>

---

## var vs. let

여기서 살짝 주의해야 하는 점이 있습니다. 일단 `var`과 `let`의 차이점부터 알아봐야 할 듯 합니다.
<br><br>

{% include callout.html
type="danger"
content="**`var` keyword로 선언한 변수는 같은 scope 내에서 중복선언이 가능합니다.**"
%}
<br>

변수 중복 선언 시 초기화 구문이 있는 `var` 변수 선언문은 JavaScript Engine에 의해 `var`
키워드가 없는 것처럼 동작하고 초기화 구문이 없는 변수 선언문은 무시됩니다. 다음의 코드를 보시죠.

~~~javascript

var x = 1;
var y = 2;

// var keyword를 이용한 변수 중복 선언
var x = 100;    // var 키워드가 없는 것처럼 동작
var y;          // 초기화 구문이 없으면 무시

console.log(x)   // 100
console.log(y)   // 2

~~~

{% include callout.html
type="danger"
content="**`var` keyword로 선언한 변수는 오로지 함수의 code block만을 local scope로 인정합니다.**"
%}

말이 좀 어려운데 예제를 이용해 이해해보시는게 좋습니다.

~~~javascript


var x = 1;    // 전역변수

if(true) {

    // 이 부분은 block level의 code block입니다.
    // 따라서 var 키워드는 이 영역에서 local scope를 가지지 않습니다.
    // 결국 여기서 x 변수를 var로 재정의해도 위쪽에 전역변수 x를 재정의하는
    // 것이 됩니다. 
    // 의도치 않게 전역변수의 값을 변경시킬 수 있는 여지가 있습니다.
    var x = 100;
}

console.log(x);    // 100

// for문 역시 마찬가지 현상이 발생합니다. 

var i = 10;

for(var i=0; i<5; i++) {
    console.log(i)
}

console.log(i)   // 5

~~~

이번에는 조금 다른 예를 보죠.

~~~javascript

var x = 1;    // 전역변수

function myFunc() {
    // var 키워드로 선언된 변수는 function level scope를 가진다.
    // 따라서 전역변수 x와 아래의 x는 다른 scope를 가지기 때문에 다른 변수임.
    var x = 100;
    console.log(x);   // 100
}

console.log(x);   // 1

myFunc();         

console.log(x);   // 1

~~~

{% include callout.html
type="danger"
content="**`var` 키워드로 변수를 선언하면 `variable hoisting`에 의해 변수 선언문이 scope의
선두로 끌어올려진 것처럼 동작합니다.**"
%}

아래의 예를 보시죠.

~~~javascript

// 초기에 변수 hoisting에 의해 x 변수가 undefined로 초기화.

console.log(x);    // undefined

x = 100;

console.log(x);    // 100

var x;      

~~~

다음과 같은 경우 함수 scope안에서 hoisting이 발생하기 때문에 code 오류가 발생합니다.

~~~javascript

// 아래의 코드는 오류.
// var keyword는 function level scope를 가지기 때문에 
// 해당 함수 scope내에서 hoisting이 발생

console.log(x);    // ReferenceError: x is not defined

x = 100;

console.log(x);    // 100

function myFunc() {    
    console.log(x);    // undefined
    var x;             
}

myFunc();

~~~

이렇게 변수문 이전에 변수를 사용하는 것은 `variable hoisting`에 의해 오류를 발생시키지는
않지만 프로그램의 가독성을 떨어뜨리고 오류를 발생시킬 여지를 남기게 됩니다.
<br><br>

이런 `var` 키워드를 이용한 변수 선언의 단점을 해결하기 위해 `ES6`에서는 새로운 변수 선언
키워드인 `let`과 `const`를 제공합니다.

{% include callout.html
type="danger"
content="**`let` keyword로 변수를 선언하면 같은 scope내에서 변수의 중복 선언이 금지됩니다.**"
%}

~~~javascript

let score = 100;

// SyntaxError: Identifier 'score' has already been declared
let score = 10;

~~~

{% include callout.html
type="danger"
content="**`let` keyword로 선언된 변수는 모든 code block을 local scope로 인정하는
`block-level scope`를 따릅니다.**"
%}

~~~javascript

let score = 100;

{
    let score = 10;
    let myVar = 1;
}

console.log(score);  // 100
console.log(myVar);  // ReferenceError: myVar is not defined

~~~

함수도 code block이기 때문에 scope를 생성합니다. 다음의 예를 잘 이해해 보세요!

~~~javascript

let i = 100;            // global scope(전역 scope) 

function myFunc() {
    let i = 10;         // function level scope

    for(let i=0; i<2; i++) {    // block level scope
                               
        console.log(i);   // 0   1 
    }
    console.log(i);  // 10
}

myFunc();

console.log(i);  // 100

~~~

{% include callout.html
type="danger"
content="**`var` keyword로 선언한 변수와 달리 `let` keyword로 선언한 변수는 `variable hoisting`이
발생하지 않는 것처럼 동작합니다. 실제로는 hoisting이 발생합니다. 하지만 `let` keyword를 사용할 경우 변수의 
선언과 초기화가 동시에 이뤄지지 않습니다.**"
%}

~~~javascript

console.log(myVar);  // ReferenceError: myVar is not defined

let myVar = 100;

~~~

`var`은 선언과 초기화(`undefined`)가 동시에 이루어 지지만 `let` keyword로 선언한 변수는
선언단계와 초기화 단계가 분리됩니다. 즉, `runtime`이전에 JavaScript Engine에 의해
암묵적으로 선언단계가 실행되지만 초기화 단계는 변수 선언문에 도달했을 때 실행됩니다.
<br><br>

그리고 만약 초기화 단계 이전에 변수에 접근하려고 하면 `ReferenceError`가 발생하게 됩니다.
<br><br>

그래서 `let` keyword로 선언한 변수는 scope의 시작지점 부터 초기화 단계 시작 지점(변수 선언문)
까지 변수를 참조할 수 없습니다. 이 구간을 `Temporal Dead Zone(TDZ)`라고 합니다.

~~~javascript

console.log(myVar);  // ReferenceError: myVar is not defined
                     // Temporal Dead Zone

let myVar;

console.log(myVar);  // undefined

myVar = 1;

console.log(myVar);  // 1

~~~

결론적으로 `let` keyword는 `hoisting`이 발생하지 않는 것처럼 보입니다. 하지만 실제로
`hoisting`이 발생합니다. 다음의 예를 보시죠.

~~~javascript

let myVar = 1;   // global variable

{
    console.log(myVar);  // 1
}

~~~

~~~javascript

let myVar = 1;   // global variable

{
    console.log(myVar);  // ReferenceError: myVar is not defined
    let myVar = 100;
}

~~~

결론적으로 JavaScript는 `ES6`에서 도입된 `let`, `const`를 포함해 모든 선언을
`hoisting`합니다.
<br><br>

`const`는 이름에서 의미하다시피 상수를 선언할 때 많이 사용합니다. 즉, 값의 재할당을 금지하고
그렇기 때문에 선언 시 반드시 초기값을 할당해야 합니다. 또한 `let`과 마찬가지로
`block level scope`를 가진다는 것도 기억해 두세요.

{% include callout.html
type="info"
content="**한가지 주의해야 할 점이 있는데 `const`는 재 할당을 금지할 뿐이지 불변을 의미하지는 않습니다.
즉, const 변수에 객체가 할당되어 있으면 다른 객체를 const 변수에 재 할당이 안될 뿐이지
객체안의 내용을 변경할 수는 있다는 의미입니다.**"
%}

---

## 변수값의 재 할당

변수는 당연히 값을 재 할당할 수 있습니다. 간단히 그림으로 변수에 값이 재 할당되는
과정을 살펴보도록 하죠.
<br>

{% include image.html
file='es6-javascript-memory-variable-assign.png'
%}

{% include callout.html
type="info"
content="**왜 이렇게 될까요? 한마디로 말하면 `primitive value`(원시값)는 `immutable value`이기
때문입니다.**"
%}

사용되지 않는 메모리는 `garbage collector`에 의해서 자동으로 메모리에서 `release`되게
됩니다.

---

## Naming Rule

`identifier`(식별자)는 다음과 같은 Naming Rule을 준수해야 합니다.
<br><br>

* 식별자는 **특수문자를 제외한 문자, 숫자, _, $ 기호**를 사용할 수 있습니다.
* 식별자는 숫자로 시작할 수 없습니다.
* 예약어는 식별자로 사용할 수 없습니다.
<br><br>

추가적으로 하나이상의 영어단어로 구성된 식별자를 만들때는 가독성 좋게 단어를 한눈에
구분하기 위해 규정한 명명규칙(`Naming Convention`)을 따르는 것이 좋습니다.

~~~javascript

// camelCase(카멜 케이스)
let firstName;

// snake_case(스네이크 케이스)
let first_name;

// PascalCase(파스칼 케이스)
let FirstName;

// typeHungarianCase(헝가리안 케이스)
// 데이터타입을 변수앞에 명시합니다.
let strFirstName;

// javaScript에서는 일반적으로 변수나 함수이름에 대해서는
// 카멜 케이스를 사용하고

// 생성자함수, 클래스이름에는 파스칼 케이스를 사용합니다.

~~~


End.

{% include links.html %}
