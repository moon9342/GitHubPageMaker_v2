---
title: Vue Component
sidebar: vue_sidebar
summary: ""
permalink: vue_component.html
folder: vue
---

## Vue Component

component란 조합하여 화면을 구성할 수 있는 화면의 특정 영역을 가리키는 블록을 의미합니다. 

컴포넌트를 활용하면 화면을 빠르게 구조화하여 일괄적인 패턴으로 개발할 수 있습니다. 화면의 영역을 컴포넌트로 
쪼개서 재활용할 수 있는 형태로 관리하면 나중에 코드를 재 사용하기도 훨씬 편하게 됩니다. 

일반적으로 Vue 화면을 구성할 때는 네비게이션바, 테이블, 리스트, 인풋 상자등과 같이 화면을 잘게 쪼개서
컴포넌트로 관리합니다. 간단하게 도식화된 것을 보죠.

{% include image.html
file='vue/vue12.png'
%}

위의 그림에서 알 수 있듯이 컴포넌트의 관계는 tree 구조와 유사합니다. 

## 컴포넌트의 등록

컴포넌트를 등록하는 방법은 크게 전역과 지역 두 가지가 있습니다. 지역 컴포넌트는 특정 인스턴스 안에서만 유효한
밤위를 가지고 전역 컴포넌트는 여러 인스턴스에서 공통으로 사용할 수 있습니다. 

즉, 지역 컴포넌트는 특정 범위 내에서만 사용할 수 있고 전역은 Vue로 접근 가능한 모든 범위에서 사용할 수 있습니다. 

### 전역 컴포넌트 등록

전역 컴포넌트는 Vue 라이브러리를 로딩하고 나면 접근 가능한 생성자 함수 Vue를 이용하여 등록합니다. 

전역 컴포넌트를 모든 인스턴스에 등록하려면 Vue 생성자 함수에서 `.component()`를 호출하여 수행하면 됩니다. 

{% include image.html
file='vue/vue13.png'
%}

전역 컴포넌트 등록 형식에는 컴포넌트 이름과 컴포넌트 형식이 있습니다. 

컴포넌트 이름은 template 속성에서 사용할 수 있는 HTML 사용자 정의 태그이름을 의미합니다. 이때 태그이름은
가능한 모두 소문자 그리고 단어의 조합으로 되어있을 경우 케밥스타일로 하시는게 좋습니다. 하지만 강제적인 사항은 아닙니다.

그리고 컴포넌트 태그가 실제 화면의 HTML 요소로 변환될 때 표시될 속성들을 컴폰전트 내용에 작성합니다. 여기에는
template, data, methods 등의 인스턴스 옵션 속성을 정의할 수 있습니다. 

~~~javascript

<html>
  <head>
    <title>Vue Component Registration</title>
  </head>
  <body>
    <div id="app">
      <button>컴포넌트 등록</button>
      <my-component></my-component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      Vue.component('my-component', {
        template: '<div>전역 컴포넌트가 등록되었습니다!</div>'
      });

      new Vue({
        el: '#app'
      });
    </script>
  </body>
</html>

~~~

그림으로 설명하면 다음과 같습니다. 

{% include image.html
file='vue/vue14.png'
%}

전역 컴포넌트가 화면에 적용되는 과정을 살펴보면 다음과 같습니다. 

{% include image.html
file='vue/vue15.png'
%}

인스턴스가 먼저 생성되고 인스턴스 내용이 화면 요소로 변환될 때 컴포넌트 태그도 함께 변환됩니다. 

전역 컴포넌트를 등록하려면 HTML에서 사용할 사용자 정의 태그 이름을 컴포넌트의 이름으로 작성하고,
중괄호 안에 사용자 정의 태그가 실제로 화면에 그려질 때 표시될 내용을 작성해야 합니다. 여기서는
template 속성을 이용하여 정의했습니다. 

### 지역 컴포넌트 등록

지역 컴포넌트의 등록은 전역 컴포넌트의 등록과는 다르게 인스턴스에 `components` 속성을 추가하고 등록할
컴포넌트의 이름과 내용을 정의하면 됩니다. 

{% include image.html
file='vue/vue16.png'
%}

예제를 통해 알아보죠.

~~~javascript

<html>
  <head>
    <title>Vue Component Registration</title>
  </head>
  <body>
    <div id="app">
      <button>컴포넌트 등록</button>
      <my-local-component></my-local-component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      var cmp = {
        // 컴포넌트 내용
        template: '<div>지역 컴포넌트가 등록되었습니다!</div>'
      };

      new Vue({
        el: '#app',
        components: {
          'my-local-component': cmp
        }
      });
    </script>
  </body>
</html>

~~~

### 지역 컴포넌트와 전역 컴포넌트의 차이

지역 컴포넌트와 전역 컴포넌트의 차이를 알아보죠. 이 내용을 이해하려면 당연히 인스턴스의 유효범위에 대한 내용을
이해해야 합니다. 

~~~javascript

<html>
  <head>
    <title>Vue Local and Global Components</title>
  </head>
  <body>
    <div id="app">
      <h3>첫 번째 인스턴스 영역</h3>
      <my-global-component></my-global-component>
      <my-local-component></my-local-component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      // 전역 컴포넌트 등록
      Vue.component('my-global-component', {
        template: '<div>전역 컴포넌트 입니다.</div>'
      });

      // 지역 컴포넌트 내용
      var cmp = {
        template: '<div>지역 컴포넌트 입니다.</div>'
      };

      new Vue({
        el: '#app',
        // 지역 컴포넌트 등록
        components: {
          'my-local-component': cmp
        }
      });
    </script>
  </body>
</html>

~~~

여기까지는 화면에 잘 나옵니다. 그럼 여기에 Vue 인스턴스를 하나 더 생성하고 추가해 보도록 하죠.

~~~javascript

<html>
  <head>
    <title>Vue Local and Global Components</title>
  </head>
  <body>
    <div id="app">
      <h3>첫 번째 인스턴스 영역</h3>
      <my-global-component></my-global-component>
      <my-local-component></my-local-component>
    </div>
    <hr>
    <div id="app2">
      <h3>두 번째 인스턴스 영역</h3>
      <my-global-component></my-global-component>
      <my-local-component></my-local-component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      // 전역 컴포넌트 등록
      Vue.component('my-global-component', {
        template: '<div>전역 컴포넌트 입니다.</div>'
      });

      // 지역 컴포넌트 내용
      var cmp = {
        template: '<div>지역 컴포넌트 입니다.</div>'
      };

      new Vue({
        el: '#app',
        // 지역 컴포넌트 등록
        components: {
          'my-local-component': cmp
        }
      });

      // 두 번째 인스턴스
      new Vue({
        el: '#app2'
      });
    </script>
  </body>
</html>

~~~

위의 예제를 보면 명확히 이해할 수 있습니다. 전역 컴포넌트는 인스턴스를 생성할 때마다 인스턴스에 `components` 속성으로
등록할 필요가 없이 한번 등록하면 어느 인스턴스에서든 사용할 수 있습니다. 반대로 지역 컴포넌트는 새 인스턴스를 생성할 때마다
등록해 줘야 합니다. 당연하죠!!



End.

{% include links.html %}
