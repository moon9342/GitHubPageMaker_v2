---
title: MySQL SQL 기본
sidebar: mysql_sidebar
summary: "우리가 살펴본 MySQL이 사용하는 SQL(Structured Query Language)은 대부분의 DBMS에 
적용되는 ANSI-92/99 SQL의 내용을 포함하면서 MySQL의 특징을 반영하는 내용이 포함된 확장된 SQL입니다.
일단은 기본적인 4개의 SQL 구문(SELECT, INSERT, UPDATE, DELETE)부터 알아보도록 하겠습니다."
permalink: mysql_basic-sql.html
folder: mysql
---

## SQL - SELECT

`SELECT`문은 가장 많이 사용하는 SQL 구문입니다. 데이터베이스 내의 테이블에서 원하는 정보를 추출하기
위한 구문입니다.
<br><br>

사실 `SELECT`의 구문 형식은 상당히 복잡합니다. MySQL 8.0의 도움말을 보면 알 수 있는데 처음부터 이렇게
복잡한 내용으로 설명하면 너무 어려우니 조금 쉽게 접근해 보겠습니다.
<br><br>

`SELECT`는 다양한 옵션으로 전체 구문은 상당히 복잡하지만 일반적으로는 다음의 형태로
많이 사용됩니다.

~~~sql

SELECT select_expr
    FROM table_references
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}]
    [HAVING where_condition]
    [ORDER BY {col_name | expr | position}] 

~~~

SELECT 구문을 실습하기 위해 먼저 사용할 데이터베이스를 지정해야 합니다. 다음의 명령을 이용해
데이터베이스를 지정합니다.

{% include callout.html
type="danger"
content="`USE employees;`"
%}

SQL은 다른 프로그래밍 언어와는 다르게 대소문자를 구분하지 않습니다. 하지만 일반적으로
`Readability` 측면에서 예약어는 모두 대문자로 사용자 정의어는 대소문자를 섞어 사용하는게 좋습니다.
<br><br>

`Workbench`를 이용해 테이블을 조회합니다. 쿼리의 결과 개수의 제한이 없도록 위쪽에서 행 개수의
제한을 `Don't Limit`로 설정합니다.

{% include callout.html
type="danger"
content="`SELECT * FROM titles;`"
%}

쿼리를 실행하면 창 아래쪽 `Output` 영역에 쿼리의 실행상태, 실행한 쿼리의 순번, 실행된 쿼리문,
결과에 대한 메시지, 걸린시간(`Duration`은 SQL이 실행되는데 걸리는 시간, `Fetch`는 데이터를
테이블에서 가져오는데 걸린 시간입니다.)이 표시됩니다.

{% include image.html
file='database-mysql-sql-basic-select-1.png'
%}

SELECT 구문에서 `*`의 의미는 모든 `column`(열)을 의미합니다. 또한 `FROM` 키워드 뒤에는
원칙적으로 **데이터베이스이름.테이블이름** 형식으로 기술해야 합니다. 하지만 우리는 현재
`employees` 데이터베이스를 사용하고 있는 중이기 때문에 데이터베이스 이름을 생략하고 사용한
것입니다.
<br><br>

즉, 원칙적으로는

{% include callout.html
type="danger"
content="`SELECT * FROM employees.titles;`"
%}

처럼 사용해야 합니다.
<br><br>

만약 모든 column이 아닌 특정 column의 정보만을 SELECT 하려면 다음과 같이 column명을
`,`로 구분하여 명시하면 됩니다.

{% include callout.html
type="danger"
content="`SELECT first_name, last_name FROM employees;`"
%}

`Remark`(주석)은 이전에 설명한 것처럼 `--`로 1줄 주석을 표현합니다. 여러 줄 주석은
`/* */`로 묶으면 됩니다.
<br><br>

현재 서버에 어떤 데이터 베이스가 있는지를 조회하려면 다음의 구문을 이용하시면 됩니다.

{% include callout.html
type="danger"
content="`SHOW DATABASES;`"
%}

데이터베이스를 사용하려면 앞서 설명한 것처럼 다음의 구문 처럼 이용하시면 됩니다.

{% include callout.html
type="danger"
content="`USE employees;`"
%}

현재 데이터베이스에 있는 테이블의 정보를 조회하려면 다음의 구문을 이용하시면 됩니다.

{% include callout.html
type="danger"
content="`SHOW TABLE STATUS;`"
%}

특정 테이블의 상세를 보려면 다음의 `DESC` 구문을 이용하시면 됩니다.

{% include callout.html
type="danger"
content="`DESC employees;`"
%}

{% include image.html
file='database-mysql-sql-basic-desc-table.png'
%}

테이블을 SELECT를 이용해서 조회할 때 column명에 대해서 별칭(`Alias`)을 지정할 수 있습니다.
`AS` keyword를 이용하는데 만약 별칭에 공백이 있다면 `''`을 이용해 처리해야 합니다. 하지만
가능한 별칭에는 공백과 같은 특수문자를 사용하지 않는게 좋습니다.
<br><br>

` Alias`는 결과를 보기 편하게 하기 위해서 그리고 계산식에 의해서 복잡한 형태의 열이름이 될 때
사용하면 좋습니다.

~~~sql

SELECT first_name AS 이름, gender AS 성별, hire_date AS 입사일
FROM employees;

~~~

{% include image.html
file='database-mysql-sql-basic-alias.png'
%}


그럼 이제 조건을 지정하는 `WHERE`에 대해서 알아보도록 하겠습니다. 기존의 employees를
이용해도 되지만 테이블의 구조가 복잡하고 데이터가 많기 때문에 예제로 사용하기에는 한눈에
결과가 들어오지 않습니다. 해서 다음과 같은 구조의 데이터베이스를 하나 생성해서 사용하도록
하겠습니다.

{% include image.html
file='database-mysql-sql-basic-sqldb-database.png'
%}

다음의 코드를 이용해 데이터베이스와 테이블을 생성하겠습니다.

~~~sql

DROP DATABASE IF EXISTS sqldb;   -- 만약 sqldb가 존재하면 삭제.

CREATE DATABASE sqldb;  -- 데이터베이스 생성

USE sqldb;  -- sqldb 사용

CREATE TABLE usertbl
( userID    CHAR(8) NOT NULL PRIMARY KEY,  -- 사용자 ID(PK)
  name      VARCHAR(10) NOT NULL, -- 이름
  birthYear INT NOT NULL, -- 출생연도
  addr      CHAR(2) NOT NULL, -- 지역(경기, 서울, 경남, etc)
  mobile1   CHAR(3), -- 휴대폰 국번(010)
  mobile2   CHAR(8), -- 휴대폰 나머지 전화번호(하이픈제외)
  height    SMALLINT, -- 키
  mDate     DATE -- 회원가입일
);

CREATE TABLE buytbl
( num       INT AUTO_INCREMENT NOT NULL PRIMARY KEY,  -- 순번(PK)
  userID    CHAR(8) NOT NULL, -- 아이디(FK)
  prodName  CHAR(6) NOT NULL, -- 물품명
  groupName CHAR(4), -- 분류
  price     INT NOT NULL, -- 단가
  amount    SMALLINT NOT NULL, -- 수량
  FOREIGN KEY (userID) REFERENCES usertbl(userID)
);  

~~~

몇가지 사항만 살펴보도록 하겠습니다.
<br><br>

먼저 문자열을 표현하기 위한 데이터타입으로 `CHAR`와 `VARCHAR`가 보이는데
일부 다른 DBMS에서는 이 둘에 대해 영문자를 기준으로 1 Byte를 할당하고
`NCHAR`와 `NVARCHAR`는 유니코드를 기준으로 2 Bytes를 할당합니다.
그래서 한글을 입력하기 위해 유니코드를 사용해야 하기 때문에 `NCHAR` 혹은 `NVARCHAR`를
사용합니다.
<br><br>

하지만 우리가 사용하는 `MySQL 8.0`은 `CHAR`와 `VARCHAR` 모두 2 Byte 체계인
유니코드(`UTF-8`)를 사용합니다. `UTF-8`은 영문, 숫자, 기호를 입력하면 내부적으로
1 Byte를 할당하고 한글/중국어/일본어 같은 같은 경우 내부적으로 3 Byte로 할당합니다.
<br><br>

결론적으로 `CHAR(10)`으로 설정하면 영문이든 한글이든 최대 10글자까지 입력할 수 있으며,
내부적으로 MySQL이 데이터 공간을 할당하기 때문에 사용자는 특별히 신경쓰지 않아도 됩니다.
<br><br>

또한 데이터베이스 개체들의 이름을 우리는 특별히 `Identifier`(식별자)라고 합니다.
쉽게 말하면 데이터베이스 이름, 테이블 이름, 컬럼명 등을 의미합니다. 이런 식별자를
사용할 때 준수해야 하는 몇가지 규칙이 있습니다. 그 중 중요한 것 몇가지만 기억하도록 하죠.
<br><br>

* `Identifier`는 최대 64자로 제한됩니다.
* 당연히 예약어는 사용할 수 없습니다.
* 중간에 공백이 없어야 합니다. 공백을 사용하려면 `''`을 이용할 수 있지만 가능한
  사용하지 않는게 좋습니다.
* Linux에서는 데이터베이스 이름과 테이블 이름은 반드시 소문자로 사용해야 합니다. 따라서
  Windows 시스템과 호환을 유지하기 위해 개체 이름을 모두 소문자로 사용하는 것이 바람직합니다.
<br><br>

이제 아래의 코드를 이용해 데이터를 입력합니다.

~~~sql

INSERT INTO usertbl VALUES('LSG', '이승기', 1987, '서울', '011', '1111111', 182, '2008-8-8');
INSERT INTO usertbl VALUES('KBS', '김범수', 1979, '경남', '011', '2222222', 173, '2012-4-4');
INSERT INTO usertbl VALUES('KKH', '김경호', 1971, '전남', '019', '3333333', 177, '2007-7-7');
INSERT INTO usertbl VALUES('JYP', '조용필', 1950, '경기', '011', '4444444', 166, '2009-4-4');
INSERT INTO usertbl VALUES('SSK', '성시경', 1979, '서울', NULL, NULL, 186, '2013-12-12');
INSERT INTO usertbl VALUES('LJB', '임재범', 1963, '서울', '016', '6666666', 182, '2009-9-9');
INSERT INTO usertbl VALUES('YJS', '윤종신', 1969, '경남', NULL, NULL, 170, '2005-5-5');
INSERT INTO usertbl VALUES('EJW', '은지원', 1972, '경북', '011', '8888888', 174, '2014-3-3');
INSERT INTO usertbl VALUES('JKW', '조관우', 1965, '경기', '018', '9999999', 172, '2010-10-10');
INSERT INTO usertbl VALUES('BBK', '바비킴', 1973, '서울', '010', '0000000', 176, '2013-5-5');

INSERT INTO buytbl VALUES(NULL, 'KBS', '운동화', NULL , 30, 2);
INSERT INTO buytbl VALUES(NULL, 'KBS', '노트북', '전자', 1000, 1);
INSERT INTO buytbl VALUES(NULL, 'JYP', '모니터', '전자', 200, 1);
INSERT INTO buytbl VALUES(NULL, 'BBK', '모니터', '전자', 200, 5);
INSERT INTO buytbl VALUES(NULL, 'KBS', '청바지', '의류', 50, 3);
INSERT INTO buytbl VALUES(NULL, 'BBK', '메모리', '전자', 80, 10);
INSERT INTO buytbl VALUES(NULL, 'SSK', '책', '서적', 15, 5);
INSERT INTO buytbl VALUES(NULL, 'EJW', '책', '서적', 15, 2);
INSERT INTO buytbl VALUES(NULL, 'EJW', '청바지', '의류', 50, 1);
INSERT INTO buytbl VALUES(NULL, 'BBK', '운동화', NULL , 30, 2);
INSERT INTO buytbl VALUES(NULL, 'EJW', '책', '서적', 15, 1);
INSERT INTO buytbl VALUES(NULL, 'BBK', '운동화', NULL, 30, 2);

SELECT * FROM usertbl;
SELECT * FROM buytbl;

~~~

{% include image.html
file='database-mysql-sql-basic-sqldb-data-insert.png'
%}

`SELECT`한 결과가 입력된 순서로 나오지 않는 이유는 `userID`를 `PRIMARY KEY`로 지정했기
때문에 자동으로 `INDEX`가 생성되어 입력시에 `userID`열로 정렬되기 때문입니다.
<br><br>

자 여기까지 진행했으면 현재 `SQL Editor`창에 있는 SQL문을 파일로 저장해 놓습니다. 혹시라도
데이터를 잘못 조작했을 경우 백업받은 파일을 이용해 다시 테이블을 구축할 수 있도록 말이죠.
<br><br>

[sqldb database - Google Drive](https://drive.google.com/file/d/1-jqMvHCjicXhppeIN8B5QHKMEa-wzV1U/view?usp=sharing){: target="_blank" }
<br><br>

그럼 특정한 조건의 데이터만 조회하는 `WHERE`절에 대해서 알아보겠습니다.
<br><br>

쿼리문을 이용해 예를 들면서 알아보도록 하죠.

{% include callout.html
type="danger"
content="`SELECT * FROM usertbl WHERE name = '김경호';`"
%}

간단합니다. `name`이 `김경호`인 조건에 맞는 데이터를 SELECT 합니다.

{% include image.html
file='database-mysql-sql-basic-where-1.png'
%}

`WHERE`절을 이용할 때 관계 연산자를 이용할 수 있습니다.
다음의 내용을 쿼리로 작성해 보죠. 두가지 조건을 동시에 만족해야 합니다.

{% include callout.html
type="danger"
content="1970년 이후에 출생하고 신장이 182 이상인 사람의 아이디와 이름을 조회하세요."
%}

~~~sql

SELECT userID, name
FROM usertbl
WHERE birthYear >= 1970 AND height >=182;

-- 2 rows returned

~~~

이번엔 아래의 내용들을 쿼리로 작성해 보죠.

{% include callout.html
type="danger"
content="1970년 이후에 출생했거나 신장이 182 이상인 사람의 아이디와 이름을 조회하세요."
%}

~~~sql

SELECT userID, name
FROM usertbl
WHERE birthYear >= 1970 OR height >=182;

-- 7 rows returned

~~~

{% include callout.html
type="danger"
content="키가 180 ~ 183인 사람을 조회하세요."
%}

~~~sql

SELECT name, height
FROM usertbl
WHERE height >= 180 AND height <= 183;

-- 2 rows returned

-- 위의 쿼리를 아래와 같이 BETWEEN ~ AND 를 이용해 표현할 수 있다.

SELECT name, height
FROM usertbl
WHERE height BETWEEN 180 AND 183;

-- 2 rows returned

~~~

{% include callout.html
type="danger"
content="지역이 '경남', '전남', '경북' 인 사람을 조회하세요."
%}

~~~sql

SELECT name, height
FROM usertbl
WHERE addr='경남' OR addr='전남' OR addr='경북';

-- 4 rows returned

-- continuous한 값이 아닌 이산적인(discrete)값은 IN()을 이용

SELECT name, height
FROM usertbl
WHERE addr in ('경남', '전남', '경북');

~~~

문자열의 검색(패턴매칭)은 `LIKE` 연산자를 이용합니다.

{% include callout.html
type="danger"
content="성이 '김'씨인 사람들의 이름과 키를 조회하세요."
%}

~~~sql

SELECT name, height
FROM usertbl
WHERE name LIKE '김%';

-- 2 rows returned

~~~

`%`는 0개 이상의 글자를 의미하며 `_`는 1개의 글자를 의미합니다. 예를 들어
`_용%` 이라고 표현하면 여기에 해당하는 문자열은 다음과 같은 것들입니다.
<br><br>

* 조용필 리사이틀
* 사용해주셔서 감사합니다.
<br><br>

이렇게 패턴매칭을 하기 위해 `Wild Card` 문자를 이용할 수 있는데 만약 `%`나
`_`문자가 검색할 문자열의 제일 앞에 들어갈 경우 당연히 MySQL 성능에 상당히
안좋은 영향을 미칠 수 있습니다. `INDEX`를 이용할 수 없고 어쩔수 없이 전체
데이터를 검색하는 현상이 발생하기 때문입니다.
<br><br>

이번에는 `SubQuery`(서브쿼리)에 대해서 알아보죠.
<br><br>

`SubQuery`란 쿼리문 안에 또 쿼리문이 들어가 있는 것을 의미합니다. 간단한 예를
들어보죠.

{% include callout.html
type="danger"
content="김경호보다 키가 크거나 같은 사람의 이름과 키를 출력하세요."
%}

~~~sql

-- 먼저 김경호의 키를 알아낸 후 
-- 해당 값을 직접 쿼리에 적용 => 177

SELECT name, height
FROM usertbl
WHERE height >= 177;

-- 4 rows returned

 
SELECT name, height
FROM usertbl
WHERE height >= (
    SELECT height
    FROM usertbl
    WHERE name = '김경호'
);

~~~

{% include callout.html
type="danger"
content="지역이 '경남'인 사람의 키보다 크가 크거나 같은 사람을 조회하세요."
%}

~~~sql

SELECT name, height
FROM usertbl
WHERE height >= (
    SELECT height
    FROM usertbl
    WHERE addr = '경남'
);    

-- Error Code: 1242. Subquery returns more than 1 row
-- Subquery가 2개 이상의 값을 반환하기 때문에 오류발생

SELECT name, height
FROM usertbl
WHERE height >= ANY(
    SELECT height
    FROM usertbl
    WHERE addr = '경남'
);    

-- 9 rows returned

~~~

`ANY`는 `SOME`과 같은 의미로 그 중에 어떤것을 의미하고
`ALL`은 당연히 모두를 의미합니다. 잘 생각해보면 `= ANY(서브쿼리)`의 의미는
`IN(서브쿼리)`와 같게 됩니다.
<br><br>

우리는 조회된 결과물을 대체로 특정 순서로 조절해서 보고 싶어합니다. 즉,
결과물에 대해 영향은 미치지 않지만 결과가 출력되는 순서를 조절할 때 사용하는 구문이
`ORDER BY`입니다.

{% include callout.html
type="danger"
content="먼저 가입한 순서대로 회원정보를 출력하세요."
%}

~~~sql

SELECT name, mDate
FROM usertbl
ORDER BY mDate ASC;

-- 정렬의 기본은 ASC(오름차순)입니다.
-- 내림차순 정렬을 하려면 DESC를 이용합니다.

~~~

정렬할 때 정렬 기준에 대해 값이 같은 경우 2차 정렬를 하는 경우가 있는데
이런 경우 다음과 같이 하시면 됩니다.

{% include callout.html
type="danger"
content="`SELECT name, height FROM usertbl ORDER BY height DESC, name ASC;`"
%}

**`ORDER BY`에 나온 열이 `SELECT` 다음에 꼭 있을 필요는 없습니다.** `ORDER BY`는
출력을 위해 우리가 종종 사용하는 구문이지만 이 구문은 MySQL의 성능을 상당히 떨어뜨릴 소지가
있는 구문입니다. 그러기 때문에 꼭 필요한 경우가 아니라면 사용하지 않는게 좋습니다.
<br><br>

다음의 질문에 대한 쿼리를 작성해보죠

{% include callout.html
type="danger"
content="usertbl에서 회원들의 거주지역이 어디인지 출력하세요.<br><br>
`SELECT addr FROM usertbl;`"
%}

출력된 결과를 보면 알 수 있듯이 중복된 데이터가 나오게 됩니다. 어떻게 하면
중복을 배제할 수 있을까요? `DISTINCT` keyword를 이용하면 됩니다.

{% include callout.html
type="danger"
content="`SELECT DISTINCT addr FROM usertbl;`"
%}

이번에는 employee database를 잠깐 이용하도록 하죠. `hire_date`는 입사일을
나타내는 column인데 입사일이 오래된 직원 5명만을 출력하려면 어떻게 해야 할까요?
<br><br>

일단 생각나는 것은 `ORDER BY`입니다. 먼저 행 개수 제한을 `Don't Limit`로
설정하고 다음의 쿼리를 실행해 보죠.

~~~sql

USE employees;

SELECT emp_no, hire_date
FROM employees
ORDER BY hire_date ASC;

~~~

우리가 필요한 데이터는 상위 5개인데 이 데이터를 얻기 위해 총 30만개의 데이터를
더 출력했습니다. 이런 작업 자체가 MySQL에게는 부담이 됩니다. 그래서 상위 N개만
출력하는 `LIMIT` 구문이 필요합니다.

~~~sql

SELECT emp_no, hire_date
FROM employees
ORDER BY hire_date ASC, emp_no DESC
LIMIT 5;

~~~

`LIMIT`절은 `LIMIT 시작, 개수` 또는 `LIMIT 개수 OFFSET 시작` 형식으로도
사용할 수 있습니다.

~~~sql

SELECT emp_no, hire_date
FROM employees
ORDER BY hire_date ASC, emp_no DESC
LIMIT 5;   -- LIMIT 구문의 시작은 0이다.

-- 111692	1985-01-01
-- 111400	1985-01-01
-- 111035	1985-01-01
-- 110725	1985-01-01
-- 110511	1985-01-01

SELECT emp_no, hire_date
FROM employees
ORDER BY hire_date ASC, emp_no DESC
LIMIT 1,5; -- LIMIT 5 OFFSET 1과 동일    

-- 111400	1985-01-01
-- 111035	1985-01-01
-- 110725	1985-01-01
-- 110511	1985-01-01
-- 110303	1985-01-01

~~~

결과가 나오는 것도 중요하지만 해당 쿼리가 얼마나 효율적으로 작성되어 있는지도 상당히
중요합니다. 그렇지 않은 경우 MySQL에 부담을 가중시키고 전체적인 Application의
성능을 떨어뜨리게 만들기 때문입니다. 가능한 서버의 처리량을 줄여 성능을 높이는 방법을
고민해야 합니다.
<br><br>

이번에는 테이블을 복사하는 전형적인 구문에 대해서 알아보겠습니다.

~~~sql

USE sqldb;

CREATE TABLE buytbl2 (
    SELECT * FROM buytbl
);

SELECT * FROM buytbl2;

~~~

아주 간단하게 테이블 전체 혹은 필요한 column만 추출해서 테이블을
새로 생성할 수 있습니다. 하지만 이건 테이블의 데이터만 추출해서
새로운 테이블을 생성한 것이기 때문에 `PK`와 `FK`와 같은 제약조건들은
당연히 복사되지 않습니다.
<br><br>

다음으로 `GROUP BY`와 `HAVING`에 대해서 알아보겠습니다.
<br><br>

`GROUP BY`절은 말 그대로 그룹으로 묶어주는 역할을 수행합니다. 예를 위해 다음의
내용으로 쿼리를 작성해 보겠습니다.

{% include callout.html
type="danger"
content="구매테이블(buytbl)에서 사용자가 구매한 물품의 개수를 출력하세요."
%}

~~~sql

SELECT userID, amount
FROM buytbl
ORDER BY userID;

-- BBK	5
-- BBK	10
-- BBK	2
-- BBK	2
-- EJW	2
-- EJW	1
-- EJW	1
-- JYP	1
-- KBS	2
-- KBS	1
-- KBS	3
-- SSK	5

~~~

사용자별로 여러번의 구매가 이루어져 각각의 행이 별도로 출력되는 것을
확인할 수 있습니다. 이런 경우 어떻게 합계를 구할 수 있을까요?
<br><br>

`Aggregate Function`이라고 불리는 집계함수를 이용하면 됩니다.
이 집계함수는 주로 `GROUP BY`절과 함께 쓰이며 데이터를 그룹화 해주는
기능을 합니다. 쿼리를 다시 작성해 보죠.

~~~sql

SELECT userID AS '사용자 아이디', SUM(amount) AS '총 구매 개수'
FROM buytbl
GROUP BY userID
ORDER BY userID;

-- 사용자 아이디, 총 구매 개수
-- BBK	19
-- EJW	4
-- JYP	1
-- KBS	6
-- SSK	5

~~~

대충 감이 오시나요? 다음의 문제를 한번 작성해 보시기 바랍니다.

{% include callout.html
type="danger"
content="각 사용자별 구매액의 총합을 출력하세요."
%}

~~~sql

SELECT userID AS '사용자 아이디', SUM(amount*price) AS '총 구매액'
FROM buytbl
GROUP BY userID
ORDER BY userID;

-- 사용자 아이디, 총 구매액
-- BBK	1920
-- EJW	95
-- JYP	200
-- KBS	1210
-- SSK	75

~~~

집계함수는 위에서 살펴본 `SUM()` 함수 이외에 다음과 같은 함수가 있습니다.

{% include image.html
file='database-mysql-sql-basic-aggregate-function.png'
%}

{% include callout.html
type="danger"
content="전체 구매자가 구매한 물품 개수의 평균은 얼마인가요?"
%}

~~~sql

SELECT AVG(amount) AS '평균 구매 개수'
FROM buytbl;

-- 평균 구매 개수
-- 2.9167 

~~~

{% include callout.html
type="danger"
content="각 사용자별로 한번 구매 시 물건을 평균 몇 개 구매했는지 조회하세요."
%}

~~~sql

SELECT userID, AVG(amount) AS '평균 구매 개수'
FROM buytbl
GROUP BY userID

-- userID, 평균 구매 개수
-- BBK	4.7500
-- EJW	1.3333
-- JYP	1.0000
-- KBS	2.0000
-- SSK	5.0000

~~~

조금 다른 예를 살펴보죠.

{% include callout.html
type="danger"
content="가장 큰 키와 가장 작은 키의 회원 이름과 키를 출력하세요."
%}

~~~sql

SELECT name, MAX(height), MIN(height)
FROM usertbl;

-- name, MAX(height), MIN(height)
-- 바비킴	186	166
-- 결과가 이상하다.!!!

SELECT name, MAX(height), MIN(height)
FROM usertbl
GROUP BY name;

-- name, MAX(height), MIN(height)
-- 바비킴	176	176
-- 은지원	174	174
-- 조관우	172	172
-- 조용필	166	166
-- 김범수	173	173
-- 김경호	177	177
-- 임재범	182	182
-- 이승기	182	182
-- 성시경	186	186
-- 윤종신	170	170

-- 이것 역시 우리가 원한 결과가 아니다.

~~~

어떻게 해야 우리가 원하는 결과를 얻을 수 있을까요?
<br><br>

문제가 잘 안풀릴경우 `Subquery`를 이용하면 쉽게 해결하는 경우가
많습니다.

~~~sql

SELECT name, height
FROM usertbl
WHERE height = (
    SELECT MAX(height) from usertbl
) OR
height = (
    SELECT MIN(height) from usertbl
);

-- name, height
-- 조용필	166
-- 성시경	186

~~~

이번에는 휴대폰이 있는 사용자의 수를 카운트 해 보도록 하죠. 몇 건인지를
알아낼때는 `COUNT` 집계함수를 이용하면 됩니다.

~~~sql

SELECT COUNT(*)
FROM usertbl;

-- 모든 회원의 수 (10)

SELECT COUNT(mobile1)
FROM usertbl;

-- NULL 값을 제외하고 카운트 (8)

~~~

집계함수를 이용하는 방법에 대해서 알아봤는데 이번에는 이런
그룹에 대한 조건을 지정하기 위한 `HAVING`에 대해서 알아보겠습니다.
다음 내용에 대한 쿼리를 작성해 보죠.

{% include callout.html
type="danger"
content="사용자별 총 구매금액이 1,000 이상인 사용자만 출력하세요"
%}

~~~sql

SELECT userID, SUM(price*amount)
FROM buytbl
WHERE SUM(price*amount) >= 1000
GROUP BY userID;

-- Error Code: 1111. Invalid use of group function
-- 집계함수는 WHERE절에 올 수 없다.ALTER
-- 이럴때 HAVING을 이용하면 해결할 수 있습니다. 집계함수에 대해
-- 조건을 제한할 때 사용합니다.

SELECT userID, SUM(price*amount)
FROM buytbl
GROUP BY userID
HAVING SUM(price*amount) >= 1000;

-- userID, SUM(price*amount)
-- BBK	1920
-- KBS	1210

~~~

이렇게 집계함수를 사용할 때 총합, 혹은 중간합계가 필요한 경우가 있습니다. 이럴때
`GROUP BY`와 함께 `WITH ROLLUP`문을 사용하면 해결할 수 있습니다.

{% include callout.html
type="danger"
content="상품의 분류별로 합계 및 그 총합을 구하세요."
%}

~~~sql

SELECT num, groupName, SUM(price*amount)
FROM buytbl
GROUP BY groupName, num
WITH ROLLUP;

-- 1	      60
-- 10	      60
-- 12	      60
--            180
-- 7	서적  75
-- 8	서적  30
-- 11	서적  15
--      서적  120
-- 5	의류  150
-- 9	의류  50
--      의류  200
-- 2	전자  1000
-- 3	전자  200
-- 4	전자  1000
-- 6	전자  800
--      전자  3000
--            3500

~~~

여기서 `GROUP BY`절에 `num`은 각 항목이 보이게끔 일부러 넣어준 것으로
만약 소 합계 및 총합만 필요하다면 num을 빼면 됩니다.
<br><br>

여기까지가 기본 `SELECT` 구문입니다. 다음은 기본 SQL구문인
`INSERT`, `UPDATE`, `DELETE`에 대해서 알아보겠습니다. 먼저 
Transaction에 대해 간략히 알아보고 넘어가도록 하죠.

---

## Transaction

* 작업의 최소단위
* 해당 작업의 범위는 정해져있는 것이 아니라 개발자가 설정
* Transaction을 설정하면 ACID를 DBMS로부터 보장받는다
<br><br>

* A(Atomicity) : 원자성
<br><br>

* C(Consistency) : 일관성
기존의 데이터베이스가 Correct State라면 트랜잭션을 수행하고 난 후에도 Correct State여야 한다.
Correct State => 도메인의 유효범위, 무결성 제약조건 등의 제약조건을 위배하지 않는 정상적인 상태를 의미한다.
즉, 잔액은 0보다 커야 하며 성별은 남/여의 값만 가져야 한다 등. 잔액은 숫자타입인데 transaction처리 후 문자열로
변경된다는 이 역시 일관성을 위배하는 것이다.
<br><br>

* I(Isolation) : 독립성 => 한 트랜잭션이 실행하는 동안의 중간결과를 다른 트랜잭션이 접근할 수 없다.
<br><br>

* D(Durability) : 영속성 => 완료(commit) 된 트랜잭션은 데이터베이스에 영구적으로 보존되어야 한다.

~~~sql

START TRANSACTION;
-- COMMIT, ROLLBACK이 나올 때까지 실행되는 모든 SQL 추적

COMMIT;
-- 모든 코드를 실행(문제가 없을 경우에)

ROLLBACK;
 
-- START TRANSACTION 실행 전 상태로 되돌림(문제 생기면)

~~~

---

## SQL의 분류

위에서 가장 대표적인 `SELECT` 구문을 살펴보았습니다. 사실 SQL문은 크게 다음과
같은 3가지로 분류합니다.
<br><br>

* `DML` : Data Manipulation Language
* `DDL` : Data Definition Language
* `DCL` : Data Control Language
<br><br>

`DML`은 데이터를 조작(CRUD)하는데 사용되는 언어입니다. DML구문이
사용되는 대상은 `Table`의 행입니다. SQL구문 중 `SELECT`, `INSERT`, `UPDATE`, `DELETE`가
이 구문에 해당됩니다. 또한 `Transaction`이 발생하는 SQL이 이 `DML`입니다.
<br><br>

`DDL`은 데이터베이스, 테이블, 뷰, 인덱스 등의 데이터베이스 개체를 생성,삭제,변경하는 역할을 합니다.
자주 등장하는 `DDL`은 `CREATE`, `ALTER`, `DROP`등이 있습니다.  한가지 기억해야 할 점은
`DDL`은 `Transaction`을 발생시키지 않습니다. 즉, `ROLLBACK`이나 `COMMIT` 작업을 할 수 없고
실행 즉시 `MySQL`에 적용됩니다.
<br><br>

`DCL`은 사용자에게 어떤 권한을 부여하거나 제거할 때 주로 사용하는 구문으로 `GRANT`, `REVOKE`,
`DENY`등이 이에 해당합니다.
<br><br>

자 그럼 `DML`의 나머지 SQL문을 살펴보도록 하겠습니다.
<br><br>

데이터의 삽입은 기본적으로 `INSERT`문을 이용합니다.
<br><br>

`INSERT`문은 크게 어려운 내용이 없습니다. 다음의 코드를 이용해 실습하면서
이해해보도록 하죠.

~~~sql

USE sqldb;

CREATE TABLE testtbl1 (
    id        INT,
    userName  CHAR(3),
    age       INT
);

-- 일반적인 INSERT    
INSERT INTO testtbl1 VALUES (1, '홍길동', 25);    

-- 필요한 것들만 선택해서 데이터 INSERT
INSERT INTO testtbl1(id, userName) VALUES (2, '김연아');    

-- 컬럼의 순서를 바꾸어도 상관없다.
INSERT INTO testtbl1(age, userName, id) VALUES (30, '아이유', 3);    

SELECT * FROM testtbl1;

~~~

테이블 column의 속성이 `AUTO_INCREMENT`로 지정되어 있는 경우 INSERT 문에서
해당 열은 `NULL`값으로 지정하면 됩니다. `AUTO_INCREMENT`는 자동으로 1씩
증가하는 값을 입력해 줍니다. `AUTO_INCREMENT`는 당연히 숫자 형식만 사용할 수 있으며
반드시 `PRIMARY KEY` 혹은 `UNIQUE`로 지정해야 사용할 수 있습니다.
<br><br>

아래와 같이 사용합니다.

~~~sql

USE sqldb;

CREATE TABLE testtbl2 (
    id        INT  AUTO_INCREMENT PRIMARY KEY,
    userName  CHAR(3),
    age       INT
);

INSERT INTO testtbl2 VALUES (NULL, '강감찬', 10);    
INSERT INTO testtbl2 VALUES (NULL, '홍길동', 20);    
INSERT INTO testtbl2 VALUES (NULL, '이순신', 30);    

SELECT * FROM testtbl2;

~~~

`AUTO_INCREMENT`로 증가된 값이 얼마인지 확인하려면 다음과 같은 쿼리를
사용하시면 됩니다.

{% include callout.html
type="danger"
content="`SELECT LAST_INSERT_ID();`"
%}

그런데 여기서 살짝 주의해야 하는 부분이 있습니다.
`LAST_INSERT_ID()` 함수는 1개의 insert 쿼리에 대해서
성공시 마지막 auto_increment 값을 리턴합니다. 다음의 코드를 한번 보시죠.

~~~sql

CREATE TABLE testtbl3 (
    id        INT  AUTO_INCREMENT PRIMARY KEY,
    userName  CHAR(3),
    age       INT
);

INSERT INTO testtbl3(userName,age) VALUES ('강감찬', 10), ('아이유',20), ('김연아',30);    

SELECT * FROM testtbl3;

SELECT LAST_INSERT_ID();   -- 1

-- LAST_INSERT_ID() 함수는 1개의 insert 쿼리에 대해서 성공시 마지막 auto_increment 값이다.

-- 한번 더 입력한다.
INSERT INTO testtbl3(userName,age) VALUES ('강감찬', 10), ('아이유',20), ('김연아',30);    

SELECT LAST_INSERT_ID();   -- 4

~~~

추가적으로 `AUTO_INCREMENT` 입력값을 100부터 입력되도록 변경하고 싶다면 다음의
코드를 이용합니다.

{% include callout.html
type="danger"
content="`ALTER TABLE testtbl3 AUTO_INCREMENT = 100;`"
%}

증가값은 기본적으로 1로 설정되어 있습니다. 만약 `AUTO_INCREMENT`의 증가값을
변경하려면 서버 변수인 `@@auto_increment_increment` 변수를 변경해야 합니다.

{% include callout.html
type="danger"
content="`SET @@auto_increment_increment = 5;`"
%}


많은 양의 데이터를 입력할 때는 일일이 `INSERT` 구문을 사용하기 힘듭니다. 그런 경우
`INSERT INTO ~ SELECT` 구문을 이용해 데이터를 입력할 수 있습니다.

~~~sql

USE sqldb;

CREATE TABLE testtbl4 (
    id      INT,
    fname   VARCHAR(50),
    lname   VARCHAR(50)
);

INSERT INTO testtbl4
    SELECT emp_no, first_name, last_name
    FROM employees.employees;

SELECT * FROM testtbl4; 

~~~

추가적으로 몇가지 특수한 경우에 대한 `INSERT` 구문을 알아보겠습니다.
`PK` 충돌로 인해 입력이 안되는 경우 아래의 코드와 같은 방법을 이용할 수 있습니다.

~~~sql

USE sqldb;

CREATE TABLE memberTBL (
    SELECT userID, name, addr
    FROM usertbl
    LIMIT 3
);    

ALTER TABLE memberTBL
    ADD CONSTRAINT pk_memberTBL PRIMARY KEY (userId);

SELECT * FROM memberTBL;    

-- 데이터를 추가로 3건을 입력합니다. 그런데 첫번째 데이터에서 PK인 BBK의 충돌이 있습니다.

INSERT INTO memberTBL VALUES ('BBK', '김범준', '미국');
INSERT INTO memberTBL VALUES ('SJH', '서장훈', '서울');
INSERT INTO memberTBL VALUES ('HJY', '현주엽', '경기');

SELECT * FROM memberTBL;    

-- 첫번째 INSERT에서 오류가 발생해 그 밑의 SQL문은 무시되었습니다. 아래와 바꿔서 실행합니다.

INSERT IGNORE INTO memberTBL VALUES ('BBK', '김범준', '미국');
INSERT IGNORE INTO memberTBL VALUES ('SJH', '서장훈', '서울');
INSERT IGNORE INTO memberTBL VALUES ('HJY', '현주엽', '경기');

SELECT * FROM memberTBL;    

-- 첫번째 데이터는 오류이기 때문에 무시되고 나머지 2건은 추가로 입력됨을 확인할 수 있습니다.

-- 그럼 입력시에 기본 키가 중복되면 데이터가 수정되게 끔 쿼리를 수정해 보죠.

INSERT INTO memberTBL VALUES ('BBK', '김범준', '미국')
    ON DUPLICATE KEY UPDATE name = '김범준', addr = '미국';
    
SELECT * FROM memberTBL;       

~~~

이번에는 데이터의 수정을 위한 `UPDATE` 구문을 알아보겠습니다.
다음의 코드를 보시죠.

~~~sql

UPDATE testtbl4
    SET lname = '없음'
    WHERE fname = 'Kyoichi';

SELECT * FROM testtbl4 WHERE fname = 'Kyoichi';     

~~~

데이터를 삭제하기 위해서는 `DELETE` 구문을 이용합니다. `DELETE`는
행 단위로 삭제하게 되는데 다음의 예제처럼 사용합니다.

~~~sql

SELECT * FROM testtbl4 WHERE fname = 'Kyoichi';     -- 251 rows returned

-- 조건에 따라 행을 삭제합니다.
-- 만약 상위 몇건만 삭제하려면 아래와 같이 LIMIT 문을 이용합니다.

DELETE 
FROM testtbl4
WHERE fname = 'Kyoichi'
LIMIT 100;

SELECT * FROM testtbl4 WHERE fname = 'Kyoichi';     -- 151 rows returned

~~~

삭제에 관련된 SQL 구문은 크게 3가지 입니다. `DELETE`, `DROP`, `TRUNCATE`가 있는데
세 구문 모두 테이블의 행을 삭제합니다. 특히 `DROP`은 테이블 자체를 삭제하게 됩니다.
<br><br>

`DELETE`는 `Transaction Log`를 기록하기 때문에 삭제가 오래 걸립니다. `DROP`은
테이블 자체를 삭제하고 `Transaction`을 발생시키지 않기 때문에 빠릅니다. 마지막
`TRUNCATE`는 `DELETE`와 동일하지만 `Transaction Log`를 기록하지 않기 때문에
속도가 무척 빠르게 처리됩니다.

---

## SQL - WITH

`WITH`절은 `CTE`(common Table Expression)을 표현하기 위한 구문으로 `MySQL 8.0`
이후부터 사용할 수 있습니다. `CTE`는 기존의 View, 임시 테이블, 파생 테이블등으로
사용됬던것을 대체할 수 있는 간결한 표현식입니다. `CTE`는 `ANSI-SQL99` 표준에서 나온
것으로 기존의 SQL은 `ANSI-SQL92`를 기준으로 합니다.
<br><br>

`CTE`는 이번 강의에서 다루지 않습니다. 


End.

{% include links.html %}
