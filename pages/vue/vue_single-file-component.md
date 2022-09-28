---
title: Vue Single File Component
sidebar: vue_sidebar
summary: ""
permalink: vue_single-file-component.html
folder: vue
---

## HTML 파일에서 Vue 코드 작성시의 한계점

우리가 지금까지 살펴본 여러 개의 component를 이용해 화면을 구성할 수는 있지만 만약 HTML 내용이 복잡해지면
component정의 안에서 이런 HTML을 작성하는것은 대단히 비효율적인 일이 됩니다. 

아래의 그림처럼 말이죠.

{% include image.html
file='vue/vue43.png'
%}

이 문제를 해결하는 방법이 바로 싱글 파일 컴포넌트 체계입니다. 싱글 파일 컴포넌트는 `.vue` 파일로 프로젝트 구조를 구성하는
방식을 말합니다. 확장자 `.vue` 파일 1개는 Vue 웹 어플리케이션을 구성하는 1개의 component와 동일합니다. 

`.vue`파일은 아래와 같은 기본 구조를 가집니다.

{% include image.html
file='vue/vue44.png'
%}

`template` 태그안에는 HTML 태그와 뷰 데이터 바인딩 값들을 넣고, `script` 태그안에는 뷰 컴포넌트에서 사용할 속성들을
정의합니다. 마지막으로 `style` 태그에는 HTML 태그의 스타일 속성들을 정의합니다. 

---

## Vue CLI

이런 `.vue` 파일을 사용하는 싱글 파일 컴포넌트 체계를 사용하기 위해서는 .vue 파일을 웹 브라우져가 인식할 수 있는 형태의 
파일로 변환해 주어야 합니다. 정확히는 Vue Loader를 이용하여 `.vue` 파일의 내용을 브라우져에서 실행가능한 웹 페이지의 
형태로 변형해야 하죠.

이 작업을 위해 우리는 웹팩이라는 모듈 번들러를 사용합니다. 웹팩(webpack) 혹은 브라우져리파이(browserify)와 같은 도구가
바로 그것인데 웹 앱의 자원(HTML,CSS, 이미지)들을 자바스크립트 모듈로 변환해 하나로 묶어 웹의 성능을 향상시켜주는 것입니다.

실제 사용함에 있어서 웹팩의 성능과 사용의 용이성이 더 좋습니다. 하지만 우리가 이런것을 사용하기에는 또 이런것들을 배워야 하기
때문에 사실 많은 부담이 됩니다. 

그래서 Vue 개발자들이 편하게 프로젝트를 구성하기 위해 Vue 코어팀에서 Vue CLI 도구를 제공합니다. 

CLI는 커맨드 창에서 명령어로 특정 동작을 수행할 수 있는 도구입니다. 이를 이용하면 초기 프로젝트 구조를 쉽게 구성할 수 있습니다.

## Vue CLI 설치

Vue CLI를 설치하기 위해 다음의 명령을 실행합니다. Node.js가 설치되어 있어야 합니다.

{% include callout.html
type="danger"
content="**npm install vue-cli -global**"
%}

만약 네트워크가 연결되어 있지 않다면 파일을 복사한 후 다음의 명령어로 prefix를 바꿔줘서 사용해야 합니다. 

npm -g install 로 설치하는 npm 모듈들은 기본적으로 C:\Users\사용자\AppData\Roaming\npm\node_modules 아래에 설치된다. 
용량은 그리 크지 않겠지만 괜히 여기에 뭐 설치되는게 싫어서 다른 곳으로 설치를 옮기고 싶다면 다음과 같이 하면 됩니다.

npm config 명령을 사용해서 설치 위치를 변경할 수 있다.

적당한 위치에 폴더를 하나 생성한 후 복사한 npm 폴더의 압축을 풉니다. 

{% include callout.html
type="danger"
content="**npm config set prefix 'c:/npm'**"
%}

{% include callout.html
type="danger"
content="**npm config set cache 'c:/npm-cache'**"
%}

전역으로 설치된 모듈의 실행 파일 역시 PREFIX 디렉토리에 생성되므로 해당 위치를 PATH 환경변수에 추가해주는 작업을 해주면 됩니다.

설치가 잘 되었는지 `vue` 명령을 이용해 확인합니다.

{% include callout.html
type="danger"
content="**vue**"
%}

설치가 잘 되었다면 이제 프로젝트 초기 구성을 위한 명령어를 알아보도록 하죠.

다음표와 같이 총 6개의 명령이 존재합니다. 이 중 우리는 `vue init webpack-simple`을 이용하도록 하겠습니다. 

{% include image.html
file='vue/vue45.png'
%}

다음과 같은 순서로 프로젝트를 구성합니다. 

1. 뷰 프로젝트를 구성할 빈 폴더를 하나 생성하고 빈 폴더에서 `vue init webpack-simple`를 입력합니다.

2. 간단한 질문이 나오는데 적당히 입력하면 파일이 생성됩니다. 

3. `npm install`을 이용하여 관련 라이브러리를 모두 다운로드 합니다. 여기서 만약 다운로드가 안된다면 제공된 파일을 이용하여 압축을
풉니다.

4. 그러면 다음과 같은 구조가 생성됩니다.

{% include image.html
file='vue/vue46.png'
%}

`package.json`을 살펴보면 다음과 같습니다. 이 파일은 프로젝트의 정보를 담고 있는 설정파일임과 동시에
npm 명령어와 Vue로 어플리케이션을 작성하기 위한 라이브러리 정보를 가지고 있습니다. 

{% include image.html
file='vue/vue47.png'
%}

이전에 `npm install` 명령을 이용했을 때 `package.json`의 라이브러리 목록이 전부 project의 node_modules 폴더
밑에 설치됩니다. 

마지막으로 `npm run dev`를 실행하면 로컬 서버하나를 실행하고 브라우져를 실행시켜 어플리케이션을 실행합니다. 

실행이 잘 되나요? 그럼 이제 간단한 프로젝트를 구현해 보도록 하죠.


End.

{% include links.html %}
