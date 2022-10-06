---
title: ECMAScript 6.0 ajax
sidebar: ecmascript_sidebar
summary: ""
permalink: ecmascript_ajax.html
folder: ecmascript
---

## AJAX

AJAX(Asynchronous JavaScript And XML)란 자바스크립트를 사용하여 브라우져가 서버에게 비동기 방식으로 데이터를
요청하고 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식을 의미합니다. 

AJAX는 브라우져에서 제공하는 Web API인 XMLHttpRequest 객체를 기반으로 합니다.

원래 XMLHttpRequest는 1999년 MS가 개발한 것이데 당시에는 큰 주목을 받지 못했습니다. 하지만 추후에 구글이 2005년에
구글맵스를 통해 이 AJAX의 활용이 아주 큰 가치가 있다는 것을 증명한 후 현재는 웹 데이터 통신의 사실상의 표준 역할을 하고 있습니다. 

이전의 웹 페이지는 HTML 태그로 시작해서 HTML 태그로 끝나는 완전한 HTML을 서버로 부터 전송받아 웹 페이지를 처음부터 다시
랜더링하는 방식으로 동작했습니다. 따라서 화면이 전환되면 서버로부터 새로운 HTML을 전송받아 웹페이지를 처음부터 다시 랜더링 했었습니다.

{% include image.html
file='es6-ajax/es6-ajax-1.png'
%}

이러한 방식을 round-trip방식이라고도 하는데 이와같은 전통적인 방식은 다음과 같은 단점이 있습니다. 

1. 이전 웹페이지와 큰 차이가 없는 부분까지 다시 전송받아야 하기 때문에 불필요한 데이터 통신이 발생합니다. 

2. 변경할 필요가 없는 부분까지 처음부터 다시 랜더링 합니다. 이로 인해 화면 전환이 일어나면 화면이 순간적으로 깜빡이게 됩니다.

3. 클라이언트와 서버와의 통신이 동기 방식으로 동작하기 때문에 서버로부터 응답이 있을 때까지 다음 처리는 blocking됩니다. 

이러한 방식이 AJAX의 등장으로 패러다임의 전환을 맞이하게 됩니다. 

즉, 서버로부터 웹페이지의 변경에 필요한 데이터만 비동기 방식으로 전송받아 전체 웹페이지를 변경할 필요없이 필요한 부분만 랜더링되는
방식으로 전환된 것이죠.

{% include image.html
file='es6-ajax/es6-ajax-2.png'
%}

---

## JSON

JSON(JavaScript Object Notation)은 클라이언트와 서버간의 HTTP 통신을 위한 텍스트 데이터 포맷입니다. 특정 프로그래밍 언어에
종속되지 않는 언어 독립형 데이터 포맷으로 대부분의 프로그래밍 언어에서 사용합니다. 

JSON은 자바스크립트 객체 리터럴과 유사화게 키와 값으로 구성된 순수한 텍스트입니다. 

주의해야 할 점은 JSON의 키는 반드시 큰 따옴표로 묶어야 한다는 것입니다. 값은 객체 리터럴과 같은 표기법을 그대로 사용할 수 있습니다. 하지만
문자열은 반드시 큰 따옴표를 사용해야 합니다. 

### JSON.stringify

JSON.stringify 메소드는 객체를 JSON 포맷으로 변환하는 일을 합니다. 클라이언트가 서버로 객체를 전송하려면 객체를 문자열로 변환해야 하는데
우리는 이것을 직렬화(Serialization)이라고 합니다. 

~~~javascript

const obj = {
    name: '홍길동',
    age: 20,
    address: '서울',
    hobby: ['게임', '독서']
}

const json = JSON.stringify(obj);

console.log(json);   // {"name":"홍길동","age":20,"address":"서울","hobby":["게임","독서"]}

~~~

### JSON.parse

JSON.parse 메소드는 JSON 포맷의 문자열을 자바스크립트 객체로 변환합니다. 일반적으로 서버로부터 클라이언트에게 전송되는 JSON
데이터는 당연히 문자열입니다. 이 문자열을 객체로 사용하려면 JSON 포맷의 문자열을 객체화 해야 하는데 이를 역 직렬화(Deserialization)이라
합니다. 

~~~javascript

const obj = {
    name: '홍길동',
    age: 20,
    address: '서울',
    hobby: ['게임', '독서']
}

const json = JSON.stringify(obj);

console.log(json);   // {"name":"홍길동","age":20,"address":"서울","hobby":["게임","독서"]}


const myObj = JSON.parse(json);

console.log(myObj);

~~~

---

## XMLHttpRequest

자바스크립트를 사용하여 HTTP 요청을 전송하려면 XMLHttpRequest객체를 사용합니다. Web API인 XMLHttpRequests객체는 생성자 함수를 
통해 생성합니다. 

또한 여러가지 method를 가지고 있는데 이는 예제를 통해서 알아보도록 하죠.

~~~javascript


// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
// https://jsonplaceholder.typicode.com는 Fake REST API를 제공하는 서비스입니다.

xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');

// HTTP 요청 전송
xhr.send();

xhr.onreadystatechange = () => {
    // readyState 프로퍼티는 HTTP 요청의 현재 상태를 나타냅니다.
    // 이 값이 4가 아니면 서버 응답이 완료되지 않은 상태입니다. 


    // 만약 서버 응답이 아직 완료되지 않았다면 아무런 처리를 하지 않습니다. 
    if(xhr.readyState !== XMLHttpRequest.DONE) return;


    // status property는 응답 상태 코드를 나타냅니다. 
    // 이 값이 200이면 정상적으로 응답된 상태입니다. 
    // 200이 아니면 에러가 발생한 상태입니다. 
    // 정상적으로 응답한 상태이면 response property에 서버의 응답 결과가 담겨있습니다. 
    if(xhr.status === 200) {
        console.log(JSON.parse(xhr.response));
        // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
    } else {
        console.log('먼가 이상해요!!');
    }
}

~~~

End.

{% include links.html %}
