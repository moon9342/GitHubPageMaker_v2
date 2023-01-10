---
title: Maven Introduction
sidebar: maven_sidebar
summary: "Maven의 개요 및 특징에 관한 내용입니다."
permalink: maven_introduction.html
folder: maven
---

## Maven Introduction

`Maven`은 프로젝트를 만들때 가장 많이 사용하는 `Build Tool`입니다.
<br><br>
실제로 Maven은 상당히 사용하기 쉬운  빌드툴입니다. 내부적으로는 복잡하게 구성되어 있지만 사용하는 사용자의
입장에서는 상당히 쉽게 사용할 수 있습니다. 
<br><br>

---

## 프로젝트 빌드 과정

그럼 `Build`란 무었일까요? 빌드란 우리가 작성한 소스 코드 파일을 컴퓨터에서 실행할 수 있는 독립적인 형태로 변환하는 과정과 
그 결과를 의미합니다. 
<br><br>

아래의 그림은 우리가 프로젝트를 만들고 배포하는 작업과정인데 이 과정자체 그리고 그 결과물이 바로 Build입니다. 
<br><br>

{% include image.html
file='maven-project-build-process.png'
%}
<br>

이런 작업과정에서 반복되는 작업을 조금 더 효율적으로 처리할 수 있도록 도와주는 빌드 도구가 바로 `Maven`입니다.
<br><br>

그런데 `Eclipse`역시 위와같은 기능을 가지고 있습니다. 그럼 Eclipse도 빌드툴일까요? 그렇지 않습니다. Eclipse는
우리가 알다시피 `IDE`(통합개발환경) 입니다. 즉, Build작업도 하지만 추가적으로 여러가지 기능을 포함하고 있는 통합 개발 툴입니다.
추가적인 기능에는 편집기, 탐색기, 디버거, 형상관리, 콘솔과 같은 기능들이 있습니다. 
<br><br>

우리가 많이 사용하는 IDE는 `Eclipse`, `VS Code`, `IntelliJ` 같은것들이 있습니다. `Maven`은 특정 IDE에 종속된 툴이 아니고
빌드작업에 특화되어 있는 도구라고 생각하시면 됩니다. 우리는 Eclipse가 가지고 있는 기능을 이용해 프로젝트를 빌드할 수 도 있지만 
Maven이라는 빌드도구를 이용해서 빌드과정을 진행하면 훨씬 더 편리한 빌드과정을 제공받을 수 있습니다.
<br><br>

이런 빌드도구는 크게 3가지가 많이 사용되는데 `Maven`, `Gradle`, `Ant`가 있습니다. 요즘은 Maven아니면 Gradle을 사용하는데
레거시 Spring에서는 Maven이, Spring boot에서는 Gradle을 기본 빌드 도구로 이용합니다. 
<br><br>

---

## Maven 설치

Maven을 따로 설치한 후 사용해본 후 추후에 Eclipse에 포함시켜서 사용해보도록 하죠.
<br><br>

일단 Maven을 다운로드 합니다. 
<br><br>

[Maven Home](https://maven.apache.org/){: target="_blank" }
<br><br>

해당 페이지에서 `Use > Download` 링크를 클릭하면 `Binary ZIP archive`를 받을 수 있는 페이지가 열리고 해당 파일을
다운로드 하면 됩니다. 
<br><br>

다운로드 후 적절한 위치로 파일을 이동시키고 압축을 풀어줍니다. 저는 Java와 관련된 모든 라이브러리는 모두 `JAVA_HOME` 아래에 
폴더를 만들어서 관리합니다. 
<br><br>

적절한 위치에서 압축을 풀고 `M2_HOME` 이라는 환경변수를 만들고 Maven 홈을 지정하고 bin 폴더를 환경변수 `PATH`에 지정합니다.
<br><br>

---

## Maven으로 자바프로젝트 생성

Maven을 이용해서 자바프로젝트를 하나 생성해보도록 하겠습니다. 
<br><br>

`C:/maven_test`라는 폴더를 하나 만들고 그 안에서 프로젝트를 생성해보도록 하겠습니다. 
<br><br>

`CMD`창을 열고 `C:/maven_test`로 working directory를 이동합니다. 
<br><br>

여기에서 다음의 명령을 이용합니다. 

~~~text

mvn archetype:generate -DgroupId=com.test -DartifactId=myProject -DarchetypeArtifactId=maven-archetype-quickstart

~~~

위의 명령은 다른 사람이 만든 `maven-archetype-quickstart`라는 프로젝트 구조를 기본으로 프로젝트를 생성하는 명령어 입니다.
(사실 정확하게 말하면 새로운 archetype을 생성하는 것입니다.) 
<br><br>

위에서 사용한 `maven-archetype-quickstart`는 자바개발할 때 사용하는 가장 기본적인 template입니다. 
<br><br>

일반적으로 우리가 이클립스에서 프로젝트를 생성하면 맨땅(포함된 내용이 아무것도 없는)에서 프로젝트가 생성되게되는데 
Maven 프로젝트는 자주 사용되는 구조를 기반으로 기본적으로 사용되는 라이브러리와 필수파일들을 포함해서 프로젝트를 생성할 수 있습니다. 
<br><br>

`artifacttype`은 누구나 만들어서 공유할 수 있습니다. 프로젝트 형태로 공유해 놓은 이 인공물을 이용해서 기본 프로젝트를 
생성할 수 있는것이죠.
<br><br>

위에서 `-DartifactId=myProject` 라는 부분이 보이는데 내가 만드는 프로젝트의 이름이 `myProject`라는 것입니다. 
이때 이 프로젝트명이 동일할 수 있기 때문에 충돌을 피하기 위해 `-DgroupId=com.test`를 이용해서 Group ID를 지정합니다. 
Group ID는 패키지명과 비슷한의미를 가지고 일반적으로 도메인의 역순을 이용해서 작성합니다.
<br><br>

실행하게되면 많은 라이브러리를 다운로드 받은 후 프로젝트를 생성합니다. 
<br><br>

프로젝트는 interactive mode로 생성되게 되는데 프로젝트를 어떻게 생성할지를 사용자와 상호작용하면서 생성하게됩니다. 
<br><br>

version에서 `SNAPSHOP`의 의미는 개발중이라는것을 의미합니다. 만약 완성된 버전이라면 SNAPSHOP을 빼고 버전정보만 명시합니다.
<br><br>

그런다음 Group ID를 패키지명으로 하겠냐는 물음에 대해서는 그냥 엔터를 눌러서 그렇게 하겠다고 지정하면 프로젝트가 생성됩니다.
<br><br>

성공하면 `BUILD SUCCESS`가 출력됩니다. 탐색기를 통해 생성된 프로젝트를 살펴보죠.
<br><br>

`C:\maven_test\myProject\src\main\java\com\test` 폴더에 가보시면 `App.java`파일이 생성되어 있는것을 볼 수 있고
그 안에 main method가 들어있는것을 확인할 수 있습니다. 

---

## Maven 프로젝트 컴파일

`Maven project`는 루트밑에 항상 `pom.xml` 파일이 있습니다. 프로젝트를 컴파일하거나 실행할때는 항상 이 `pom.xml` 파일이 있는 
위치에서 명령어를 실행하게 됩니다. 
<br><br>

CMD창에서 현재 위치를 `C:\maven_test\myProject`로 이동합니다. 
<br><br>

이 위치에서

~~~text

mvn compile

~~~

위의 명령을 이용하면 컴파일이 진행됩니다. 
<br><br>

열심히 실행되다가 다음과 같은 에러가 발생하면서 빌드가 실패합니다. 

~~~text

[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.1:compile (default-compile) on project myProject: Compilation failure: Compilation failure:
[ERROR] Source option 5 is no longer supported. Use 6 or later.
[ERROR] Target option 1.5 is no longer supported. Use 1.6 or later.

~~~

이 문제를 해결하기 위해서는 `pom.xml`을 편집해야 합니다. 
<br><br>

편집기를 이용해서 해당 파일을 편집하면 다음과 같이 나오는데 다음과 같은 내용을 추가해야 합니다. 자세한 내용은 다음에 알아보기로 하고
맨 아래의 해당내용을 일단 삽입하도록 하죠.

~~~text

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.test</groupId>
  <artifactId>myProject</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>myProject</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  
  <properties>
	<maven.compiler.source>1.8</maven.compiler.source>
	<maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  
</project>


~~~

다시 컴파일을 진행하면 컴파일이 성공하는것을 보실 수 있습니다. 
<br><br>

위의 코드에서 `<maven.compiler.source>1.8</maven.compiler.source>` 부분은 우리 코드가 JDK 1.8로 컴파일이 되었으면 좋겠다 
하는 내용이고 `<maven.compiler.target>1.8</maven.compiler.target>` 부분은 우리 프로젝트가 최소 1.8버전이상에서 
수행될 수 있도록 컴파일하겠다는 내용입니다.
<br><br>

탐색기로 target이라는 폴더를 살펴보면 classes 폴더안에 우리 패키지안에 컴파일된 클래스가 들어가 있는것을 확인할 수 있습니다. 
<br><br>

수행하기 전에 우리 프로젝트를 패키징해보도록 하죠.

~~~text

mvn package

~~~

위의 명령을 이용하면 우리 프로젝트를 jar 파일로 생성해 줍니다. target 폴더안에 
`myProject-1.0-SNAPSHOT.jar` 라는 이름의 파일이 생성된 것을 볼 수 있습니다.
<br><br>

이 jar 파일을 이용해서 실행해보죠. 현재 working directory는 pom.xml이 위치하고 있는
C:\maven_test\myProject 입니다.
<br><br>

~~~text

java -cp target\myProject-1.0-SNAPSHOT.jar com.test.App

~~~

정상적으로 실행되는 것을 확인할 수 있습니다.
<br><br>

---

## Build LifeCycle과  Phase

Maven은 수행단계라는게 있습니다. 이런 수행단계는 여러 단계가 있고 또 순서도 가지고 있습니다. 
<br><br>

{% include image.html
file='maven-phase.png'
%}
<br>

위의 그림에서 보듯이 상당히 많은 단계가 존재합니다. 파란색으로 되어 있는 것들이 주요 단계입니다.
그럼 이런 단계를 일일이 다 실행해야 되는걸까요?
<br><br>

그렇지 않습니다. 만약  `compile`이라는 단계를 실행하면 앞의 단계가 모두 실행되고 `compile`단계까지 실행되는 것이죠.
<br><br>

한가지 주의해야 할 것은 우리 프로젝트가 어떤 종류인가에 따라서 그 단계가 살짝 상이하다는 것입니다. 하지만 거의 대부분은
비슷합니다.
<br><br>

이런 단계들의 구성은 어디서 할까요? 바로 `pom.xml`에서 설정합니다. POM은 `Project Object Model`의 약자입니다. 
우리 프로젝트의 구성요소를 하나의 모델로 가지고 있는 파일이라고 생각하시면 됩니다. 
<br><br>

또 한가지 기억할 점은 각각의 단계들이 플러그인 형태로 구성되어 있기 때문에 각각의 단계가 무조건 적으로 실행되지 
않는다는 것입니다. 아래 그림은 Java 프로젝트를 빌드할 때 사용하는 단계입니다. 

{% include image.html
file='maven-jar-phase.png'
%}
<br>

파란색으로 되어있는 단계에 대해 플러그인들이 묘사되어 있고 각 단계의 세부 플러그인 역할을 하는 `Goal`도 표현해 놓았습니다. 하얀색으로 되어있는 단계는
플러그인이 설치되어 있지 않는 것들이죠.
<br><br>

이런 플로그인들을 다시 설정하거나 플러그인들이 설정되지 않은 것들을 설정하려면 어떻게 해야 할까요? 당연히 `pom.xml` 파일에서
설정하면 됩니다.
<br><br>

다음의 명령어를 이용하면 이런 단계와 설치되어 있는 플러그인들을 확인할 수 있습니다.

~~~text

mvn help:describe -Dcmd=compile

~~~

다음과 같은 결과를 볼 수 있습니다. 

~~~text

[INFO] Scanning for projects...
[INFO]
[INFO] -------------------------< com.test:myProject >-------------------------
[INFO] Building myProject 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-help-plugin:3.3.0:describe (default-cli) @ myProject ---
[INFO] 'compile' is a phase corresponding to this plugin:
org.apache.maven.plugins:maven-compiler-plugin:3.1:compile

It is a part of the lifecycle for the POM packaging 'jar'. This lifecycle includes the following phases:
* validate: Not defined
* initialize: Not defined
* generate-sources: Not defined
* process-sources: Not defined
* generate-resources: Not defined
* process-resources: org.apache.maven.plugins:maven-resources-plugin:2.6:resources
* compile: org.apache.maven.plugins:maven-compiler-plugin:3.1:compile
* process-classes: Not defined
* generate-test-sources: Not defined
* process-test-sources: Not defined
* generate-test-resources: Not defined
* process-test-resources: org.apache.maven.plugins:maven-resources-plugin:2.6:testResources
* test-compile: org.apache.maven.plugins:maven-compiler-plugin:3.1:testCompile
* process-test-classes: Not defined
* test: org.apache.maven.plugins:maven-surefire-plugin:2.12.4:test
* prepare-package: Not defined
* package: org.apache.maven.plugins:maven-jar-plugin:2.4:jar
* pre-integration-test: Not defined
* integration-test: Not defined
* post-integration-test: Not defined
* verify: Not defined
* install: org.apache.maven.plugins:maven-install-plugin:2.4:install
* deploy: org.apache.maven.plugins:maven-deploy-plugin:2.7:deploy

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.465 s
[INFO] Finished at: 2023-01-10T23:32:09+09:00
[INFO] ------------------------------------------------------------------------

~~~

위의 결과를 보면 `validate`단계는 플러그인이 설치되지 않았음을 확인할 수 있습니다. 그리고 몇몇개는 플러그인이 설치되어 있는걸
확인할 수 있습니다. 이런 플러그인들은 maven이 제공해주는것들도 있고 다른곳에서 제공하는 것들도 있습니다.
<br><br>

Maven이 제공하는 플러그인을 살펴보려면 다음의 링크를 이용하시면 됩니다. 
<br><br>

https://maven.apache.org/plugins/index.html
<br><br>


End.

{% include links.html %}
