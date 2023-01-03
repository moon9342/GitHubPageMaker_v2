---
title: jQuery Introduction
sidebar: jquery_sidebar
summary: "jQuery의 개요 및 특징에 관한 내용입니다."
permalink: jquery_introduction.html
folder: jquery
---

## jQuery Introduction

`jQuery`는 모든 브라우저에서 동작하는 클라이언트 자바스크립트 라이브러리입니다. 
<br><br>

무료로 사용 가능한 오픈 소스 라이브러리로 쉽고 강력한 기능으로 많은 사람들이 사용하고 있는
대표적인 라이브러리입니다.
<br><br>

### jQuery 사용 목적

* DOM과 관련된 처리 쉽게 구현
* 일관된 이벤트 연결 쉽게 구현
* 시각적 효과 쉽게 구현
* Ajax 애플리케이션 쉽게 개발
<br><br>

### jQuery 적용

* http://www.jquery.com
* CDN 방식 ( Content Delivery Network )으로 편하게 사용합니다.
* 일반적으로, 개발 시점에는 download 방식으로 서비스 시점에는 CDN방식을 이용합니다.

---

## jQuery selector

### 전체 선택자 (Universal Selector)

* 가장 기본적인 선택자
* 하위에 있는 모든 문서객체를 선택하는 선택자
* 예제 ( 모든 문서객체의 color 스타일 속성을 "red"로 변경 )
<br><br>

### 타입 선택자 (Type Selector) 

* Type 선택자는 특정한 태그만 선택하는 선택자
* 하나 이상의 태그 선택자를 동시에 사용하고 싶을 때는 콤마( `,` )로 선택자 구분
<br><br>

###  아이디 선택자 (ID Selector)

* HTML 페이지 내에 존재하는 id속성의 값은 각각 다르다. ( unique )
* id 속성의 값을 이용하여 문서 객체를 선택하는 선택자
* 유일한 문서객체를 선택하게 되며 속도 면에서 가장 빠르고 편하다.
<br><br>

### 클래스 선택자 (Class Selector)

* class 속성의 값을 이용하여 문서 객체를 선택하는 선택자
<br><br>

### 자식 선택자와 후손 선택자 (Child combinator & Descendant combinator)

* 기본 선택자의 뒤에 붙여 사용하며 기본 선택자의 범위 제한
* 자식 선택자는 `>`  기호로 표시
* 후손 선택자는 `' '`  공백으로 표시
<br><br>

### 동위 선택자 (Adjacent sibling combinator & General sibling combinator)

* element A + element B : element A 바로 다음에 위치하는 element B를 선택
* element A ~ element B : element A 다음에 위치하는 모든 element B를 선택
<br><br>

### 속성 선택자 (Attribute selectors)

* element[attribute] : 특정 속성을 가지고 있는 문서객체 선택
* element[attribute=value] : 속성값이 value값과 같은 문서객체 선택
* element[attribute~=value] : 속성값이 value값을 단어로서 포함하는 문서객체 선택
* element[attribute^=value] : 속성값이 value값으로 시작하는 문서객체 선택
* element[attribute$=value] : 속성값이 value값으로 끝나는 문서객체 선택
* element[attribute*=value] : 속성값이 value값을 포함하는 문서객체 선택
<br><br>

### 선택자 연습문제

{% include image.html
file='html5-jquery-selector-exec.png'
%}
<br><br>

### 상태 선택자 (The UI element states pseudo-classes)

* :checked : 체크된 입력양식을 선택
* :disabled : 비활성화된 입력양식을 선택
* :enabled : 활성화된 입력양식을 선택
* :focus : 현재 포커스가 잡혀있는 입력 양식 선택
* :selected : option객체 중 선택된 객체를 선택
<br><br>

### 함수 필터 선택자

* :contains(string) :  특정 문자열을 포함하는 문서객체 선택
* :eq(n) : n번째 위치하는 문서객체 선택
* :gtn(n) : n번째를 초과하는 위치에 있는 문서객체 선택
* :lt(n) : n번째 미만의 위치에 있는 문서객체 선택
* :not(선택자) : 선택자와 일치하는 않는 문서객체 선택
* :nth-child(2n+1) : 2n+1번째에 위치하는 문서객체 선택

---

## jQuery의 배열처리

* each() method 사용
* $.each(object, function(index,item) { } )
* $(selector).each(function(index,item) { } )

---

## jQuery Method 

* css() method
* addClass() method
* attr() method
* removeAttr() method
* html() method
* text() method
* remove()
* empty() method
* $() : 새로운 element 생성
* $(A).append(B), $(A).prepend(B)
* $(A).after(B), $(A).before(B)
* clone() method

---

## jQuery Event

* on() method 사용
* $(selector).on(event, function(event) { … })
* Event handler안에서의 `this`는 `event source` 문서객체를 지칭
* off() : 모든 이벤트 핸들러를 제거
* off("eventName") : 특정 이벤트 핸들러를 제거
* one() : 이벤트를 한 번만 연결

---

## jQuery AJAX

### $.ajax() method의 형태

* $.ajax(options);
* $.ajax(url, options);
* Ajax가 성공했을 때 자동으로 success 실행
* success handler의 첫 번째 매개 변수는 Ajax가 성공했을 때  서버로 부터 받은 데이터
<br><br>

### $.ajax() method 옵션

* async : 동기, 비동기 지정 ( boolean )
* complete : Ajax 완료 event handler 지정 ( function )
* data : 요청 매개 변수 지정 ( 일반적으로 JSON )
* error : Ajax 실패 event handler 지정 ( function )
* success : Ajax 성공 event handler 지정 ( function )
* timeout : 만료시간 지정 ( number )
* type : "GET", "POST" 지정 ( string )
* url : 호출 대상 URL 지정 ( string )
* dataType : 결과 데이터가 json 혹은 jsonp일 경우 설정 ( string )
* jsonp : JSONP방식일 경우 서버로 보내질 hash값에 대한 Key ( string )

---

## 예제 작성

영화진흥위원회 Open API를 이용한 영화정보를 출력하는 예제를 작성해보자.


End.

{% include links.html %}
