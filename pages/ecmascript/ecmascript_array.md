---
title: ECMAScript 6.0 array
sidebar: ecmascript_sidebar
summary: "array는 사용빈도가 매우 높은 기본적인 자료구조이기 때문에 가능한 많은 메소드나 활용을 알고 있는게 
JavaScript를 다루는데 많은 도움을 줍니다."
permalink: ecmascript_array.html
folder: ecmascript
---

## Array

`array`는 여러 개의 값을 순차적으로 나열한 자료구조입니다. `array`는 사용빈도가 매우 높은
기본적인 자료구조이기 때문에 가능한 많은 메소드나 활용을 알고 있는게 JavaScript를 다루는데
많은 도움을 줍니다. 즉, 코딩에 많은 도움이 된다는 말이죠.
<br><br>

다음은 배열 literal입니다.

~~~javascript

const arr = ['apple', 'banana', 'orange'];

~~~

배열이 가지고 있는 각 값들을 우리는 `element`(요소)라고 합니다. JavaScript에서 나오는 모든 값이라
평가되는 것들은 모두 `element`가 될 수 있습니다.
<br><br>

`array`는 배열에서 자신의 위치를 나타내는 `index`를 가지고 있습니다. 대부분의 프로그래밍 언어에서 0부터
시작하죠.
<br><br>

배열에 접근할 때는 당연히 `bracket notation`([])을 이용합니다.
<br><br>

한가지 기억해야 할 점은 JavaScript에는 array라는 data type이 존재하지 않습니다. `object` 입니다.
<br><br>

그런데 배열은 객체이면서 일반 객체와는 차이가 있습니다. 아래의 그림을 보시죠.

{% include image.html
file='es6-javascript-array-object-difference.png'
%}

---

## JavaScript 배열의 특이점

`Data Structure`(자료구조)에서 말하는 `array`는 동일한 크기의 메모리 공간이 빈틈없이
연속적으로 나열된 자료구조를 의미합니다.
<br><br>

즉, 배열의 요소는 하나의 데이터 타입으로 통일되어 있으며 서로 연속적으로 인접해 있습니다.

{% include image.html
file='es6-javascript-array-general-character.png'
%}

이러한 배열을 우리는 `dense array`(밀집 배열)이라고 부릅니다.
<br><br>

이런 밀집 배열의 특징은 인덱스를 이용해 특정 element에 고속으로 접근할 수 있다는 겁니다.
같은 크기의 공간이 연속적으로 위치해 있기 때문에 간단한 주소 연산을 통해 빠르게 요소에
접근할 수 있습니다.
<br><br>

하지만 알다시피 요소를 삽입하거나 삭제할 경우 요소를 연속적으로 이동시켜야 할 필요성이 있기
때문에 이런 경우 효율이 좋지 않습니다.
<br><br>

JavaScript의 배열은 자료구조에서 말하는 배열과 약간 다릅니다. 즉, 각각의 element을 위한
메모리 공간이 동일한 크기를 가지지 않으며 연속적으로 이어져 있지 않을수도 있습니다. 이런 배열을
`sparse array`(희소 배열)이라고 부릅니다.
<br><br>

그래서 우리의 JavaScript 배열은 엄밀히 말해 일반적인 배열의 동작을 흉내 낸 특수한 형태의 객체라고
보는게 맞습니다. 왜 이런 구조를 가지게 했을까요? JavaScript 배열은 검색보다는 삽입과 삭제같은
동작에 조금 더 성능을 높이기 위해 이런 구조를 가지게 되었습니다.
<br><br>

배열은 literal로 생성할 수 도 있지만 `Array()` 생성자 함수를 이용해서 만들 수 도 있습니다.

~~~javascript

// 전달된 인수가 1개이고 숫자인 경우 length 값이 인자인 
// 배열을 생성
let arr = new Array(10);

console.log(arr);  // [ <10 empty items> ]

// 배열의 최대 크기는 2**32-1 (약 43억)개입니다.

arr = Array();
console.log(arr);  // []

// 전달된 인자가 2개 이상이거나 숫자가 아닌경우 인수를 요소로 같는 배열 생성

arr = Array(1,2,3);
console.log(arr);  // [ 1, 2, 3 ]

// 나머지 배열의 참조는 기본적인 형태이고
// 배열의 메소드는 차차 알아가면 된다.

~~~

End.

{% include links.html %}
