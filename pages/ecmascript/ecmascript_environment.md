---
title: ECMAScript 6.0 Environment
sidebar: ecmascript_sidebar
summary: "ES6를 학습하기 위한 개발환경 설정입니다."
permalink: ecmascript_environment.html
folder: ecmascript
---

## ECMAScript 실행환경

모든 Web browser는 JavaScript를 해석하고 실행할 수 있는 `JavaScript Engine`을 내장하고 있습니다.
당연히 browser의 vendor마다 각기 다른 JavaScript Engine을 사용하고 있습니다. Google의 `Chrome`같은 경우
`V8`이라는 JavaScript Engine을 사용하고 있고 MS의 `Edge`는 `Chakra`라는 이름의 엔진을 사용합니다.
Apple의 `Safari`는 `Webkit` 엔진을 모질라재단의 `Firefox`는 `SpiderMonkey`라는 엔진을 사용하고 있습니다.
<br><br>
우리가 작성한 JavaScript code는 대부분 Web browser상에서 실행되겠죠. 이와는 별개로 `Node.js`도
V8 JavaScript Engine을 내장하고 있습니다. 따라서 `Node.js`에서 우리의 JavaScript code를
실행 시킬 수도 있습니다.
<br><br>
단, Web browser는 `BOM`, `DOM`과 같은 `Client Side Web API`를 포함하고 있지만 `Node.js`는
`Host API`만을 가지고 있기 때문에 같은 실행환경은 아닙니다.
<br><br>
아래 그림은 browser 환경과 Node.js 환경에 대한 간단한 도식입니다.

{% include image.html
file='es6-javascript-browser-vs-nodejs.png'
%}

---

## 기본 실행환경 : Web Browser

기본적으로 `Chrome` browser를 사용합니다. Chrome은 최신의 ECMAScript를 가장 잘 지원하고
가장 높은 점유율(2021년 기준 65%)을 가지고 있기 때문입니다. 또한 개발에 필요한 개발자도구
역시 Chrome browser에 내장되어 있기 때문에 별도로 설치할 필요 없습니다.
<br><br>
출력확인 역시 개발자도구에서 제공하는 `console`을 이용하여 확인하도록 하겠습니다.

---

## 개발 Tool : Visual Studio Code

`Client Side Web Application`을 개발할 때는 크게 2가지 Tool을 많이 이용합니다.
첫번째는 `VS Code`이고 두번째는 `WebStorm`입니다. 둘 다 아주 좋은 개발 툴이지만
`VS Code`는 무료이고 `WebStorm`은 유료입니다. 가장 높은 점유율을 차지하고 있는 것은
당연히 `VS Code`입니다.
<br><br>
우리는 `VS Code`를 이용하도록 하죠. 아래의 링크에 접속하면 VS Code를 다운로드 할 수 있습니다.
<br><br>
[Visual Studio Code Download](https://code.visualstudio.com/download){: target="_blank" }
<br><br>
설치파일을 다운로드 하고 설치를 진행합니다.
<br><br>
설치가 끝나면 적절한 위치에 프로젝트 폴더를 하나 생성한 후 JavaScript 파일을 하나 생성합니다.
<br><br>
바탕화면에 `myApp`이라는 폴더를 생성하고 VS Code에서 해당 폴더를 Open합니다. 그리고 새로운
파일을 하나 생성한 후(index.js) 다음의 코드를 입력합니다.

~~~javascript

const myArr = [1, 2, 3]

myArr.forEach(function(data,idx, arr) {
    console.log('요소 : ' + data)
    console.log('인덱스 : ' + idx)
    console.log('배열 : ' + arr)
})

~~~

만약 `Node.js`를 설치한 상태라면 `node` 명령을 이용해 `terminal`에서 해당 코드를
실행시킬 수 있습니다. 하지만 아직 `Node.js`를 설치한 상태가 아니기 때문에 그리고 우리는
`terminal` 환경에서 실행시킬 것은 아니기 때문에 `Code Runner` 확장 플러그인을 설치 및
사용하여 결과를 확인하도록 하겠습니다.

`VS Code`의 확장 버튼을 클릭하고 `Code Runner`를 검색한 후 설치합니다.

{% include image.html
file='es6-javascript-vscode-code-runner-install.png'
%}
<br>
설치가 끝나면 코드를 실행시켜서 결과를 확인해보죠.
실행 단축키는 `ctrl + alt + n` 입니다.

기본설정 중 몇가지만 처리하고 가죠.

`Code Runner` 실행 시 `Clear Output`을 같이 수행하게 하려면

{% include callout.html
type="info"
content="**File -> Preference -> Settings -> User tab -> Extensions ->
Run Code Configuration -> Clear Previous Output**"
%}

항목을 찾아 check해 주면됩니다. (이 설정은 check해 놓아야 편합니다.)

또한 기본적으로 실행시 자동저장이 되지 않는데 아래의 항목을 찾아 check해주면 편리합니다.
이 설정 역시 check해 놓아야 편합니다.

{% include callout.html 
type="info" 
content="**File -> Preference -> Settings -> User tab -> Extensions ->
Run Code Configuration -> Save File Before Run**" 
%}

코드 작성시 `console.log()`를 많이 사용하게 되는데 `log`라고 치면 자동완성이 나오고 이 중 `Log to the Console`을
선택하면 자동으로 `console.log()`를 사용할 수 있습니다.

만약 자주사용되는 구문이 있다면 자동완성 `snippet`을 만들어 사용하면 그나마 편하게 작업할 수 있습니다.

{% include callout.html
type="info"
content="**File -> Preference -> Configure User Snippet -> javascript 검색**"
%}

그러면 `javascript.json` 파일이 나오는데 예제로 되어 있는 부분을 참고하여 본인에 맞게 설정하여 사용하면 됩니다.

다시 돌아와서

만약 아래와 같은 코드면 당연히 오류가 발생합니다. `alert()` 함수는 web API이기 때문에
browser 환경에서 실행시켜야 합니다.

~~~javascript

const myArr = [1, 2, 3]

myArr.forEach(function(data,idx, arr) {
    alert('요소 : ' + data)
})

~~~

위와 같은 코드를 수행하려면 HTML 파일을 생성해야 하고 Web Server를 이용해 파일을 로딩해서
실행해야 합니다. 이를 위해 `Live Server`라는 확장 플러그인을 이용합니다. 플러그인을
설치하고 HTML 파일(`index.html`)을 생성합니다. 빈 파일이 생성되는데 `! + tab`을 하시면
HTML5 기본구조가 자동완성됩니다. 적당히 코드를 작성한 후 실행(`Go Live`버튼 클릭)시켜 보죠.

{% include image.html
file='es6-javascript-vscode-live-server-install.png'
%}
<br>
코드가 변경되면 Live Server 서버에 자동으로 적용됩니다. 이때 역시 변경 내용이 저장되어야 서버에 적용되기
때문에

{% include callout.html
type="info"
content="**File -> Preference -> Settings -> Commonly Used -> Auto Save**"
%}

해당 옵션을 `onWindowChange`로 하시면 사용하기 편리합니다.

End.

{% include links.html %}
