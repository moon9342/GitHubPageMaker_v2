---
title: MySQL Data Modeling 기본
sidebar: mysql_sidebar
summary: "Database Modeling이란 현 세계에서 구현해야 하는 작업이나 사물들을 
DBMS의 database 개체로 옮기기 위한 과정이라고 할 수 있습니다. 상당히 어려운 내용인데
기본적인 부분부터 알아보도록 하겠습니다."
permalink: mysql_basic-data-modeling.html
folder: mysql
---

## Data Modeling

`Database Modeling`이란 현 세계에서 구현해야 하는 작업이나 사물들을 DBMS의
database 개체로 옮기기 위한 과정이라고 할 수 있습니다. 상당히 추상적인데 조금 쉽게
설명하면 현실에서 사용되는 데이터를 `table`안의 데이터로 변경하기 위한 작업이라고
이해하시면 좋습니다.
<br><br>

사실 모델링 작업은 모델링을 수행하는 사람에 따라서 각기 다른 결과가 나오게 됩니다.
그렇기 때문에 모델링에 정답은 없습니다. 단, 좋은 모델링과 그렇지 않은 모델링은 분명
존재합니다.
<br><br>

이 모델링 작업은 상당히 난이도가 높은 작업입니다. 많은 경험과 관련지식이 필요하고
해당 도메인에 대한 지식도 확실히 가지고 있어야 하는 작업이기 때문입니다. 우리는
아직 모델링의 구체적인 단계나 과정에 대해서 공부하고 이해하기에는 아는것이 너무
적기 때문에 지금은 이론보다는 간단한 데이터베이스 모델링을 실습을 통해 모델링
과정을 알아보도록 하겠습니다.

---

## 개념적 이해

새로운 오프라인 쇼핑몰을 오픈했다고 가정합니다. 지금부터 우리는 우리 쇼핑몰의 고객
명단을 기록하고 물건을 구매할 때 구매한 내역도 기록할 것입니다. 이 데이터를 이용해
데이터베이스 모델링을 수행해 보도록 하죠.
<br><br>

다음과 같은 내용이 엑셀에 저장되어 있습니다.

{% include image.html
file='database-mysql-modeling-step-1.png'
%}

데이터가 정렬되지 않고 막 섞여 있습니다. 구매한 고객과 구매하지 않고 방문만 한 고객을
구분지어서 보면 좀 편할 거 같습니다.

{% include image.html
file='database-mysql-modeling-step-2.png'
%}

물건을 구매한 적이 없는 고객을 위쪽으로 정렬했습니다.
<br><br>

위의 그림을 보니 전체 테이블이 `L`자 모양의 테이블이 되었습니다. 이러한 형태를 `L자형 테이블`
이라고 하는데 이런 형태의 가장 큰 문제점은 **공간이 낭비**된다는 것입니다.
<br><br>

어떻게 하면 공간의 낭비를 없앨 수 있을까요? 테이블을 분리하면 됩니다. 아래의 그림을 보시죠.

{% include image.html
file='database-mysql-modeling-step-3.png'
%}

위의 그림처럼 테이블을 2개로 나누면 낭비되는 빈 공간을 없앨 수 있습니다.
<br><br>

자 그럼 여기서 또 생각해야 할 것은 무엇일까요? 일단 고객테이블에 같은 내용이 중복되어
들어가 있다는 것입니다. 즉, 여러번 물건을 산 고객의 정보는 동일한 정보가 여러 번 기록되어
있습니다. 이것 역시 공간의 낭비이기 때문에 중복되는 고객을 제거하고 하나만 남기도록 하겠습니다.

{% include image.html
file='database-mysql-modeling-step-4.png'
%}

중복을 제거했습니다. 그런데 구매 테이블을 보니 구매한 내역만 존재하지 누가 구매했는지에 대한
정보가 없습니다. 그래서 구매 테이블에 고객 테이블의 기본키인 고객 이름을 넣어줘서 누가 구매했는지에
대한 정보를 추가합니다. 즉, 다음 그림과 같이 만들어 지게 됩니다.

{% include image.html
file='database-mysql-modeling-step-5.png'
%}

테이블의 구분은 잘 되었습니다. 그런데 이 고객 테이블과 구매 테이블은 서로 밀접한 관련이 있는
테이블 입니다. 그리고 구매 테이블만으로는 고객에게 배송을 할 수 없습니다. 주소를 가지고 있지
않기 때문이죠. 그래서 이 두 테이블의 업무적인 연관성을 맺어줘야 합니다.
<br><br>

이를 `Relation`(관계)라고 부릅니다.
<br><br>

두 테이블 중에서 부모 테이블과 자식 테이블을 결정해보죠. 부모와 자식을 구분하는 방법은
`Master`(주)가 되는쪽은 부모로, `Detail`(상세)가 되는 쪽을 자식으로 설정하는 것입니다.
<br><br>

그러면 상식적으로 어느쪽이 `Master`가 될까요? 당연히 고객 테이블이 부모 테이블이 되고
구매 테이블이 자식 테이블이 됩니다.
<br><br>

또 다른 방법은 기준이 하나인 것과 하나의 기준이 여러 개의 기록을 남기는 것으로 부모와 자식 테이블을
결정하는 것입니다.
<br><br>

우리의 고객 한명당 여러 구매 내역을 가질 수 있기 때문에 `1:N`의 관계를 가지게 되고 이는
관계형 데이터베이스의 가장 보편적인 테이블 사이의 관계입니다.
<br><br>

부모 테이블인 고객 테이블과 자식 테이블인 구매 테이블의 관계를 맺어주는 역할은
`Primary Key`(기본키)와 `Foreign Key`(외래키)를 설정함으로 이루어집니다.
아래의 그림을 보시죠.

{% include image.html
file='database-mysql-modeling-step-6.png'
%}

이렇게 `Relation`이 맺어진 후에는 **제약 조건**이라는 관계가 자동으로 설정됩니다. 이 제약조건에
대해서는 추후에 다시 자세히 알아보도록 하겠습니다.
<br><br>

이제 완성된 고객 테이블과 구매 테이블의 테이블 구조를 정의할 차례입니다. column의 이름,
데이터 형식, Null 여부등을 결정하는 과정입니다.
아래의 그림을 보시죠.

{% include image.html
file='database-mysql-modeling-step-7.png'
%}

---

## Workbench를 이용한 모델링

위에서 간단하게 모델링의 개념과 방법을 알아봤으니 이번에는 `MySQL`이 제공해 주는 데이터베이스
모델링 툴을 사용해 보겠습니다.
<br><br>

`Workbench`를 실행해 `Model diagram`을 작성합니다. `File` 메뉴의 `New Model`을
선택합니다. 기본적인 데이터베이스 이름은 `mydb`로 설정되는데 `Edit Schema`를 선택해
이름을 `modelDB`로 변경합니다.

{% include image.html
file='database-mysql-modeling-workbench-step-1.png'
%}

그런다음 `Add Diagram`을 선택해 `EER Diagram` 탭을 추가하고 왼쪽의 `Place a New Table`
아이콘을 이용해 `userTBL` 테이블을 생성하고 아래의 그림처럼 적절히 column을 설정합니다.

{% include image.html
file='database-mysql-modeling-workbench-step-2.png'
%}

같은 요령으로 `buyTBL` 테이블을 생성하고 `Place a Relationship Using Existing column` 아이콘을
이용해 관계를 맺어줍니다. 그 후 `Save Model`을 선택해 `modelDB.mwb`라는 이름으로 저장합니다.
참고로 저장할 폴더명이 한글일 경우 오류가 발생할 여지가 있으니 주의해야 합니다.

{% include image.html
file='database-mysql-modeling-workbench-step-3.png'
%}

{% include image.html
file='database-mysql-modeling-workbench-step-4.png'
%}

모델링을 끝냈으니 이제 이 모델링 파일을 실제 데이터베이스에 적용시켜 보겠습니다.
<br><br>

`Database` 메뉴의 `Forward Engineer` 메뉴를 선택합니다.
<br><br>

다음과 같은 화면을 볼 수 있습니다.

{% include image.html
file='database-mysql-modeling-workbench-step-5.png'
%}

`Next`를 클릭하고 다음 `Set Options for Database to be Created`에서 여러 옵션을 선택할 수 있지만
그대로 두고 `Next`를 클릭합니다. 그러면 `root` password를 물어보는데 입력하면 다음과 같은
화면을 볼 수 있습니다.

{% include image.html
file='database-mysql-modeling-workbench-step-6.png'
%}

`Next`를 클릭하면 `Review the SQL Script to be Executed`라는 화면이 보입니다. 우리가 하려는
작업에 대한 SQL문을 파일로 저장할 수도 있습니다. 여기서는 그냥 `Next`를 클릭합니다. 그러면 다시
root password를 물어보는데 입력하면 성공적으로 데이터베이스에 적용되는것을 확인할 수 있습니다.

{% include image.html
file='database-mysql-modeling-workbench-step-7.png'
%}

이제 잘 생성이 되었는지를 `Navigator`에서 확인하면 됩니다.
<br><br>

마지막으로 기존에 존재하는 데이터베이스인 `shopDB`를 이용해서 다이어그램을 작성하는 방법을
알아보겠습니다.
<br><br>

`Database` 메뉴의 `Reverse Engineer`를 선택한 후 대화상자에 나오는 내용을 잘 읽고
체크하면서 진행하시면 됩니다. 이 부분은 앞서 나온 것과 과정만 반대가 되는 것이기 때문에
따로 부가적인 설명을 진행하지는 않겠습니다.
<br><br>

모든 과정이 완료되면 다음과 같은 그림을 볼 수 있습니다.

{% include image.html
file='database-mysql-modeling-workbench-step-8.png'
%}

지금까지 간단하게 모델링에 대한 개념적인 내용과 실습을 진행했습니다. 가장 대표적인 모델링 툴은
`CA`사의 `ERwin`이라는 제품이 있습니다. 하지만 가격이 많이 비싸기 때문에 다른 다양한 툴들을
이용하여 모델링을 진행하기도 합니다.


End.

{% include links.html %}
