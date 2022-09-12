---
title: MySQL Environment
sidebar: mysql_sidebar
summary: "MySQL 설치와 환경설정입니다."
permalink: mysql_environment.html
folder: mysql
---

## Install

MySQL을 설치해보도록 하죠. [MySQL](https://www.mysql.com/){: target="_blank" }에
접속해서 일단 프로그램을 다운로드 받아야 합니다.
<br><br>

다운로드 메뉴에 들어가서 맨 하단에 `MySQL Community (GPL) Downloads »`를 클릭합니다.
우리는 무료로 사용할 수 있는 Community Edition을 사용해야 합니다.
<br><br>

다운로드 링크들이 여러개 나오게 되는데 이 중 `MySQL Community Server`를 클릭합니다.
<br><br>

[MySQL Download](https://dev.mysql.com/downloads/mysql/){: target="_blank" }링크를
이용하면 다운로드 페이지로 넘어갑니다. 현재 우리의 OS와 architecture에 맞게 MSI installer를
다운로드한 후 설치를 시작합니다.
<br><br>

설치할 MySQL의 type을 설정합니다. `Developer Default`는 개발용으로 서버, 클라이언트,
대부분의 개발툴들이 모두 설치되게 됩니다. 여기서 우리는 필요한 것들만 선택해서 설치할 것이기
때문에 `Custom`을 선택합니다.

{% include image.html
file='database-mysql-install-step-1.png'
%}

설치할 제품을 선택하는 화면입니다. 아래의 내용처럼 몇개의 제품을 선택합니다.

{% include image.html
file='database-mysql-install-step-1_1.png'
%}

{% include image.html
file='database-mysql-install-step-1_2.png'
%}

만약 `MS Visual Studio`가 설치되어 있지 않다면 설치해야 한다는 내용이 나옵니다.
`MS Visual Studio 2019`를 설치한 후 다시 진행하시면 됩니다.
<br><br>

[Visual Studio 2019](https://drive.google.com/file/d/1-RWEBGA_jlT7PBn6cIgzyruxTe-OWycR/view?usp=sharing){: target="_blank" }
링크를 이용해 다운로드 하고 설치하시면 됩니다.

{% include image.html
file='database-mysql-install-step-2.png'
%}

`MS Visual Studio 2019`를 설치하면 다음과 같은 화면을 볼 수 있습니다. `execute`를 클릭해서
설치를 시작합니다.

{% include image.html
file='database-mysql-install-step-3.png'
%}

설치가 끝나면 설정화면으로 넘어갑니다. Type and Networking 설정은 default로 두시면 됩니다.

{% include image.html
file='database-mysql-install-step-4.png'
%}

다음화면은 좀 주의해야 합니다. `Authentication` 방법을 설정하는 것인데 `RECOMMNEDED`로
되어 있는 위의 것을 선택합니다. 단 `Strong Password Encryption` 방식은 MySQL 8.0에서
새롭게 도입된 방식이기 때문에 보안은 강화되었지만 설정과 사용방법이 까다롭습니다.
<br><br>

기존 application과의 호환이 문제가 될 경우 아래의 `Legacy` 방식을 이용하는 것이
좋습니다.

{% include image.html
file='database-mysql-install-step-5.png'
%}

`Root Password`와 새로운 사용자를 생성하는 화면입니다. 적절한 password를 입력합니다.
새로운 사용자는 추후에 추가하도록 하겠습니다.

{% include image.html
file='database-mysql-install-step-6.png'
%}

다음 화면은 Windows Service로 등록할지 여부를 묻는 화면입니다. Windows 서비스로
등록해서 사용하기로 하겠습니다.

{% include image.html
file='database-mysql-install-step-8.png'
%}

지금까지 설정한 내용을 적용하는 화면입니다. `Execute`를 클릭해서 실행시킵니다.

{% include image.html
file='database-mysql-install-step-9.png'
%}

몇가지 자잘한 확인 화면이 지나고 Server에 연결을 테스트하는 화면이 나타나는데 `root`
password를 입력하고 `Check`버튼을 클릭해 정상적으로 서버에 연결할 수 있는지 확인합니다.

{% include image.html
file='database-mysql-install-step-10.png'
%}

마지막 화면 `Apply Configuration`에서 `execute`를 클릭해 설치하고 설치를 종료합니다.

---

## MySQL Workbench

일반적으로 `MySQL` DBMS를 설치한 후 SQL Client를 설치합니다.
여러가지 다양한 Client tool들이 존재하는데 우리는 MySQL 설치 시 같이 설치한 `Workbench`를
사용하도록 하겠습니다.
<br><br>

많이 사용되는 툴로는 [DataGrip](https://www.jetbrains.com/datagrip/){: target="_blank" }과
[DBeaver](https://dbeaver.io/){: target="_blank" }가 있습니다.
<br><br>

`DataGrip`는 `DBeaver`는 `MongoDB`도 같이 연결할 수 있고 사용하기 편하다는
이점이 있지만 유료툴이기 때문에 30일 trial 버전을 사용해야 합니다.
<br><br>

`DBeaver`는 무료버전이 존재하는데 `Freeware`를 사용할 경우 `Java`를 설치해야 합니다.
혹시 설치가 되어 있지 않다면 `DBeaver`를 사용하기 위해 Java는 11버전을 설치해야 합니다.
<br><br>

[Java SE 11 Archive Downloads](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html){: target="_blank" }
<br><br>

[Java SE 11 Archive Downloads - Google Drive](https://drive.google.com/file/d/1ak9JZAIkXgVe_Q4mP26xkQThYQkShMDy/view?usp=sharing){: target="_blank" }
<br><br>

이제 `Workbench`를 이용해 설치한 `MySQL`에 접속해 보도록 하겠습니다.
<br><br>

설치된 `Wrokbench`를 실행시키면 아래와 같은 화면을 보실 수 있습니다.

{% include image.html
file='database-mysql-workbench-1.png'
%}

아직 MySQL Server에 접속된 상태는 아닙니다. 왼쪽 하단에 `Local instance MySQL`을
클릭해 MySQL Server에 접속합니다. 참고로 `Workbench`는 MySQL과 관련된 대부분의 작업을
`GUI`로 처리하고 결과를 확인할 수 있도록 해주는 통합 개발 환경입니다.
<br><br>

root 계정의 password를 입력하면 아래와 같은 화면을 볼 수 있습니다.

{% include image.html
file='database-mysql-workbench-2.png'
%}

화면 오른쪽의 `SQL Additions`는 사용할 일이 없기 때문에 살짝 닫아줍니다.
<br><br>

몇가지 설정을 해야 하는데 설정창을 열기 위해 `Edit > Preferences`를 선택합니다.
<br><br>

아래그림 처럼 설정창에서 `SQL Editor`를 선택하고 맨 아래쪽에 `Safe Updates` 항목의
check를 **해제**합니다. 이 option이 켜져 있으면 추후에 `Update`나 `Delete` 구분에서
반드시 `Primary Key` column이 포함되어야 하기 때문에 사용을 조금 원활하게 하기 위해서
check를 해제합니다.

{% include image.html
file='database-mysql-workbench-3.png'
%}

이제 `Workbench`를 종료하고 시스템의 환경변수 `PATH`에 설치한 `MySQL`의 bin 폴더를 설정합니다.
현재 `MySQL` 설치된 폴더는 `C:\Program Files\MySQL\MySQL Server 8.0` 입니다.

{% include image.html
file='database-mysql-path-setting.png'
%}

이제 sample database를 설치해보도록 하겠습니다. 실습을 할 때 종종 대량의 데이터가
필요한 경우가 있는데 그 데이터를 우리가 일일이 입력할 수 는 없겠죠. 그래서 `MySQL`은 전통적으로
`employees`라는 이름의 sample database를 제공하고 있습니다.
<br><br>

다음의 구글 드라이브 링크를 이용해 `employees` database를 다운로드 하도록 하죠.
<br><br>

[employees database - Google Drive](https://drive.google.com/file/d/1y8jcrN-b_EWoUbPOrzcjJ1PMkj6IIzEp/view?usp=sharing){: target="_blank" }
<br><br>

파일을 다운로드 하고 압축을 풀어줍니다.
<br><br>

그런다음 `cmd`창을 관리자 권한으로 열어서 working directory를 압축을 풀은 directory로
이동합니다.
<br><br>

그럼다음 아래의 명령을 이용해 MySQL에 console로 접속합니다.

{% include callout.html
type="danger"
content="`mysql -u root -p`"
%}

접속이 성공했으면 다음의 SQL문으로 employees database를 가져옵니다.

{% include image.html
file='database-mysql-employees-loading.png'
%}

아래 그림과 같이 database가 설치되면 다음의 명령으로 database를 확인합니다.

{% include callout.html
type="danger"
content="`show databases;`"
%}

{% include image.html
file='database-mysql-database-show.png'
%}

---

이제 준비가 모두 끝났습니다. 마지막으로 한가지 더 확인할 사항이 있는데 우리가 사용하는
모든 `Database`는 모두 `C:\ProgramData\MySQL\MySQL Server 8.0\Data` 폴더안에
database 이름으로 되어 있는 폴더안에 저장되게 됩니다.

{% include image.html
file='database-mysql-database-folder.png'
%}


End.

{% include links.html %}
