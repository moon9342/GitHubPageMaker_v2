---
title: ECMAScript 6.0 primitive value vs. object
sidebar: ecmascript_sidebar
summary: "primitive value와 object에 대한 비교설명입니다."
permalink: ecmascript_primitive-object.html
folder: ecmascript
---

## primitive value vs. object

이전에 알아본것처럼 JavaScript의 Data Type은 총 7개가 있습니다. (number, string, boolean,
null, undefined, symbol, object) 이 7개의 타입은 다시 크게 `primitive type`과 `reference type`으로
나눠집니다. 이런 두 타입은 다음과 같이 3가지 측면에서 서로 다릅니다.

* `primitive value`는 변경 불가능한 값입니다. (`immutable value`) 하지만 `reference value`인 객체는
  변경 가능한 값입니다. (`mutable value`)

* `primitive value`를 변수에 할당하면 변수(확보된 메모리 공간)에는 실제 값이 저장됩니다. 이에 비해 객체는
  변수에 할당하면 해당 변수에는 참조값이 저장됩니다.

* `primitive value`를 다른 변수에 할당하면 원본의 `primitive value`가 복사되어 전달됩니다. 이를 `pass by value`
  (값에 의한 전달)라고 합니다. 이에 비해 객체를 가리키는 변수를 다른 변수에 할당하면 원본의 참조값이 복사되어
  전달됩니다. 이를 `pass by reference`라고 합니다.


---

## primitive value

`primitive value`은 `immutable value` 입니다. 변경이 불가능하다는 것은 당연히 변수가 그렇다는 것이 아니라
값이 그렇다는 것이죠. 혼동하면 안됩니다. 그림 하나로 설명이 가능할 듯 합니다.

{% include image.html
file='es6-javascript-memory-variable-assign.png'
%}

위의 그림에서 보듯이 `score`변수에 값을 재 할당하면 새로운 값이 메모리에 생성되는 것을 볼 수 있습니다.
아래 그림처럼 동작하지 않는 것이죠.

{% include image.html
file='es6-javascript-memory-variable-assign-error.png'
%}

위의 그림처럼 동작해도 큰 문제가 없어보이지만 이런 경우 예기치 않게 변수의 값이 변경될 경우
값의 변경의 추적이 어렵다는 문제가 생기게 됩니다.
<br><br>
위의 예는 숫자를 가지고 예로 들었지만 문자열(`string`) 역시 마찬가지 입니다. 그런데 우리가
코드를 작성하면 아래와 같이 혼동스러운 경우를 많이 보게 됩니다.

~~~javascript

let str = 'hello';

// 문자열은 배열과 유사하게 index를 이용해 접근할 수 있다.
console.log(str[0]); // h

// primitive value인 문자열이 마치 객체처럼 동작
console.log(str.length); // 5
console.log(str.toUpperCase()); // HELLO

~~~

여기서 한가지 알아두어야 할 것은 `primitive value`를 객체처럼 사용하면
`primitive value`를 감싸는 `wrapper object`가 묵시적으로 생성되어 사용 된다는 것입니다.
그래서 원시값을 우리는 객체처럼 사용할 수 있는 것이죠.
<br><br>
또한 위의 예처럼 `string`은 마치 배열처럼 index를 이용해 접근할 수 있고
`length property`를 가지고 있는데 이러한 객체를 `array-like object`(유사 배열 객체)라고
합니다. 이러한 유사 배열 객체는 `for`문등으로 순회할 수도 있습니다.
<br><br>
문자열이 정말 `primitive value`일까요? 아래의 예를 보면 확인할 수 있습니다.

~~~javascript

let str = 'hello';

// 문자열은 유사 배열 객체 이기 때문에 
// index로 접근이 가능합니다.

str[0] = 'S';   // 값이 변경되지 않고 오류가 발생하지 않습니다.

console.log(str);  // hello

~~~

---

## pass by value

`pass by value`(값에 의한 전달)이라는 표현은 사실 `ES6` 표준 스펙에는 없습니다. 하지만
일반적으로 사용하는 표현이니 알아두도록 하죠. 우리가 익히 알고 있는 내용입니다. 코드와 그림을
보죠.

~~~javascript

let score = 80;

let copy = score;

console.log(score, copy);
console.log(score === copy);  // true

score = 100;

console.log(score, copy);
console.log(score === copy);  // false

~~~

{% include image.html
file='es6-javascript-pass-by-value.png'
%}

한가지 염두해 둬야 할 점은 위의 그림은 실제 JavaScript Engine의 내부동작과는
일치하지 않을 수도 있다는 점입니다. 왜냐하면 `ECMAScript` 표준은 변수를 통해
메모리를 어떻게 관리하는지에 대한 명확한 정의가 없기 때문입니다. JavaScript Engine을
구현하는 제조사에 의해 내부 동작 방식은 미묘하게 차이가 날 수 있습니다.
<br><br>

표현은 `pass by value`라고 했지만 실제로 변수에는 메모리 주소(참조값)이 들어 있습니다.
그렇기 때문에 정확히 말하면 변수에 전달되는 값은 메모리 주소입니다. 혼동이 있을 수 있는데
결국 **변수가 지칭하는 메모리 공간에 실제 값이 있느냐 메모리 주소값이 있느냐**로
조금 심플하게 이해하시면 될 듯 합니다.

---

## object

객체는 `property`의 수가 정해져 있지 않고 동적으로 추가, 삭제할 수 있습니다. 또한
`property`의 값에도 제약이 없죠. 그렇기 때문에 확보해야 할 메모리 공간을 사전에 정해
둘 수 없습니다.
<br><br>

객체는 복합적인 자료구조이기 때문에 객체를 관리하는 방식은 `primitive value`와 비교해서
복잡하며 그 구현도 JavaScript Engine에 따라서 달라집니다.
<br><br>

일반적인 객체 구현 방식은 다음과 같습니다. 자바스크립트 객체는 property key를 index로
사용하는 `hash table`구조로 구현합니다. `hash table`은 다른 표현으로 `associative array`(연관 배열)
혹은 `map`, `directory`, `lookup table`이라는 용어로 불리기도 합니다.
<br><br>

참고로 이러한 방식은 property 접근에 많은 비용이 들게 되는데 `V8` JavaScript Engine은 이 비용을
줄이기 위해 `dinamic lookup`(동적 탐색)방식 대신 `hidden class`(히든 클래스)라는 방식을 이용한다고
알려져 있고 이 방식은 기존 `C++`언어의 객체 property 접근과 거의 유사한 성능을 내는 것으로 알려져
있습니다.
<br><br>
어쨋든, 객체는 `mutable value`(변경 가능한 값)입니다. 변수에 객체를 할당하면 어떻게 되는지 코드와
그림으로 알아보죠.

~~~javascript


let person = {
    'name': 'Lee'
};

console.log(person);  // { name: 'Lee' }

~~~

{% include image.html
file='es6-javascript-memory-object-assign.png'
%}

이제 다음 코드로 객체의 값을 변경한 후 그림을 다시 보도록 하겠습니다.

~~~javascript

let person = {
    'name': 'Lee'
};

person.name = 'Kim';

person.address = 'Seoul'

console.log(person);  // { name: 'Kim', address: 'Seoul' }

~~~

{% include image.html
file='es6-javascript-memory-object-mutable.png'
%}

객체를 `immutable value`로 사용하게 되면 메모리의 효율적인 소비가 어렵고 성능이
나빠지게 됩니다. 그래서 객체는 메모리를 효율적으로 사용하기 위해 `mutable value`로
설계되어 있는 것입니다.
<br><br>

그렇기 때문에 따라오는 문제가 하나 있는데 그것은 `primitive value`와는 다르게
여러개의 식별자가 하나의 객체를 공유할 수 있다는 것입니다.
<br><br>

다음의 코드와 그림으로 알아보도록 하겠습니다.

~~~javascript

let person = {
    'name': 'Lee'
};

let copy = person;

~~~

{% include image.html
file='es6-javascript-object-shared.png'
%}

위의 그림과 같이 객체가 공유되게 되면 다음 코드와 같은 현상이 발생하게 됩니다.

~~~javascript

let person = {
    'name': 'Lee'
};

let copy = person;

console.log(copy === person);  // true

copy.name = 'Kim';

person.address = 'Seoul';

console.log(person);  // { name: 'Kim', address: 'Seoul' }
console.log(copy);  // { name: 'Kim', address: 'Seoul' }

~~~

결국 `pass by value`와 `pass by reference`는 식별자가 기억하는 메모리
공간에 있는 값을 복사해서 전달한다는 면에서는 동일합니다. 다만, 식별자가
기억하는 메모리 공간, 즉 변수에 저장되어 있는 값이 원시값이냐 참조값이냐의
차이만 있을 뿐입니다. 따라서 엄밀히 말하자면 JavaScript에는 `pass by reference`는
존재하지 않고 `pass by value`만 존재한다고 말할 수 있습니다.
<br><br>

하지만 일반적인 의미로 전달되는 값이 `primitive value`인지 혹은 `reference value`인지에
따라서 `pass by value`, `pass by reference`라고 표현하니 우리도 그렇게 표현하도록
하겠습니다.


End.

{% include links.html %}
