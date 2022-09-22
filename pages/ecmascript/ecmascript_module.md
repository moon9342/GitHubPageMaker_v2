---
title: ECMAScript 6.0 module
sidebar: ecmascript_sidebar
summary: "JavaScript에서 지원하는 module에 대해서 알아보겠습니다."
permalink: ecmascript_module.html
folder: ecmascript
---

## module

module이란 일반적으로 application을 구성하는 재사용 가능한 코드조각을 의미합니다. 일반적으로 이런 module은 
기능을 기준으로 파일 단위로 분리해서 사용합니다. 

이때 모듈이 성립하려면 모듈은 자신만의 파일 스코프(모듈 스코프)를 가질 수 있어야 합니다. 

자신만의 모듈 스코프를 갖는 모듈안에 포함되어 있는 요소(변수, 함수, 객체 등)는 기본적으로 비공개 상태입니다. 즉,
모듈 스코프를 가지는 모듈의 모든 자산은 캡슐화되어 다른 모듈에서 접근할 수 없습니다. 

하지만 이렇게 접근할 수 없으면 재사용이 불가능하므로 다른 모듈에 의해 사용될 수 있도록 공개가 필요한 자산에 한정하여
명시적으로 선택적 공개가 가능한데 이를 export라고 합니다. 

즉, export된 모듈의 자산은 다른 모듈에 의해서 재사용될 수 있습니다. 모듈을 사용하는 측면에서는 모듈이 공개한 자산 중
일부 혹은 전체를 선택해서 자신의 스코프로 불러들여 사용하게 되는데 이를 import라고 합니다. 

---

## JavaScript에서의 모듈

자바스크립트는 기본적으로 모듈을 지원하지 않았습니다. 여러개의 JavaScript 파일이 html 파일안에 포함되어 browser에 
불려지게 되면 하나의 전역공간을 공유하게 되는 것이죠.

과거에는 문제가 없었는데 이제 JavaScript언어를 범용적으로 사용하기 시작하면서 모듈의 지원이 필요하게 되었고 이를 해결하려는
시도가 생기게 되었습니다. 대표적인것이 commonJS이죠. Node.js는 이 commonJS의 방식을 채택, 독자적인 진화를 거쳐서 모듈시스템을
구축하고 있습니다. 

이런 상황에서 client side의 ECMAScript 역시 모듈 기능 추가가 필요하게 되었고 ES6에 들어와서 모듈 기능을 추가했습니다. 
이를 ESM이라고 합니다. IE를 제외한 대부분의 browser에서 현재 지원되고 있습니다. 

사용법은 상당히 간단한데 다음과 같이 사용합니다. 

~~~javascript

<script type="module" src="lib.mjs"></script>

~~~

기억해야 하는 점은 module은 기본적으로 strict mode가 적용된다는 점입니다. 

---

## module scope

module로 불러들이지 않은 두개의 자바스크립트 파일은 독자적인 scope를 가지지 않습니다. 모두 전역공간을 공유해서 사용합니다. 

하지만 module로 불러들인 경우는 독자적인 scope를 생성합니다. 

~~~javascript

// foo.mjs

var x = 'foo';

console.log(x);   // foo
console.log(window.x);  // undefined

// bar.mjs

console.log(x);  // undefined

var x = 'bar';

console.log(x);  // bar
console.log(window.x);  // undefined

~~~

모듈 내부에서 선언한 식별자는 다른 모듈에서 참조가 불가능 합니다. 

---

## export keyword

{% include image.html
file='es6-module/es6-module-1.png'
%}

{% include image.html
file='es6-module/es6-module-2.png'
%}

{% include image.html
file='es6-module/es6-module-3.png'
%}

---

## import keyword

{% include image.html
file='es6-module/es6-module-4.png'
%}

{% include image.html
file='es6-module/es6-module-5.png'
%}



End.

{% include links.html %}
