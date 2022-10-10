---
title: Vue to-do-app
sidebar: vue_sidebar
summary: ""
permalink: vue_to-do-app.html
folder: vue
---

## to-do-app 소개

일반적으로 Front-End Framework를 배울 때 가장 먼저 작성하는 프로젝트는 to-do app입니다. 이 앱을 구현하는 이유는
적은 양의 코드, 컴포넌트 구조화 및 컴포넌트 통신, 기본적인 CRUD가 다 들어있기 때문입니다.

우리 앱은 데이터를 서버측의 Database에 저장하지 않고 browser의 저장소인 `localStorage`를 이용하도록 하겠습니다.

다음 그림은 우리 앱의 컴포넌트 구조도입니다. 

{% include image.html
file='vue/vue48.png'
%}

## vue CLI를 이용한 프로젝트 생성

프로젝트 초기 구성을 위해 뷰 CLI로 프로젝트를 생성합니다. 원하는 폴더 위치에 새로운 폴더 `vue-todo`를 생성하고 해당 폴더
내에서 `cmd`창을 열어 `vue init webpack-simple`을 입력하고 실행합니다. 

## 프로젝트 초기 설정

이 부분은 넘어가자.

index.html에 필요한 라이브러리 추가(axios)

## 컴포넌트 생성하고 등록

컴포넌트 하나만 생성하자

프로젝트 폴더 src 폴더 밑에 components 폴더를 생성하고 그 아래에 todoheader.vue 파일 생성

~~~html
{% raw %}

<template>
    <div>
        <div>header</div>
        <button v-on:click="myFunc">서버 호출</button>
    </div>
</template>

<script>
    export default {
        methods: {
            myFunc: function() {
                axios({
                    method: 'get',
                    // CORS 에러가 난다. 
                    // url: 'https://cors-anywhere.herokuapp.com/https://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json',
                    // url: '/api/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json',
                    url: '/V1/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json',

                    // headers는 사용자 지정 헤더입니다.
                    headers: { 'Access-Control-Allow-Origin': '*',
                        'Content-Type': 'application/json; charset=utf8'
                    },

                    // params은 요청과 함께 전송되는 URL 파라미터입니다.
                    // 반드시 일반 객체나 URLSearchParams 객체여야 합니다.
                    // 참고: null이나 undefined는 URL에 렌더링되지 않습니다.
                    params: {
                        key: 'f5eef3421c602c6cb7ea224104795888',
                        targetDt: '20220901'
                    },
                    // data는 요청 바디로 전송될 데이터입니다.  
                    // 'PUT', 'POST', 'PATCH', 'DELETE' 메소드에서만 적용 가능합니다.
                    data : {
                    },
                    // responseType은 서버에서 받는 데이터의 타입입니다.
                    // 옵션: 'arraybuffer', 'document', 'json', 'text', 'stream'
                    // 브라우저 전용: 'blob'
                    responseType: 'json', // 기본값                                          
                }).then(function(response) {
                    result = response.data;
                    console.log(result);
                }).catch(function(err) {
                    console.log(err);
                });
            }
        }
    }
</script>

<style>

</style>

{% endraw %}

~~~


proxy 설정(vue.config.js)

~~~html
{% raw %}

module.exports={
    devServer: {
      proxy: { 
          '/V1': { 
              target: 'https://www.kobis.or.kr', 
              changeOrigin: true,
              secure: false,
              pathRewrite: { '^/': '' }
          }
      }
    }
}


{% endraw %}

~~~


End.

{% include links.html %}
