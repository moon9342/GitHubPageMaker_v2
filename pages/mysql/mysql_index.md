---
title: MySQL Index
sidebar: mysql_sidebar
summary: "이번에는 Index에 대해 조금 더 자세히 알아보도록 하겠습니다."
permalink: mysql_index.html
folder: mysql
---

## Index

인덱스의 개념은 **책**을 예로 들어 이해하면 좋습니다. 자바책을 예로 들어보기로 하죠.
<br><br>

책에서 `Factory Method Pattern`(팩토리 메소트 패턴)이라는 용어나 나온 부분을 찾는다고 가정하면,
우리는 일반적으로 책의 맨 뒤로 가서 색인(Index)이라고 되어 있는 곳에서 알파벳 순으로 정렬되어 있는
`F`를 찾아 `Factory Method Pattern`을 쉽게 찾고 그 옆에 페이지 번호를 알아내서 해당 페이지를
펼쳐 원하는 내용을 빠른 시간내에 찾을 수 있습니다.
<br><br>

그런데 만일 책의 뒷부분에 색인(Index)이 없는 책인 경우 어떻게 해야 할까요? 어쩔 수 없습니다.
책의 첫 페이지부터 넘겨가며 확인을 해야 합니다. 운이 좋아 책의 앞부분에서 나온다 할지라도 뒤쪽에
다시 나올 수 있기 때문에 결국 책의 끝까지 확인해야 합니다.
<br><br>

색인이 있는 경우 해당 페이지 번호들이 명시되어 있으니 페이지를 왔다갔다 몇번만 하면 내용을 다
찾을 수 있는 반면 색인이 없으면 책 전체를 살펴봐야 합니다.
<br><br>

이 개념이 바로 `Index`의 개념입니다.
<br><br>

우리는 지금까지 테이블을 사용할 때 Index에 대해 크게 고려하지 않았습니다. 그 이유는 데이터의
양이 얼마 되지 않았기 때문입니다. 하지만 데이터량이 많아지면, 책으로 비유하면 책의 두께가
두꺼워지면 우리가 원하는 내용을 찾기 위해 많은 시간이 걸릴것은 자명합니다.

{% include callout.html
type="danger"
content="`Index`(인덱스)는 데이터를 좀 더 빠르게 찾을 수 있도록 해 주는 도구"
%}

라고 생각하시면 됩니다.
<br><br>

그러면 Index는 무조건 좋은 것이라는 생각이 들 수 있는데 실제로는 그렇지 않습니다. 이런 Index 역시
단점이 존재합니다.
<br><br>

예를 들어, 자바책에서 `Java`라는 글자를 찾는다고 가정해보죠. 당연히 거의 모든 페이지에서 자바라는
글자가 등장할 것이고 색인부분에 `Java`라는 단어 옆에 페이지 번호가 수백, 수천개 연속해서 나올 것입니다.
<br><br>

책에서 색인 파트는 책의 두께에 비해 한참 적어야 정상인데 이 경우 오히려 색인이 책의 페이지만큼 되거나
오히려 책의 내용보다 색인이 더 두꺼워질 수 있습니다. 또한 내용을 찾을 때 원낙 페이지수가 많다보니
계속 페이지를 왔다 갔다 해야 하는 작업을 해야 합니다. 차라리 책을 앞에서 부터 넘기면서 찾는게 오히려
더 좋지 않나 싶습니다. (데이터베이스 테이블에서 테이블의 처음부터 끝까지 검색하는 것을 우리는
`Full Table Scan`이라고 합니다.)
<br><br>

데이터베이스에서도 이와 같은 일이 발생할 수 있습니다. 필요 없는 `Index`(인덱스)를 만드는 바람에
데이터베이스가 차지하는 공간만 더 늘어나고 인덱스를 이용해서 데이터를 찾는 것이 전체 테이블을
찾는 것보다 훨씬 느려질 수 있습니다.

{% include callout.html
type="danger"
content="데이터베이스는 효율을 위해 인덱스를 생성해 놓아도, 인덱스를 사용하는 것이 빠를지 아니면 그냥
테이블 전체를 검색하는 것이 빠를지를 `MySQL`이 알아서 판단합니다."
%}

이런 `Index`(인덱스)는 데이터베이스 `Tuning`(튜닝)에 즉각적인 효과를 내는 가장 빠른 방법입니다.
Index만 잘 사용하면 우리의 데이터베이스 성능을 많이 향상 시킬 수 있다는 의미입니다.
<br><br>

인덱스를 만들어서 발생되는 가장 큰 장점은
<br><br>

* 검색 속도가 매우 빨라질 수 있습니다. (항상 그런것은 아닙니다.)
* 해당 쿼리의 부하가 줄어들어서 결국 전체 시스템의 성능이 향상될 수 있습니다.
<br><br>

반대로 인덱스로 인해 발생되는 단점은 다음과 같습니다.
<br><br>

* 인덱스가 데이터베이스 공간을 차지해서 추가적인 공간이 필요합니다. 데이터베이스 크기의 약
  10% 정도의 크기를 추가로 요구합니다.
* 처음 인덱스를 생성하는데 시간이 많이 소요될 수 있습니다.
* 데이터의 변경작업이 빈번할 경우 오히려 성능이 많이 나빠질 수 있습니다.

---

## Index의 종류

`MySQL`에서 사용되는 Index의 종류는 다음과 같이 크게 두가지로 나누어 집니다.
<br><br>

* `Clustered Index`(클러스터형 인덱스)
* `Secondary Index`(보조 인덱스). 다른 DBMS에서는 `NonClustered Index`라는
  표현을 쓰기도 합니다.
<br><br>

책으로 비유를 하자면 클러스터형 인덱스는 **영어 사전**과 같은 책이고 보조 인덱스는
책 뒤에 있는 색인이 있는 책이라고 할 수 있습니다.
<br><br>

먼저 기억해야 하는 것은 클러스터형 인덱스는 테이블 당 한개만 생성할 수 있고
보조 인덱스는 테이블 당 여러 개를 생성할 수 있다는 것입니다. 그리고 클러스터형
인덱스는 행 데이터를 인덱스로 지정할 열에 맞춰서 자동으로 정렬하게 됩니다.
<br><br>

그럼 이제 테이블에 적용되는 인덱스를 예를 들어 살펴보기로 하겠습니다.
<br><br>

`Index`는 테이블의 `column`단위에 생성됩니다. 하나의 열에 생성할 수 있고
여러 열에 하나의 인덱스를 생성할 수도 있습니다.
<br><br>

이전에 실습한 `sqldb`의 `usertbl`을 예로 들어 보기로 하죠.

{% include image.html
file='database-mysql-index-usertbl.png'
%}

만약 열 하나당 하나의 index를 설정한다면 이 테이블에는 총 8개의 서로 다른 인덱스를
생성할 수 있습니다.
<br><br>

이 테이블을 생성할 때 `userID` 컬럼을 `PRIMARY KEY`로 지정해서 테이블을 생성했는데
이렇게 `PRIMARY KEY`로 지정하면 자동으로 클러스터형 인덱스가 해당 열에 생성되게 됩니다.
<br><br>

클러스터형 인덱스튼 테이블 당 한개만 존재할 수 있다고 했는데 `PRIMARY KEY` 역시 테이블 당
하나만 존재할 수 있습니다.
<br><br>

아래의 코드를 가지고 추가적인 설명을 더 이어나가겟습니다.

~~~sql

USE sqldb;

CREATE TABLE tbl1 (
    a   INT PRIMARY KEY,
    b   INT,
    c   INT
);    

SHOW INDEX FROM tbl1;

~~~

{% include image.html
file='database-mysql-index-tbl1-show-index.png'
%}

`SHOW INDEX` 구문을 이용해 인덱스를 확인하면 위의 그림처럼 보입니다. 여기서
<br><br>

* `Non_unique` : 0이면 Unique index, 1이면 Nonunique index를 의미합니다.
* `Key_name` : index_name과 같은 의미로 인덱스의 이름입니다. `PRIMARY`로 지정되어
  있으면 `Clustered Index`(클러스터형 인덱스)로 보면 됩니다. 보조 인덱스는 이 부분에
  열 이름 혹은 키의 이름이 적혀 있습니다.
* `Seq_in_index` : 해당 열에 여러 인덱스가 설정되었을 때 순서를 나타냅니다. 대부분 1.
* `Null` : Null값의 허용여부입니다. 비어있으면 NO를 의미합니다.
* `Index_type` : 어떤 형태로 인덱스가 구성되었는지를 나타냅니다. `MySQL`은 `B-TREE`
  구조를 가집니다.
<br><br>

이번에는 `PRIMARY KEY`와 `UNIQUE` 제약조건도 이용해서 테이블을 생성해 보죠.

~~~sql

CREATE TABLE tbl2 (
    a   INT PRIMARY KEY,
    b   INT UNIQUE,
    c   INT UNIQUE,
    d   INT
);    

SHOW INDEX FROM tbl2;

~~~

{% include image.html
file='database-mysql-index-tbl1-show-index-unique.png'
%}

`UNIQUE` 제약조건으로 설정해서 여러 개의 보조 인덱스가 자동으로 생성되는 것을 확인할 수 있습니다.
<br><br>

이번에는 `PRIMARY KEY` 없이 `UNIQUE`만으로 지정해 보죠.

~~~sql

CREATE TABLE tbl3 (
    a   INT UNIQUE,
    b   INT UNIQUE,
    c   INT UNIQUE,
    d   INT
);    

SHOW INDEX FROM tbl3;

~~~

{% include image.html
file='database-mysql-index-tbl1-show-index-unique-2.png'
%}

이번에는 모두 보조 인덱스로 지정된 것을 확인할 수 있습니다.
<br><br>

이번에는 `UNIQUE`에 `NOT NULL`을 추가해 `Clustered Index`(클러스터형 인덱스)로
지정해 보겠습니다.

~~~sql

CREATE TABLE tbl4 (
    a   INT UNIQUE NOT NULL,
    b   INT UNIQUE,
    c   INT UNIQUE,
    d   INT
);    

SHOW INDEX FROM tbl4;

~~~

{% include image.html
file='database-mysql-index-tbl1-show-index-unique-3.png'
%}

a 컬럼은 `Non_unique`값이 0이기 때문에 `Unique` 인덱스이고 `Null`값이 비어있기 때문에
`Not Null`이 됩니다. 따라서 a 컬럼은 클러스터형 인덱스로 지정되게 됩니다.
<br><br>

만약 `UNIQUE NOT NULL`과 `PRIMARY KEY`가 동시에 지정되어 있으면 어떻게 될까요?

~~~sql

CREATE TABLE tbl5 (
    a   INT UNIQUE NOT NULL,
    b   INT UNIQUE,
    c   INT UNIQUE,
    d   INT PRIMARY KEY
);    

SHOW INDEX FROM tbl5;

~~~

{% include image.html
file='database-mysql-index-tbl1-show-index-unique-4.png'
%}

이런 경우 d 컬럼이 클러스터형 인덱스가 되고 a 컬럼은 보조 인덱스가 생성됩니다. 클러스터 인덱스는
테이블당 하나밖에 지정되지 않으므로 `PRIMARY KEY`로 설정한 열에 클러스터형 인덱스가 생성됩니다.
<br><br>

클러스터형 인덱스는 행 데이터를 자신의 열의 기준으로 정렬합니다. 실습으로 확인하는것이 가장
이해하기 좋으니 다음의 코드를 이용해 직접 확인해 보도록 하겠습니다.

~~~sql

CREATE DATABASE IF NOT EXISTS testdb;

USE testdb;

DROP TABLE IF EXISTS usertbl;

CREATE TABLE usertbl (
    userID    CHAR(8) NOT NULL PRIMARY KEY,
    name      VARCHAR(10) NOT NULL,
    birthYear INT NOT NULL,
    addr      CHAR(2) NOT NULL
);    

INSERT INTO usertbl VALUES('LSG', '이승기', 1987, '서울');
INSERT INTO usertbl VALUES('KBS', '김범수', 1979, '경남');
INSERT INTO usertbl VALUES('KKH', '김경호', 1971, '전남');
INSERT INTO usertbl VALUES('JYP', '조용필', 1950, '경기');
INSERT INTO usertbl VALUES('SSK', '성시경', 1979, '서울');

SELECT * FROM usertbl;

-- JYP	조용필	1950	경기
-- KBS	김범수	1979	경남
-- KKH	김경호	1971	전남
-- LSG	이승기	1987	서울
-- SSK	성시경	1979	서울

-- userID에 클러스터형 인덱스가 설정되어 있기 때문에 입력되는 즉시
-- userID로 데이터를 정렬한다.

ALTER TABLE usertbl 
DROP PRIMARY KEY;

ALTER TABLE usertbl
    ADD CONSTRAINT pk_name PRIMARY KEY(name);

SELECT * FROM usertbl;

-- KKH	김경호	1971	전남
-- KBS	김범수	1979	경남
-- SSK	성시경	1979	서울
-- LSG	이승기	1987	서울
-- JYP	조용필	1950	경기
	
-- name 열로 다시 정렬된 것을 확인할 수 있다.

~~~

---

## Index의 내부작동

`Index`는 내부적은 `B-Tree`(Balanced Tree, 균형트리)를 이용합니다. `B-Tree`는
자료구조에 나오는 범용적인 데이터 구조인데 인덱스를 구현할 때 많이 이용합니다.
<br><br>

아래의 그림은 `B-Tree`의 기본 구조입니다.

{% include image.html
file='database-mysql-index-btree-basic.png'
%}

`Node`(노드)란 트리 구조에서 데이터가 존재하는 공간을 말합니다. 위의 그림에는 노드가 4개가
존재합니다. `Root Node`(루트 노드)란 노드의 가장 상위 노드를 의미합니다. 모든 출발은 이
`Root Node`에서 시작됩니다. `Leaf Node`는 제일 마지막에 존재하는 노드를 의미하며 데이터가
많다면 중간에 `Intermediate Node`가 존재하게 됩니다.
<br><br>

`MySQL`에서는 `Node`에 해당하는 개념이 `Page`입니다. `Page`란 16Kb 크기의 최소한의 저장 단위로
아무리 작은 데이터를 한 개만 저장하더라도 한 개 페이지(16Kb)를 차지한게 된다는 의미입니다.
<br><br>

이런 `B-Tree` 구조는 데이터를 검색할 때 아주 뛰어난 성능을 발휘합니다. 만약 위의 그림에서 `MMM`이라는
데이터를 검색한다고 가정하고 `B-Tree`를 사용하지 않는다면 모든 `Page`를 다 검색해야 하고 처음부터
검색해서 총 3개의 `Page`를 거쳐 8건의 데이터(AAA~MMM)를 검색해야 그 결과를 알 수 있습니다.
<br><br>

하지만 `B-Tree`구조를 사용한다면 루트노드의 3개의 데이터를 읽고 3번째 `Page`에서 LLL,MMM 두개의
데이터를 읽으면 MMM을 찾을 수 있습니다. 즉, 2개의 `Page`를 거쳐 5건의 데이터를 검색하면 그 결과를
알 수 있게 됩니다.
<br><br>

하지만 Index를 구성해서 데이터를 `B-Tree`로 구성해 놓으면 데이터의 변경 작업 시 성능이 나빠질 수
있습니다. 그 이유는 **페이지 분할** 이라는 작업이 발생하기 때문입니다.
<br><br>

그림으로 이해하면 좋습니다.
<br><br>

위의 `B-Tree` 구조에 `III` 데이터를 삽입하면 다음 그림과 같습니다.

{% include image.html
file='database-mysql-index-btree-insert-1.png'
%}

아직까지는 큰 문제가 없습니다. 공간이 남아있었기 때문이지요. 정렬이 되어야 하기 때문에
`JJJ`가 한칸 이동했을 뿐 큰 작업이 발생하지는 않았습니다.
<br><br>

이번에는 `GGG`를 입력해 보도록 하죠. 다음 그림과 같은 현상이 발생합니다.

{% include image.html
file='database-mysql-index-btree-insert-2.png'
%}

데이터를 한 개만 추가했는데 상당히 복잡한 작업이 이루어지게 되고 이로 인해 부가적인
`Overhead`가 발생한다는 것을 알 수 있습니다. 더 복잡한 예는 자료구조시간에 배우기로 하죠.

---

## 클러스터형 인덱스와 보조 인덱스

그럼 이번에는 클러스터형 인덱스와 보조 인덱스의 구조는 어떻게 다른지 알아보겠습니다.
<br><br>

먼저 인덱스가 없이 테이블을 생성하고 다음과 같이 데이터를 입력합니다.

~~~sql

CREATE DATABASE IF NOT EXISTS testdb;

USE testdb;

DROP TABLE IF EXISTS clustertbl;

CREATE TABLE clustertbl (
    userID    CHAR(8),
    name      VARCHAR(10)
);

INSERT INTO clustertbl VALUES('LSG', '이승기');
INSERT INTO clustertbl VALUES('KBS', '김범수');
INSERT INTO clustertbl VALUES('KKH', '김경호');
INSERT INTO clustertbl VALUES('JYP', '조용필');
INSERT INTO clustertbl VALUES('SSK', '성시경');
INSERT INTO clustertbl VALUES('LJB', '임재범');
INSERT INTO clustertbl VALUES('YJS', '윤종신');
INSERT INTO clustertbl VALUES('EJW', '은지원');
INSERT INTO clustertbl VALUES('JKW', '조관우');
INSERT INTO clustertbl VALUES('BBK', '바비킴');

~~~

만약, 페이지당 4개의 행이 입력된다고 가정하면 이 데이터는 다음의 그림과 같이
구성되어 있을 것입니다. `MySQL`의 기본 페이지 크기는 16KB이기 때문에
한 페이지에 훨씬 많은 데이터가 들어갑니다. 지금은 예를 드는 것이기 때문에
한페이지에 4개의 행이 들어간다고 가정하는 것입니다. 참고로 `MySQL`의 페이지
크기는 다음의 구문으로 확인할 수 있으며 필요하다면 변경할 수 있습니다.

{% include callout.html
type="danger"
content="`SHOW VARIABLES LIKE 'innodb_page_size';`"
%}

{% include image.html
file='database-mysql-index-difference-1.png'
%}

`SELECT` 구문을 이용해 정렬된 순서만 확인해보도록 하겠습니다. `Index`가 존재하지
않기 때문에 입력된 순서와 동일한 순서로 보이게 될 것입니다.

~~~sql

SELECT * FROM clustertbl;    

-- userID, name
-- LSG	이승기
-- KBS	김범수
-- KKH	김경호
-- JYP	조용필
-- SSK	성시경
-- LJB	임재범
-- YJS	윤종신
-- EJW	은지원
-- JKW	조관우
-- BBK	바비킴

~~~

이제 이 테이블의 userID에 클러스터형 인덱스를 구성해 보겠습니다. userID를
`PRIMARY KEY`로 지정하면 클러스터형 인덱스로 구성됩니다.

~~~sql

ALTER TABLE clustertbl
    ADD CONSTRAINT PK_clustertbl_userID
        PRIMARY KEY (userID);
        
SELECT * FROM clustertbl;    

-- userID, name
-- BBK	바비킴
-- EJW	은지원
-- JKW	조관우
-- JYP	조용필
-- KBS	김범수
-- KKH	김경호
-- LJB	임재범
-- LSG	이승기
-- SSK	성시경
-- YJS	윤종신

~~~

당연히 userID로 오름차순 정렬되어 있게 됩니다. 그림으로 표현하면 다음 그림처럼
`B-Tree` 형태의 인덱스가 형성됩니다.

{% include image.html
file='database-mysql-index-difference-2.png'
%}

클러스터형 인덱스를 구성하기 위해 행 데이터를 해당 열로 정렬한 후에 루트 페이지를 만듭니다.
클러스터형 인덱스는 영어사전과 같다는 표현을 위에서 했었는데 영어사전은 책 자체가 알파벳
순서의 찾아보기로 구성되어 있기 때문에 찾아보기의 끝(Leaf Level)이 바로 영어 단어(데이터 페이지)가 됩니다.
<br><br>

이번에는 userID를 보조 인덱스로 만들어보겠습니다.

~~~sql

CREATE DATABASE IF NOT EXISTS testdb;

USE testdb;

DROP TABLE IF EXISTS secondarytbl;

CREATE TABLE secondarytbl (
    userID    CHAR(8),
    name      VARCHAR(10)
);

INSERT INTO secondarytbl VALUES('LSG', '이승기');
INSERT INTO secondarytbl VALUES('KBS', '김범수');
INSERT INTO secondarytbl VALUES('KKH', '김경호');
INSERT INTO secondarytbl VALUES('JYP', '조용필');
INSERT INTO secondarytbl VALUES('SSK', '성시경');
INSERT INTO secondarytbl VALUES('LJB', '임재범');
INSERT INTO secondarytbl VALUES('YJS', '윤종신');
INSERT INTO secondarytbl VALUES('EJW', '은지원');
INSERT INTO secondarytbl VALUES('JKW', '조관우');
INSERT INTO secondarytbl VALUES('BBK', '바비킴');

ALTER TABLE secondarytbl
    ADD CONSTRAINT UK_secondarytbl_userID
        UNIQUE (userID);
        
SELECT * FROM secondarytbl;    

-- userID, name
-- LSG	이승기
-- KBS	김범수
-- KKH	김경호
-- JYP	조용필
-- SSK	성시경
-- LJB	임재범
-- YJS	윤종신
-- EJW	은지원
-- JKW	조관우
-- BBK	바비킴

~~~

데이터를 입력한 순서와 변함이 없는 것을 확인할 수 있습니다. 내부적으로는
다음의 그림과 같이 구성됩니다.

{% include image.html
file='database-mysql-index-difference-3.png'
%}

위의 그림을 보면 보조 인덱스는 데이터 페이지를 건드리지 않고 별도의 장소에 인덱스
페이지를 생성합니다.
<br><br>

인덱스 페이지의 리프 페이지에 인덱스로 구성한 열을 정렬합니다. 그리고 데이터 위치
포인터를 생성합니다. 데이터의 위치 포인터는 클러스터형 인덱스와 달리 주소값이 기록되어
바로 데이터의 위치를 가리키게 됩니다.
<br><br>

이제 `SELECT`문을 하나 실행했을 때 어떻게 되는지를 살펴보도록 하죠.
<br><br>

만약 `JKW`(조관우)를 검색한다면 클러스터형 인덱스는 2개의 페이지를 읽게 됩니다. 하지만
보조 인덱스를 이용할 경우 루트 페이지, 리프 페이지, 데이터 페이지를 읽게 되어 총 3개의
페이지를 읽게 됩니다. 즉, 검색 자체는 일반적으로 클러스터형 인덱스가 우수합니다.
<br><br>

이 상황에서 데이터를 입력해보죠.

~~~sql

INSERT INTO clustertbl VALUES('FNT', '푸니타');
INSERT INTO clustertbl VALUES('KAI', '카아이');

~~~

클러스터형 인덱스인 경우 첫번째 리프 페이지(데이터 페이지)에서 페이지 분할이 일어납니다.
아래 그림과 같게 됩니다.

{% include image.html
file='database-mysql-index-difference-4.png'
%}

이번에는 보조 인덱스인 경우 데이터를 입력했을 때 어떻게 되는지를 살펴보겠습니다.
보조 인덱스는 데이터 페이지를 정렬하는게 아니라는 점에 주의하세요. 그리고 약간은
일부러 그 차이를 보이게 만들기 위해 예시를 들고 있다는 것도 감안해주세요.

{% include image.html
file='database-mysql-index-difference-5.png'
%}

살펴본 것처럼 보조 인덱스를 이용할 경우 클러스터형 인덱스를 이용하는 것보다 데이터 입력에서는
성능에 주는 부하가 더 적음을 알 수 있습니다.
<br><br>

간단하게 정리를 한 번 하고 넘어가겠습니다.
<br><br>

* 클러스터형 인덱스

{% include callout.html
type="danger"
content="`데이터 페이지 전체가 정렬됩니다. 그러므로 이미 대용량의 데이터가 입력된 상태라면
클러스터 인덱스를 생성하는 것은 상당히 많은 부하를 시스템에 줄 수 있습니다.<br><br>
클러스터형 인덱스는 인덱스 자체의 리프 페이지가 곧 데이터 입니다. 따라서 인덱스에
데이터가 포함되어 있다고 볼 수 있습니다.<br><br>
클러스터형 인덱스는 보조 인덱스보다 검색속도는 더 빠릅니다. 하지만 데이터의 입력/수정/삭제
같은 작업은 더 느립니다.<br><br>
클러스터형 인덱스는 성능이 좋지만 테이블에 한 개만 생성할 수 있습니다. 그러므로 어떤 열에
클러스터형 인덱스를 생성하는지에 따라 시스템의 성능이 달라질 수 있습니다.
"
%}

* 보조 인덱스

{% include callout.html
type="danger"
content="보조 인덱스의 생성 시에는 데이터 페이지는 그냥 둔 상태에서 별도의 페이지에 인덱스를
구성합니다.<br><br>
보조 인덱스는 인덱스 자체의 리프 페이지가 데이터가 아니라 데이터가 위치하는 주소값입니다.
따라서 클러스터형보다 검색속도는 더 느리지만 데이터의 입력/수정/삭제는 덜 느립니다.<br><br>
보조 인덱스는 여러 개 생성할 수 있습니다. 하지만 남용할 경우 오히려 시스템 성능을
떨어뜨리는 결과를 초래할 수 있으므로 꼭 필요한 열에만 생성하는 것이 좋습니다."
%}

---

## 인덱스의 혼합

그러면 클러스터형 인덱스와 보조 인덱스를 혼합해서 사용하는 경우는 어떻게 될까요? 현실적으로
이런 경우가 더 많습니다.
<br><br>

코드로 보자면 아래처럼 진행하면 됩니다.

~~~sql

CREATE DATABASE IF NOT EXISTS testdb;

USE testdb;

DROP TABLE IF EXISTS mixedtbl;

CREATE TABLE mixedtbl (
    userID    CHAR(8) NOT NULL,
    name      VARCHAR(10) NOT NULL,
    addr      CHAR(2)
);

INSERT INTO mixedtbl VALUES('LSG', '이승기','서울');
INSERT INTO mixedtbl VALUES('KBS', '김범수','경남');
INSERT INTO mixedtbl VALUES('KKH', '김경호','전남');
INSERT INTO mixedtbl VALUES('JYP', '조용필','경기');
INSERT INTO mixedtbl VALUES('SSK', '성시경','서울');
INSERT INTO mixedtbl VALUES('LJB', '임재범','서울');
INSERT INTO mixedtbl VALUES('YJS', '윤종신','경남');
INSERT INTO mixedtbl VALUES('EJW', '은지원','경북');
INSERT INTO mixedtbl VALUES('JKW', '조관우','경기');
INSERT INTO mixedtbl VALUES('BBK', '바비킴','서울');

~~~

만약 페이지당 4개의 행이 입력된다고 가정하면 이 데이터는 다음과 같이 구성되어
있게됩니다. (현재는 Index가 없는 상태입니다.)

{% include image.html
file='database-mysql-index-mixed-1.png'
%}

이 테이블의 `userID`에 클러스터형 인덱스를 먼저 생성해보죠. `PRIMARY KEY`로 지정하면
됩니다.

~~~sql

ALTER TABLE mixedtbl
    ADD CONSTRAINT PK_mixedtbl_userID
        PRIMARY KEY (userID);

~~~

당연히 다음 그림처럼 리프 페이지(데이터 페이지)가 정렬되어 구성된다는 것을 알 수 있습니다.

{% include image.html
file='database-mysql-index-mixed-2.png'
%}

이번에는 `UNIQUE` 제약 조건으로 보조 인덱스를 추가하겠습니다.

~~~sql

ALTER TABLE mixedtbl
    ADD CONSTRAINT UK_mixedtbl_name
        UNIQUE (name);

SHOW INDEX FROM mixedtbl;        

~~~

그림에서 보는 것처럼 클러스터형 인덱스와 보조 인덱스가 생성된 것을 확인할 수 있습니다.

{% include image.html
file='database-mysql-index-mixed-3.png'
%}

자 그럼 이제 두 인덱스가 혼합된 구조가 어떻게 되는지 그림으로 살펴보죠. 클러스터형
인덱스는 변함이 없습니다. 대신 보조 인덱스가 좀 다릅니다.
<br><br>

보조 인덱스 같은 경우 `name`(이름)으로 정렬이 되어야 합니다. 여기까지는 똑같은데
보조 인덱스의 리프 페이지가 데이터 페이지의 주소값을 가지지 않고 클러스터형 인덱스의
키값을 가지게 됩니다. 즉, 보조 인덱스를 검색하면 아래 그림에 표현한 것처럼 클러스터형
인덱스의 루트 페이지부터 검색한다는 것입니다.
<br><br>

왜 데이터 페이지의 주소값을 가지지 않을까? 클러스터형 인덱스와 보조 인덱스가 혼합되어
있는 경우 만약 데이터의 순서가 변동이 된다면 데이터 페이지의 주소값이 대폭 변동될
가능성이 있기 때문입니다. 그래서 오히려 성능이 더 않좋아 질 수 있기 때문에 아래
그림과 같은 구조를 가지게 된 것입니다.

{% include image.html
file='database-mysql-index-mixed-4.png'
%}

---

## Index의 생성/삭제

지금까지는 제약조건을 이용해 자동으로 생성되는 인덱스를 사용했는데 이번에는 인덱스를
직접 생성하는 구문에 대해서 알아보겠습니다.
<br><br>

기억해야 할 점은 `CREATE INDEX` 구문으로 생성되는 index는 모두 보조 인덱스가 된다는
것입니다. 인덱스를 생성할 때 `UNIQUE` 옵션을 줄 수있는데 이 경우 고유 인덱스를 만들겠다는
의미입니다. 즉, 동일한 값이 들어올 수 없다는 의미이며 디폴트는 중복이 허용되는 인덱스 입니다.
<br><br>

간단한 예를 보면 다음과 같습니다.

~~~sql

CREATE INDEX idx_usertbl_addr
    ON usertbl (addr);
    
DROP INDEX idx_usertbl_addr ON usertbl;    

~~~

인덱스를 생성하고 삭제하는 간단한 구문만 알아두시면 될 듯 합니다.


End.

{% include links.html %}
