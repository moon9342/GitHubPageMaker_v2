---
title: Vue Component Communication
sidebar: vue_sidebar
summary: ""
permalink: vue_component-comm.html
folder: vue
---

## Vue Component 통신

앵귤러 1과 같은 초창기 프레임워크에서는 한 화면을 1개의 View로 간주했습니다. 따라서 한 화면의 데이터를
해당 화면 영역 어디에서나 사용할 수 있었습니다. 

하지만 Vue의 경우 component로 화면을 구성하기 때문에 같은 웹 페이지라도 데이터를 공유할 수 없습니다. 

그 이유는 component 자체적으로 고유한 scope를 가지기 때문입니다. 따라서 각 컴포넌트의 유효 범위가 독립적이기
때문에 다른 컴포넌트의 값을 직접적으로 참조할 수 없습니다. 

예를 들어보죠.

~~~javascript

{% raw %}

<html>
    <head>
        <title>Vue Component Scope</title>
    </head>
    <body>
        <div id="app">
            <my-component1></my-component1>
            <my-component2></my-component2>
        </div>

        <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
        <script>
            // 첫 번째 컴포넌트 내용
            var cmp1 = {
                template: '<div>첫 번째 지역 컴포넌트 : {{ this.cmp1Data }}</div>',
                // 컴포넌트의 데이터는 함수를 이용해야 한다.
                data: function() {
                    return {
                        cmp1Data : 100,
                        myValue1 : 200
                    }
                }
            };

            // 두 번째 컴포넌트 내용
            var cmp2 = {
                template: '<div>두 번째 지역 컴포넌트 : {{ cmp2Data }}</div>',
                data: function() {
                    return {
                    // 접근이 불가능 - scope가 다르다.
                        cmp2Data : cmp1.data.cmp1Data
                    }
                }
            };

            new Vue({
                el: '#app',
                // 지역 컴포넌트 등록
                components: {
                    'my-component1': cmp1,
                    'my-component2': cmp2
                }
            });
        </script>
    </body>
</html>

{% endraw %}

~~~

위의 예제는 하나의 지역 컴포넌트에서 다른 지역 컴포넌트가 가지고 있는 값을 직접 참조하는 예제입니다. scope가 다르기 때문에
접근이 안됩니다. 

어떻게 해야 할까요? Vue에서는 미리 정의해 놓은 데이터 전달 방식에 따라 일관된 구조로 데이터를 전달, 사용할 수 있습니다. 

## 상, 하위 컴포넌트 관계일 경우

Vue가 가지고 있는 가장 기본적인 데이터 전달 방식은 상위(부모) - 하위(자식) 컴포넌트 간의 데이터 전달 방법입니다. 

상위-하위 컴포넌트란 트리 구조에서 부모노드, 자식노드처럼 컴포넌트간의 관계가 부모, 자식으로 이루어진 컴포넌트를 의미합니다. 

우리가 앞에서 살펴본 것처럼 인스턴스가 지역 혹은 전역 컴포넌트를 등록하면 등록된 컴포넌트는 자연스럽게 인스턴스의 하위 컴포넌트가
됩니다. 당연히 상위 컴포넌트를 등록한 인스턴스는 상위 컴포넌트가 되는 것이구요.

다음의 그림은 Vue에서 상위-하위 컴포넌트간에 데이터를 전달하는 기본적인 구조를 나타냅니다. 

{% include image.html
file='vue/vue17.png'
%}

상위에서 하위로는 `props`라는 특별한 속성을 전달합니다. 그리고 하위에서 상위로는 기본적으로 이벤트만 전달할 수 있습니다. 
이 이벤트를 이용해 데이터를 전달 할 수 있습니다. 이벤트의 두번째 인자로 값을 전달하면 데이터를 전달 할 수 있습니다. 

### 상위에서 하위 컴포넌트로 데이터 전달하기

`props`는 상위 컴포넌트에서 하위 컴포넌트로 데이터를 전달할 때 사용하는 속성입니다. 

`props`를 사용하려면 `props` 속성을 먼저 다음과 같이 하위 컴포넌트의 속성에 정의합니다. 

{% include image.html
file='vue/vue18.png'
%}

그 다음에 상위 컴포넌트의 HTML 코드에 등록된 하위 컴포넌트 태그에 `v-bind` 속성을 추가합니다. 

{% include image.html
file='vue/vue19.png'
%}

`v-bind` 속성의 왼쪽 값으로 하위 컴포넌트에서 정의한 props 속성을 넣고 오른쪽 값으로 하위 컴포넌트에 전달할
상위 컴포넌트의 data 속성을 지정합니다. 다음의 코드를 보죠.

~~~javascript
{% raw %}

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue Props Sample</title>
</head>
<body>
    <div id="app">
        <!-- 팁 : 오른쪽에서 왼쪽으로 속성을 읽으면 더 수월합니다. -->
        <child-component v-bind:propsdata="message"></child-component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
        Vue.component('child-component', {
            props: ['propsdata'],
            template: '<p>{{ propsdata }}</p>',
        });

        new Vue({
            el: '#app',
            data: {
                message: 'Hello Vue! passed from Parent Component'
            }
        });
    </script>
</body>
</html>

{% endraw %}


~~~

그림으로 보면 다음과 같습니다. 

{% include image.html
file='vue/vue20.png'
%}

{% include image.html
file='vue/vue21.png'
%}

아하 그렇군요!!

여기서 한가지 짚고 넘어갈게 있습니다. 우리 코드에서는 `child-component`를 전역으로 등록한 것 이외에
딱히 상위 컴포넌트를 지정하지 않았습니다. 그럼에도 불구하고 Vue 인스턴스 안에 마치 상위 컴포넌트가 존재하는 
것처럼 하위 컴포넌트로 `props`를 내려보냈습니다. 그 이유는 component를 등록함과 동시에 Vue instance가 
상위 컴포넌트가 되기 때문입니다. 

다음 그림처럼 표현됩니다. 

{% include image.html
file='vue/vue22.png'
%}

위와 같이 새 컴포넌트를 등록한 인스턴스를 최상위 컴포넌트라고 합니다. 

### 하위에서 상위 컴포넌트로 이벤트 전달하기

이벤트 발생과 수신에 대해서 알아보겠습니다. 앞에서 배운 `props`는 상위에서 하위 컴포넌트로 데이터를 전달하는 방식입니다. 

그럼 반대로 하위 컴포넌트에서 상위 컴포넌트로의 통신은 어떻게 할까요? event를 emit시켜 상위 컴포넌트에 신호를 보내면 됩니다.

상위 컴포넌트에서는 하위 컴포넌트의 특정 이벤트가 발생하기를 기다리고 있다가 하위 컴포넌트에서 특정 이벤트가 발생하면
상위 컴포넌트에서 해당 이벤트를 수신하여 상위 컴포넌트의 메소드를 호출하는 방식을 사용합니다. 

여기서 아까 언급한 내용에 대해 첨언을 하자면 Vue는 공식적으로 event 발생 시 하위에서 상위로 데이터를 전달하는 방법을
다루지 않습니다. 이는 편리하긴 하지만 기본적인 단방향 데이터 흐름에 어긋나기 때문입니다. 따라서 이 경우에는 
Event Bus라고 불리는 다른 방식을 사용할 것입니다.

이벤트 발생과 수신 형식을 알아보죠. 이벤트 발생과 수신은 `$emit()`와 `v-on:` 속성을 사용하여 구현합니다. 

이벤트 발생과 수신 형식은 다음과 같습니다. 

{% include image.html
file='vue/vue23.png'
%}

`$emit()`를 호출하면 괄호안에 정의된 이벤트가 발생합니다. 일반적으로 `$emit()`를 호출하는 위치는 하위 컴포넌트의 특정
메소드입니다. 따라서 `$emit()`를 호출할 때 사용하는 this는 하위 컴포넌트를 가리킵니다. 

호출한 이벤트는 하위 컴포넌트를 등록하는 태그(즉, 상위 컴포넌트의 template 속성에 위치)에서 `v-on:`으로 받습니다. 

하위 component에서 발생한 이벤트명을 `v-on:` 속성에 지정하고, 속성의 값에 이벤트가 발생했을 때 호출될 상위 컴포넌트의
메소드를 지정합니다. 

코드로 알아보죠.

~~~html
{% raw %}

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue Event Emit Sample</title>
</head>
<body>
<div id="app">
    <child-component v-on:show-log="printText"></child-component>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script>
    Vue.component('child-component', {
        template: '<button v-on:click="showLog">show</button>',
        methods: {
            showLog: function() {
                this.$emit('show-log');
            }
        }
    });

    new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue! passed from Parent Component'
        },
        methods: {
            printText: function() {
                console.log("received an event");
            }
        }
    });
</script>
</body>
</html>

{% endraw %}
~~~

그림으로 살펴보면 다음과 같습니다. 

{% include image.html
file='vue/vue24.png'
%}

### 같은 레벨의 컴포넌트 간 통신

지금까지는 상위에서 하위로 props를 전달하고 하위에서 상위로 이벤트를 전달하는 내용에 대해서 알아봤습니다. 이번에는 상위-하위
관계가 아니라 같은 레벨에 있는 컴포넌트끼리 어떻게 통신하는지를 알아보죠.

그림으로 표현하면 다음과 같습니다. 

{% include image.html
file='vue/vue25.png'
%}

위의 그림은 상위 컴포넌트를 가지는 2개의 하위 컴포넌트를 나타냅니다. Vue는 상위에서 하위로만 데이터를 전달해야 하는 기본적인
통신 규칙을 따르기 때문에 바로 옆 컴포넌트에 값을 전달하려면 하위에서 공통 상위 컴포넌트로 이벤트를 전달한 후 공통 상위 
컴포넌트에서 2개의 하위 컴포넌트에 `props`를 내려 보내야 합니다. 

다른 컴포넌트간의 값을 직접적으로 참조하지 못하기 때문에 발생하는 현상입니다. 하지만 이런 통신 구조를 유지하다보면
상위 컴포넌트가 필요없음에도 통신을 위해 강제로 상위 컴포넌트를 만드는 등의 문제가 발생할 수 있습니다. 이를 해결해야 하는데
이를 해결할 수 있는 방법이 바로 `Event Bus` 입니다. 

## Event Bus

`Event Bus`는 개발자가 지정한 2개의 컴포넌트간에 데이터를 주고 받을 수 있는 방법입니다. 앞에서 배운 컴포넌트 통신은
항상 상위-하위 구조를 유지해야만 데이터를 주고 받을 수 있었는데 이벤트 버스를 이용하면 이런 구조를 유지하지 않아도 됩니다. 

그림으로 보면 다음과 같습니다. 

{% include image.html
file='vue/vue26.png'
%}

위의 그림에서 알 수 있듯이 웹앱이 커지고 컴포넌트가 많아지게 되면 기본 통신방식은 매우 번거롭게됩니다. 이런경우 Event Bus가
유용하게 사용될 수 있는거죠.

### Event 버스 형식

{% include image.html
file='vue/vue27.png'
%}

이벤트 버스를 구현하려면 어플리케이션 로직을 담은 인스턴스와는 별개의 인스턴스를 하나 더 생성해야 합니다. 

그리고 이 인스턴스를 이용하여 이벤트를 보내고 받게됩니다. 이벤트를 보내는 컴포넌트에서는 `.$emit()`를 이용하고
이벤트를 받는 컴포넌트에서는 `.$on()`을 구현합니다. 

실제 구현된 내용을 가지고 이해해보도록 하겠습니다. 

~~~html
{% raw %}

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue Event Bus Sample</title>
  </head>
  <body>
    <div id="app">
      <child-component></child-component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      var eventBus = new Vue();

      Vue.component('child-component', {
        template: '<div>하위 컴포넌트 영역입니다.<button v-on:click="showLog">show</button></div>',
        methods: {
          showLog: function() {
            eventBus.$emit('triggerEventBus', 100);
          }
        }
      });

      var app = new Vue({
        el: '#app',
        created: function() {
            eventBus.$on('triggerEventBus', function(value){
                console.log("이벤트를 전달 받음. 전달 받은 값 : ", value);
            });
        }
      });
    </script>
  </body>
</html>

{% endraw %}
~~~

End.

{% include links.html %}
