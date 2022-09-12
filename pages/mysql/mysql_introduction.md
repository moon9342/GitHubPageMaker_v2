---
title: MySQL Introduction
sidebar: mysql_sidebar
summary: "Database라는 용어는 비단 IT분야뿐만 아니라 일반 분야에서도 널리 사용되는 용어입니다. 
그 중 무료이지만 성능의 좋은 MySQL이라는 Database에 대해 자세히 알아보도록 하겠습니다."
permalink: mysql_introduction.html
folder: mysql
---

## Introduction

`Database`라는 용어는 비단 IT분야뿐만 아니라 일반 분야에서도 널리 사용되는 용어입니다.
하지만 정작 `Database`를 정의해보라고 하면 정확하게 말하지 못하는 경우가 많습니다.

{% include callout.html
type="danger"
content="`Database`는 **데이터의 집합**, 조금 더 정확히는 **대용량의 데이터 집합을 체계적으로
구성해 놓은 것** 이라고 정의할 수 있습니다."
%}

또한 이러한 Database는 오직 하나가 아니라 용도에 따라 여러 Database가 존재할 수 있으며
각 Database는 혼자서 사용하는 것이 아닌 여러명의 사용자나 시스템이 서로 공유하면서
사용됩니다. 그렇기 때문에 이런 Database를 관리해주는 시스템 혹은 소프트웨어가 필요하게 됩니다.

{% include callout.html
type="danger"
content="`DBMS`(Database Management System)는 데이터베이스를 관리해 주는 시스템 혹은
소프트웨어를 일컫는 말입니다."
%}

정리를 해보면 `Database`는 데이터의 집합이라고 정의할 수 있고 `DBMS`는 이 `Database`를
관리,운영하는 역할을 수행합니다. 또한 `Database`는 여러 명의 사용자나 프로그램이 공유하고
동시에 접근이 가능해야 합니다.
<br><br>

추가적으로 `Database`는 **데이터의 저장공간 자체**를 의미하기도 합니다. 즉, 파일로 구성되는
디스크 공간을 Database로 취급하기도 합니다.
<br><br>

일반적으로 많이 사용되는 DBMS는 다음 그림과 같습니다.

{% include image.html
file='relational-database-popular-dbms.png'
%}

---

## DBMS의 특징

`DMBS` 혹은 `Database`는 다음과 같은 몇 가지 중요한 특징을 가지고 있습니다.
<br><br>

1. `Integrity`(무결성) : 데이터베이스 안의 데이터는 오류가 있어서는 안된다는 특징입니다.
   이런 무결성을 위해 데이터베이스는 `constraints`(제약조건)라는 특성을 이용합니다.
   간단한 예를 들면 학생 데이터의 모든 학생은 반드시 학번이 존재해야 하며 각 학생의 학번은
   서로 중복되지 않는다는 `contraints`를 가질 수 있습니다. 이 제약조건만 지키면 같은 학번의
   사람이 둘 이상 존재하는 잘못된 데이터가 생성되는 것을 방지할 수 있습니다.
<br><br>

2. `데이터의 독립성` : 데이터베이스 크기를 변경하거나 데이터 파일의 저장소를 변경하더라도 기존에
   작성된 응용 프로그램은 전형 영향을 받지 않아야 한다는 것입니다. 즉, 데이터베이스와 응용프로그램은
   서로 의존적 관계가 아닌 독립적인 관계여야 한다는 의미입니다.
<br><br>

3. `보안` : 데이터베이스안의 데이터는 아무나 접근할 수 있는것이 아니라 데이터를 소유한 사람이나
   데이터의 접근이 허가된 사람만이 데이터에 접근할 수 있어야 합니다. 또한 데이터에 접근할 때도
   사용자 계정에 따라서 다른 권한을 가지도록 설정해야 합니다.
<br><br>

4. `데이터 중복의 최소화` : 데이터 중복이란 동일한 데이터가 여러 개 중복되어 저장되는 것을 의미합니다.
   데이터 중복에 의해 데이터의 무결성이 훼손되거나 처리시간이 오래걸리는 등의 문제가 발생할 여지가
   많기 때문에 데이터의 중복은 반드시 최소화 해야 합니다.
<br><br>

5. `안전성` : 대부분의 DBMS는 백업과 복원기능을 제공하고 이를 이용함으로 데이터에 문제가 생겼을 때
   원상으로 복원 또는 복구하는 방법을 제공합니다.

---

## DBMS의 분류

`DBMS`의 유형은 크게 5가지 정도로 분류됩니다.
<br><br>

각각을 나열해 보면 `Hierarchical DMBS`(계층형 DBMS), `Network DBMS`(망형 DBMS),
`Relational DBMS`(관계형 DBMS), `Object-Oriented DBMS`(객체지향형 DBMS),
`Object-Relational DBMS`(객체관계형 DBMS) 입니다.
<br><br>

이 중 가장 많이 사용되고 있는 것은 Relational DBMS입니다. 우리가 들어본 많은 DBMS들이
모두 이 범주안에 들어갑니다.
<br><br>

`Hierarchical DMBS` : 처음나온 DBMS입니다. 아래 그림처럼 `Tree` 구조를 가지는 형태로
데이터 구축이 쉽고 특정 상태의 검색은 빠르지만 구조를 변경하기 어렵고 임의의 검색에는 어려움이
따르는 모델입니다.

{% include image.html
file='database-hierarchical-dbms.png'
%}

`Network DBMS` : 계층형 DBMS의 문제를 해결하기 위해 나온 모델로 효과적이고 빠른
데이터 추출이 가능합니다. 하지만 구현 자체가 너무 복잡해서 실제 널리 상용화 되지 않았습니다.

{% include image.html
file='database-network-dbms.png'
%}

`Relational DBMS` : E.F.Codd(에드가 F 커드)라는 사람이 수학적 모델에 근거해서
만든 모델입니다. `RDBMS`의 핵심은 **Database는 Relation(Table)이라 불리는
최소 단위로 구성되어 있고 이 Table안에는 하나 이상의 column으로 구성된다**라는
것입니다. 초기 논문에서는 `Table`이라는 용어대신 `Relation`이라는 용어를
사용했습니다.
<br><br>

RDBMS의 장점은 다른 DBMS에 비해 업무가 변화될 때 쉽게 변화에 순응할 수 있는 구조로
되어 있으며 유지보수 하기 좋은 구조로 되어있다는 것입니다. 또한 대용량의 데이터의
관리가 용이하고 데이터의 무결성을 잘 보장해주는 특성이 있습니다.

{% include image.html
file='database-relational-dbms.png'
%}

---

## MySQL

`MySQL`은 `Oracle`사에서 제작한 DBMS입니다. `Open Source`로 제공되기 때문에
비 상업용이나 교육용으로는 제한없이 사용할 수 있습니다. 하지만 2010년 Oracle이 MySQL을
인수하면서 상용으로 사용하기 위해서는 별도의 상용 license를 취득해야 합니다.
<br><br>

만약 MySQL을 상업적인 용도로 사용하려면 MySQL 초기 개발자들이 만든 `MariaDB`를
사용하시면 됩니다. `MariaDB`는 `MySQL`과 대부분의 내용이 호환됩니다.
<br><br>

MySQL은 크게 상용 Edition과 무료 Edition으로 나뉘어 지는데 `Standard`, `Enterprise`,
`Cluster CGE`의 3개의 상용 Edition과 `Community` 무료 Edition이 제공됩니다. 사실
무료인 `Community` Edition은 `Enterprise` Edition과 거의 차이가 없습니다. 단지
라이센스의 차이라고 생각하시면 됩니다.
<br><br>

우리는 `Community` Edition을 이용하도록 하겠습니다.

---

## SQL 개요

`SQL`(Structured Query Language)은 데이터베이스에서 사용되는 언어로 **에스큐엘**, 혹은
**씨퀄**이라고 읽습니다. RDBMS를 사용하고자 한다면 SQL을 당연히 익혀야 합니다. 이 SQL은
데이터베이스를 조작하는 언어이지만 일반적인 프로그래밍 언어와는 약간 다른 특성을 가집니다.
<br><br>

1. SQL은 DBMS 제작회사와 독립적입니다. 즉, SQL은 표준으로 지정되어 있고 여러
   DBMS vendor들이 이 표준을 준수하면서 DBMS를 만들고 있습니다. 따라서 표준 SQL은
   대부분의 RDBMS 제품에서 공통적으로 호환됩니다.
<br><br>

2. 대화식 언어로 기존 프로그래밍 언어처럼 프로그램 작성, 컴파일, 실행이라는 과정을
   거치지 않아도 결과를 확인할 수 있습니다. SQL은 바로 질의하고 결과를 얻는 대화식 언어로
   구성되어 있습니다.

---

MySQL을 시작하기 앞서 기본적인 데이터베이스와 DBMS에 대한 기본적인 내용과 특징들을
살펴보았습니다. 이제 다음장부터 MySQL을 설치하고 사용하는 방법에 대해서
알아보도록 하겠습니다.


End.

{% include links.html %}
