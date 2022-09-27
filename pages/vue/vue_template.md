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

이 template 속성을 이용하는 방법은 두가지인데 하나는 ES5 형태로 Vue 인스턴스의 template 속성을 사용하는 것입니다. 

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
우리는 이전에 `props`를 사용하기 위해 이 속성을 이용했었습니다. 기억나시죠?

다음코드를 보고 이해해보죠.

~~~html
{% raw %}

<html>
<head>
    <title>Vue Template - Data Binding</title>
    <style>
        .testStyle {
            background-color: yellow;
        }
    </style>
</head>
<body>
<div id="app">
    <p v-bind:id="myID">아이디 바인딩</p>
    <p v-bind:class="myStyle">클래스 바인딩</p>
    <p :class="myStyle">이렇게 축약해도 됩니다.</p>
    <!-- <p id="{{ testID }}">이렇게 해도 바인딩 될까요?</p> -->
    <p>이건 바인딩 될까요?? {{ testID }}</p>
    <child-global-component v-bind:mypropsglobal="globalParentMessage"></child-global-component>
    <child-local-component :mypropslocal="localParentMessage"></child-local-component>

</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    Vue.component('child-global-component', {
        props: ['mypropsglobal'],
        template: '<div>global component의 상위 component의 data : {{ mypropsglobal }}</div>'
    });

    new Vue({
        el: '#app',
        data: {
            myID: 10,
            myStyle: 'testStyle',
            testID: 'testID',
            globalParentMessage: '유치환 - 깃발',
            localParentMessage: '이것은 소리없는 아우성!'

        },
        components: {
            'child-local-component' : {
                props: ['mypropslocal'],
                template: `<div>local component의 상위 component의 data : {{ mypropslocal }}</div>`
            }
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
    <child-component></child-component>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: '이것은 소리없는 아우성!'
        },
        components: {
            'child-component' : {
                // component의 data 속성은 함수!! 하나의 값만 리턴!
                // data: {
                //   childMessage: '하위 component입니다.'
                // },
                data: function() {
                    return {
                        childMessage1: '하위 컴포넌트 1번 입니다.',
                        childMessage2: '하위 컴포넌트 2번 입니다.',
                    }
                },
                template: '<div>{{ childMessage2 }}</div>'
            }
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

    <!-- X, 선언문은 사용 불가능 -->
    <!-- <p>{{ var a = 10; }}</p>  -->

    <!-- X, 분기 구문은 사용 불가능 -->
    <!-- <p>{{ if (true) {return 100} }}</p>  -->

    <!-- O, 삼항 연산자로 표현 가능 -->
    <p>{{ true ? 100 : 0 }}</p>

    <!-- X, 복잡한 연산은 인스턴스 안에서 수행 -->
    <p>{{ message.split('').reverse().join('') }}</p>

    <!-- 복잡한 연산은 함수화시켜서 수행 => methods로 만들경우. -->
    <p>{{ methodsReversedMessage() }}</p>
    <p>{{ methodsReversedMessage() }}</p>

    <!-- 복잡한 연산은 함수화시켜서 수행 => computed로 만들경우. -->
    <p>{{ computedReversedMessage }}</p>
    <p>{{ computedReversedMessage }}</p>

</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: '소리없는 아우성!'
        },
        methods: {
            methodsReversedMessage: function() {
                console.log('일반 method가 호출되었습니다.!');
                return this.message.split('').reverse().join('');
            }
        },
        computed: {
            computedReversedMessage: function() {
                console.log('computed method가 호출되었습니다.!');
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

데이터를 가동하는등의 복잡한 연산은 뷰 인스턴스 안에서 수행하고 최종적으로 HTML에는 데이터를 표현만 해야 한다고 했죠.
computed 속성은 이러한 데이터 연산들을 정의하는 영역입니다. 

이 방식의 장점은 data 속성 값의 변화에 따라 자동으로 다시 연산한다는 점이고 두번째는 캐싱입니다. 한번 연산을 수행한 후
동일한 연산을 반복적으로 하지 않기 위해 결과값을 캐싱하고 있다가 필요할 때 불러와서 사용하게 됩니다.

이런 점에서는 methods와 비교된다고 할 수 있습니다. methods 속성은 호출할 때마다 해당 로직이 수행되기 때문이죠.
computed 속성은 대상 데이터의 값이 변경되면 자동적으로 수행된다는 점도 차이라고 볼 수 있습니다. 즉, 수동적으로 데이터를
갱신하느냐 아니면 능동적으로 데이터를 갱신하느냐의 차이라고 보시면 됩니다.


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
    <a v-if="flag">만약 false이면 DOM에서 제거됩니다.</a>
    <ul>
        <li v-for="subject in subjects">{{ subject }}</li>
    </ul>

    <table>
        <thead>
        <tr>
            <th scope="col">번호</th>
            <th scope="col">이름</th>
            <th scope="col">이메일</th>
            <th scope="col">전화번호</th>
            <th scope="col">주소</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="user in users">
            <td>{{user.no}}</td>
            <td>{{user.name}}</td>
            <td>{{user.email}}</td>
            <td>{{user.mobile}}</td>
            <td>{{user.address}}</td>
        </tr>
        </tbody>
    </table>


    <p v-show="flag">shop는 css를 이용한 숨김설정이 가능합니다.</p>

    <h2 v-bind:id="uid">이건 이전에 살펴봤죠!!</h2>

    <button v-on:click="showAlert">상위 컴포넌트로 이벤트 전달</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            flag: true,
            subjects: ['국어', '영어', '수학'],
            users: [
                {
                    no: '1',
                    name: '홍길동',
                    email: 'hong@gmail.com',
                    mobile: '010-1234-5678',
                    address: '서울'
                },
                {
                    no: '2',
                    name: '김길동',
                    email: 'kim@gmail.com',
                    mobile: '010-1111-2222',
                    address: '인천'
                },
                {
                    no: '3',
                    name: '박길동',
                    email: 'park@gmail.com',
                    mobile: '010-2222-2222',
                    address: '부산'
                },
            ],
            uid: 10
        },
        methods: {
            showAlert: function() {
                return alert('버튼이 클릭되었습니다.!!');
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

`v-on` 디렉티브로 메서드를 호출할 때 다음과 같이 입력양식과 같이 처리하는 방법도 있습니다. 

~~~html
{% raw %}

<html>
<head>
    <title>Vue Template - Event Handling</title>
</head>
<body>
<div id="app">
    <input v-model="clickCount">
    <button v-on:click="clickBtn">클릭</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            clickCount: 10
        },
        methods: {
            clickBtn: function() {
                alert('입력값은 ' + this.clickCount + ' 입니다.');
            }
        },
        watch: {
            clickCount: function(data) {
                console.log(`입력값이 ${data}로 변경되었습니다.`);
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
    <button v-bind:id='btn1' v-on:click="clickBtn">클릭1</button>
    <button v-bind:id='btn2' v-on:click="clickBtn">클릭2</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            btn1: 'button1',
            btn2: 'button2',
        },
        methods: {
            clickBtn: function(event) {
                // evnet객체에 접근! => event 소스를 확인할 수 있다.
                console.log(event);
                if(event.target == document.getElementById('button1'))
                    alert('버튼1 클릭되었습니다.')
                else if (event.target == document.getElementById('button2'))
                    alert('버튼2 클릭되었습니다.')
                else
                    alert('처리안됨!')
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
