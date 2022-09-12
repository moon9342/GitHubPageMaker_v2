---
title: ECMAScript 6.0 Introduction
sidebar: ecmascript_sidebar
summary: "과거 JavaScript 탄생부터 시작해서 어떻게 발전해왔는지 알아보고 
현재 Front End 개발의 전반적인 기술 상황에 대해서 정리해 보죠."
permalink: ecmascript_introduction.html
folder: ecmascript
---

## JavaScript 탄생

과거부터 Web 작업을 해 오신 분들은 아시겠지만 JavaScript의 과거 인식은
**"알면 좋고 몰라도 상관없는 딱 그 수준의 언어"** 였습니다. 
<br><br>

하지만 개발 방법론이 진화하고 `Front End Web Application` 개발이라는 분야가 등장하면서 
JavaScript기반의 Library나 Framework의 필요성이 증대되고 그에 따라 JavaScript라는
언어의 위상도 상당히 올라갔습니다.
<br><br>

여기에 많은 공헌을 했던게 바로 `HTML5`와 `Node.js`, 그리고 각종 Framework(`Angular`, `React`, `Vue`)입니다.
<br><br>

JavaScript라는 언어의 전신은 1995년 Netscape사에서 Brendan Eich(브랜든 아이크)가
개발한 `Mocha`라는 이름의 스크립트 언어입니다.
<br><br>

이 `Mocha`라는 언어는 **"Prototype의 개념과 Functional Language(함수형 언어)의
특성을 가지고 Web에 동적으로 활용할 수 있는 언어를 만들어보자"** 라는 취지로
2주일 이라는 굉장히 짧은 기간안에 만들어졌습니다.
<br><br>

개발을 완료하고 후에 `LiveScript`라는 이름으로 세상에 공개되었습니다. 그런데 이 시점에
Netscape사가 Java를 개발한 Sun Microsystems사와 협업하면서 `Java Applet`이 큰 인기를
끌게 되었는데 이를 상업적으로 이용하고자 이름을 `LiveScript`에서 `JavaScript`로
변경하게 된 것입니다.
<br><br>

**이름의 변경 순서가 `Mocha` <i class="fa fa-arrow-right"></i> `LiveScript`
<i class="fa fa-arrow-right"></i> `JavaScript` 가 되는군요.**

---

## ECMAScript

여하간 Netscape사는 이 JavaScript의 표준을 잡기 위해 `ECMA`(European Computer
Manufacturers Association - 정보통신기술 비영리 표준화 기구)에 표준화 작업을 요청합니다.
당연히 `JavaScript`라는 이름을 사용하려고 했지만 Sun과의 라이선스 문제 때문에
`ECMAScript`라는 이름으로 표준화가 진행되게 됩니다.
<br><br>

이렇게만 놓고 보면 JavaScript와 ECMAScript가 비슷한 의미로 사용되는 것처럼 보이는데
사실 작은 차이가 있습니다.
<br><br>

**우리가 흔히 말하는 JavaScript는 `ECMAScript`라는 Core Script언어와 환경에 따라
`Client side Web API` 또는 `Host API`로 구성됩니다.**
<br><br>

이 `Client side Web API`안의 가장 대표적 구성요소가 바로 `BOM`(Browser Object Model),
`DOM`(Document Object Model)입니다. 이 외에 `Web Storage`, `IndexedDB`, `Web Worker`,
`Canvas`, `XMLHttpRequest`, `SVG(Scalable Vector Graphics)`, `Web Component`등이
포함됩니다.
<br><br>

`Host API`는 JavaScript언어를 서버쪽 구현 언어로 사용하는 `Node.js`에서 제공하는 API의 집합이라고
생각하시면 됩니다.
<br><br>

달리 말하자면 `ECMAScript`는 JavaScript를 구성하는 Core Script언어로 웹 환경에서만
사용되는 언어가 아닙니다. 웹 환경은 ECMAScript가 사용되는 환경 중 하나일 뿐입니다.

{% include callout.html 
type="info" 
content="**`JavaScript` = `ECMAScript` + `Client side Web API` or `Host API`**" 
%}

현재 ECMAScript 배포판은 12까지 나와있습니다. (`ECMAScript 2021` = `ES12`) 하지만
우리가 중요하게 생각하는 버전은 `ECMAScript 2015`(`ES6`)입니다. 왜냐하면,
이 때 부터 `let,const`, `arrow function`, `class`, `module`과 같은 범용 프로그래밍
언어로서 갖춰야할 기능들이 대거 도입되는 큰 변화가 이루어졌기 때문입니다. `ES6` 이후의
버전업은 비교적 작은 기능을 추가하는 수준으로 매년 공개가 되고 있습니다.
<br><br>

이렇듯 `JavaScript`와 `ECMAScript`는 정확한 의미의 차이는 분명히 있지만 그냥 저냥 대충
통용해서 사용합니다. 그래서 우리가 JavaScript라고 표현하면 ECMAScript를 의미한다고 보시면
됩니다.
<br><br>

과거 MS(Microsoft)에서는 이런 JavaScript에 대항(?)하기 위해 `JScript`를 만들고
`IE 3`부터 JScript를 포함시켜서 제공했습니다. 당연한 말이지만 `JavaScript`와 `JScript`는
서로 비슷하지만 특징이 다릅니다. 그로인해 우리가 잘 알고있는 JavaScript의 고질적인 문제인
`Fragmentation`(파편화 현상)이 발생하게 됩니다.
<br><br>

이런 JavaScript는 상당히 좋은 언어이지만 여러가지 문제점을 내포하고 있었는데 가장 큰 문제를 꼽자면
<br><br>

* 언어의 모호성
* 구현의 어려움
* Cross Browsing의 한계

<br>
정도를 들 수 있습니다. 그래서 사용자 입력값을 검사하는 용도, 사용자의 이벤트를 감지해서 처리하는
용도로는 사용할 수 있지만 언어 자체가 가지고 있는 근본적인 문제때문에 본격적인 사용은 힘들었습니다.

---

## jQuery

위에서 언급한 것처럼 과거 JavaScript의 Fragmentation 현상이 심할 때  구세주가 등장합니다. 
우리가 잘 알고 있는 `jQuery` library입니다.
<br><br>

`jQuery`는 open source javascript library로 순수 javascript가 가지고 있었던
DOM처리의 어려움과 Cross Browsing에 대한 issue를 해결하면서 **JavaScript의 대표적 library**로
사용되게 되었습니다. jQuery로 인해 JavaScript가 조금은 쓸만한 언어로 여기지면서 사용되기 시작했습니다.
<br><br>

그러면서 그냥 그냥 아주 소소하게 client web browser안에서 JavaScript 코드와 jQuery 코드가 이용되었던 거지요.
특별히 중요하지도 않은 단위코드 위주로 사용이 되는 그런 수준이었습니다. 그러면서 우리 기억에도
JavaScript는 **"알면 좋고 몰라도 상관없는 언어"**로 인식되었습니다.
<br><br>

그렇게시간은 흘러갑니다.!!

---

## HTML5

`W3C`(World Wide Web Consortium)라는 기구를 알고 계실 겁니다.
아시다시피 W3C는 `HTML`과 `CSS`의 표준을 관장하는 기구입니다. 1999년 12월
`W3C`에서 하나의 발표를 합니다. 이때의 HTML의 버전은 4.01버전이었는데 W3C는
이 버전을 마지막으로 더 이상 HTML의 버전업은 없을 것이라고 공표하게 됩니다.
그때까지 잘 사용해왔던 HTML을 왜 버전업하지 하지 않겠다고 했을까요?
<br><br>

여러가지 이유가 있지만 프로그래밍 언어의 측면에서 바라보면 HTML이란 언어는 다음과 같은
한계점이 있습니다.
<br><br>

* **정형성** : HTML언어는 정형성이 없기때문에 유지보수에 적합하지 않다.
* **확장성** : 정해진 Tag만 이용하기 때문에 언어자체에 대한 기능적 확장이 어렵다.

<br>
`W3C`는 저 두가지 이유를 들어 HTML언어를 이대로 끌고 가기 보다는 좀 더 다른 수단을 강구해야
겠다고 판단합니다. W3C의 선택은 바로 `XML`의 도입이었습니다. 즉, HTML이 가지는 두가지
문제점을 해결할 수 있는 XML을 도입해서 새로운 Web언어의 표준을 끌고 나갈 생각이었습니다.
그 결과 2000년 들어오면서 HTML과 XML이 결합된 새로운 표준인 `XHTML 1.0`이 발표되게 됩니다.
<br><br>

W3C의 이런 의도와는 다르게 몇몇개의 회사들이 모여서 조금 다른 접근을 모색합니다.
XML을 도입하지 말고 **순수하게 HTML을 기능을 버전업시킬 수 있는 방법**을 찾기 시작한 것이죠.
그래서 `WHATWG`(Web Hypertext Application Technology Working Group)이라는
Working Group이 만들어지고 이 안에서 기존의 HTML을 발전시킬 방법을 연구하게 됩니다.
<br><br>

이 WG에서 나온것이 바로 현재의 표준인 `HTML5`입니다. HTML을 이용해서 단순히 정보를 보여주는
Web page가 아닌 Web Application을 만들자는 취지입니다. 정확히는 `client-side web application`을
말이죠.
<br><br>

Web browser로 필요한 프로그램을 다운로드해서 기기에 상관없이 application을 사용할 수 있도록 만들자는
것이 초기의 취지였습니다.
<br><br>

`Web Application`의 가장 대표적인 예로 `Synology NAS DSM Live Demo`를 들 수 있습니다.
<br><br>

[Synology NAS DSM Live Demo](https://demo.synology.com/ko-kr/dsm){: target="_blank" }
<br><br>

HTML5안에 Client Web Application을 작성할 수 있는 API가 포함되어 있는데 이게
`JavaScript`로 되어 있습니다. 결국 HTML5가 issue화 되면서 JavaScript의 위상도 조금씩
더 올라가게 됩니다.
<br><br>

이제 HTML5라는 말은 의미가 넓어져, 꼭 W3C의 HTML5 표준만이 아닌 최신 웹 기술
(CSS3, ES6등)을 통틀어 의미합니다.
<br><br>

그래서인지 표준화 후 쿨쿨 잠을자던 ECMAScript 개정판(`ES5`)이 2009년에 등장합니다.
이제 슬슬 JavaScript에 대한 발전이 시작됩니다.

---

## Node.js

2013년 `Node.js`가 출시됩니다. 아무리 HTML5라고 해도 이때까지의 JavaScript는
Browser안에서 동작하는 언어였습니다. 우리가 알고 있는 `Java`, `C#`과 같은 언어가 될 수
없는 태생적 한계가 있는거죠. 이런 browser에 갇힌 JavaScript를 browser 외부로 탈출하게끔
만들어준 일등공신이 바로 `Node.js`입니다.
<br><br>

결국 JavaScript로 다른 언어처럼 `OS`위에서 동작하는 일반 application을 작성할 수 있는
길이 열린것이고 그에 따라 언어의 위상이 완전히 변했습니다.
<br><br>

그에 발맞춰 `ECMAScript`도 빠르게 변화하기 시작했습니다.
2015년에 ECMAScript 2015(`ES6`)가 등장하면서 JavaScript 언어자체에 아주 큰 변화들이
많이 생겼습니다. 거의 1년마다 새로운 배포판이 나올정도로 빠르게 변화하면서 언어적 측면에서
가지고 있던 많은 문제점들을 극복하려고 노력하고 있습니다.
<br><br>

현재 대부분의 browser들은 `ES6`를 지원하고 있습니다.
[ECMAScript의 버전 호환성](https://kangax.github.io/compat-table/es6/){: target="_blank" }에 접속해
보시면 특정 browser에 대해 각 ECMAScript의 버전 호환성을 보실 수 있습니다.
<br><br>

아직 완벽하게 Cross Browsing이 안되는데 어떻게 하느냐에 대한 문제는
`Babel`, `TypeScript`와 같은 `transpiler`를 이용해서 해결할 수 있습니다.
Babel, TypeScript는 ES6(또는 그 상위버전) 문법으로 쓰여있는 JavaScript code를 ES5 형태의
JavaScript로 변환시켜주기 때문에 IE에서 ES6지원이 안됨에도 불구하고 ES5로 변환해서
사용할 수 있습니다.

---

## TypeScript

아마도 `TypeScript`라는 것도 들어보셨을 거라 생각합니다. 참고로 알아두도록 하죠.
<br><br>
TypeScript는 또 어떤걸까요? ECMAScript와는 어떻게 다를까요?
<br><br>
`TypeScript`는 Microsoft에서 개발하여 2012년에 발표한 오픈 소스 프로그래밍 언어입니다.
JavaScript로 대규모 application개발을 하기 위해 개발되었고 `TypeScript`언어로 작성된
코드를 컴파일하면 JavaScript 코드가 됩니다. 변환 언어의 한 종류라 볼 수 있습니다.
이러한 변환 작업을 `transpile`이라고 합니다. 그리고 이런 프로그래밍을
`Meta Programming` 이라고 합니다.
<br><br>
그냥 JavaScript로 개발하면 되지 굳이 이런 변환언어를 사용하는 이유는 무엇일까요?
<br>

{% include callout.html type="danger" content="**가장 큰 이유는 기능의 추가에 있습니다.**" %} 

<br>
변환언어의 가장 큰 장점은 변환되는 언어에 기능을 쉽게 추가할 수 있다는 것인데 예를 들면,
추상 클래스, 인터페이스, 정적 데이터타입 사용, 엄격한 타입 체크등을 JavaScript에 추가하여
개발을 쉽게 끌고 갈 수 있습니다.
<br><br>
즉, 전통적인 OOP방식으로 JavaScript개발을 진행할 수 있다는 것이죠.
<br><br>
`TypeScript`는 **JavaScript의 superset**입니다.
그렇기 때문에 이미 JavaScript를 알고 있으면 쉽게 TypeScript를 사용할 수 있으며
`ES6`(ECMAScript 2015)나 `ES7`(ECMAScript 2016)에서 제공되는 기능 중 필요한 것만
골라서 이용할 수 있습니다.
<br><br>
아래의 그림은 `TypeScript`와 `ECMAScript`간의 포함관계를 보여줍니다.
<br><br>

{% include image.html 
file="es5-es6-typescript-circle-diagram.png"
%}

---

## 빠른 기술의 발전 속도

그런데 문제는 기술의 발전 속도가 너무 빠르다는데 있습니다. 또한 알아야 할게 한두 가지가
아니구요. 과거 Back End에서 사용했던 프로그래밍 기술과 여러가지 개발 툴의 개념들이
다 Front End로 넘어왔기 때문입니다. 너무 많은기술들이 빠르게 생기고 빠르게 사라지고 있습니다.
<br><br>
사실 몇년전에 자주 사용했던 JavaScript Library와 Framework들 중 많은 것들이 이미 다른
것으로 대체되었습니다. 그렇다고 손 놓고 있을 순 없으니 현재 상태에서 우리가
Front End 개발을 하기 위해 필수적으로 알아야 하는 것 몇가지만 살펴보도록 하겠습니다.
<br><br>
* **Package Manager : `NPM`**<br>
  외부 라이브러리를 사용할 경우 해당 라이브러리를 설치, 삭제, 의존성 관리등을 해야 하는데
  이를 위한 도구가 몇몇 존재합니다. 그중 하나만 하자면 당연 `npm`입니다.
  Node Package Manager의 약자이고 Node.js를 살짝 맛보시면 npm이 어떻게 동작하는지
  아실 수 있습니다. 굳이 Node.js에 대해서 학습할 필요는 없습니다. 당연히 알면 좋지만요.
<br><br>
* **Transpiler : `TypeScript`**<br>
  `Cross Browsing`을 위한 Transpiler입니다. 저는 `TypeScript`는 반드시 해야 한다고
  생각합니다. 앞으로 TypeScript는 더 발전할 듯 보이거든요. 하지만 배우기 쉽지 않습니다.
  언어적 측면을 파고 들면 내용이 꽤나 많습니다.
  다행히 TypeScript는 ECMAScript의 superset이니 ECMAScript 공부한다고 생각하시면
  속 편합니다. 또한 제 개인적인 생각과는 반대로 TypeScript언어의 단점도 분명히 존재합니다.
  엄격한 type checking덕분에 JavaScript가 가지는 프로그래밍의 유연함이 많이 떨어진다는
  것이죠.
<br><br>
* **자동화 도구 : Grunt, `Gulp`**<br>
  빌드, 테스트, 배포등을 위한 자동화 도구입니다. 과거에는 Grunt를 많이 사용했지만 현재는
  `Gulp`인듯 합니다. 시간 좀 지나면 Gulp도 Grunt와 마찬가지로 역사속으로 사라지게 되겠지만
  지금은 Gulp가 대세입니다.
<br><br>
* **module bundler : `webpack`, rollup**<br>
  코드 재사용을 위한 모듈화 도구들과 bundler 역시 여러가지 있는데 요즘 많이 사용하는
  건 `webpack`입니다. ES6 들어오면서 언어적 차원에서 module을 지원하기 시작했고
  이런 module들을 하나로 모아 packaing하는 작업이 필요하게 되었습니다.
  이를 위한 도구 역시 발전을 하고 있구요. 최근에는 webpack, rollup을 이용하여 처리합니다.
<br><br>
* **Framework : `Angular`, `React`, `Vue`**<br>
  우리의 목적은 사실 저 위의 3가지를 배우는 것입니다. 각기 나름대로의 장점과 특징들을 가지고
  있습니다. 추후에 각각의 Framework에 대해서 알아보도록 하겠습니다.

<br>
사실 제가 강조한 것들은 제가 현재 사용하는 것들입니다.
그것들이 더 좋다는 보장은 어디에도 없습니다. 당연히 자기 손에 익숙한게 최고입니다. ^^

End.

{% include links.html %}
