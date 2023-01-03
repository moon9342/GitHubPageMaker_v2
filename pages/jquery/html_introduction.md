---
title: HTML Introduction
sidebar: jquery_sidebar
summary: "HTML의 개요 및 특징에 관한 내용입니다."
permalink: HTML_introduction.html
folder: jquery
---

## HTML Introduction

HTML과 CSS에 대한 내용은 정말 간단하게 살펴보도록 하겠습니다. 

### HTML5 구성

{% include image.html
file='html5-composition.png'
%}

### HTML5에서 달라진 점

많은 부분에서 변경사항이 있지만 그 중 Element와 Attribute 측면에서
알아보도록 하겠습니다. 

{% include image.html
file='html5-doc-type.png'
%}

{% include image.html
file='html5-remove-declaration.png'
%}

{% include image.html
file='html5-data-attribute.png'
%}

{% include image.html
file='html5-additional-control.png'
%}

### CSS 기본

HTML은 문서의 내용을 담당하고 CSS는 문서의 모양을 담당합니다. CSS를 적용하지 않은 페이지는
아래 그림처럼 `UI/UX` 측면에서 좋지 않습니다.

{% include image.html
file='html5-using-css.png'
%}
<br>

HTML은 문서의 구조를 지정하는 언어이고 웹 페이지는 브라우저에 따라 다르게 표현하는데,
이 때 CSS를 이용하여 웹 페이지의 시각적 표현을 제어합니다.
<br><br>

웹 초기에는 페이지 제목을 나타내는데 `<h1>`과 같은 제목 표시 element를 사용하는 것이 아니라 
`<font>`를 이용해서 원하는 글꼴 및 모양으로 바꾸는 표현하는 일이 빈번해졌습니다. 
<br><br>

HTML이 이렇게  모양내기를 위해 활용되면서 불필요하고 의미 없는 태그와 속성이 많아지고, 
웹 페이지가 매우 복잡해지면서, 코드의 수정이 매우 어려운 상황에 직면하게 되었죠. 그래서 이러한 
문제의 해결을 위해 CSS가 등장한 것입니다. 
<br><br>

당연한 말이지만 웹 페이지의 가치는 콘텐츠에 있지만 이것 만으로는 많은 사용자들을 만족시킬 수 없습니다.
CSS를 사용하여 디자인이라는 시각적 가치를 부여함으로써 웹 페이지 전체의 가치를 높일 수 있는것이죠.
<br><br>

### CSS 기본문법

{% include image.html
file='html5-css-basic-syntax.png'
%}

{% include image.html
file='html5-css-basic-syntax-1.png'
%}

{% include image.html
file='html5-css-basic-syntax-2.png'
%}
<br><br>

### CSS 적용방법

{% include image.html
file='html5-css-using.png'
%}
<br><br>

### Bootstrap 활용

`Bootstrap`을 이용한 화면 디자인을 연습해보고 로그인 화면 및 게시판 리스트 예제를 수정해서
이용해보도록 하죠.



End.

{% include links.html %}
