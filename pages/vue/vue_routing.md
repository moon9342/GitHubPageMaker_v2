---
title: Vue Routing
sidebar: vue_sidebar
summary: ""
permalink: vue_routing.html
folder: vue
---

## Routing

이번에는 `Routing`에 대해서 알아보겠습니다. 
<br><br>

라우팅이란 웹 페이지 간의 이동방법을 지칭합니다. 라우팅은 현대 웹 앱 형태 중 하나인 `SPA`에서 주로 사용하고 있습니다. 
<br><br>

이런 라우팅을 이용하면 화면 간의 전환이 매끄러울 뿐 아니라 어플리케이션 사용자 경험을 향상 시킬 수 있습니다. 즉, 
깜빡거림과 같은 현상 없이 화면을 매끄럽게 전환시킬 수 있을 뿐아니라 더 빠르게 화면을 조작할 수 있다는 의미입니다. 
<br><br>

Vue 뿐만 아니라 React, Angular 역시 모두 `routing`을 사용합니다. 이런 라우팅을 구현하기 위해 별도의 라우팅 라이브러리
(`router.js`)를 사용할 수 도 있지만 우리는 Vue에서 제공하는 `Vue 라우터`를 이용하도록 하겠습니다. 

---

## vue router

`Vue 라우터`는 Vue에서 라우팅 기능을 구현할 수 있도록 지원하는 공식 라이브러리 입니다. Vue 라우터를 이용하여 Vue로 만든
페이지간에 자유롭게 이동할 수 있습니다. Vue 라우터를 구현할 때 사용하는 태그와 기능은 다음과 같습니다. 

{% include image.html
file='vue/vue28.png'
%}
<br>

직접 코드를 작성하여 어떻게 움직이는지 확인하도록 하겠습니다. 

~~~html
{% raw %}

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue Router Sample</title>
</head>
<body>
<div id="app">
    <h1>뷰 라우터 예제</h1>
    <p>
        <router-link to="/main">Main 컴포넌트로 이동</router-link>
        <router-link to="/login">Login 컴포넌트로 이동</router-link>
    </p>
    <router-view></router-view>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>

<!-- 2022년 2월 7일부터 Vue.js 라이브러리와 Vue Router 라이브러리의 CDN 주소는 Vue 3 기반의 라이브러리 
  코드를 들고 옵니다. 따라서, Vue 2로 학습하고 개발하시는 분들은 위와 같이 3.5.3 버전을 사용해 주세요  -->
<script src="https://unpkg.com/vue-router@3.5.3/dist/vue-router.js"></script>
<script>
    // 3. Main. Login 컴포넌트 내용 정의
    var Main = { template: '<div>main</div>' };
    var Login = { template: '<div>login</div>' };

    // 4. 각 url에 해당하는 컴포넌트 등록
    var routes = [
        { path: '/main',
            component: Main },
        { path: '/login',
            component: Login }
    ];

    // 5. 뷰 라우터 정의
    var router = new VueRouter({
        // mode: 'history',
        // 축약표현으로 routes
        routes : routes

    });

    // 6. 뷰 라우터를 인스턴스에 등록
    var app = new Vue({
        router: router
    }).$mount('#app');
</script>
</body>
</html>

{% endraw %}

~~~

{% include image.html
file='vue/vue29.png'
%}
<br>

위의 코드는 Vue 라우팅의 기본적인 사용방법으로 페이지를 전환하는 예제입니다. 
<br><br>

다음의 순서로 처리됩니다. 
<br><br>

1. 각 `<router-link>` 엘리먼트는 화면상에서 `<a>` 엘리먼트로 변경됩니다. 각 링크를 클릭하면 to에 정의된
텍스트 값이 브라우져 URL 끝에 추가됩니다. 여기서 '/'는 있어도 되고 없어도 됩니다. vue router의 기본 URL형식은
해시값(#)을 사용합니다. 만약 이 #을 없애고 싶으면 `history mode`를 사용하면 됩니다. 일단은 그냥 사용하죠.  

2. `<router-view>`는 갱신된 URL에 해당하는 화면을 보여주는 영역입니다. `<router-view>`에 나타날 화면은 `<script>`에서
component로 정의합니다.

3. Main과 Login 컴포넌트는 template 속성으로 각 컴포넌트를 구분할 수 있을 정도의 간단한 HTML 코드를 정의합니다.

4. routes 변수에는 URL값이 /main일 때 Main 컴포넌트를, /login일 때 Login 컴포넌트를 표시하도록 정의합니다.

5. router 변수에는 Vue router를 생성하고 routes를 삽입해 URL에 따라 화면이 전환 될 수 있게 정의합니다.

6. 마지막으로 vue instance를 생성하고 라우터의 정보가 담긴 router를 추가합니다. 그리고 `.$mount()`를 이용하여 el 속성과
같이 인스턴스를 화면에 부착하는 역할을 하게 합니다. 사실 vue router의 공식문서는 el 속성을 사용하지 않고 라우터만 지정하고
생성된 인스턴스를 `.$mount()`를 이용하여 부착하는 식으로 설명하고 있습니다. 
<br><br>

위에서 살펴본 내용이 기본라우팅입니다. 

---

## nested router(네스티드 라우터)

네스티드 라우터(`Nested router`)는 라우터로 페이지를 이동할 때 최소 2개 이상의 컴포넌트를 화면에 나타낼 수 있습니다. 
당연히 상위 컴포넌트 1개에 하위 컴포넌트 1개를 포함하는 구조로 다음의 그림처럼 동작합니다. 

{% include image.html
file='vue/vue30.png'
%}
<br>

위의 그림처럼 nested router를 사용하면 URL에 따라 컴포넌트의 하위 컴포넌트가 다르게 표시됩니다. 맨 왼쪽의 그림은 
하위에 아무것도 지정하지 않았기 때문에 User 컴포넌트만 표시되고 오른쪽 2개의 그림에는 URL값 posts, profile에 따라
각각 Post, Profile 컴포넌트가 표시됩니다. 
<br><br>

코드로 알아보죠.

~~~html
{% raw %}

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue Nested Router</title>
  </head>
  <body>
    <div id="app">
      <!-- User component가 뿌려질 영역 -->
      <router-view></router-view>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router@3.0.1/dist/vue-router.js"></script>
    <script>

      // User component 정의
      // 하위 component가 뿌려질 영역을 설정한다.
      var User = {
        template: `
          <div>
            User Component
            <router-view></router-view>  
          </div>
        `
      };

      // UserProfile component 정의
      var UserProfile = { template: '<p>User Profile Component</p>' };
      // UserPost component 정의
      var UserPost = { template: '<p>User Post Component</p>' };

      // nested routing 정의
      var routes = [
        {
          path: '/user',
          component: User,
          children: [
            {
              path: 'posts',
              component: UserPost
            },
            {
              path: 'profile',
              component: UserProfile
            },
          ]
        }
      ];

      // vue router 정의
      var router = new VueRouter({
        routes
      });

      // vue instance에 router 추가
      var app = new Vue({
        router
      }).$mount('#app');
    </script>
  </body>
</html>

{% endraw %}
~~~

위의 예제는 nested router 예제이고 기본 router와의 차이점은 최상의 component에도 `<router-view>`가 존재하고
하위 컴포넌트에도 `<router-view>`가 존재한다는 것입니다. 그림으로 표현하면 다음과 같겠죠.

{% include image.html
file='vue/vue31.png'
%}

---

## Named View - 네임드 뷰

네임드 뷰는 특정 페이지로 이동했을 대 여러 개의 컴포넌트를 동시에 표시하는 라우팅 방식입니다. 위에서 살펴본 
nested router는 상위 컴포넌트가 하위 컴포넌트를 포함하는 형식이지만 named view는 같은 레벨에서 여러 개의
컴포넌트를 한번에 표시합니다. 
<br><br>

그림을 살펴보죠.

{% include image.html
file='vue/vue32.png'
%}
<br>

코드로 구현하면 다음과 같습니다. 

~~~html
{% raw %}

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue Named View Sample</title>
</head>
<body>
<div id="app">
    <router-view name="header"></router-view>
    <router-view></router-view>
    <router-view name="footer"></router-view>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router@3.0.1/dist/vue-router.js"></script>
<script>
    var Body = { template: '<div>This is Body</div>' };
    var Header = { template: '<div>This is Header</div>' };
    var Footer = { template: '<div>This is Footer</div>' };

    var router = new VueRouter({
        routes: [
            {
                // path는 named view가 실행될 URL을 정의하는 속성. 여기서는 application을 마주하면
                // 마주치는 기본 URL인 '/'로 설정.
                path: '/',
                // 앞서 정의한 <routing-view>에 정의한 name 속성에 따라 표시될 컴포넌트를 정의.
                components: {
                    default: Body,
                    header: Header,
                    footer: Footer
                }
            }
        ]
    })

    var app = new Vue({
        router
    }).$mount('#app');
</script>
</body>
</html>

{% endraw %}
~~~

{% include image.html
file='vue/vue33.png'
%}
<br>

이렇게 named view를 이용하면 특정 페이지로 이동했을 때 해당 URL에 맞추어 여러 개의 컴포넌트를 한번에 표시할 수 
있습니다. 

End.

{% include links.html %}
