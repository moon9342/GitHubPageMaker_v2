---
title: Vue Environment
sidebar: vue_sidebar
summary: ""
permalink: vue_environment.html
folder: vue
---

## 환경설정
<br>

Vue개발을 위해서 다음과 같은 도구들이 필요합니다. 
<br><br>

1. `Chrome Browser`
2. `Code Editor`
3. `Node.js`
4. `Vue 개발자 도구`(크롬 플러그인 - vue devtools)
<br><br>

1번은 기본으로 준비되어 있고 2번 코드에디터는 `Visual Studio Code`를 이용합니다. 
<br><br>

`Node.js`는 서버측에서 실행되는 자바스크립트 실행 환경을 의미합니다. 나중에 Vue 프로젝트 구성을 할 때 
`Vue CLI(Command Line Interpreter)`를 이용하여 쉽게 Vue 프로젝트를 구성할 수 있는데 이때 Vue CLI를
사용하기 위해서 Node.js가 필요합니다.

---

## Vue Hello World!
<br>

1. HTML 파일 생성
2. Vue 소스코드 추가
3. 브라우져 실행

~~~html

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
        }
    });
</script>
</body>
</html>
{% endraw %}

~~~

실행시켜 결과를 확인해보죠.



End.

{% include links.html %}
