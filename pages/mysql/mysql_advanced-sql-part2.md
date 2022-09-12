---
title: MySQL SQL 고급(2)
sidebar: mysql_sidebar
summary: "지금까지는 하나의 테이블을 다루는 작업에 대해서 알아보았는데 
이번에는 두 개 이상의 테이블을 대상으로 SQL문을 처리하는 방법에 대해서 알아보겠습니다."
permalink: mysql_advanced-sql-part2.html
folder: mysql
---

## INNER JOIN

`Join`이란 두 개 이상의 테이블을 서로 묶어서 하나의 결과 집합을 만들어 내는
것을 의미합니다.
<br><br>

데이터베이스의 테이블은 중복과 공간 낭비를 피하고 데이터의 무결성을 위해
여러 개의 테이블에 데이터를 나누어 저장합니다. 그리고 이 분리된 테이블들은
서로 `Relation`(관계)을 맺고 있습니다.  이 관계를 기준으로 두 테이블을
결합해서 사용해 보도록 하죠.
<br><br>

`Join`중에서 가장 많이 사용되는 Join이 바로 `Inner Join`입니다. 일반적으로
그냥 Join이라고 하면 Inner Join을 의미합니다. 일단 형식을 살펴보죠.

~~~text

SELECT <열 목록>
FROM <첫 번째 테이블>
     INNER JOIN <두 번째 테이블>     
     ON <조인 조건>
WHERE <검색 조건>

~~~

간단한 예를 이용해 알아보도록 하겠습니다. 구매 테이블(buytbl)에서 'JYP'라는
아이디를 가진 사람이 구매한 물건을 발송하기 위해 이 사람의 주소가 필요하면
주소(addr)는 usertbl에 있기 때문에 두 테이블을 결합해서 결과를 알아내야 합니다.

~~~sql

USE sqldb;

SELECT *
FROM buytbl
    INNER JOIN usertbl
    ON buytbl.userID = usertbl.userID
WHERE buytbl.userID = 'JYP';    

-- num, userID, prodName, groupName, price, amount, userID, name, birthYear, addr, mobile1, mobile2, height, mDate
-- 3	JYP	모니터	전자	200	1	JYP	조용필	1950	경기	011	4444444	166	2009-04-04

~~~

위의 예에서 `ON`과 `WHERE`에 '테이블이름.열이름' 형식으로 되어있는걸 볼 수 있습니다.
만약 두 테이블에 동일한 열이름이 있을 경우 혼동될 우려가 있기 때문에 '테이블이름.열이름'
형식으로 작성하는 것이 좋습니다.
<br><br>

위의 예에서 항목이 너무 많기 때문에 아이디, 이름, 구매물품, 주소만 출력하도록
코드를 수정하겠습니다.

~~~sql

SELECT buytbl.userID, name, prodName, addr
FROM buytbl
    INNER JOIN usertbl
    ON buytbl.userID = usertbl.userID
WHERE buytbl.userID = 'JYP';    

~~~

위의 예에서도 마찬가지 개념으로 SELECT 다음에 나오는 `buytbl.userID`은 반드시
테이블명을 명시해야 합니다. 그렇지 않으면 column명이 ambiguous하다는 오류를
만나게 됩니다.
<br><br>

참고로 예전에는 `WHERE`절의 조건을 이용해서 `INNER JOIN`을 표현하고 사용했었는데
다른 DBMS와의 호환성 문제 때문에 이렇게 하지 않는게 좋습니다. 아래와 같이
사용할 수 있습니다.

~~~sql

SELECT buytbl.userID, name, prodName, addr
FROM buytbl, usertbl
WHERE buytbl.userID = usertbl.userID 
      AND buytbl.userID = 'JYP'; 

~~~

명확하게 SQL을 작성하려면 각각의 column이 어느 테이블의 column인지를 명시하는게
좋습니다. 아래 코드 처럼 말이죠.

~~~sql

SELECT buytbl.userID, usertbl.name, buytbl.prodName, usertbl.addr
FROM buytbl, usertbl
WHERE buytbl.userID = usertbl.userID;

~~~

너무 복잡합니다. 간결하게 표현하기 위해 테이블에 `Alias`를 줄 수 있습니다.
아래의 코드를 보시죠.

~~~sql

SELECT B.userID, U.name, B.prodName, U.addr
FROM buytbl B, usertbl U
WHERE B.userID = U.userID;

~~~

테이블에 별칭을 주기 위해서는 간단히 `FROM` 절 뒤에 나오는 테이블의 이름뒤에
별칭을 붙여주면 됩니다. `Join`에서는 이런 방법이 적극적으로 권장됩니다.
<br><br>

이번에는 다른 예제에 대한 쿼리를 작성해보겠습니다.

> 물품을 한번이라도 구매한 기록이 있는 회원들에게 감사의 안내문을
보내기 위해 회원의 이름과 주소를 출력하세요.

~~~sql

SELECT U.userID, U.name, U.addr
FROM usertbl U
    INNER JOIN buytbl B
    ON U.userID = B.userID
ORDER BY U.userID;    

-- BBK	바비킴	서울
-- BBK	바비킴	서울
-- BBK	바비킴	서울
-- BBK	바비킴	서울
-- EJW	은지원	경북
-- EJW	은지원	경북
-- EJW	은지원	경북
-- JYP	조용필	경기
-- KBS	김범수	경남
-- KBS	김범수	경남
-- KBS	김범수	경남
-- SSK	성시경	서울

-- 동일한 결과가 여러개 존재합니다. 이런 경우 DISTINCT를 이용하면 됩니다.

SELECT DISTINCT U.userID, U.name, U.addr
FROM usertbl U
    INNER JOIN buytbl B
    ON U.userID = B.userID
ORDER BY U.userID;   

~~~

또 다음과 같이 작성할 수 도 있습니다.

~~~sql

SELECT U.userID, U.name, U.addr
FROM usertbl U
    WHERE EXISTS (
        SELECT *
        FROM buytbl B
        WHERE U.userID = B.userID );

~~~

`EXITS`는 `Subqeury` 수행 결과가 존재하면 `TRUE`를 리턴하고 `Subquery`의 결과
행이 존재하지 않으면 `FALSE`를 리턴합니다. 위의 예에서는 `U.userID`가 하나씩
`Subquery`에 대입되서 수행하고 결과가 있는지를 확인하고 결과가 있으면 SELECT하는
방식으로 동작하게 됩니다. `EXISTS` 대신 `IN`을 사용해도 되지만 Subquery를 사용할
경우 `EXISTS`가 효율이 좋습니다.
<br><br>

이번에는 세 개의 테이블을 조인해 보겠습니다.
<br><br>

아래의 그림과 같은 테이블 관계를 이용해 몇가지 문제를 풀어보도록 하죠.

{% include image.html
file='database-mysql-sql-advanced-schema.png'
%}

학생이름을 `PK`로 잡는 것은 타당하지 않지만 예제를 위해 그냥 넘어가기로 합니다.
<br><br>

먼저 테이블을 생성해야 겠죠. 다음의 쿼리문을 이용해 테이블을 생성하고 간단하게
데이터를 입력하도록 하겠습니다.

~~~sql

USE sqldb;

CREATE TABLE stdtbl (
    stdName    VARCHAR(10) NOT NULL PRIMARY KEY,
    addr       CHAR(4) NOT NULL
);

CREATE TABLE clubtbl (
    clubName   VARCHAR(10) NOT NULL PRIMARY KEY,
    roomNo     CHAR(4) NOT NULL
);

CREATE TABLE stdclubtbl (
    num        INT AUTO_INCREMENT NOt NULL PRIMARY KEY,
    stdName    VARCHAR(10) NOT NULL,
    clubName   VARCHAR(10) NOT NULL,
    FOREIGN KEY(stdName) REFERENCES stdtbl(stdName),
    FOREIGN KEY(clubName) REFERENCES clubtbl(clubName)
);
    
INSERT INTO stdtbl VALUES ('김범수', '경남');    
INSERT INTO stdtbl VALUES ('성시경', '서울');    
INSERT INTO stdtbl VALUES ('조용필', '경기');    
INSERT INTO stdtbl VALUES ('은지원', '경북');    
INSERT INTO stdtbl VALUES ('바비킴', '서울');

INSERT INTO clubtbl VALUES ('수영', '101호');
INSERT INTO clubtbl VALUES ('바둑', '102호');
INSERT INTO clubtbl VALUES ('축구', '103호');
INSERT INTO clubtbl VALUES ('봉사', '104호');

INSERT INTO stdclubtbl VALUES (NULL, '김범수', '바둑');
INSERT INTO stdclubtbl VALUES (NULL, '김범수', '축구');
INSERT INTO stdclubtbl VALUES (NULL, '조용필', '축구');
INSERT INTO stdclubtbl VALUES (NULL, '은지원', '축구');
INSERT INTO stdclubtbl VALUES (NULL, '은지원', '봉사');
INSERT INTO stdclubtbl VALUES (NULL, '바비킴', '봉사');

~~~

테이블을 생성하고 데이터를 입력했으면 다음의 문제를 해결해보도록 하죠.

{% include callout.html
type="danger"
content="학생을 기준으로 학생이름, 지역, 가입한 동아리, 동아리방을 출력하세요."
%}

~~~sql

SELECT S.stdName, S.addr, C.clubName, C.roomNo
FROM stdtbl S
    INNER JOIN stdclubtbl SC
    ON S.stdName = SC.stdName
    INNER JOIN clubtbl C
    ON SC.clubName = C.clubName
ORDER BY S.stdName;    

-- stdName, addr, clubName, roomNo
-- 김범수	경남	바둑	102호
-- 김범수	경남	축구	103호
-- 바비킴	서울	봉사	104호
-- 은지원	경북	봉사	104호
-- 은지원	경북	축구	103호
-- 조용필	경기	축구	103호

~~~

{% include callout.html
type="danger"
content="동아리를 기준으로 가입한 학생의 목록을 출력하세요."
%}

~~~sql

SELECT C.clubName, C.roomNo, S.stdName, S.addr
FROM stdtbl S
    INNER JOIN stdclubtbl SC
    ON SC.stdName = S.stdName
    INNER JOIN clubtbl C
    ON SC.clubName = C.clubName
ORDER BY C.clubName;    

-- clubName, roomNo, stdName, addr
-- 바둑	102호	김범수	경남
-- 봉사	104호	은지원	경북
-- 봉사	104호	바비킴	서울
-- 축구	103호	김범수	경남
-- 축구	103호	조용필	경기
-- 축구	103호	은지원	경북

~~~

---

## OUTER JOIN

`OUTER JOIN`은 Join의 조건에 만족되지 않는 행까지도 포함시키는 것입니다. 자주
사용되는 편은 아니지만 가끔 유용하게 사용되기 때문에 알아두어야 합니다.

~~~text

SELECT <열 목록>
FROM <첫 번째 테이블(LEFT 테이블)>
     <LEFT | RIGHT | FULL> OUTER JOIN <두 번째 테이블(RIGHT 테이블)>     
     ON <조인 조건>
WHERE <검색 조건>

~~~

예제를 위해 기존의 `sqldb`의 테이블을 이용하도록 하겠습니다. 다음과 같은
쿼리를 작성해보죠.

{% include callout.html
type="danger"
content="전체 회원의 구매기록을 조회하세요. 단, 구매 기록이 없는 회원도 출력되어야 합니다."
%}

~~~sql

SELECT U.userID, U.name, B.prodName, U.addr
FROM usertbl U
    LEFT OUTER JOIN buytbl B
    ON U.userID = B.userID
ORDER BY U.userID;    

~~~

{% include callout.html
type="danger"
content="한 번도 구매한 적이 없는 회원의 목록을 출력하세요."
%}

~~~sql

SELECT U.userID, U.name, B.prodName, U.addr
FROM usertbl U
    LEFT OUTER JOIN buytbl B
    ON U.userID = B.userID
WHERE B.prodName IS NULL    
ORDER BY U.userID; 

~~~

---

## CROSS JOIN

`CROSS JOIN`은 한쪽 테이블의 모든 행들과 다른 쪽 테이블의 모든 행을 Join 시키는
기능을 합니다. 그래서 `CROSS JOIN`의 결과 개수는 두 테이블 행의 개수를 곱한 결과가
됩니다. 이러한 `CROSS JOIN`을 우리는 일반적으로 `Cartesian Product`(카티션 프로덕트)
라고 부릅니다.
<br><br>

그림으로 표현하면 아래의 그림과 같습니다.

{% include image.html
file='database-mysql-sql-advanced-cross-join.png'
%}

~~~sql

SELECT *
FROM buytbl
    CROSS JOIN usertbl;
    
-- 다른 방식으로 표현하면 다음과 같습니다. 표준적인 방법은
-- 아니지만 많이 사용됩니다. 하지만 권장하지는 않습니다.
    
SELECT *
FROM buytbl, usertbl    

~~~

이런 `CROSS JOIN`에는 `ON`을 사용할 수 없습니다. 그러면 이러한 `CROSS JOIN`의
사용용도는 무엇일까요? 일반적으로 테스트할 목적으로 많은 양의 데이터를 생성할 때
주로 사용됩니다.

---

## SELF JOIN

`SELF JOIN`은 자신과 자기 자신이 Join하는 것을 의미합니다. 흔한 경우는 아니지만
아래 그림과 같은 조직도와 관련된 테이블인 경우에는 유용하게 사용됩니다.

{% include image.html
file='database-mysql-sql-advanced-self-join.png'
%}

위의 그림을 테이블로 나타내면 다음과 같습니다.

{% include image.html
file='database-mysql-sql-advanced-self-join-table.png'
%}

위의 그림에서 **이부장**은 직원이면서 동시에 상관이기 때문에 직원 column과
상관 column 둘 다 존재합니다. 만약, 우대리 상관의 구내번호를 알려면 `EMP`
column과 `MANAGER` column을 join해야 이부장의 구내번호를 알 수 있습니다.

~~~sql

USE sqldb;

CREATE TABLE emptbl (
    emp      CHAR(3),
    manager  CHAR(3),
    empTel   VARCHAR(8)
);    

INSERT INTO empTbl VALUES('나사장', NULL, '0000'); 
INSERT INTO empTbl VALUES('김재무', '나사장', '2222'); 
INSERT INTO empTbl VALUES('김부장', '김재무', '2222-1'); 
INSERT INTO empTbl VALUES('이부장', '김재무', '2222-2'); 
INSERT INTO empTbl VALUES('우대리', '이부장', '2222-2-1'); 
INSERT INTO empTbl VALUES('지사원', '이부장', '2222-2-2'); 
INSERT INTO empTbl VALUES('이영업', '나사장', '1111'); 
INSERT INTO empTbl VALUES('한과장', '이영업', '1111-1'); 
INSERT INTO empTbl VALUES('최정보', '나사장','3333'); 
INSERT INTO empTbl VALUES('윤차장', '최정보', '3333-1'); 
INSERT INTO empTbl VALUES('이주임', '윤차장', '3333-1-1');


SELECT A.emp AS '부하직원', B.emp AS '직속상관', B.empTel AS '전화번호'
FROM emptbl A
    INNER JOIN emptbl B
    ON A.manager = B.emp
WHERE A.emp = '우대리';    

-- 부하직원, 직속상관, 전화번호
-- 우대리	이부장	2222-2

~~~

---

## UNION/UNION ALL/IN/NOT IN

`UNION`은 두 쿼리의 결과를 행으로 합치는 연산입니다.
<br><br>

아래의 그림은 `UNION`의 예시입니다.

{% include image.html
file='database-mysql-sql-advanced-union.png'
%}

위의 예에서 SELECT 문장 1과 SELECT 문장 2의 결과 열의 개수가 같아야 합니다.
데이터 형식도 각 열 단위로 같거나 서로 호환이 되는 형식이어야 합니다. `UNION`만
사용하면 중복된 열은 제거되고 데이터가 정렬되어 나오며, `UNION ALL`을 사용하면
중복된 열까지 모두 출력됩니다.
<br><br>

`NOT IN`은 첫번째 쿼리의 결과 중에서, 두번째 쿼리에 해당하는 것을 제외하기
위한 구문입니다. 예를 들면 다음과 같습니다.

{% include callout.html
type="danger"
content="sqldb의 모든 사용자를 조회하되 전화가 없는 사람을 제외하고 출력하세요."
%}

~~~sql

SELECT name, CONCAT(mobile1, mobile2)
FROM usertbl
WHERE name NOT IN ( SELECT name 
                    FROM usertbl
                    WHERE mobile1 IS NULL );

~~~

`IN`은 `NOT IN`과 반대로 첫번째 쿼리의 결과 중에서, 두번째 쿼리에 해당하는
것만 조회하기 위해서 사용합니다.

{% include callout.html
type="danger"
content="전화가 없는 사람만 조회하세요."
%}

~~~sql

SELECT name, CONCAT(mobile1, mobile2)
FROM usertbl
WHERE name IN ( SELECT name 
                FROM usertbl
                WHERE mobile1 IS NULL );   

~~~

---

## 동적 SQL

`PREPARE`와 `EXECUTE`문에 대해서 알아보겠습니다. 간단하게 말해
`PREPARE`는 SQL문을 실행하지 않고 미리 준비만 해 놓고, `EXECUTE`문은
준비한 쿼리문을 실행시키는 것입니다. 그리고 실행이 된 후에는
`DEALLOCATE PREPARE`를 이용해 해제해 주는게 좋습니다.
<br><br>

이렇게 미리 쿼리문을 준비한 후에 나중에 실행하는 것을 `동적 SQL`이라고 합니다.
<br><br>

아래의 코드를 이용해 살펴보겠습니다.

~~~sql

USE sqldb;

DROP TABLE IF EXISTS myTable;

CREATE TABLE myTable (
    id     INT AUTO_INCREMENT PRIMARY KEY,
    mDate  DATETIME
);

SET @curDATE = CURRENT_TIMESTAMP();

PREPARE myQuery FROM 'INSERT INTO myTable VALUES(NULL,?)';

EXECUTE myQuery USING @curDATE;

DEALLOCATE PREPARE myQuery;

SELECT * FROM myTable;     

~~~


End.

{% include links.html %}
