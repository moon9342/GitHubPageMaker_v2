---
title: Vue Instance
sidebar: vue_sidebar
summary: ""
permalink: vue_instance.html
folder: vue
---

## Vue Instance
<br>

`Vue Instance`는 Vue로 화면을 개발하기 위해 필수적으로 생성해야 하는 기본단위입니다. 
<br><br>

이전 예제에서 화면에 Hello World가 출력 될 수 있었던 것은 Instance가 있었기 때문에 가능했던 것이죠.
<br><br>

Vue로 instance를 생성하기 위해서는 다음과 같은 형식을 사용합니다. 

{% include image.html
file='vue/vue4.png'
%}
<br>

이전코드를 다시 한번 살펴보죠.

{% include image.html
file='vue/vue5.png'
%}
<br>

먼저 Vue 인스턴스를 생성했습니다. 
<br><br>

그리고 `el` 속성으로 Vue 인스턴스가 그려질 지점을 지정하고 `data` 속성의 message 값을 정의하여 화면의
`{%raw%}{{ message }}{%endraw%}`에 연결했습니다. 
<br>

### Vue instance 생성자
<br>

`new Vue()`로 인스턴스를 생성할 때 Vue를 생성자라고 합니다. 정확히는 생성자 함수죠. Vue 생성자는 Vue 라이브러리를
로딩한 후 사용할 수 있습니다. 생성자를 사용하는 이유는 Vue로 개발할 때 필요한 기능들을 생성자에 미리 정의해 놓고
사용자가 기능을 재 정의해 편리하게 사용하기 위함입니다. 
<br><br>

`Vue instance 옵션` 속성은 인스턴스를 생성할 때 재정의할 `data`, `el`, `template`등의 속성을 의미합니다.
<br><br>

우리는 `data`라는 미리 정의되어 있는 속성을 사용해서 그 안에 message 속성을 추가하고 값을 hello world라는 값을
부여했습니다. 
<br><br>

`el` 속성 역시 마찬가지로 미리 정의되어 있는 속성으로 Vue로 만든 화면이 그려지는 시작점을 의미합니다. 즉, `Vue instance`로
화면을 `rendering`할 때 화면이 그려질 위치의 `DOM 요소`를 지정해 주어야 하며 일반적으로 `CSS selector`를 이용하여 요소를 선택합니다.
<br><br>

우리가 사용하지는 않았지만 다음과 같은 속성도 있습니다. 
<br><br>

`template` : 화면에 표시할 HTML, CSS등의 마크업 요소를 정의하는 속성입니다. 
<br><br>

`method` : 화면 로직 제어와 관계된 메서드를 정의하는 속성입니다. 클릭 이벤트 처리와 같은 화면의 전반적인 이벤트 처리와
화면 동작과 관련된 로직을 정의합니다.
<br><br>

`created` : Vue instance가 생성되자마자 실행할 로직을 정의할 수 있는 속성입니다. `Vue instance life cycle` 부분에서
다시 알아보도록 하겠습니다. 

---

## Vue instance의 유효범위
<br>

Vue instance를 생성하면 HTML의 특정 범위 안에서만 옵션 속성들이 적용됩니다. 당연하겠죠. 이를 `instance의 유효범위`라고 합니다. 
당연히 instance의 유효범위는 `el` 속성과 밀접한 관련이 있습니다. 
<br><br>

아래의 그림은 Vue instance가 생성된 후 화면에 어떻게 적용되는지를 나타냅니다. new Vue()로 인스턴스를 생성하고 나서 
화면에 인스턴스 옵션 속성을 적용하는 과정은 다음과 같습니다. 
<br>

{% include image.html
file='vue/vue6.png'
%}
<br>

아까의 코드를 다시 살펴보면 다음과 같습니다. 

{% include image.html
file='vue/vue7.png'
%}

{% include image.html
file='vue/vue8.png'
%}

{% include image.html
file='vue/vue9.png'
%}
<br>

이번에는 유효범위에 대해서 알아보죠.

{% include image.html
file='vue/vue10.png'
%}

---

## Vue instance 라이프 사이클
<br>

앞서 잠깐 언급했던 created 속성이 있었는데 이것은 인스턴스가 생성되었을 때 호출할 함수를 정의하는 속성입니다. 
<br><br>

이처럼 인스턴스의 상태에 따라 호출할 수 있는 속성들을 `life cyle 속성`이라고 합니다. 그리고 각 라이프 사이클 속성에서
실행되는 커스텀 로직을 우리는 `life cycle hook`(라이프 사이클 훅)이라고 합니다. 
<br><br>

라이프 사이크 속성에는 총 8개가 있습니다. 각각에 대해 잘 알아두어야 합니다. 이 속성들은 인스턴스의 생성과 변경 소멸과 
연관되어져 있기 때문이죠.
<br><br>

일단 그림으로 알아보죠.

{% include image.html
file='vue/vue11.png'
%}
<br>

위의 그림은 인스턴스가 생성되고 화면에 인스턴스가 부착된 후 소멸되기까지의 전체적인 흐름을 나타낸 Vue instance  라이프 사이클
다이어그램입니다. 

각 단계에 대해서 알아보죠.
<br>

### beforeCreate

인스턴스가 생성되고 가장 처음에 실행되는 라이프 사이클 단계입니다. 
<br><br>

이 단계에서는 data속성과 method 속성이 아직 인스턴스에 정의되어 있지 않고 DOM과 같은 화면 요소에도 접근할 수 없습니다. 
아직 부착되지도 않았기 때문이죠. 코드로 하나씩 추가하면서 실제로 호출되는지 살펴보도록 하겠습니다. 

~~~javascript

{% raw %}
<html>
  <head>
    <title>Vue Sample</title>
  </head>
  <body>
    <!-- 영역을 잡는 element -->
    <div id="app">
      {{ message }}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.10/dist/vue.js"></script>
    <script>
      // Vue 인스턴스 생성 후 
      // 인스턴스에 정의된 데이터 객체의 message property를 화면에 출력
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        },
        beforeCreate: function() {
          console.log('beforeCreate call!!');
        }
      });
    </script>
  </body>
</html>
{% endraw %}

~~~
<br>

### created

beforeCreate 라이프 사이클 단계 다음에 실행되는 단계입니다. `data`속성과 `methods`속성이 정의되었기 때문에 `this.data`와 같은
접근을 이용하여 data속성과 methods속성에 정의된 값에 접근하여 로직을 실행할 수 있습니다. 
<br><br>

다만 아직 화면에 부착되기 전이기 때문에 template속성에 정의된 DOM 요소에는 접근할 수 없습니다. 
<br><br>

그럼 이 단계에서는 어떤일을 수행할까요? data 속성과 methods 속성에 접근할 수 있는 첫번재 라이프 사이클 단계이기 때문에 
서버에 데이터를 요청해서 받아오는 로직을 수행하기에 적합합니다. `axios` 처리같은게 들어가겠죠.

~~~javascript
{% raw %}
<html>
  <head>
    <title>Vue Sample</title>
  </head>
  <body>
    <!-- 영역을 잡는 element -->
    <div id="app">
      {{ message }}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.10/dist/vue.js"></script>
    <script>
      // Vue 인스턴스 생성 후 
      // 인스턴스에 정의된 데이터 객체의 message property를 화면에 출력
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        },
        methods: {
          myFunc: function() {
            console.log('myFunc method call!!');
          }
        },
        beforeCreate: function() {
          console.log('beforeCreate call!!');
        },
        created: function() {
          console.log(this.message);
          this.myFunc();
        }
      });
    </script>
  </body>
</html>
{% endraw %}
~~~
<br>

### beforeMount

created 단계 이후 template 속성에 지정한 마크업 속성을 render() 함수로 변환한 후 el 속성에 지정한 화면 요소(돔)에 
인스턴스를 부착하기 전에 호출되는 단계입니다. 
<br><br> 

render() 함수가 호출되어 화면에 그림이 그려지기 전 수행해야 할 일이 있다면 여기에서 처리합니다. 주의해야 할 점은 아직
DOM 요소에는 접근할 수 없다는 것입니다.

~~~javascript
{% raw %}
<html>
  <head>
    <title>Vue Sample</title>
  </head>
  <body>
    <!-- 영역을 잡는 element -->
    <div id="app">
      {{ message }}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.10/dist/vue.js"></script>
    <script>
      // Vue 인스턴스 생성 후 
      // 인스턴스에 정의된 데이터 객체의 message property를 화면에 출력
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        },
        methods: {
          myFunc: function() {
            console.log('myFunc method call!!');
          }
        },
        beforeCreate: function() {
          console.log('beforeCreate call!!');
        },
        created: function() {
          console.log(this.message);
          this.myFunc();
        },
        beforeMount: function() {
          console.log('beforeMount call!!');
        }
      });
    </script>
  </body>
</html>
{% endraw %}
~~~
<br>

### mounted

`el` 속성에 지정한 화면 요소에 인스턴스가 부착되고 나면 호출되는 단계로, template 속성에 정의한 화면 요소(DOM)에 접근할
수 있습니다. 그래서 화면 요소를 제어하는 로직을 수행하기 좋은 단계입니다. 
<br><br>

단, 조심해야 할 점은 DOM에 인스턴스가 부착되자마자 바로 호출되기 때문에 하위 컴포넌트나 외부 라이브러리에 의해 추가된 화면 요소들이
최종 HTML 코드로 변환되어 DOM에 추가되는 시점과 다를 수 있습니다. 
<br><br>

이렇게 변환되는 시점이 다른 경우 `$nextTick()` API를 이용하여 HTML 코드가 최종 파싱될 때 까지 기다린 후 DOM 제어 로직을 추가할
수 있습니다.

~~~javascript
{% raw %}
<html>
  <head>
    <title>Vue Sample</title>
  </head>
  <body>
    <!-- 영역을 잡는 element -->
    <div id="app">
      {{ message }}
      <div id="myMessage"></div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.10/dist/vue.js"></script>
    <script>
      // Vue 인스턴스 생성 후 
      // 인스턴스에 정의된 데이터 객체의 message property를 화면에 출력
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        },
        methods: {
          myFunc: function() {
            console.log('myFunc method call!!');
          }
        },
        beforeCreate: function() {
          console.log('beforeCreate call!!');
        },
        created: function() {
          console.log(this.message);
          this.myFunc();
        },
        beforeMount: function() {
          console.log('beforeMount call!!');
        },
        mounted: function() {
          console.log('mounted call!!');
          const myDiv = document.getElementById('myMessage');
          myDiv.innerText = '안녕하세요!!'
        }
      });
    </script>
  </body>
</html>
{% endraw %}
~~~
<br>

### beforeUpdate

el 속성에서 지정한 화면 요소에 인스턴스가 부착되고 나면 인스턴스에 정의한 속성들이 화면에 치환됩니다. 치환된
값을 Vue의 반응성을 제공하기 위해 $watch 속성으로 감시하게 되는데 이를 데이터 관찰이라고 합니다. 
<br><br>

Vue의 반응성이란 Vue의 특징 중 하나로 코드의 변화에 따라 화면이 반사적으로 반응하여 빠르게 화면을 갱신하는 것을
의미합니다. 
<br><br>

beforeUpdate는 관찰하고 있는 데이터가 변경되면 가상 돔으로 화면을 다시 그리기 전에 호출되는 단계이며, 변경 예정인
새 데이터에 접근할 수 있어 변경 예정 데이터의 값과 관련된 로직을 수행할 수 있습니다. 여기에 값을 변경하는 로직을
넣더라도 화면이 다시 그려지지 않는다는 것은 주의해야 합니다. 

~~~javascript
{% raw %}
<html>
    <head>
        <title>Vue Sample</title>
    </head>
    <body>
        <!-- 영역을 잡는 element -->
        <div id="app">
            {{ message }}
            <div id="myMessage"></div>
            <button v-on:click=myClick>클릭하세요!!</button>
        </div>

        <script src="https://cdn.jsdelivr.net/npm/vue@2.7.10/dist/vue.js"></script>
        <script>
            // Vue 인스턴스 생성 후
            // 인스턴스에 정의된 데이터 객체의 message property를 화면에 출력
            new Vue({
                el: '#app',
                data: {
                    message: 'Hello Vue.js!'
                },
                methods: {
                    myFunc: function() {
                        console.log('myFunc method call!!');
                    },
                    myClick: function() {
                        console.log('btn click!!');
            
                        const myDiv = document.getElementById('myMessage');
                        myDiv.innerText = '버튼이 클릭되었어요!'  // 이런 경우는 beforeUpdate가 호출안된다.
            
                        this.message = '버튼 클릭'  // 데이터가 바뀌었기 때문에 beforeUpdate 호출
                    }
                },
                beforeCreate: function() {
                    console.log('beforeCreate call!!');
                },
                created: function() {
                    console.log(this.message);
                    this.myFunc();
                },
                beforeMount: function() {
                    console.log('beforeMount call!!');
                },
                mounted: function() {
                    console.log('mounted call!!');
                    const myDiv = document.getElementById('myMessage');
                    myDiv.innerText = '안녕하세요!!'
                    this.message = '깔깔깔!!'
                },
                // watch: {
                //   message: function (newVal, oldVal) {
                //     console.log(`${oldVal}이 ${newVal}로 변경됩니다.`);
                //   }
                // },
                beforeUpdate: function() {
                    console.log('beforeUpdate call!!');
                    console.log(`변경된 message값은 ${this.message}입니다.`);
                }
            });
        </script>
    </body>
</html>
{% endraw %}
~~~
<br>

### updated

데이터가 변경되고 나서 가상 돔으로 화면을 그리고 나면 실행되는 단계입니다. 데이터 변경으로 화면 요소 변경까지
완료된 시점이므로, 데이터 변경 후 화면 요소 제어와 관련된 로직을 처리하기 좋은 단계입니다. 
<br><br>

조심해야 할 점은 이 단계에서 데이터 값을 변경하면 무한 루프에 빠질 수 있습니다. 따라서 데이터 값을 갱신하는 코드는 
beforeUpdate에 추가하고 updated에서는 변경된 화면요소와 관련된 DOM 관련 로직만 처리하는것이 좋습니다. 

~~~javascript
{% raw %}
<html>
  <head>
    <title>Vue Sample</title>
  </head>
  <body>
    <!-- 영역을 잡는 element -->
    <div id="app">
      {{ message }}
      <div id="myMessage"></div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.7.10/dist/vue.js"></script>
    <script>
      // Vue 인스턴스 생성 후 
      // 인스턴스에 정의된 데이터 객체의 message property를 화면에 출력
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        },
        methods: {
          myFunc: function() {
            console.log('myFunc method call!!');
          }
        },
        beforeCreate: function() {
          console.log('beforeCreate call!!');
        },
        created: function() {
          console.log(this.message);
          this.myFunc();
        },
        beforeMount: function() {
          console.log('beforeMount call!!');
        },
        mounted: function() {
          console.log('mounted call!!');
          const myDiv = document.getElementById('myMessage');
          myDiv.innerText = '안녕하세요!!'
          this.message = '깔깔깔!!'
        },
        watch: {
          message: function (newVal, oldVal) {
            console.log(`${oldVal}이 ${newVal}로 변경됩니다.`);
          }
        },
        beforeUpdate: function() {
          console.log('beforeUpdate call!!');
          console.log(`변경된 message값은 ${this.message}입니다.`);
        },
        updated: function() {
          console.log('updated call!!');
        }
      });
    </script>
  </body>
</html>
{% endraw %}
~~~
<br>

### beforeDestroy

Vue instance가 파괴되기 직전에 호출되는 단계입니다. 이 단계에서는 아직 인스턴스에 접근할 수 있습니다. 따라서 Vue 인스턴스의
데이터를 삭제하기 좋습니다. 
<br>

### destroyed

Vue instance가 완전히 파괴되고 나서 호출되는 단계입니다. Vue instance의 모든 속성이 제거되고 하위에 선언한 인스턴스들 역시
모두 파괴됩니다. 


End.

{% include links.html %}
