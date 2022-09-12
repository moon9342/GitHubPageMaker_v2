---
title: MySQL Table & View
sidebar: mysql_sidebar
summary: "이번에는 Table과 View에 대해 조금 더 자세히 알아보도록 하겠습니다."
permalink: mysql_table-view.html
folder: mysql
---

## Table

이번에는 Table과 View에 대해 조금 더 자세히 알아보도록 하겠습니다. 이전에 사용했던 `sqldb`와 동일한
형식의 `tabledb`를 만들어서 실습을 진행하도록 하죠.
<br><br>

다음의 그림처럼 만들어지게 됩니다.

{% include image.html
file='database-mysql-table-view-tabledb.png'
%}

SQL을 이용해 `tabledb`를 생성하고 각각의 table을 생성합니다. 여기서 한가지 주의해야 할 점은
**구매테이블**보다 **회원테이블**을 먼저 생성해야 한다는 것입니다. 외래키가 존재하기 때문에
구매테이블을 먼저 만들 경우 reference하는 컬럼이 존재하지 않으므로 테이블 생성에 오류가 발생하게
됩니다.

~~~sql

DROP DATABASE IF EXISTS tabledb;   -- 만약 tabledb가 존재하면 삭제.

CREATE DATABASE tabledb;  -- 데이터베이스 생성

USE tabledb;  -- tabledb 사용

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

---

## Constraint

`Constraint`(제약조건)는 데이터의 무결성을 지키기 위한 제한된 조건을 의미합니다. 즉,
특정 데이터를 조작할 때 무조건 실행되는것이 아닌 조건을 만족했을 때 실행되서 데이터의
무결성이 보장되도록 만드는 장치라고 이해하시면 됩니다.
<br><br>

`MySQL`은 데이터의 무결성을 위해 다음의 5가지 제약조건을 제공합니다.
<br><br>

* `PRIMARY KEY` 제약조건
* `FOREIGN KEY` 제약조건
* `UNIQUE` 제약조건
* `CHECK` 제약조건 (MySQL 8.0.16 버전부터 정식지원)
* `DEFAULT` 정의
* `NULL` 값 허용
<br><br>

하나씩 살펴보도록 하겠습니다.

---

## PRIMARY KEY 제약조건

{% include callout.html
type="danger"
content="PRIMARY KEY에 입력되는 값은 중복될 수 없으며 NULL 값이 입력될 수 없다."
%}

기본 키로 생성하는 것은 자동으로 인덱스가 생성됩니다. 또한 하나의 컬럼이 아닌
여러개의 컬럼을 묶어서 기본 키로 지정할 수 있습니다. 일반적으로 대부분의 테이블은
기본 키를 가지는데 기본 키를 가지지 않아도 문제는 없지만 실무에서는 대부분의
테이블에 기본 키를 설정합니다.
<br><br>

기본키를 설정할 때는 다음의 3가지 형태가 사용될 수 있습니다.

~~~sql

USE tabledb;

CREATE TABLE testUserTBL (
    userID    CHAR(8) NOT NULL PRIMARY KEY,
    name      VARCHAR(10) NOT NULL
);    

DROP TABLE IF EXISTS testUserTBL;

CREATE TABLE testUserTBL (
    userID    CHAR(8) NOT NULL,
    name      VARCHAR(10) NOT NULL,
    CONSTRAINT PRIMARY KEY PK_testusertbl_userID (userID)
);    

DROP TABLE IF EXISTS testUserTBL;

CREATE TABLE testUserTBL (
    userID    CHAR(8) NOT NULL,
    name      VARCHAR(10) NOT NULL
);    

ALTER TABLE testUserTBL
    ADD CONSTRAINT PK_testusertbl_userID
        PRIMARY KEY (userID);
        
        
DESC testUserTBL;

~~~

다음 그림과 같은 경우는 `PRIMARY KEY`를 설정할 때 두 개의 컬럼을 이용해야 합니다.

{% include image.html
file='database-mysql-table-view-two-column-primary-key.png'
%}

하나의 컬럼으로는 기본 키를 설정할 수 없습니다. 이런 경우 2개의 컬럼을 묶어서 기본 키를 지정합니다.

~~~sql

DROP TABLE IF EXISTS testProdTBL;

CREATE TABLE testProdTBL (
    prodCode    CHAR(3) NOT NULL,
    prodID      CHAR(4) NOT NULL,
    prodDatre   DATETIME NOT NULL,
    prodCur     CHAR(10) NULL,
    CONSTRAINT PK_testprodtbl_prodCode_prodID
        PRIMARY KEY(prodCode, prodID)
);

DROP TABLE IF EXISTS testProdTBL;

CREATE TABLE testProdTBL (
    prodCode    CHAR(3) NOT NULL,
    prodID      CHAR(4) NOT NULL,
    prodDatre   DATETIME NOT NULL,
    prodCur     CHAR(10) NULL
);

ALTER TABLE testProdTBL
    ADD CONSTRAINT PK_testprodtbl_prodCode_prodID
        PRIMARY KEY(prodCode, prodID);

SHOW INDEX FROM testProdTBL;

~~~

---

## FOREIGN KEY 제약조건

`FOREIGN KEY`(외래 키) 제약조건은 두 테이블 사이의 관계를 선언함으로 데이터의 무결성을
보장해 주는 역할을 합니다. 외래 키 관계를 설정하면 하나의 테이블이 다른 테이블에 의존하게
됩니다.

{% include callout.html
type="danger"
content="외래 키 테이블에 데이터를 입력할 때는 반드시 기준 테이블을 참조해서 입력하기 때문에
기준 테이블에 이미 데이터가 존재해야 합니다. 또 외래 키 컬럼이 참조하는 기준 테이블의
컬럼은 반드시 `PRIMARY KEY` 이거나 `UNIQUE` 제약 조건이 설정되어 있어야 합니다."
%}

~~~sql

DROP TABLE IF EXISTS testUserTBL, testBuyTBL;

CREATE TABLE testUserTBL (
    userID    CHAR(8) NOT NULL PRIMARY KEY,
    name      VARCHAR(10) NOT NULL    
);  

CREATE TABLE testBuyTBL (
    num      INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    userID   CHAR(8) NOT NULL,
    prodName CHAR(6) NOT NULL,
    FOREIGN KEY(userID) REFERENCES testUserTBL(userID)
    -- CONSTRAINT FK_testusertbl_testbuytbl FOREIGN KEY(userID) REFERENCES testUserTBL(userID)
    -- 제약조건 이름을 직접 설정할 때는 위와 같이 이름을 명시할 수 있다.
);

~~~

외래 키의 옵션 중에 기억해야 할 것들이 있는데 바로 `ON DELETE CASCADE`, `ON UPDATE CASCADE` 입니다.
기준 테이블의 데이터가 삭제되거나 수정되었을 때 외래 키 테이블도 자동으로 삭제되거나 수정되게끔 설정해주는
옵션이라고 보시면 됩니다.

~~~sql

CREATE TABLE testBuyTBL (
    num      INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    userID   CHAR(8) NOT NULL,
    prodName CHAR(6) NOT NULL,
    FOREIGN KEY(userID) REFERENCES testUserTBL(userID)
    ON DELETE CASCADE
);

~~~

---

## UNIQUE 제약조건

`UNIQUE` 제약조건은 **중복되지 않는 유일한 값**을 입력해야 한다는 조건입니다. `PRIMARY KEY`와의
차이점은 `UNIQUE`는 `NULL` 값을 허용한다는 것입니다. 참고로 `NULL`은 여러개가 입력되어도 상관 없습니다.
회원테이블의 `EMAIL` 컬럼 같은 것이 `UNIQUE` 제약조건을 가지는 대표적 예가 될 수 있습니다.

~~~sql

DROP TABLE IF EXISTS testUserTBL, testBuyTBL;

CREATE TABLE testUserTBL (
    userID    CHAR(8) NOT NULL PRIMARY KEY,
    name      VARCHAR(10) NOT NULL,
    email     CHAR(30) NULL UNIQUE
);  

~~~

---

## CHECK 제약조건

`CHECK` 제약조건은 데이터를 점검하는 기능을 합니다. 다음의 예를 보면 쉽게 이해할 수 있습니다.

~~~sql

DROP TABLE IF EXISTS testUserTBL, testBuyTBL;

CREATE TABLE testUserTBL (
    userID    CHAR(8) NOT NULL PRIMARY KEY,
    name      VARCHAR(10),
    birthYear INT CHECK (birthYear >= 1900 AND birthYear <= 2023),
    email     CHAR(30) NULL UNIQUE,
    CONSTRAINT CK_name CHECK ( name IS NOT NULL )
);  

INSERT INTO testUserTBL VALUES('1', '아이유', 2000, NULL);
INSERT INTO testUserTBL VALUES('2', '김연아', 1890, NULL);  -- ERROR

~~~

---

## DEFAULT 선언

`DEFAULT`는 값을 입력하지 않았을 때, 자동으로 입력되는 기본 값을 정의하는 방법입니다.

~~~sql

DROP TABLE IF EXISTS testUserTBL, testBuyTBL;

CREATE TABLE testUserTBL (
    userID    CHAR(8) NOT NULL PRIMARY KEY,
    name      VARCHAR(10) NOT NULL,
    birthYear INT DEFAULT -1
);  

INSERT INTO testUserTBL(userID, name) VALUES('1', '아이유');

SELECT * FROM testUserTBL;

~~~

---

## TEMPORARY TABLE

`TEMPORARY TABLE`(임시 테이블)은 이름처럼 잠깐 사용되는 용도로 사용하는 테이블입니다. 이런
임시 테이블은 `Session`내에서만 존재하며, Session이 닫히면 자동으로 삭제됩니다. 또한 임시
테이블은 생성한 클라이언트만 사용가능하며 다른 클라이언트는 접근할 수 없습니다.
<br><br>

임시테이블이 삭제되는 시점은 다음과 같습니다.
<br><br>

* 사용자가 DROP TABL로 직접 삭제
* `MySQL` 클라이언트를 종료할 때
* `MySQL` 서비스가 재시작될 때

~~~sql

USE tabledb;

CREATE TEMPORARY TABLE IF NOT EXISTS tempTBL (
    id   INT,
    name CHAR(5)
);

DESC tempTBL;

DROP TABLE tempTBL;    

~~~

---

## TABLE 수정

`ALTER` 구문은 상당히 복잡합니다. 여기서는 실제로 많이 사용되는 것들만 살펴보도록 하죠.
코드를 이용해 예를 들어 보겠습니다.

~~~sql

USE tabledb;

DROP TABLE IF EXISTS testUserTBL, testBuyTBL;

CREATE TABLE testUserTBL (
    userID    CHAR(8) NOT NULL PRIMARY KEY,
    name      VARCHAR(10) NOT NULL,
    birthYear INT CHECK (birthYear >= 1900 AND birthYear <= 2023),
    email     CHAR(30) NULL UNIQUE
);  

ALTER TABLE testUserTBL
    ADD homepage VARCHAR(30)
        DEFAULT 'http://google.com'
        NOT NULL;

ALTER TABLE testUserTBL
    DROP COLUMN email;
    
ALTER TABLE testUserTBL
    CHANGE COLUMN name uname VARCHAR(20) NULL;
    
ALTER TABLE testUserTBL
    DROP PRIMARY KEY;
    
DESC testUserTBL;

~~~

---

## VIEW

`TABLE`에 대한 내용을 살펴보았습니다. 이번에는 사용자의 입장에서 `TABLE`과 동일하게 여겨지는 개체인
`VIEW`에 대해 알아보겠습니다.
<br><br>

`VIEW`의 실체는 `SELECT`구문입니다.
<br><br>

`VIEW`를 생성하는 구문은 다음과 같습니다.

~~~sql

USE tabledb;

CREATE VIEW v_usertbl
AS
    SELECT userID, name, addr FROM userTBL;

SELECT * FROM v_usertbl;  -- view를 table로 생각하고 이용하면 된다.

~~~

`VIEW`의 동작 방식을 그림으로 표현하면 아래의 그림과 같습니다.

{% include image.html
file='database-mysql-table-view-use-view.png'
%}

그렇다면 `VIEW`는 수정이 가능할까요? `VIEW`는 기본적으로 **읽기전용**으로 많이 사용됩니다.
하지만 `VIEW`를 통해 테이블의 데이터를 수정할 수 도 있습니다. 하지만 바람직하지는 않습니다.
그러나 꼭 필요한 경우가 생길 수 있기 때문에 그 방법도 알아 둘 필요는 있습니다.
<br><br>

`VIEW`를 사용하는 장점은 무엇일까요?
<br><br>

* 보안에 도움이 됩니다. `VIEW`를 이용해 테이블에 존재하는 데이터 중 일반 사용자에게 보여지지
  말아야 할 데이터를 제외시킬 수 있습니다.
* 복잡한 쿼리를 단순화 시킬 수 있습니다. 자주 사용되는 복잡한 쿼리를 `VIEW`로 생성해 놓으면
  사용자들은 해당 VIEW만 이용하면 되기 때문에 훨씬 쉽게 데이터를 사용할 수 있습니다.
<br><br>

그럼 간단하게 VIEW를 만들어서 사용해 보도록 하죠.

~~~sql

USE tabledb;

CREATE OR REPLACE VIEW v_userbuytbl
AS
    SELECT U.userid AS 'USER ID',
           U.name AS 'USER NAME',
           B.prodname AS 'PRODUCT NAME',
           U.addr,
           CONCAT(U.mobile1, U.mobile2) AS 'MOBILE PHONE'
    FROM usertbl U
        INNER JOIN buytbl B
        ON U.userid = b.userid;
        
SELECT * FROM v_userbuytbl;        

-- 컬럼명에 백틱을 사용해야 한다.
SELECT `USER ID`, 
       `PRODUCT NAME`, 
       `MOBILE PHONE` 
FROM v_userbuytbl;       

~~~

`VIEW`를 만들고 사용하는 것은 생각보다 간단합니다. 이번에는 간단한 예를 통해
VIEW를 이용한 수정과 데이터 입력이 가능한지를 살펴보도록 하죠.

~~~sql

USE tabledb;

CREATE OR REPLACE VIEW v_usertbl
AS
    SELECT userid, name, addr FROM userTBL;
    
DESC v_usertbl;    

-- VIEW의 코드를 확인해보자
SHOW CREATE VIEW v_usertbl;


SELECT * 
FROM usertbl
WHERE userid = 'JKW';

-- userID, name, birthYear, addr, mobile1, mobile2, height, mDate
-- 'JKW', '조관우', '1965', '경기', '018', '9999999', '172', '2010-10-10'


-- VIEW를 통한 데이터 수정
UPDATE v_usertbl 
SET addr = '부산'
WHERE userid='JKW';


SELECT * 
FROM usertbl
WHERE userid = 'JKW';

-- userID, name, birthYear, addr, mobile1, mobile2, height, mDate
-- 'JKW', '조관우', '1965', '부산', '018', '9999999', '172', '2010-10-10'

-- VIEW를 통한 데이터 입력
-- ERROR! 테이블에 null값이 들어가면 안되는 컬럼(birthYear)이 있기 때문.
INSERT INTO v_usertbl(userid, name, addr) VALUES('iu', '아이유', '서울');

~~~

위의 예제에서 처럼 수정은 어느정도 가능하지만 입력같은 경우는 많은 제약이 따릅니다. 일반적으로
VIEW는 `READ ONLY`형태로 사용하는것이 가장 좋습니다. 또한 두개 이상의 테이블이 관련된 VIEW는
UPDATE를 할 수 없습니다.

---

## TABLESPACE

데이터베이스가 테이블이 저장되는 논리적인 공간이라면 `TABLESPACE`는 테이블이 실제로 저장되는
물리적인 공간을 의미합니다.
<br><br>

소용량의 데이터를 다룰 때는 테이블이 저장되는 물리적인 공간인 `TABLESAPCE`에 대해서 별로 신경쓰지
않아도 되지만 대용량의 데이터를 다룰 때는 성능 향상을 위해 `TABLESPACE`에 대한 설정을 하는것이
좋습니다.
<br><br>

지금까지는 테이블을 생성할 때 별도의 테이블스페이스를 지정하지 않았기 때문에
`System Tablespace`(시스템 테이블 스페이스)에 테이블이 저장되는데 이에 대한 정보는
시스템 변수인 `innodb_data_file_path`에 저장되어 있습니다.

~~~sql

SHOW VARIABLES LIKE 'innodb_data_file_path';

-- innodb_data_file_path	ibdata1:12M:autoextend

~~~

`ibdata1:12M:autoextend`의 의미는 **파일명:파일크기:최대파일크기**를 의미합니다.
`MySQL 8.0`에서 테이블스페이스 파일은 기본적으로 `C:\ProgramData\MySQL\MySQL Server 8.0\Data`
폴더에 저장되어 있습니다.
<br><br>

기본설정은 모든 테이블이 시스템 테이블스페이스에 저장되는 것인데 대용량 테이블을 동시에
여러 개 사용하는 상황이라면 테이블마다 별도의 테이블스페이스를 가지게 하는게 더 효율적입니다.
<br><br>

먼저 각 테이블이 별도의 테이블스페이스에 저장되도록 시스템 변수 `innodb_file_per_table`의 값이
`ON`으로 설정되어 있어야 합니다. 기본적으로 `ON`으로 설정되어 있습니다.

~~~sql

SHOW VARIABLES LIKE 'innodb_file_per_table';
-- innodb_file_per_table	ON

~~~

자 그럼 3개의 테이블 스페이스를 생성해 보죠

~~~sql

CREATE TABLESPACE ts_a ADD DATAFILE 'ts_a.ibd';
CREATE TABLESPACE ts_b ADD DATAFILE 'ts_b.ibd';
CREATE TABLESPACE ts_c ADD DATAFILE 'ts_c.ibd';

~~~

`TABLESPACE`의 확장자명은 `.ibd`를 사용합니다. 위의 구문을 실행 한 후
`C:\ProgramData\MySQL\MySQL Server 8.0\Data` 폴더를 살펴보면
3개의 파일이 생성된 것을 확인할 수 있습니다.
<br><br>

그런다음 테이블을 생성할 때 `TABLESPACE`문을 이용해 저장하고자 하는
테이블스페이스를 지정하면 됩니다.

~~~sql

USE tabledb;

CREATE TABLE sampleTBL_a (
    id    INT
) TABLESPACE ts_a;    
-- 정상적으로 테이블이 생성된다.


CREATE TABLE sampleTBL_b (
    SELECT * FROM employees.employees
); 
ALTER TABLE sampleTBL_b TABLESPACE ts_c;
-- 시스템에 따라서 오류가 발생할 수 있다. 
-- 이유는 쿼리의 응답시간을 기다리는 시간이 너무 짧게 설정되어 있기 때문이다.

~~~

쿼리의 응답을 기다리는 시간을 제한없음 으로 설정해야 위의 코드가 정상적으로
수행됩니다.

{% include image.html
file='database-mysql-table-view-tablespace.png'
%}


End.

{% include links.html %}
