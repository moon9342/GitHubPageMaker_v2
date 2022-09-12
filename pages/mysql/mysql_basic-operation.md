---
title: MySQL terminology & 기본운영
sidebar: mysql_sidebar
summary: "MySQL을 사용하기 위한 기본적인 용어와 운영방법에 대한 내용입니다."
permalink: mysql_basic-operation.html
folder: mysql
---

## 시스템 구축 절차

정보시스템을 구축하기 위해서는 기본적으로 분석, 설계, 구현, 테스트, 유지보수의 5단계를
거치게 됩니다.
<br><br>

이 중 분석 단계는 프로젝트의 가장 첫번째 단계로 `시스템 분석` 혹은 `요구사항분석` 이라고
합니다. 이 단계에서는 우리가 무엇(`what`)을 할 것인지를 결정합니다.
<br><br>

그 다음에 진행되는 것은 설계 단계입니다. 주로 `시스템 설계` 혹은 `프로그램 설계`라고
부르며 우리가 구축하고자 하는 시스템을 어떻게(`how`) 구현할 것인지를 결정합니다.
<br><br>

이 과정이 끝나면 결과로 나온 문서들을 프로그래머에게 넘겨서 실제 프로그램 구현에 들어가게
됩니다. 따라서 시스템 설계가 끝나면 가장 큰 작업이 끝난 것으로 간주하고 대부분의 프로젝트는
이 분석과 설계의 과정이 전체 공정의 50% 이상을 차지하게 됩니다.
<br><br>

그리고 이 분석과 설계 과정 중에서 가장 중요한 과정중의 하나가 바로 `Database Modeling`입니다.
<br><br>

`Database Modeling`이란 현실 세계에서 사용되는 데이터를 Database에 어떻게
옮겨 놓을 것인지를 결정하는 과정이라고 쉽게 이해하시면 됩니다.
<br><br>

자 그럼 간단하게 인터넷 쇼핑몰의 데이터베이스 모델링 결과를 한번 살펴보고
각 용어들부터 천천히 알아보도록 하겠습니다.

{% include image.html
file='database-mysql-db-modeling.png'
%}

`Table` : 회원이나 제품의 데이터를 입력하기 위해, 표 형태로 표현한 것을 의미합니다.
<br><br>

`Database` : 테이블이 저장되는 저장소를 의미합니다. DBMS는 여러개의 Database를 유지할
수 있으며 각 Database는 서로 다른 고유한 이름을 가지고 있어야 합니다.
<br><br>

`DBMS` : 데이터베이스를 관리하는 시스템 혹은 소프트웨어의 집합을 의미합니다.
<br><br>

`Column` : 열 혹은 필드라고도 합니다. 각 테이블은 열로 구성됩니다.
<br><br>

`Column name` : 각 열을 구분하기 위한 이름입니다. 열 이름은 하나의 테이블 내에서는 중복이
허용되지 않습니다.
<br><br>

`Data Type` : 열의 데이터 형식을 의미합니다. 숫자나 문자같은 것을 의미합니다.
<br><br>

`Row` : 행 혹은 레코드라고 합니다. 실질적인 데이터를 의미합니다.
<br><br>

`Primary Key` : 기본키라고 불리는 column은 각각의 행을 유일하게 구분하기 위해
유니크한 값을 가지는 열을 의미합니다. (우리가 지정해서 사용합니다.) 또한 Primary Key는
각 테이블에 최대 1개만 존재할 수 있습니다.
<br><br>

`Foreign Key` : 두 개의 테이블의 관계를 맺어주는 역할을 수행하는 column을 의미합니다.
(이 역시 우리가 지정해서 사용합니다.)
<br><br>

자 기본적인 용어들에 대해서 정리해보았으니 이제 실습을 통해 데이터베이스 사용에 대해서
알아보도록 하겠습니다.

---

## Database 생성

`Workbench`를 실행시킨 후 Local MySQL Server에 접속합니다.
<br><br>

`Navigator`창의 아래쪽에 `Schemas`라는 탭을 클릭합니다.
<br><br>

원래 `Database Schema`라는 용어는 Database에서 data의 구조와 data의 표현방법,
data간의 관계를 형식 언어로 정의한 구조를 의미합니다. 총 3개의 layer로 구성되고 각각의
schema의 의미는 다음과 같습니다.
<br><br>

* `External Schema` : 프로그래머나 사용자의 입장에서 database의 모습을 의미합니다.
<br><br>

* `Conceptual Schema` : database안의 모든 data에 대한 구조를 논리적으로 정의한 것을
  의미합니다.
<br><br>

* `Internal Schema` : database의 물리적 저장형태를 기술하는 것을 의미합니다.
<br><br>

이렇게 `Schema`라는 용어는 `Database`라는 용어와 본래 다른 뜻이지만
MySQL에서는 이 Schema라는 용어를 Database와 동일한 개념으로 사용합니다.
<br><br>

여하간 Schema tab에서 마우스 오른쪽 팝업 메뉴를 이용해 `Create Schema`를 실행합니다.
Schema의 이름을 `shopdb`로 지정하고 apply를 클릭하면 창이 열리면서 사용되는 SQL문을
확인할 수 있습니다.
<br><br>

일반적으로 이렇게 GUI 툴을 이용해서 사용하는것 보다는 SQL명령어를 이용해서 Database를
제어합니다. 우리의 강의도 앞으로 가능한 SQL명령을 이용해서 제어하도록 하겠습니다.
<br><br>

`shopdb`라는 데이터베이스가 생성된 것을 확인했으니 이제 data를 저장하는 구조인 `table`을
생성할 차례입니다.

---

## Table 생성

우리는 2개의 Table을 생성할 것이고 다음과 같은 내용으로 생성합니다.
<br><br>

* 회원 테이블(`memberTBL`)

{% include image.html
file='database-mysql-member-table.png'
%}

* 제품 테이블(`productTBL`)

{% include image.html
file='database-mysql-product-table.png'
%}

위의 내용대로 `table`을 생성합니다. `shopdb`를 확장해 `Tables`를 선택한 후
마우스 오른쪽 팝업 메뉴를 이용해 `Create Table` 메뉴를 선택한 후 다음 그림과 같이
column을 설정합니다.

{% include image.html
file='database-mysql-member-table-create.png'
%}

한가지 기억할것은 `MySQL`은 기본적으로 table이름, column이름등을 모두 **소문자**로 처리한다는
것입니다. 위의 예에서 우리가 대문자로 입력한다 하더라도 내부적으로는 모두 소문자로 변경되어 처리되는걸
기억하도록 하죠. 대문자를 사용한 이유는 Readability를 높이기 위함입니다.
<br><br>

이번엔 다음 그림과 같이 `productTBL`을 생성하도록 하겠습니다.

{% include image.html
file='database-mysql-product-table-create.png'
%}

테이블이 생성되었으니 이제 샘플데이터를 입력해보도록 하겠습니다.

---

## 데이터 입력

만들어진 `memberTBL`을 선택한 후 마우스 오른쪽 팝업 메뉴를 이용해 `Select Rows - Limits 1000`을
선택한 후 `Result Grid`에서 데이터를 입력합니다. 입력을 다 하면 apply를 눌러 적용합니다.

{% include image.html
file='database-mysql-member-table-insert.png'
%}

동일한 방식으로 `productTBL`을 선택한 후 아래 그림 처럼 데이터를 입력합니다.

{% include image.html
file='database-mysql-product-table-insert.png'
%}

데이터 입력이 끝났으니 이제 데이터를 활용하는 방법에 대해서 알아보겠습니다.

---

## 데이터 활용

일반적으로 데이터를 활용한다는 의미는 SQL `SELECT` 구문을 이용해 검색하는 것을 의미합니다.
<br><br>

`Workbench` 상단 제일 왼쪽의 메뉴를 이용해 새로운 SQL Tab을 생성합니다. `Schemas`에서
특정 database를 더블클릭하면 글자 색상이 진하게 변하는데 앞으로 query창에 입력하는 SQL이
선택된 database에 적용된다는 것을 의미합니다.
<br><br>

자 그럼 몇가지 SQL문을 실습해보죠.

{% include callout.html
type="danger"
content="`SELECT * FROM productTBL;`"
%}

{% include callout.html
type="danger"
content="`SELECT memberName, memberAddress FROM memberTBL;`"
%}

{% include callout.html
type="danger"
content="`SELECT * FROM memberTBL WHERE memberName='아이유';`"
%}
 

SQL의 주석은 `--` 입니다. 반드시 `--` 뒤에 공백이 하나 있어야 주석으로 인식된다는
것도 같이 기억해야 합니다.

{% include callout.html
type="danger"
content="`-- 주석입니다.!!`"
%}

table이름에 공백을 포함해도 되지만 이런 경우는 일반적인 상황은 아닙니다.
<br><br>

아주 간단하게 `SELECT` 구문을 살펴봤는데 자세한 사항은 뒤쪽에서 다시 한번 다루기로
하겠습니다.

---

## Index

`Index`는 대부분의 책 뒤쪽에 위치한 찾아보기(색인)과 같은 개념입니다. 즉, 책의 내용 중
특정 단어를 찾고자 할 때 책의 첫페이지부터 마지막까지 전부 찾아보는 것보다는 책 뒤의
찾아보기를 먼저 보고 색인에 나와있는 페이지로 바로 찾아가는게 훨씬 빠르다는 개념을 database에
구현해 놓은 것이라고 생각하시면 됩니다.
<br><br>

데이터가 얼마 없을때는 Index를 이용하지 않아도 크게 느려지지 않지만 데이터가 많아지게되면
Index를 사용하지 않고 데이터를 검색하는데 상당히 오랜 시간이 걸리게 됩니다. 이런 Index를
적절히 적용하고 사용하고 있는지가 database 시스템의 성능을 크게 좌우하게 됩니다.
<br><br>

`Index`는 table의 column 단위에 생성됩니다. (복합 index도 존재합니다.) table에
`primary key`를 설정하면 자동으로 index가 생성다는 것도 기억해 두죠.
<br><br>

아주 간단하게 `index`의 효과를 살펴보도록 하겠습니다. 아래의 query문을 이용해 새로운
table을 하나 생성합니다.

~~~sql

create table indexTBL (
    first_name varchar(14),
    last_name varchar(16),
    hire_date date
);
 
INSERT INTO indexTBL
    SELECT first_name, last_name, hire_date
    FROM employees.employees
    LIMIT 500;

SELECt * FROM indexTBL;

~~~

`employees` database 안에 있는 `employees` table에서 500개의 record를 select해서
`indexTBL` table에 삽입하는 코드입니다. 결과를 확인해보면 총 500개의 record가 정상적으로
삽입된 것을 확인할 수 있습니다.
<br><br>

현재는 `indexTBL` table에 `index`가 설정되지 않은 상태입니다.
<br><br>

`indexTBL` table에서 first_name이 Mary인 사람을 조회해 보도록 하죠

{% include callout.html
type="danger"
content="`SELECT * FROM indexTBL WHERE first_name='Mary';`"
%}

실행 시킨 후 아래 그림처럼 `Execution Plan` 항목을 확인합니다.

{% include image.html
file='database-mysql-no-index-select.png'
%}

`Full Table Scan`이라는 글자가 보입니다. 즉, index를 사용하지 않고 table 전체를
검색했다는 의미입니다. 500건을 모두 읽어서 1개의 결과를 찾아난거죠. 데이터가 많았으면
아마 시간이 더 걸리겠죠.
<br><br>

다음의 SQL을 실행해서 index를 생성한 후 SELECT를 해 보도록 하겠습니다.

{% include callout.html
type="danger"
content="`CREATE INDEX idx_indexTBL_firstname ON indexTBL(first_name);`<br>
`SELECT * FROM indexTBL WHERE first_name='Mary';`"
%}

다음의 그림처럼 결과는 동일하지만 내부적인 작동은 index를 생성하기 전과 차이가 있습니다.
`Non-Unique Key Lookup`이라고 나오는데 이는 index를 사용해서 처리했다는 의미입니다.
지금은 데이터가 얼마 되지 않아서 체감이 안되는데 데이터가 많아지면 실제로 체감이 되겠죠.

{% include image.html
file='database-mysql-index-select.png'
%}

이런 `index`는 당연히 필요하고 잘 사용하면 좋은 성능을 끌어낼 수 있지만 잘못 사용하거나
남용하게 되면 오히려 더 않좋은 결과를 가져올 수 있습니다. 자세한 내용은 뒤에서 다시
살펴보도록 하겠습니다.

---

## View

`View`는 가상의 table이라고 생각하시면 됩니다. 사용하는 사람의 입장에서는 table과 동일하게
보이지만 실제 View는 데이터를 가지고 있지 않습니다. 진짜 table에 `Link`된 개념이라고 생각하시면
됩니다. 따라서 View를 SELECT하면 결국 진짜 table을 조회하는 것과 동일한 결과를 얻을 수
있습니다.
<br><br>

기존 table에서 일부의 데이터를 이용해 따로 table을 생성해야 하는 경우 일반적으로 View를 이용해서
처리하는 경우가 많습니다. 데이터의 중복문제 그리고 권한과 같은 문제를 해결할 수 있기 때문입니다.
<br><br>

간단하게 View를 생성해 보도록 하겠습니다.

~~~sql

CREATE VIEW v_memberTBL
AS
    SELECT memberName, memberAddress FROM memberTBL;

SELECT * FROM v_memberTBL;

~~~

{% include image.html
file='database-mysql-view-select.png'
%}

잘 생각해보면 `View`의 실체가 무엇인지 눈치챌 수 있습니다. View의 실체는 바로 `SELECT`문
입니다. 우리가 View에 접근하게 되면 View 생성 시 입력한 SELECT문이 그 때 동작하게 됩니다.

---

## Stored Procedure

`Stored Procedure`란 MySQL에서 제공하는 프로그래밍 기능을 의미합니다. 즉, SQL문
여러개를 하나로 묶어서 편리하게 사용하는 기능이라고 생각하시면 됩니다. 또한 단순히 SQL문을
묶는 역할만 하는게 아니라 일반적인 프로그래밍 언어와 같은 기능을 담당할 수 도 있습니다.
<br><br>

일반적으로 실무에서는 직접적인 SQL문을 이용하는 것보다 Stored Procedure를 만들어놓고
호출하는 방식을 이용합니다.

~~~sql

DELIMITER //
CREATE PROCEDURE myProc()
BEGIN
    SELECT * FROM memberTBL WHERE memberName='아이유';
    SELECT * FROM productTBL WHERE productName='냉장고';
END //

DELIMITER ;

~~~

위의 코드에서 `DELIMITER`는 구분문자를 의미합니다. 구분문자를 `;`에서 `//`로 변경한다는
의미이고 이렇게 하는 이유는 BEGIN부터 END까지 하나의 단락으로 묶어주기 위함입니다. 함수선언이
끝나면 다시 원래대로 `DELIMITER`를 `;`으로 돌려놔야 합니다.
<br><br>

이렇게 함수를 생성한 후 호출해보죠

{% include callout.html
type="danger"
content="`CALL myProc();`"
%}

{% include image.html
file='database-mysql-call-stored-procedure.png'
%}

---

## Trigger

`Trigger`는 table에 부착되어 table에 `INSERT`, `UPDATE`, `DELETE` 작업이
발생되면 실행되는 코드를 의미합니다.
<br><br>

간단한 예로 memberTBL에서 회원을 삭제할 때 추후에 삭제된 사람들의 명단을 알고 싶다면
삭제하기 전에 삭제대상이 된 사람의 정보를 다른 table에 저장해 놓아야 합니다. 이런 작업을
자동화 해주는 방법이 바로 Trigger라고 생각하시면 됩니다.
<br><br>

간단한 코드로 실습을 한번 진행하도록 하죠.

~~~sql

CREATE TABLE deleteMemberTBL (
    memberID CHAR(8),
    memberName CHAR(10),
    memberAddress CHAR(20),
    deletedDate DATE
);    

DELIMITER //
CREATE TRIGGER trg_deleteMemberTBL
    AFTER DELETE  -- 삭제 후 동작하게 지정
    ON memberTBL -- Triger를 부착할 table
    FOR EACH ROW
BEGIN
    INSERT INTO deleteMemberTBL VALUES (
        OLD.memberID, OLD.memberName, OLD.memberAddress, CURDATE());
END //

DELIMITER ;

~~~

위의 코드를 이용해 `deleteMemberTBL`와 `Trigger`를 생성한 후 아래의 코드로
Trigger가 정상적으로 동작하는지 확인해보도록 하죠.

~~~sql

DELETE FROM membertbl WHERE memberName = '아이유';

SELECT * FROM deletemembertbl;

~~~

{% include image.html
file='database-mysql-trigger-example.png'
%}

---

## Backup & Restore

database관리 중 가장 중요한 일 중 하나는 바로 `Backup`입니다. 불의의 사태에 대비해
데이터는 항상 주기적으로 backup을 받는것이 원칙입니다.
<br><br>

간단하게 우리 `shopdb` database를 backup하는 과정에 대해서 알아보도록 하겠습니다.
<br><br>

`Workbench`에서 하단의 `Administration` tab을 선택하면 `Data Export` 메뉴가 있고
이를 이용해서 backup을 수행합니다.

{% include image.html
file='database-mysql-backup-progress.png'
%}

{% include image.html
file='database-mysql-backup-progress-finished.png'
%}

이제 현재 사용중인 `productTBL`안의 모든 데이터를 `DELETE` 처리한 후 백업 받은 데이터를
이용해서 복구해보도록 하겠습니다.
<br><br>

먼저 다음의 SQL문으로 데이터를 삭제합니다.

{% include callout.html
type="danger"
content="`DELETE FROM productTBL;`<br>
`SELECT * FROM productTBL;`"
%}

현재 사용중인 Database를 복원하면 문제발생의 소지가 있기 때문에 현재 database를
다른 database로 변경합니다.

{% include callout.html
type="danger"
content="`USE sys;`   -- sys database로 현재 사용 database 변경"
%}

이번에는 `Data Import`를 이용해서 복원처리를 합니다.

{% include image.html
file='database-mysql-restored-progress.png'
%}


End.

{% include links.html %}
