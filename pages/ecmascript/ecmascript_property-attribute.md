---
title: ECMAScript 6.0 property attribute
sidebar: ecmascript_sidebar
summary: "생성자 함수에 의한 객체 생성을 알아보기 전에 먼저 property attribute라는 것에 대해 간단히 알아보고 가겠습니다."
permalink: ecmascript_property-attribute.html
folder: ecmascript
---

## property attribute

JavaScript는 `internal slot`(내부 슬롯)과 `interal method`(내부 메소드)라는 것을 가지고 있습니다.
`ECMAScript` 사양에 등장하는 이중 대괄호(`[[...]]`)로 감싼 이름이 바로 내부 슬롯과 내부 메소드입니다.
<br><br>

이런 내부 슬롯과 내부 메소드는 JavaScript Engine에 의해 실제로 동작하지만 개발자가 직접 접근 할 수 있도록
외부에 공개된 객체의 `property`는 아닙니다. JavaScript Engine의 내부 로직이기 때문에 원칙적으로
내부 슬롯과 내부 메소드에 대한 직접적인 접근이나 직접적인 호출을 제공하지 않습니다.
<br><br>

단, 일부 내부 슬롯과 내부 메소드에 한해 간접적으로 접근할 수 있는 수단을 제공합니다. 가장 대표적인게
`[[Protorype]]`이라는 내부 슬롯입니다. `[[Protorype]]` 내부 슬롯은 `__proto__`를 통해 간접적으로
접근할 수 있습니다.

~~~javascript

const obj = {};

// console.log(obj.[[Prototype]]);  // SyntaxError

// 아래의 코드는 browser 환경에서 실행해야 합니다.
console.log(obj);  // {}
console.log(obj.__proto__);  // {}

~~~

자 이제 내부 슬롯이 무엇인지 알았으니 `property attribute`에 대해서 알아보도록 하죠.

{% include callout.html
type="danger"
content="JavaScript Engine은 `property`를 생성할 때 property의 상태를 나타내는 `property attribute`를
기본값으로 자동 정의합니다."
%}

property의 상태란 property의 값, property값의 갱신 여부, 열거 가능 여부, 재정의 가능 여부를 말합니다.
이런 상태를 나타내는 `property attribute`는 내부 슬롯으로 정의됩니다. 각각 `[[Value]]`, `[[Writable]]`,
`[[Enumerable]]`, `[[Configurable]]`으로 정의됩니다.
<br><br>

이 값들에 직접 접근할 수는 없습니다. 하지만 확인하기 위해서는 `Object.getOwnPropertyDescriptor()`메소드를
이용해 간접적으로 확인할 수는 있습니다.

~~~javascript

const person = {
    name: 'Lee',
    age: 20
};

// browser 환경에서 실행
console.log(person);

console.log(Object.getOwnPropertyDescriptors(person));
// { name:
//     { value: 'Lee',
//       writable: true,
//       enumerable: true,
//       configurable: true },
//    age:
//     { value: 20,
//       writable: true,
//       enumerable: true,
//       configurable: true } }

console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// { value: 'Lee',
//   writable: true,
//   enumerable: true,
//   configurable: true }


~~~

아래의 그림은 각 `property attribute`가 어떤 의미를 가지는지를 나타냅니다.

{% include image.html
file='es6-javascript-property-attribute.png'
%}

---

## property define

`property define`(프로퍼티 정의)란 새로운 property를 추가하면서 `property attribute`를 명시적으로
정의하거나, 기존 property의 `property attribute`를 재정의하는 것을 의미합니다.
<br><br>

`Object.defineProperty()`메소를 이용하면 이 작업을 할 수 있는데 아래의 코드로 이해해 보도록 하죠.
참고로 `Object.defineProperty()`는 한번에 한 개의 `property`만 재정의 할 수 있고 `Object.defineProperties()`
를 이용하면 여러개의 `property`를 재정의 할 수 있습니다.

~~~javascript

// 해당 code는 browser 환경에서 실행합니다.
// code runner에서는 결과가 정상적으로 출력되지 않습니다.

const person = {}

Object.defineProperty(person, 'firstName', {
    value: '홍',
    writable: true,
    enumerable: true,
    configurable: true,
});

console.log(person);

person.firstName = '김';

console.log(person);

// 열거 가능
for(let idx in person) {     // key가 나온다.
    console.log(person[idx]);
}

for(let value of person) {   // value가 나온다.
    console.log(value);
}

~~~

조금 복잡하게 코드를 작성해서 나머지도 알아보죠.

~~~javascript

// 해당 code는 browser 환경에서 실행합니다.
// code runner에서는 결과가 정상적으로 출력되지 않습니다.

const person = {}

Object.defineProperties(person, {
    firstName: {
        value: '홍',
        writable: true,
        enumerable: true,
        configurable: true,    
    },
    lastName: {
        value: '길동',
        writable: false,
        enumerable: true,
        configurable: false,    
    },
    mobile: {
        value: ['123', '456', '789'],
        writable: false,
        enumerable: false,
        configurable: false,    
    }
});

console.log(person);

person.firstName = '김';
person.lastName = '연아';  // 변경되지 않는다.

console.log(person);

// mobile은 열거되지 않는다.
for(let idx in person) {
    console.log(person[idx]);
}

~~~

---

## 객체 변경 금지

객체는 변경 가능한 값(`mutable value`)이기 때문에 재할당 없이 직접 변경할 수 있습니다.
<br><br>

JavaScript는 필요에 따라 객체의 변경을 금지하는 다양한 메소드를 제공합니다. 아래의 표로
알아보도록 하죠.

{% include image.html
file='es6-javascript-object-change-prevent.png'
%}

---

## 객체 확장 금지

`Object.preventExtensions()` 메소드는 객체의 확장을 금지합니다. 즉, 객체의
`property`의 추가를 금지하는 겁니다. 확장이 가능한 객체인지는 `Object.isExtensible()`
메소드로 확인할 수 있습니다. 참고로 `property define`에 의한 추가도 당연히 금지됩니다.

~~~javascript

const person = {
    name: 'Lee'
}

console.log(Object.isExtensible(person));  // true

// 객체 확장 금지
Object.preventExtensions(person);

console.log(Object.isExtensible(person));  // false

// 무시됩니다. strict mode에서는 error가 발생
person.age = 20;

console.log(person);  // { name: 'Lee' }

console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// { value: 'Lee',
//   writable: true,
//   enumerable: true,
//   configurable: true }

// property attribute 재정의 가능
Object.defineProperty(person, 'name', {
    value: '아이유',
    writable: false,
    enumerable: true,
    configurable: true,
});

console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// { value: '아이유',
//   writable: false,
//   enumerable: true,
//   configurable: true }

console.log(person);  // { name: '아이유' }

// 삭제는 가능
delete person.name;

console.log(person);  // {}

~~~

---

## 객체 밀봉

객체 밀봉 금지는 객체의 `property`의 추가와 삭제를 금지하는 것을 의미합니다.
<br><br>

즉, 밀봉된 객체는 읽기와 쓰기만 할 수 있습니다. 위의 표에서는 `property attribute`의
재정의가 안된다고 했는데 아래 코드를 보면 실제로 가능합니다.
밀봉된 객체인지는 `Object.isSealed()` 메소드로 확인이 가능합니다.

~~~javascript

const person = {
    name: 'Lee'
}

console.log(Object.isSealed(person));  // false

Object.seal(person);

console.log(Object.isSealed(person));  // true

// 추가와 삭제 둘 다 무시됩니다.
person.age = 20;
delete person.name;

console.log(person); // { name: 'Lee' }

// 값 갱신은 가능합니다.
person.name = '아이유';

console.log(person); // { name: '아이유' }

// property attribute 변경은 가능(실제로는 가능합니다.)
Object.defineProperty(person, 'name', {
    value: '김연아',
    writable: false
});

console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// { value: '김연아',
//   writable: false,
//   enumerable: true,
//   configurable: false }

~~~

---

## 객체 동결

`Object.freeze()`메소드는 객체를 동결시킵니다. 동결된 객체는 오직 읽기만 가능합니다.
동결된 객체인지의 여부는 `Object.isFrozen()`메소드로 확인할 수 있습니다.

~~~javascript

const person = {
    name: 'Lee'
}

console.log(Object.isFrozen(person));  // false

Object.freeze(person);

console.log(Object.isFrozen(person));  // true

// 추가와 삭제, 값 갱신 모두 무시됩니다.
person.age = 20;
delete person.name;
person.name = '아이유';

console.log(person); // { name: 'Lee' }

// property attribute 변경도 불가능
// Cannot redefine property: name

// Object.defineProperty(person, 'name', {
//     value: '김연아',
//     writable: false
// });


~~~


End.

{% include links.html %}
