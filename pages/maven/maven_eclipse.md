---
title: Maven Eclipse 설정
sidebar: maven_sidebar
summary: "Maven을 Eclipse에서 사용하는 방법에 관한 내용입니다."
permalink: maven_eclipse.html
folder: maven
---

## Eclipse 연동

현재 우리가 사용하는 Eclipse에는 Maven 프로젝트를 위한 플러그인이 기본적으로 설치되어 있습니다. 만약 이전 버전의
Eclipse인 경우 설치가 안되어 있는 경우도 있을 수 있는데 이런 경우 Help > Eclipse MarketPlace에서 Maven Integration을
검색해서 설치하면 됩니다.
<br><br>

일단 기존의 Maven Project를 Eclipse에 import해서 Eclipse에서 관리해서 사용해보도록 하죠.
<br><br>
 
`File > import`를 클릭하면 Maven을 찾을 수 있고 하위 메뉴를 열어보면 `Existing Maven Projects`메뉴가 있습니다. 이 메뉴를
클릭하신 후 기존에 만들어 놨던 `myProject`를 찾아 선택합니다. `pom.xml`파일이 있는 폴더를 선택하는 겁니다.
<br><br>

Eclipse에서 불러들이면 프로젝트에 작은 글씨로 `M`이 표시되어 있습니다. Maven Project를 알려주는 아이콘 같은 것입니다.
<br><br>

기본으로 작성된 App.java  파일을 찾아서 실행시켜보죠. ctl + F11키를 이용해서 실행하면 실행됩니다.
<br><br>

한가지 기억할점은 우리 프로젝트는 Eclipse Project가 아니라 Maven Project라는 것입니다. 즉, 다른 IDE에 우리 Maven Project를
그대로 가져다가 사용할 수 있다는 것이죠.
<br><br>

자 그럼 조금씩 세부적으로 알아보도록 하죠.

---

## 플러그인 변경

이전에 단계별 실행을 담당하고 있는 플러그인들을 살펴본적이 있는데 그 중 compile을 담당하고 있는 플러그인을 살펴보도록 하죠.

{% include image.html
file='maven-compile-phase.png'
%}
<br>

compile을 담당하고 있는 플러그인은 Maven 플러그인에서 지원하고 있는 플러그인입니다. Maven Apache 홈페이지의 플러그인에 가 보면
compile이라는 플러그인에 대해 우리가 살펴볼 수 있는데 Goal이 두개인것을 확인할 수 있습니다.
<br><br>

* compiler:compile is bound to the compile phase and is used to compile the main source files.
* compiler:testCompile is bound to the test-compile phase and is used to compile the test source files.
<br><br>

위의 그림을 보면 compile 단계와 test-compile 단계가 같은 플러그인을 사용하는 걸 확인할 수 있습니다. 하지만 Goal이 다르죠.
<br><br>
 
사실 이 플러그인에 대한 자세한 내용을 우리가 다 알필요는 없습니다. 다만 이런 플러그인의 속성을 변경해서 우리가 어떤 일들을
할 수 있는지 그 주요한 것들만 알아두시면 됩니다.
<br><br>

간단하게 현재 프로젝트의 설정을 바꿔보도록 하죠.
<br><br>

현재 우리 프로젝트의 JRE System Library 항목을 보면 JavaSE-1.8이라고 되어 있는걸 확인할 수 있습니다.
이제 우리 프로젝트의 `pom.xml`을 열고 이전에 입력해 놓았던 아래의 내용을 삭제합니다.

~~~text

  <properties>
	<maven.compiler.source>1.8</maven.compiler.source>
	<maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  
~~~

기본적으로 `pom.xml`파일을 수정하면 우리 프로젝트에서 오른쪽 버튼 > Maven > Update Project를 클릭해서 프로젝트를
업데이트 해 주는것이 좋습니다.
<br><br>

이렇게 업데이트를 하면 JRE System Library 항목이 J2SE-1.5로 바뀌면서 버전이 낮아진걸 확인할 수 있습니다. 사실 이게 기본버전입니다.
우리가 프로젝트를 생성할 때 maven-project-quickstart 버전으로 만들었는데 이 버전의 JDK버전이 1.5버전입니다. 
<br><br>

이제 플러그인을 통해 설정을 바꿔보도록 하죠. 위에서 언급한 compile단계에서 사용하는 플러그인을 이용해 설정을 바꿔보도록 
하겠습니다. 

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
  <build>
  	<plugins>
  		<plugin>
  			<artifactId>maven-compiler-plugin</artifactId>
  			<version>3.8.1</version>
  			<configuration>
  				<source>1.8</source>
  				<target>1.8</target>
  			</configuration>
  		</plugin>
  	</plugins>
  </build>
</project>


~~~

위의 내용으로 build 부분을 설정하면 기존의 내용을 `override`하면서 설정을 바꾸게 됩니다. 
우리 프로젝트를 다시 update하게 되면 이제 JDK 버전이 다시 1.8로 변경되는 것을 확인할 수 있습니다.
<br><br>

그럼 기존에 있었던 내용은 위의 내용과 어떻게 다른걸까요? 기존에 있었던 내용은 새로운 버전에서 추가된 형식입니다.
즉, 조금 더 간소화 시켜서 작성할 수 있도록 지원하고 있는것이죠.
<br><br>

---

## 웹 프로젝트로 변경



End.

{% include links.html %}
