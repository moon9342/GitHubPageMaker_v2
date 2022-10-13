---
title: Vue to-do app
sidebar: vue_sidebar
summary: ""
permalink: vue_to-do-app.html
folder: vue
---

## to-do app 소개

일반적으로 Front-End Framework를 배울 때 가장 먼저 작성하는 프로젝트는 to-do app입니다. 이 앱을 구현하는 이유는
적은 양의 코드, 컴포넌트 구조화 및 컴포넌트 통신, 기본적인 CRUD가 다 들어있기 때문입니다.
<br><br>

우리 앱은 데이터를 서버측의 Database에 저장하지 않고 browser의 저장소인 `localStorage`를 이용하도록 하겠습니다.
<br><br>

다음 그림은 우리 앱의 컴포넌트 구조도입니다. 

{% include image.html
file='vue/vue48.png'
%}

---

## vue CLI를 이용한 프로젝트 생성

프로젝트 초기 구성을 위해 뷰 CLI로 프로젝트를 생성합니다. 원하는 폴더 위치에 새로운 폴더 `vue-todo`를 생성하고 해당 폴더
내에서 `cmd`창을 열어 `vue init webpack-simple`을 입력하고 실행합니다. 그 후 `npm install`을 실행해 필요한 module을
설치합니다. 

---

## 프로젝트 초기 설정

우리 프로젝트의 시작점인 `index.html`에 반응형 웹 디자인 태그를 설정합니다. 반응형 웹 디자인은 하나의 웹 사이트로 PC, mobile, 
tablet등 어느기기에서도 깨지지 않는 자연스러운 레이아웃을 제공하는 웹 디지안 방식입니다. 
<br><br>

우리예제는 모바일 환경을 기준으로 설계할 예정이기 때문에 그에 맞게 설정합니다. 
<br><br>

앱에서 사용할 아이콘을 위해 `Awesome Icon`을 이용합니다. `Awesome Icon`은 구글의 `Material Icon`보다 더 많은 종류를 제공하며
대중적으로 가장 많이 사용하는 아이콘 CSS입니다. 하지만 AJAX CORS 에러가 발생해서 이 부분은 생략하도록 하겠습니다.  
<br><br>

`font awesome cdn`을 검색해서 CDN을 추가합니다. (생략)
<br><br>

사용할 폰트까지 지정해보죠. 사용할 폰트는 구글 폰트 중 `ubuntu` 폰트를 사용해보죠. 이 역시 `google font ubunt cdn`를 검색해서
CDN을 찾아 링크를 복사해 넣어줍니다. 

수정된 index.html은 다음과 같습니다. 

~~~html
{% raw %}

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
<!--    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.2.0/css/all.min.css">-->
    <link href='https://fonts.googleapis.com/css?family=Ubuntu' rel='stylesheet'>
    <title>vue-todo</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="/dist/build.js"></script>
  </body>
</html>

{% endraw %}
~~~

## 컴포넌트 생성과 등록

프로젝트 초기 설정을 했으니 이제 필요한 컴포넌트를 생성하고 등록해보죠. 대상 component는 `TodoHeader`, `TodoInput`,
`TodoList`, `TodoFooter` 총 4개 입니다. 
<br><br>

프로젝트 폴더 src 폴더 밑에 components 폴더를 생성하고 그 아래에 `TodoHeader.vue`, `TodoInput.vue`,
`TodoList.vue`, `TodoFooter.vue` 파일을 생성합니다. 
<br><br>

컴포넌트는 관례상 `src/components` 폴더에서 관리합니다. 그리고 많은 component를 관리해야 할 경우 계층 구조로 관리할 수 있습니다.
<br><br>

작성된 파일은 다음과 같습니다. 모두 같은 형식이고 `<div>`영역의 내용만 수정합니다.  
~~~vue
{% raw %}

<template>
    <div>Header</div>
</template>

<script>
export default {

}
</script>

<style>

</style>

{% endraw %}

~~~

파일 4개를 생성했으면 이제 등록을 해보죠. 어플리케이션에서 사용할 component는 모두 최상위 component인 `App.vue`에 등록합니다.
<br><br>

기본 코드를 모두 지우고 다음과 같이 입력합니다. 

~~~vue

<template>
  <div id="app">

  </div>
</template>

<script>
export default {

}
</script>

<style>

</style>


~~~

컴포넌트 등록방법 생각나시죠? local component로 등록합니다. 그럼 아래와 같은 코드가 될 것입니다. 그런데 주의해야 할 점이 있습니다.
각 파일로 나누어져 있기 때문에(내부적으로는 모듈로 처리됩니다.) `import` 구문이 반드시 있어야 사용할 수 있습니다. 
<br><br>

또한 컴포넌트 태그 4개를 추가하는 내용이 들어와야 합니다.

~~~vue

{% raw %}
<template>
  <div id="app">
    <TodoHeader></TodoHeader>
    <TodoInput></TodoInput>
    <TodoList></TodoList>
    <TodoFooter></TodoFooter>
  </div>
</template>

<script>
import TodoHeader from './components/TodoHeader.vue'
import TodoInput from './components/TodoInput.vue'
import TodoList from './components/TodoList.vue'
import TodoFooter from './components/TodoFooter.vue'

export default {
  components: {
    'TodoHeader': TodoHeader,
    'TodoInput': TodoInput,
    'TodoList': TodoList,
    'TodoFooter': TodoFooter
  }

}
</script>

<style>

</style>
{% endraw %}

~~~

---

## component의 내용 구현

제일 먼저 스타일링을 위해 `App.vue`의 css부터 손보도록 하죠.

~~~css

body {
    text-align: center;
    background-color: #F6F6F8;
}

input {
    border-style: groove;
    width: 200px;
}

button {
    border-style: groove;
}

.shadow {
    box-shadow: 5px 10px 10px rgba(0,0,0, 0.3);
}

~~~

다음은 `TodoHeader.vue`의 내용입니다.

~~~vue
{% raw %}

<template>
  <header>
    <h1>My TODO App</h1>
  </header>
</template>

<script>
export default {

}
</script>

<style>

h1 {
  color: #2F3B52;
  font-weight: 900;         /* 텍스트 굵기 */
  margin: 2.5rem 0 1.5rem
}
</style>

{% endraw %}
~~~

다음은 `TodoInput.vue`의 내용입니다.

~~~vue
{% raw %}

<template>
  <div>
    <input type="text" v-model="newTodoItem" placeholder="할 일을 입력하세요"
           v-on:keyup.enter="addTodo">
    <button v-on:click="addTodo">추가</button>

  </div>
</template>

<script>
export default {
  data() {
    return {
      newTodoItem: ''
    }
  },
  methods: {
    addTodo() {
      console.log(this.newTodoItem);  // 확인용

      // 만약 입력된 내용이 없다면 저장하지 않는다.
      if(this.newTodoItem !== '') {
        var value = this.newTodoItem.trim();
        localStorage.setItem(value, value);
        this.newTodoItem = '';
      }
      // localStorage확인은 chrome에서 확인!
    }
  }
}
</script>

<style>

</style>

{% endraw %}

~~~

다음은 `TodoList.vue`의 내용입니다.

~~~vue
{% raw %}

<template>
  <section>
    <ul>
      <li v-for="todoItem in todoItems">{{ todoItem }}<button v-on:click="removeTodo(todoItem)">삭제</button></li>
    </ul>
  </section>
</template>

<script>
export default {
  data() {
    return {
      todoItems: []
    }
  },
  created() {
    if(localStorage.length > 0) {
      for(var i=0; i< localStorage.length; i++) {
        if(localStorage.key(i) !== 'loglevel:webpack-dev-server') {
          this.todoItems.push(localStorage.key(i))
        }
      }
    }
  },
  methods: {
    removeTodo(todoItem) {
      localStorage.removeItem(todoItem);
    }
  }
}
</script>

<style>

</style>

{% endraw %}
~~~

다음은 `TodoFooter.vue`의 내용입니다.

~~~vue
{% raw %}

<template>
  <div>
    <button v-on:click="clearTodo">모두 삭제</button>
  </div>
</template>

<script>
export default {
  methods: {
    clearTodo() {
      localStorage.clear();
    }
  }
}
</script>

<style>

</style>

{% endraw %}
~~~

여기까지 구현하면 단위기능은 잘 동작하나 화면이 자동으로 갱신되지 않는 문제점이 있습니다. 그림으로 보면
다음과 같습니다. 

{% include image.html
file='vue/vue49.png'
%}
<br>

이 문제를 해결하려면 다음과 같은 그림이 되어야 할 것입니다 .

{% include image.html
file='vue/vue50.png'
%}
<br>

코드를 작성해 보죠.
<br><br>

먼저 `App.vue`입니다.

~~~vue
{% raw %}

<template>
  <div id="app">
    <TodoHeader></TodoHeader>
    <TodoInput v-on:addTodo="addTodo"></TodoInput>
    <TodoList v-bind:propsdata="todoItems" v-on:removeSelected="removeSelected"></TodoList>
    <TodoFooter v-on:removeAll="clearAll"></TodoFooter>
  </div>
</template>

<script>
import TodoHeader from './components/TodoHeader.vue'
import TodoInput from './components/TodoInput.vue'
import TodoList from './components/TodoList.vue'
import TodoFooter from './components/TodoFooter.vue'

export default {
  data() {
    return {
      todoItems: []
    }
  },
  methods: {
    addTodo(value) {
      localStorage.setItem(value, value);
      this.todoItems.push(value)
    },
    clearAll() {
      localStorage.clear();
      this.todoItems = [];
    },
    removeSelected(todoItem) {
      console.log(todoItem);
      localStorage.removeItem(todoItem);
      delete this.todoItems[this.todoItems.indexOf(todoItem)]; // 반응성에 문제가 있다. 해결해야됨!
    }
  },
  components: {
    'TodoHeader': TodoHeader,
    'TodoInput': TodoInput,
    'TodoList': TodoList,
    'TodoFooter': TodoFooter
  },
  created() {
    if(localStorage.length > 0) {
      for(var i=0; i< localStorage.length; i++) {
        if(localStorage.key(i) !== 'loglevel:webpack-dev-server') {
          this.todoItems.push(localStorage.key(i))
        }
      }
    }
  },
}
</script>

<style>

body {
  text-align: center;
  background-color: #F6F6F8;
}

input {
  border-style: groove;
  width: 200px;
}

button {
  border-style: groove;
}

.shadow {
  box-shadow: 5px 10px 10px rgba(0,0,0, 0.3);
}


</style>


{% endraw %}
~~~

다음은 `TodoInput.vue`입니다.

~~~vue
{% raw %}

<template>
  <div>
    <input type="text" v-model="newTodoItem" placeholder="할 일을 입력하세요"
           v-on:keyup.enter="addTodo">
    <button v-on:click="addTodo">추가</button>

  </div>
</template>

<script>
export default {
  data() {
    return {
      newTodoItem: ''
    }
  },
  methods: {
    addTodo() {
      console.log(this.newTodoItem);  // 확인용

      // 만약 입력된 내용이 없다면 저장하지 않는다.
      if(this.newTodoItem !== '') {
        var value = this.newTodoItem.trim();
        // localStorage.setItem(value, value);
        this.$emit('addTodo', value);
        this.newTodoItem = '';
      }
      // localStorage확인은 chrome에서 확인!
    }
  }
}
</script>

<style>

</style>

{% endraw %}
~~~

다음은 `TodoFooter.vue`입니다.

~~~vue

<template>
    <div>
        <button v-on:click="clearTodo">모두 삭제</button>
    </div>    
</template>

<script>
export default {
    methods: {
        clearTodo() {
            // localStorage.clear();
            this.$emit('removeAll');
        }
    }
}
</script>

<style>

</style>

~~~

End.

{% include links.html %}
