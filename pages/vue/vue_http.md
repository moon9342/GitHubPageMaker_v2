---
title: Vue HHTP 통신
sidebar: vue_sidebar
summary: ""
permalink: vue_http.html
folder: vue
---

## 웹 앱의 HTTP 통신

웹 앱의 필수적인 기능 중 하나가 서버에 데이터를 요청하는 HTTP 통신입니다. 
<br><br>

이제는 사용자와의 상호작용에 따라 데이터를 동적으로 화면에 표시해줘야 하기 때문이죠.
<br><br>

`HTTP`는 브라우저와 서버간에 데이터를 주고받는 통신 protocol입니다. browser가 특정 데이터를 보내달라고
`request`를 보내면 서버에서 `response`로 해당 데이터를 보내주는 방식으로 동작합니다. 
<br><br>

그림으로 보면 다음과 같습니다. 

{% include image.html
file='vue/vue34.png'
%}
<br>

웹 앱의 대표적인 통신방식으로는 `AJAX`가 있습니다. AJAX는 서버에서 받은 데이터를 표시할 때 화면 전체를 갱신하지 않고
화면의 일부분만 변경할 수 있게 하는 JavaScript 기법입니다. 이 AJAX가 대중화가 되면서 많은 웹 앱에서 AJAX를 사용하고
있습니다. 
<br><br>

우리 Vue에서도 AJAX를 지원하기 위한 라이브러리를 사용합니다. 크게 두가지가 있는데 `vue resource`와 `axios` 입니다. 
<br><br>

원래 `vue resource`는 초기에 vue core팀에서 공식적으로 지원하는 라이브러리 였으나 2016년말에 공식적인 지원을
중단하기로 결졍하면서 지금은 거의 대부분 `axios`를 이용합니다. 
<br><br>

우리도 axios를 이용하는 방법을 알아보도록 하죠.

---

## axios

`Axios`는 현재 vue 커뮤니티에서 가장 많이 사용되는 HTTP 통신 라이브러리입니다. vue의 창시자인 에반 역시 vue resource를
공식 라이브러리에서 제외하면서 axios를 언급할 정도로 많이 사용되고 있습니다. 
<br><br>

axios의 장점은 다양한 형태의 API가 제공되어 별도의 로직구현없이 주어진 API만으로도 원하는 로직을 구현할 수 있다는 것입니다. 
<br><br>

axios의 설치는 CDN을 이용하는 방법과 NPM을 이용한 설치로 나누어지는데 우리는 CDN을 이용하는 방법을 사용하도록 하겠습니다. 
<br><br>

axios는 HTTP 통신에 간단하고 직관적인 API를 제공하는데 다음 그림처럼 제공됩니다. 

{% include image.html
file='vue/vue35.png'
%}
<br>

get 혹은 post방식으로 호출할 수 있으며 요청이 성공하면 서버에서 데이터를 받아와서 then()안에 정의한 로직이 수행됩니다. 
만약 오류가 있으면 catch()안에 정의한 로직이 수행되는 것이죠.
<br><br>

간단한 코드를 실행해 보도록 하겠습니다. 

~~~html
{% raw %}

<html>
<head>
    <title>Vue with Axios Sample</title>
</head>
<body>
<div id="app">
    <button v-on:click="getData">영화 박스오피스 순위가져오기</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
    new Vue({
        el: '#app',
        methods: {
            getData: function() {
                axios({
                    method: 'get',
                    // CORS 에러가 난다. 
                    url: 'https://cors-anywhere.herokuapp.com/https://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json',
                    // url: 'https://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json',

                    // headers는 사용자 지정 헤더입니다.
                    headers: {'X-Requested-With': 'XMLHttpRequest'},

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
    });
</script>
</body>
</html>

{% endraw %}
~~~

위의 코드에서 가장 문제가 되는건 `CORS` 에러입니다. 
<br><br>

`CORS`는 서로 다른 출처(Origin) 간에 리소스를 전달하는 방식을 제어하는 체제이며,
CORS 요청이 가능하려면 서버에서 특정 헤더인 `Access-Control-Allow-Origin`과 함께 응답할 필요가 있습니다. 
<br><br>

CORS 정책을 위반하여 서로 다른 출처를 가진 상태에서 무언가를 요청하게 되면 브라우저가 보안 상의 이유로 차단합니다. 
예를 들어, 클라이언트 포트가 3000번이고 서버의 포트가 8000번 일 때,
클라이언트에서 서버로 리소스를 요청했을 때 CORS 에러 메시지가 클라이언트 콘솔에 빨갛게 뜨고 데이터를 주지 않게 됩니다. 
<br><br>

정리하면 현재 우리의 문제는 다음과 같습니다. 
<br><br>

첫 번째는 클라이언트에서 외부 API 서버로 바로 요청을 보내서 CORS 문제가 발생한다는 것입니다.
<br><br>

두 번째 : 외부 API를 사용하고 있었기 때문에 내가 서버를 제어할 수 없으므로 HTTP 응답 헤더인 
Access-Control-Allow-Origin 를 설정할 수 없다는 것입니다.
<br><br>

세 번째 : 우리가 따로 `proxy server`를 구축하기에는 현실적으로 불가능하다는 것입니다.
<br><br>

따라서 클라이언트에서 외부 서버로 바로 요청을 해버리는 것이 아니라 외부 `proxy server`를 사용해서 우회하는 방법을 택했습니다.
`proxy server`는 클라이언트가 프록시 서버 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해줍니다. 
쉽게 말해 브라우저와 서버 간의 통신을 도와주는 중계서버라고 생각하면 됩니다.
<br><br>

`https://cors-anywhere.herokuapp.com`
<br><br>

위의 주소를 browser에 입력하면 일시적인 proxy서버 사용권한을 얻을 수 있습니다.  
<br><br>

그런다음 위의 코드를 실행하면 됩니다 .

End.

{% include links.html %}
