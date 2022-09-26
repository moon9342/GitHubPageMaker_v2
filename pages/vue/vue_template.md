---
title: Vue Template
sidebar: vue_sidebar
summary: ""
permalink: vue_template.html
folder: vue
---

## Vue template

Vue의 template은 HTML, CSS등의 마크업 속성과 Vue 인스턴스에서 정의한 데이터 및 로직들을 연결하여
사용자가 브라우저에서 볼 수 있는 형태의 HTML로 변환해주는 속성입니다. 

이 template 속성을 이용하는 방법은 두가지인데 ES5 형태로 Vue 인스턴스의 template 속성을 사용하는 것입니다. 

지금까지 예제에서 주로 사용했던 방법이죠.

{% include image.html
file='vue/vue36.png'
%}

참고로 알아보면 다음과 같은 두 코드는 최종적으로 화면에 나타나는 내용은 같습니다. 단, 인스턴스의 내용이 적용되는 시점이 
다릅니다.

{% include image.html
file='vue/vue37.png'
%}

왼쪽의 코드는 먼저 `<h3>`를 화면에 먼저 표시하고 인스턴스가 생성되면 message의 값을 치환합니다. 하지만 오른쪽 코드는
`<div>`에 아무런 내용이 없다가 인스턴스가 생성되고 화면에 부착되면 그 때 `<h3>`가 화면에 붙게됩니다. 

두번째 방법은 싱글 파일 컴포넌트 체계의 `<tempalte>` 코드를 활용하는 방법입니다. 

{% include image.html
file='vue/vue38.png'
%}

템플릿에서 사용하는 속성과 문법은 다음과 같습니다. 하나씩 살펴보죠.

### 데이터 바인딩

데이터 바인딩은 HTML 화면 요소를 뷰 인스턴스의 데이터와 연결하는 것을 의미합니다.

&#123;&#123;&#125;&#125; 문법과 `v-bind` 속성이 있습니다. 

&#123;&#123;&#125;&#125; 문법은 Vue 인스턴스의 데이터를 HTML 태그에 연결하는 가장 기본적인 텍스트 삽입 방식입니다. 다른 언어에서도
많이 사용하는 템플릿 문법이죠. 

아래 코드는 지금까지 우리가 많이 보아왔던 코드인데 data 속성의 message 속성값을 &#123;&#123;message&#125;&#125;에 연결하여 화면에
나타내는 코드입니다. 만약 data 속성의 message값이 변경되면 반응성에 의해 화면이 자동으로 갱신됩니다. 

{% include image.html
file='vue/vue39.png'
%}

만약 데이터가 변경되어도 바꾸고 싶지 않을 경우 다음과 같이 `v-once` 속성을 사용합니다. 

{% include image.html
file='vue/vue40.png'
%}

### v-bind

v-bind는 아이디, 클래스등의 HTML attribute값에 뷰 데이터 값을 연결할 때 사용하는 데이터 연결방식입니다. 
다음코드를 보고 이해해보죠.

~~~html
{% raw %}

<html>
<head>
    <title>Vue Template - Data Binding</title>
</head>
<body>
<div id="app">
    <p v-bind:id="idA">아이디 바인딩</p>
    <p v-bind:class="classA">클래스 바인딩</p>
    <p v-bind:style="styleA">스타일 바인딩</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            idA: 10,
            classA: 'container',
            styleA: 'color: blue'
        }
    });
</script>
</body>
</html>

{% endraw %}

~~~

참고로 `v-bind:` 문법은 `:`로 간소화 할 수 있습니다. 즉, `v-bind:id`는 `:id`로 표현할 수 있습니다. 하지만
가급적 `v-bind:` 형태로 사용하는게 좋습니다. 

### javascript 표현식 사용

vue의 template에서는 javascript 표현식을 쓸 수 있습니다. 데이터 바인딩 방법 중 하나인 &#123;&#123;&#125;&#125; 안에
자바스크립트 표현식을 넣으면 됩니다. 

다음 코드를 보시죠.

~~~html
{% raw %}

<html>
<head>
    <title>Vue Template - Javascript Expression</title>
</head>
<body>
<div id="app">
    <p>{{ message }}</p>
    <p>{{ message + "!!!" }}</p>
    <p>{{ message.split('').reverse().join('') }}</p>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue.js!'
        }
    });
</script>
</body>
</html>

{% endraw %}
~~~

위의 예제처럼 표현식을 사용할 수 있지만 표현식 안에서 선언문과 분기문을 사용할 수 없습니다. 단, 3항 연산자는 사용할 수 있습니다.
그리고 복잡한 연산은 가능한 인스턴스 안에서 처리하고 화면에는 연산 결과만 표시하게 하는것이 좋습니다. 

다음의 코드를 보시죠.

~~~html
{% raw %}

<html>
  <head>
    <title>Vue Template - Javascript Expression</title>
  </head>
  <body>
    <div id="app">
      <!-- 1. -->
      {{ var a = 10; }} <!-- X, 선언문은 사용 불가능 -->
      {{ if (true) {return 100} }} <!-- X, 분기 구문은 사용 불가능 -->
      {{ true ? 100 : 0 }} <!-- O, 삼항 연산자로 표현 가능 -->

      <!-- 2. -->
      {{ message.split('').reverse().join('') }} <!-- X, 복잡한 연산은 인스턴스 안에서 수행 -->
      {{ reversedMessage }} <!-- O, 스크립트에서 computed 속성으로 계산 후 최종 값만 표현 -->
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        },
        computed: {
          reversedMessage: function() {
            return this.message.split('').reverse().join('');
          }
        }
      });
    </script>
  </body>
</html>

{% endraw %}
~~~

위의 코드에서 computed 속성을 사용했는데 캐싱 기능을 가지고 있어서 반복적인 연산에 있어서 성능을 높일 수 있습니다. 

### 디렉티브

vue 디렉티브란 HTML 태그 안에 `v-` 접두사를 가지는 모든 속성을 의미합니다. 우리가 앞에서 배운 `v-bind`역시 디렉티브입니다.

간단한 예를 보자면 다음과 같습니다. 

{% include image.html
file='vue/vue41.png'
%}

위의 a 태그는 데이터 속성에 정의된 flag의 값에 따라 보이기도 하고 보이지 않기도 합니다. 값이 true이면 화면에 보일것이고
그렇지 않으면 화면에 나타나지 않겠죠.

이런 디렉티브는 화면의 요소를 더 쉽게 조작하기 위해 사용하는 기능입니다. 뷰의 데이터 값이 변경되었을 때 화면의 요소들이 
리액티브하게 반응하여 변경된 데이터 값에 따라 갱신되게 되는데 화면의 요소를 직접 제어할 필요없이 디렉티브를 활용하면
화면 요소들을 편하게 조작할 수 있습니다. 

주요한 디렉티브는 다음과 같습니다. 

{% include image.html
file='vue/vue42.png'
%}

코드로 살펴보죠

~~~html
{% raw %}

<html>
  <head>
    <title>Vue Template - Directives</title>
  </head>
  <body>
    <div id="app">
      <a v-if="flag">두잇 Vue.js</a>
      <ul>
        <li v-for="system in systems">{{ system }}</li>
      </ul>
      <p v-show="flag">두잇 Vue.js</p>
      <h5 v-bind:id="uid">뷰 입문서</h5>
      <button v-on:click="popupAlert">경고 창 버튼</button>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      new Vue({
        el: '#app',
        data: {
          flag: true,
          systems: ['android', 'ios', 'window'],
          uid: 10
        },
        methods: {
          popupAlert: function() {
            return alert('경고 창 표시');
          }
        }
      });
    </script>
  </body>
</html>

{% endraw %}
~~~


### 이벤트 처리

웹 앱에서 사용자의 이벤트를 처리하는 것은 필수적으로 일어나는 일입니다. Vue 역시 화면에서 발생한 이벤트를 처리하기 위해
`v-on` 디렉티브와 methods 속성을 이용합니다. 코드로 살펴보죠.

~~~html
{% raw %}

<html>
<head>
    <title>Vue Template - Event Handling</title>
</head>
<body>
<div id="app">
    <button v-on:click="clickBtn">클릭</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        methods: {
            clickBtn: function() {
                alert('clicked');
            }
        }
    });
</script>
</body>
</html>

{% endraw %}
~~~

`v-on` 디렉티브로 메서드를 호출할 때 다음과 같이 인자를 넘기는 방법도 있습니다. 

~~~html
{% raw %}

<html>
  <head>
    <title>Vue Template - Event Handling</title>
  </head>
  <body>
    <div id="app">
      <button v-on:click="clickBtn(10)">클릭</button>
		</div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      new Vue({
        el: '#app',
        methods: {
          clickBtn: function(num) {
            alert('clicked ' + num + ' times');
          }
        }
      });
    </script>
  </body>
</html>

{% endraw %}
~~~

마지막으로 이벤트 객체에 접근하는 것도 한번 해보죠.

~~~html
{% raw %}

<html>
  <head>
    <title>Vue Template - Event Handling</title>
  </head>
  <body>
    <div id="app">
      <button v-on:click="clickBtn">클릭</button>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      new Vue({
        el: '#app',
        methods: {
          clickBtn: function(event) {
            console.log(event);
          }
        }
      });
    </script>
  </body>
</html>

{% endraw %}
~~~

### 고급 템플릿 기법

computed 속성에 대해서 알아보겠습니다. 

데이터를 가동하는등의 복잡한 연산은 뷰 인스턴스 안에서 수행하고 최종적으로 HTML에는 데이터를 표현만 해야 한다고 했죠.
computed 속성은 이러한 데이터 연산들을 정의하는 영역입니다. 아까 앞에서 살펴보았습니다. 

이 방식의 장점은 data 속성 값의 변화에 따라 자동으로 다시 연산한다는 점이고 두번째는 캐싱입니다. 한번 연산을 수행한 후
동일한 연산을 반복적으로 하지 않기 위해 결과값을 캐싱하고 있다가 필요할 때 불러와서 사용하게 됩니다. 

이런 점에서는 methods와 비교된다고 할 수 있습니다. methods 속성은 호출할 때마다 해당 로직이 수행되기 때문이죠. 
computed 속성은 대상 데이터의 값이 변경되면 자동적으로 수행된다는 점도 차이라고 볼 수 있습니다. 즉, 수동적으로 데이터를
갱신하느냐 아니면 능동적으로 데이터를 갱신하느냐의 차이라고 보시면 됩니다. 

~~~html
{% raw %}

<html>
<head>
    <title>Vue Template - Computed vs Methods</title>
</head>
<body>
<div id="app">
    <p>{{ message }}</p>
    <button v-on:click="reverseMsg">문자열 역순</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue.js!'
        },
        methods: {
            reverseMsg: function() {
                this.message = this.message.split('').reverse().join('');
                console.log('123');
                return this.message;
            }
        }
    });
</script>
</body>
</html>

{% endraw %}
~~~

위의 코드와 다음코드를 비교해서 보시죠.

~~~html
{% raw %}

<html>
<head>
    <title>Vue Template - Javascript Expression</title>
</head>
<body>
<div id="app">
    {{ reversedMessage }} <!-- O, 스크립트에서 computed 속성으로 계산 후 최종 값만 표현 -->
    {{ reversedMessage }} <!-- O, 스크립트에서 computed 속성으로 계산 후 최종 값만 표현 -->
    {{ reversedMessage }} <!-- O, 스크립트에서 computed 속성으로 계산 후 최종 값만 표현 -->
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue.js!'
        },
        computed: {
            reversedMessage: function() {
                console.log('123');
                return this.message.split('').reverse().join('');
            }
        }
    });
</script>
</body>
</html>

{% endraw %}
~~~

watch 속성도 알아보죠. watch 속성은 데이터의 변화를 감지하여 자동으로 특정 로직을 수행합니다. computed 속성과 유사하지만
일반적으로 computed 속성은 내장 API를 활용한 간단한 연산정도에 적합한 반면, watch 속성은 데이터 호출과 같이 시간이 상대적으로
더 많이 소요되는 비동기 처리에 적합합니다. 

~~~html
{% raw %}

<html>
  <head>
    <title>Vue Template - Watch</title>
  </head>
  <body>
    <div id="app">
      <input v-model="message">
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        },
        watch: {
          message: function(data) {
            console.log("message의 값이 바뀝니다 : ", data);
          }
        }
      });
    </script>
  </body>
</html>

{% endraw %}
~~~

위의 코드는 간단한 입력 상자에 입력값을 v-model 디렉티브로 연결하여 입력 값에 변화가 있을 때마다 watch 속성에서
변화된 값을 로그로 출력하는 예제입니다. 

다음은 싱글 파일 컴포넌트에 대해서 알아보고 to-do-list를 만드는 예제로 넘어가도록 하죠.

End.

{% include links.html %}
