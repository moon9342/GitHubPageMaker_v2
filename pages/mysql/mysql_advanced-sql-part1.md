---
title: MySQL SQL 고급(1)
sidebar: mysql_sidebar
summary: "SQL 기본에서 SELECT, INSERT, UPDATE, DELETE문에 대해서 알아보았습니다. 
기본적인 SQL문을 익혔으니 이제 SQL에 대해서 조금 더 깊이 있게 알아보도록 하겠습니다."
permalink: mysql_advanced-sql-part1.html
folder: mysql
---

## SQL Data Type

제일먼저 알아볼 부분은 `Data Type`입니다. 사실 먼저 나왔어야 하는 내용이지만 기본적인
SQL을 알고 얘기하는게 조금 더 이해하지 좋습니다.
<br><br>

`MySQL`에서 사용하는 `Data Type`은 총 30개 정도 됩니다. 이를 다 쓰는것은 아니며
자주 사용되는것은 어느정도 정해져 있습니다.
<br><br>

* 숫자 데이터 형식

{% include image.html
file='database-mysql-sql-datatype-numeric.png'
%}

범위에 따라 적절한 데이터 타입을 사용하면 됩니다. `DECIMAL`은 정확한 수치를
저장할 수 있고 `FLOAT`나 `DOUBLE`은 근사치를 저장합니다. 대신 상당히 큰 숫자를
저장할 수 있다는 장점이 있습니다. 따라서 소수점이 들어간 숫자를 저장할 때는
가능한 `DECIMAL`을 사용하는 것이 좋습니다. 참고로 부호없는 정수를 사용할 경우
뒤쪽에 `UNSIGNED` 예약어를 붙여주면 됩니다. 하지만 많이 사용되는 것은 아닙니다.
<br><br>

* 문자 데이터 형식

{% include image.html
file='database-mysql-sql-datatype-string.png'
%}

위의 그림에도 설명되어 있지만 `CHAR`은 고정길이 데이터 타입입니다. `CHAR(100)`
이라고 하고 `ABC`만 저장하면 뒤의 97자리는 낭비하게 됩니다. 반면 `VARCHAR`는
그렇지 않습니다. 필요한 자리수만 사용하게 되죠. 즉 데이터공간을 효율적으로 사용할
수 있습니다. 그럼 왜 `CHAR`을 사용할까요? 그 이유는 `INSERT`와 `UPDATE`시
실행 속도가 빠르기 때문입니다.
<br><br>

`TEXT`는 대용량의 글자를 저장하기 위한 형식이고 `BLOB`는 사진이나 동영상과 같은
대용량의 이진 데이터를 저장할 때 사용됩니다.
<br><br>

* 날짜 데이터 형식

{% include image.html
file='database-mysql-sql-datatype-date.png'
%}

날짜와 시간형 데이터에 대해서는 다음의 코드로 간단하게 알아보도록 하죠.

~~~sql

SELECT CAST('2020-01-05 13:45:23.123' AS DATE);
SELECT CAST('2020-01-05 13:45:23.123' AS TIME);
SELECT CAST('2020-01-05 13:45:23.123' AS DATETIME);

~~~

* 기타 데이터 형식

{% include image.html
file='database-mysql-sql-datatype-json.png'
%}

* LONGTEXT, LONGBLOB

`MySQL`은 `LOB`(Large Object :  대량의 데이터)를 저장하기 위해 `LONGTEXT`,
`LONGBLOB` 데이터 형식을 지원합니다. 지원되는 크기는 약 4GB 입니다.

---

## SQL 변수 사용

`SQL`도 다른 프로그래밍 언어처럼 `Variable`(변수)를 선언하고 사용할 수 있습니다.
변수의 선언과 값의 대입은 다음 처럼 사용합니다.

~~~text

SET @변수이름 = 변수의 값;

SELECT @변수이름;

~~~

위에서 보듯이 SQL문에서 변수는 `@`기호를 이용하여 사용합니다. 대신 `Stored Procedure`나
함수 안에서의 변수를 사용할 때는 `@`기호를 빼고 사용합니다. 함수를 선언하고 사용하는
방법은 뒤쪽에서 다시 알아보도록 하겠습니다.
<br><br>

먼저 간단히 변수의 사용법을 실습해 보도록 하죠.

~~~sql

USE sqldb;

SET @myVar1 = 5;
SET @myVar2 = 3;
SET @myVar3 = 3.1415;
SET @myVar4 = '가수 이름 : ';

SELECT @myVar1;

SELECT @myVar2 + @myVar3;

-- 변수는 일반적인 SELECT ~ FROM 구문에서 같이 사용할 수 있습니다.

SELECT @myVar4, name 
FROM usertbl
WHERE height > 180;

~~~

주의해야 할 점은 SQL문에 항상 변수를 같이 사용할 수 있는것은 아닙니다.
조금 다른 예제도 한번 살펴보죠.

~~~sql

SET @myVar5 = 3;

SELECT name, height
FROM usertbl
ORDER BY height
LIMIT @myVar5;

~~~

변수를 설정하고 `LIMIT`의 값으로 변수를 사용하려고 했지만 `Syntax Error`(문법오류)가
발생합니다. 이런 경우 다음과 같이 동적 SQL문을 이용해 처리하면 됩니다.

~~~sql

SET @myVar5 = 3;

PREPARE myQuery
    FROM 'SELECT name, height FROM usertbl ORDER BY height LIMIT ?';
    
EXECUTE myQuery USING @myVar5

~~~

---

## SQL 데이터 형 변환

가장 일반적으로 사용되는 데이터 형 변환에 관련된 함수는 `CAST()`, `CONVERT()`
입니다. 형식만 다를 뿐 둘 다 비슷한 역할을 합니다.
<br><br>

예제를 이용해 알아보도록 하겠습니다.

~~~sql

USE sqldb;

SELECT AVG(amount) AS '평균 구매 개수'
FROM buytbl;

SELECT CAST(AVG(amount) AS SIGNED INTEGER) AS '평균 구매 개수'
FROM buytbl;

-- 반올림한 결과를 확인할 수 있습니다. 

-- 다양한 구분자를 날짜 형식으로 바꿀 수 있습니다.

SELECT CAST('2021$01$05' AS DATE);
SELECT CAST('2021/01/05' AS DATE);

-- 쿼리의 결과를 보기 좋게 만들기 위해서도 사용할 수 있습니다. 

SELECT num, 
       CONCAT(CAST(price AS CHAR(10)), ' * ', CAST(amount AS CHAR(4))) AS '단가 * 수량', 
       price*amount AS '구매액'
FROM buytbl;

-- num, 단가 * 수량, 구매액
-- 1	30 * 2	60
-- 2	1000 * 1	1000
-- 3	200 * 1	200
-- 4	200 * 5	1000
-- 5	50 * 3	150
-- 6	80 * 10	800
-- 7	15 * 5	75
-- 8	15 * 2	30
-- 9	50 * 1	50
-- 10	30 * 2	60
-- 11	15 * 1	15
-- 12	30 * 2	60

~~~

많이 혼동되는 것 중 하나가 `Implicit Conversion`(묵시적 형변환)입니다.
`CAST()`를 이용하는 방법을 `Explicit Conversion`(명시적 형변환)이라고 합니다.
묵시적 형변환은 `CAST()`등을 사용하지 않고 형이 변환되는 것을 의미합니다.
<br><br>

아래의 코드를 보면서 다양한 경우에 대한 묵시적 형변환에 대해
이해해보도록 하죠.

~~~sql

SELECT '100' + '200';   
  -- 문자와 문자를 더하는 경우 숫자로 변경되서 연산
  -- 300
  -- 한가지 조심해야 하는건 다른 DBMS에서는 문자열 연결로 처리되는 경우가 있음.
  -- '100200'
                
SELECT 100 + '200';         
  -- 문자와 숫자를 더하는 경우 문자를 숫자로 변경되서 연산
  -- 300

SELECT 'Hello' + 'World';   
  -- 더하기 연산을 수행하기 위해 문자를 숫자로 변경
  -- 문자 숫자는 온전히 숫자로 바뀌지만 문자로 시작하는 경우 0으로 바뀐다는 점.
  -- 0

SELECT 100 + '35World';   
  -- 더하기 연산을 수행하기 위해 문자를 숫자로 변경
  -- 문자가 숫자로 시작할 때 숫자부분만 숫자로 변경되고 문자는 버림
  -- 135

SELECT CONCAT('Hello', 'World');  
  -- 문자열 연결
  -- 'HelloWorld'

SELECT CONCAT('100', 200);    
  -- 문자열 연결
  -- '100200'

SELECT 1 > '2hoho';           
  -- 1 > 2 의 의미가 되기 때문에 False(0)으로 출력
                              
SELECT 3 > '2hoho';           
  -- 3 > 2 의 의미가 되기 때문에 True(1)으로 출력                              

SELECT 0 = 'Hello';           
  -- 0 = 0, 문자가 숫자와 비교하기 위해 숫자로 변경 결국 True(1)

~~~

---

## MySQL 내장함수

`MySQL`은 많은 내장 함수를 포함하고 있습니다. 수백개가 넘는 함수를 모두 알아보는것은
무리가 있으니 자주 사용되는 것들을 코드를 통해 알아보도록 하겠습니다.
<br><br>

* 제어 흐름 함수

~~~sql

SELECT IF(100 > 200, '참이다', '거짓이다');   -- 거짓이다

-- 우리가 일반적으로 알고 있는 IF문을 함수로 만들어 놓은 것입니다. 
-- 첫번째 인자가 조건이고 조건이 참이면 두번째 인자를 거짓이면 세번째 인자를
-- 선택합니다.

SELECT IFNULL(NULL, 'NULL 입니다.');       -- NULL 입니다.
SELECT IFNULL(100, 'NULL이 아닙니다.');     -- 100

-- IFNULL(인자1, 인자2) 형식에서 
-- 인자1이 만약 NULL이면 인자2를 반환하고
-- 인자1이 NULL이 아니면 인자1을 반환합니다.

SELECT NULLIF(100, 100), NULLIF(100, 200);  -- NULL    100

-- NULLIF(인자1, 인자2) 형식에서
-- 인자1과 인자2가 같으면 NULL을 반환하고
-- 인자1과 인자2가 다르면 인자1을 반환합니다.

SET @my_var = 5;

SELECT CASE @my_var
            WHEN 1 THEN '일'
            WHEN 5 THEN '오'
            WHEN 10 THEN '십'
            ELSE '알수없음'
       END AS 'CASE 연습'     
       
-- 다중 분기에 가장 흔하게 사용되는 CASE문입니다. 함수는 아니지만
-- 많이 사용되기 때문에 구문을 알아두어야 합니다. 

~~~

* 문자열 함수

~~~sql

SELECT ASCII('A'), CHAR(66);

-- 문자의 ASCII값을 리턴하거나 아스키 코드값에 해당하는 문자를 리턴합니다.
-- workbench에서는 BLOB로 출력되는데 Open Value In Viewer를 이용하면
-- 정상적으로 확인할 수 있습니다. 명령어 라인에서는 정상 출력됩니다.

SELECT CHAR_LENGTH('abcde'),   -- 5
       CHAR_LENGTH('홍길동'),    -- 3
       LENGTH('abcde'),        -- 5
       LENGTH('홍길동');         -- 9  (한글은 3Byte, 3*3 =9)

-- MySQL은 기본적으로 UTF-8을 사용합니다.

SELECT CONCAT('Hello', 'World'),           -- HelloWorld
       CONCAT_WS('-', '2021','05', '30');  -- 2021-05-30
       
-- CINCAT_WS()는 구분자를 이용해서 문자열을 연결합니다.


SELECT ELT(3, '홍길동', '신사임당', '아이유', '김연아'),         -- 아이유 
       FIELD('김연아', '홍길동', '신사임당', '아이유', '김연아'),  -- 4
       INSTR('이것은소리없는 아우성', '소리');                   -- 4

-- ELT()는 위치 번째에 해당하는 문자열을 반환합니다.
-- FIELD()는 찾을 문자열의 위치를 찾아서 반환합니다.
-- INSTR()은 기준문자열에서 부분 문자열을 찾아서 그 시작위치를 반환합니다.

SELECT FORMAT(1234567.1415926535, 3);  -- 1,234,567.142    

-- 숫자를 소수점 아래 자릿수까지 표현합니다. 3자리 마다 콤마를 표현하고 반올림 처리합니다.

SELECT INSERT('ABCDEFGH', 3, 2, '@@@'),              -- AB@@@EFGH
       LEFT('ABCDEFGH', 3),                          -- ABC
       UPPER('abcdefg'),                             -- ABCDEFG
       TRIM('   이것은 소리없는 아우성   '),               -- 이것은 소리없는 아우성
       TRIM(BOTH '@' FROM '@@@이것은 소리없는 아우성@@@'), -- 이것은 소리없는 아우성
       REPEAT('김연아', 3),                            -- 김연아김연아김연아
       REPLACE('이것은 소리없는 아우성', '소리', '양심'),    -- 이것은 양심없는 아우성
       REVERSE('김연아'),                              -- 아연김
       CONCAT('이것은', SPACE(5), '소리없는 아우성'),      -- 이것은     소리없는 아우성
       SUBSTRING('이것은 소리없는 아우성', 3, 5);          -- 은 소리없

~~~

* 수학 함수

~~~sql

SELECT ABS(-100),                 -- 100
       CEILING(3.1415),           -- 4
       FLOOR(3.1415),             -- 3
       ROUND(3.1415),             -- 3 
       MOD(157, 10),              -- 7
       RAND(),                    -- 0.8718332125542995
       TRUNCATE(12345.12345, 2);  -- 12345.12
       
-- RAND()는 0과 1사이의 실수를 랜덤하게 생성합니다. 
-- TRUNCATE는 소수점 기준으로 위치를 구한 후 나머지는 버립니다.       

~~~ 

* 날짜 및 시간함수

~~~sql

SELECT ADDDATE('2021-01-31', INTERVAL 31 DAY),   -- 2021-03-03
       ADDDATE('2021-01-31', INTERVAL 1 MONTH),  -- 2021-02-28
       CURDATE(),                                -- 2021-12-07       
       NOW(),                                    -- 2021-12-07 14:12:19 
       YEAR(CURDATE()),                          -- 2021
       DATE(NOW());                              -- 2021-12-07

~~~

* 시스템 정보 함수

~~~sql

SELECT USER(),            -- root@localhost (현재 사용자)
       DATABASE(),        -- shopdb (현재 선택된 Database)
       FOUND_ROWS(),      -- 1 (바로 이전의 SELECT 문에서 조회된 행의 개수)
       ROW_COUNT(),       -- 0 (바로 이전의 Insert, Update, Delete 문에서 처리된 행의 개수)
       SLEEP(5);          -- 쿼리의 실행을 입력된 숫자 초만큼 지연한다.

~~~

살펴본 것처럼 상당히 많은 내장함수를 보유하고 있고 이런 내장 함수를 이용하면
데이터 처리를 조금 더 쉽게 할 수 있습니다.

---

## LONGTEXT & LONGBLOB

데이터 타입때 잠깐 언급했던 두 개의 특이한 타입에 대해 어떻게 사용하면 되는지를
알아보겠습니다. 일반적인 다른 데이터 타입은 사용하는데 크게 무리가 없지만
이 두개의 특이한 데이터 타입은 조금 다르게 사용해야 합니다.
<br><br>

먼저 아래의 링크에서 입력으로 사용할 대용량의 TEXT 파일과 동영상(BLOB)파일을
다운로드 합니다.
<br><br>

[영화 대본(Schindler.txt) - Google Drive](https://drive.google.com/file/d/1fBC85nmtGUZZSWYM_ADIyzyXfXB4xPA7/view?usp=sharing){: target="_blank" }
<br><br>

[영화 동영상(Schindler.mp4) - Google Drive](https://drive.google.com/file/d/1xQMzEVff2GYqpiwPpvi3SlJDkavz3RiD/view?usp=sharing){: target="_blank" }
<br><br>

아래의 코드를 이용해 영화 데이터베이스 및 영화 테이블을 생성하고 데이터를
입력합니다.

~~~sql

CREATE DATABASE moviedb;       


USE moviedb;

CREATE TABLE movietbl (
    movie_id         INT,
    movie_title      VARCHAR(30),
    movie_director   VARCHAR(30),
    movie_star       VARCHAR(30),
    movie_script     LONGTEXT,
    movie_film       LONGBLOB
) DEFAULT CHARSET=utf8mb4;    

-- 데이터 입력

-- 파일을 데이터로 입력하려면 LOAD_FILE() 함수 사용

INSERT INTO movietbl VALUES (1, '쉰들러 리스트', '스필버그', '리암 니슨',
    LOAD_FILE('C:/sql/movies/Schindler.txt'),
    LOAD_FILE('C:/sql/movies/Schindler.mp4')
);
    
SELECT * FROM movietbl;    

-- 결과를 살펴보면 영화 대본과 동영상은 NULL값으로 표시되고 입력되지 않았음.

~~~

위에서 살펴본 것처럼 정상적으로 대본과 동영상이 입력되지 않았습니다. 두가지 이유때문에
입력이 되지 않았는데 첫번째는 바로 최대 패킷 크기를 넘었기 때문이고 두번째는 파일을
업로드/다운로드할 폴더에 대한 권한을 허용하지 않아서 입니다.
<br><br>

환경설정파일 `my.ini`를 수정해야 합니다.
<br><br>

`Workbench`를 종료하고 `C:\ProgramData\MySQL\MySQL Server 8.0\my.ini` 파일을
찾아 에디터로 파일을 열고 `max_allowed_packet`을 찾아 그 값을 현재 `4M`에서
`1024M`로 변경합니다.
<br><br>

또 `secure-file-priv`를 찾습니다. 현재 폴더 하나가 설정되어 있는데 그 밑에
추가로 현재 동영상이 들어 있는 폴더를 설정합니다.

{% include callout.html
type="danger"
content="`secure-file-priv=C:/sql/movies`"
%}

그런다음 MySQL Server를 재 시작해야 합니다. **관리자 권한**으로
`CMD`창을 열고 `CMD`창에서 다음의 명령을 입력하면 서비스를 재 시작할 수 있습니다.

{% include callout.html
type="danger"
content="`NET STOP MySQL80`<br><br>`NET START MySQL80`"
%}

재 시작한 후 `INSERT` 문을 다시 실행
시키면 정상적으로 입력되는 것을 확인할 수 있습니다.
<br><br>

자 그럼 이번에는 입력된 데이터를 파일로 다운로드 해 보겠습니다.

~~~sql

SELECT * FROM movietbl;    

SHOW variables LIKE 'max_allowed_packet';
SHOW variables LIKE 'secure_file_priv';

-- LONGTEXT 파일 다운로드
SELECT movie_script
FROM movietbl
WHERE movie_id = 1
INTO OUTFILE 'C:/sql/movies/text_out.txt'
LINES TERMINATED BY '\\n';

-- LONGBLOB 파일 다운로드
SELECT movie_film
FROM movietbl
WHERE movie_id = 1
INTO DUMPFILE 'C:/sql/movies/video_out.mp4';

~~~

---

## JSON 데이터

`JSON`(JavaScript Object Notation)은 데이터 교환을 위한 개방형 표준
포맷을 의미합니다. `Key`와 `Value`의 쌍으로 데이터를 표현하는 방식으로
비록 JavaScript의 객체형태에서 그 모양을 차용했지만 실제로 프로그래밍언어에
종속되지 않는 독립적인 데이터 포맷입니다.
<br><br>

`JSON`의 가장 단순한 형태는 다음과 같습니다.

~~~json

{
  "user_id"     : "iu",
  "user_name"   : "아이유",
  "user_addr"   : "서울",
  "user_mobile" : "01012345678"
}

~~~

`MySQL`은 `JSON` 관련된 내장 함수를 제공해 줍니다. 다음의 코드를 보시죠.

~~~sql

USE sqldb;

SELECT JSON_OBJECT('name', name, 'height', height) AS 'JSON'
FROM usertbl;

-- {"name": "바비킴", "height": 176}
-- {"name": "은지원", "height": 174}
-- {"name": "조관우", "height": 172}
-- {"name": "조용필", "height": 166}
-- {"name": "김범수", "height": 173}
-- {"name": "김경호", "height": 177}
-- {"name": "임재범", "height": 182}
-- {"name": "이승기", "height": 182}
-- {"name": "성시경", "height": 186}
-- {"name": "윤종신", "height": 170}

SET @json = (SELECT JSON_OBJECT('name', name, 'height', height) AS 'JSON' FROM usertbl);
-- Error Code: 1242. Subquery returns more than 1 row

SELECT @json := JSON_OBJECT('name', name, 'height', height) FROM usertbl;
-- SELECT 문에서는 '=' 가 비교연산자로 이용되기 때문에 변수에 값을 대입하기 위해서는 ':='을 사용

SELECT @json;

~~~

변수에 `JSON` 데이터가 저장되어 있을 때 이를 사용하는 몇가지 함수를 알아보도록 하겠습니다.
변수에 아래와 같은 형식의 JSON 데이터를 저장하는 방법에 대해서는 고민해보도록 하죠.

~~~sql

SET @json = '{
    "mytbl" : [
        {"name": "바비킴", "height": 176},
        {"name": "은지원", "height": 174},
        {"name": "조관우", "height": 172},
        {"name": "조용필", "height": 166},
        {"name": "김범수", "height": 173},
        {"name": "김경호", "height": 177},
        {"name": "임재범", "height": 182},
        {"name": "이승기", "height": 182},
        {"name": "성시경", "height": 186},
        {"name": "윤종신", "height": 170}        
    ]
}';

SELECT JSON_VALID(@json);   -- 문자열이 JSON 형식을 만족하면 1, 그렇지 않으면 0
SELECT JSON_SEARCH(@json, 'one', '은지원');
-- "$.mytbl[1].name"    index는 0부터 시작, 두번째 index의 name key값에 존재.
--                      'one'은 처음으로 매치되는 하나만 반환, 'all'로 하면 매치되는 모든 것을 반환. 
SELECT JSON_EXTRACT(@json, '$.mytbl[3].height');   -- 166
SELECT JSON_INSERT(@json, '$.mytbl[0].mobile', '01012345678');
SELECT JSON_REPLACE(@json, '$.mytbl[0].name', '아이유');
SELECT JSON_REMOVE(@json, '$.mytbl[1]');  -- 특정 key를 지우는 것도 가능하다.

~~~


End.

{% include links.html %}
