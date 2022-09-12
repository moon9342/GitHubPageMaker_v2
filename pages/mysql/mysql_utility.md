---
title: MySQL Utility
sidebar: mysql_sidebar
summary: "MySQL은 여러가지 유용한 기능들을 우리에게 제공해주고 있습니다. 
이번장에서는 알아두면 좋을 몇가지 utility 기능들에 대해서 알아보도록 하겠습니다."
permalink: mysql_utility.html
folder: mysql
---

## SQL문 자동생성

`Workbench`의 Navigator의 Schemas 탭에 있는 `shopDB`의 `memberTBL`을 선택한 후
마우스 오른쪽 팝업 메뉴를 이용해 `Send to SQL Editor` > `Create Statement`를 이용하면
해당 테이블을 생성하기 위한 SQL이 출력되는 것을 볼 수 있습니다. 다른 형식의 구문도
생성이 가능하며 `View`에 대한 SQL구문도 생성이 가능합니다.

{% include image.html
file='database-mysql-utility-create-sql.png'
%}

---

## MySQL 관리

`Workbench`의 Navigator의 Administration 탭에 있는 `Server Status`를 클릭하면 현재 접속된 서버의
상태를 알 수 있습니다. 서버의 가동 상태, 포트, 환경 파일의 경로, 메모리와 CPU 사용상태등을 확인할
수 있습니다.
<br><br>

만약 `Could not acquire management access for administration` 오류가 발생하면
`Workbench`의 버전을 `MySQL Workbench 8.0.20`으로 변경하면 해결됩니다.
버전이 올라가면서 생긴 오류인듯 합니다.
<br><br>

[MySQL Workbench 8.0.20 Download](https://downloads.mysql.com/archives/workbench/){: target="_blank" }
<br><br>

[MySQL Workbench 8.0.20 Download - Google Drive](https://drive.google.com/file/d/1toievs4aib8FOrLm-nroxN6_tsRnhT-l/view?usp=sharing){: target="_blank" }

{% include image.html
file='database-mysql-utility-server-status.png'
%}

`Workbench`의 Navigator의 Administration 탭에 있는 `Client Connections`를 클릭하면 연결된 client와
connection수를 확인할 수 있고 필요하다면 연결을 강제로 종료시킬 수 도 있습니다.

{% include image.html
file='database-mysql-utility-client-connections.png'
%}

`User and Privileges`에서는 MySQL 사용자 관리를 할 수 있습니다. 이 부분은 조금 뒤에 다시 알아보도록 하죠.
<br><br>

`Status and System Variables`에서는 MySQL 서버에 설정된 시스템 변수들을 확인하고 변경할 수 있습니다.
<br><br>

`INSTANCE > Startup / Shutdown`에서 MySQL 서버의 현재 작동 상태를 확인할 수 있고 또한 서버의 중지와 시작을
할 수 있습니다. 여기서 `Instance`(인스턴스)라는 용어는 MySQL 서버가 메모리상에서 실행중인 상태를 지칭하는 용어입니다.

{% include image.html
file='database-mysql-utility-startup-shutdown.png'
%}

`INSTANCE > Server Logs`는 서버에 기록된 오류, 경고등의 로그를 확인할 수 있습니다. 만약 서버에 문제가 발생하면
이 내용을 참조하여 문제를 해결해야 합니다.
<br><br>

`INSTANCE > Options File`은 MySQL의 가장 중요한 설정파일인 `my.ini` 파일의 내용을 GUI 모드로 보여줍니다. 여기서
설정을 변경하면 실제 파일의 내용이 변경되게 됩니다.
<br><br>

마지막으로 `PERFORMACE` 부분의 3가지 링크는 서버의 각 상태를 알기 쉽게 요약 설명해주는 기능을 합니다. 추후에 튜닝을
할 경우 참조할 내용입니다.

---

## Query Editor

우리가 가장 많이 사용하는 창입니다. SQL 구문을 입력하고 실행하는 에디터 정도로 인식하시면 됩니다.
<br><br>

몇가지 기능만 살펴보겠습니다.
<br><br>

첫번째는 예약어를 대문자 또는 소문자로 변경하는 기능입니다. SQL의 예약어는 대문자로 표기하는것이
관용적 표현입니다. 우리가 SQL 구문을 작성할 때 직접 대소문자를 구별해서 작성할 수 있지만
`Edit > Format > UPCASE Keywords`를 이용하면 예약어를 모두 대문자로 변경할 수 있습니다.

{% include image.html
file='database-mysql-utility-upcase-keywords.png'
%}

두번째는 자동으로 제공되는 자동완성 기능입니다. 만약 자동완성을 자동으로 실행되지 않는다면
`Ctrl + Space`을 이용해 자동완성을 실행할 수 있습니다.
<br><br>

세번째는 주석처리입니다. 주석으로 처리할 여러 행을 선택한 후 모든 툴에서 공통적으로 사용되는
`Ctrl + /`를 이용해 주석처리를 할 수 있습니다.
<br><br>

네번째는 많이 사용되는 것 중 하나인 `Beautify Query`입니다. 우리가 작성한 SQL 구문을
표준 형태로 다시 표현해 줍니다.  `Ctrl + B`를 이용하거나 메뉴를 이용하시면 됩니다.
<br><br>

다섯번째는 출력되는 Query의 개수를 제한하는 기능인데 기본적으로 `1000 rows`로 설정되어 있습니다.
`Don't Limit`를 선택하고 실제 Query를 실행해 결과를 살펴보시기 바랍니다.

{% include image.html
file='database-mysql-utility-dont-limit.png'
%}

여섯번째는 자동완성되는 예약어를 대문자로 설정하는 것입니다. 자동완성되는 예약어는 기본적으로 소문자로 표현되는데
이를 대문자로 바꿀 수 있습니다. `Edit > Preferences`를 선택한 후 `SQL Editor > Query Editor`를 선택하고
`Use UPPERCASE keywords on completion`을 체크하시면 됩니다.
<br><br>

마지막으로 처리된 결과 데이터를 `CSV`, `JSON`, `XML`등으로 변환해주는 기능도 가지고 있습니다.

---

## 사용자 관리

우리는 지금까지 `root` 계정을 이용해서 MySQL을 사용했습니다. 하지만 실무에서는 `MySQL` 데이터베이스를 혼자
사용하는 것이 아니기 때문에 여러 계정을 만들어 사용해야 합니다. 또한 여러 사용자에게 모두 관리자 권한을
부여한다면 문제가 발생할 여지가 많습니다. 따라서 모든 권한이 아닌 적당한 권한을 부여해 사용자를 관리할
필요가 있습니다. 아래의 그림을 보시죠.

{% include image.html
file='database-mysql-privileges-concept-figure.png'
%}

용어에 대해서 주의해야 하는데 `Privileges`(권한)은 단편적인 것들을 의미합니다. 즉, SELECT 권한,
INSERT 권한, DELETE 권한 등을 의미합니다. `Role`(역할)은 이런 권한의 집합을 의미합니다.
`DBA`의 `Role`에는 모든 권한이 포함되어 있습니다.
<br><br>

`Workbench`를 이용해 새로운 사용자 `director`를 생성하고 데이터베이스 관리자(DBA)의 `role`을
부여해보도록 하겠습니다.
<br><br>

`User and Privileges > Add Acount`를 클릭해 새로운 사용자 `director`를 추가합니다. 보안을
좀 더 강화하고 싶으면 `Limit to Hosts Matching`의 `%` 대신 접속할 컴퓨터의 IP 주소를 입력하면
됩니다. `%` 기호는 모든 컴퓨터를 지칭하기 때문에 id와 password만 안다면 어디에서나 접속이 가능합니다.

{% include image.html
file='database-mysql-privileges-add-user-director.png'
%}

`Account Limits` 탭에서는 최대 쿼리 수, 최대 업데이트 수, 시간당 최대 접속 수, 실시간 동시 접속
최대 수등을 설정할 수 있습니다. `0`값은 특별이 제한이 없다는 의미입니다.
<br><br>

`Administrative Roles`에서는 MySQL 자체에 대한 권한을 설정할 수 있습니다. `DBA` Role로 체크한 후
사용자를 생성합니다.
<br><br>

이 과정자체를 SQL 구문으로 수행할 수 있습니다. 또한 SQL구문을 이용하면 조금 더 세밀한 권한 부여도 가능합니다.
<br><br>

이번에는 `ceo` 계정을 추가하고 MySQL의 모든 데이터에 대해 `SELECT`(읽기) 권한을 부여해 보겠습니다.

{% include image.html
file='database-mysql-privileges-add-user-ceo.png'
%}

마지막으로 `staff` 계정을 추가하고 아래의 권한을 부여합니다.

{% include callout.html
type="danger"
content="`shopDB` 데이터베이스의 모든 테이블에 대해 읽기(SELECT), 쓰기(INSERT, UPDATE, DELETE)
권한을 부여합니다. 또 `Stored Procedure`를 생성하고 수정할 수 있는 권한도 부여합니다.
추가로 `employees` 데이터베이스의 테이블에 대해서는 읽기(SELECT) 권한만 부여합니다."
%}

이번에는 각 `Schema`에 대한 권한을 부여해야 하기 때문에 `Schema Privileges` 탭을 이용해야
합니다. `Add Entry`를 이용해 위에 명시한 내용으로 권한을 부여합니다.

{% include image.html
file='database-mysql-privileges-add-user-staff.png'
%}

자 이제 `Workbench`를 종료한 후 다시 실행시켜 새로운 연결을 생성한 후 우리가 설정한 권한으로
새로운 사용자가 작업을 수행할 수 있는지를 확인하면 됩니다. 이 부분은 개별적으로 해보도록 하죠.


End.

{% include links.html %}
