---
title: Java Module
sidebar: java_sidebar
summary: "Java Module에 대한 설명입니다."
permalink: java_module.html
folder: java
---

## Java Module 개요

Java `Module`은 Java 9에서 공식적으로 등장했습니다. 원래 용어는 
`Java Platform Module System (JPMS)`이라 하며 줄여서 `module` 이라고 부릅니다. 
<br><br>

조금 어렵게 표현하면 새로운 수준의 추상화를 도입한 것이라 볼 수 있습니다. 
<br><br>

모듈에는 종속성(`dependency`)의 개념이 있으며, `public API`를 내보내고 구현의 세부 정보를 비공개 상태로 유지할 수 있습니다.
<br><br>

---

## package와 module의 차이

### package

자바의 패키지는 비슷한 성격의 클래스를 묶어놓는 논리적인 개념입니다. 물리적으로는 하나의 디렉토리로 표현되게 됩니다. 
상위 패키지가 하위 패키지를 포함하는 중첩된 구조도 가능하며 그런 경우, `.(dot)`를 사용해서 표현합니다. 

### module

자바의 모듈은 외부에서 재사용 할 수 있는 패키지들을 묶은 것을 말합니다. 
이클립스에서 개발하는 프로젝트는 하나의 모듈을 개발하는 것이라고 볼 수 있는거죠.
<br><br>

서로 다른 프로젝트가 `import`와 `export`를 이용하여 패키지(package)를 주고 받을 수 있습니다. 
즉, 패키지의 상위 개념이라고 할 수 있습니다. 

---

## module이 도입된 이유

그렇다면 왜 자바는 모듈과 패키지로 나누어져 있는 걸까요? 
그것도 초기부터가 아니고 나중에서야 모듈이 추가되게 된 것일까요?
<br><br>

당연히 기존의 패키지만으로는 부족한 점들을 커버하기 위해서 생긴 것 입니다. 
자바가 점점 더 다양한 곳에 사용되고 규모가 커짐에 따라 여러가지 문제가 생기기 시작했는데 
다음과 같은 것들을 들 수 있습니다. 

### 패키지의 캡슐화

패키지는 캡슐화(`Encapsulation`)가 완벽하지 않습니다. 
<br><br>

객체지향 프로그래밍에서 세부 구현을 숨기는 캡슐화는 필수적인 기법입니다. 자바에서 클래스의 멤버들은
access modifier를 이용해 멤버들의 공개 여부를 원하는대로 지정할 수 있는데 비해 클래스 자체는 그 은폐 정도가 약합니다. 
<br><br>

클래스를 숨길 수는 있지만 공개 또는 비공개 둘 중 하나만 선택할 수 있고 여러 패키지에서 공용으로 사용할 클래스를 
외부에 대해 숨길 수 있는 방법은 없습니다. 공용으로 사용할 클래스를 공개(public)해 버리면 라이브러리 내부 뿐만 아니라 외부에서도 
자유롭게 사용할 수 있게되는 것이죠. 
<br><br>

즉, 클래스는 프로젝트 외부에 공개하지 않고 프로젝트 내부 package 에서만 사용할 방법이 없게됩니다. 
한가지 방법은 문서나 가이드를 통해 내부용 패키지는 사용하지 말라고 부탁 또는 협박하는 수밖에 없는데 모든 개발자가 
이런 권장 사항을 다 지키는 것은 아니어서 사용자가 내부 기능을 직접 사용하면 이후 자유롭게 유지, 보수할 수 없게됩니다.
그래서 문법적으로 완전히 숨길 수 있는 강한 캡슐화가 필요해진거죠.
<br><br>

### dynamic loading

`Java`는 동적 로딩을 지원합니다.

`JVM`은 자바 컴파일러에 의해 생성된 바이트코드(`.class`)를 `Class Loader`(클래스 로더)를 통해 불러옵니다. 
이 클래스 로더는 바이트코드로부터 실제 객체를 생성하는 일을 수행합니다. 즉, 클래스의 인스턴스화를 수행하는 것이 바로 클래스 로더입니다.

이런 Class Loader는 두가지 방식으로 동적 로딩을 수행합니다. 

#### 로드타임 동적 로딩 (Load-time dynamic loading)

~~~java

public class Main {
	public static void main(String[] args) {
    	System.out.println("hello world");
    }
}

~~~

위의 코드에서 Main 클래스를 실행했을 때 클래스 로더는 다음과 같은 일들을 수행합니다.

* Object 클래스를 읽어옵니다. Object 클래스는 모든 클래스가 상속하기 때문이죠.
* Main.class 파일을 읽습니다.
* 이 때 Main.class 파일을 로딩하기 위해 필요한 java.lang.String, java.lang.System 클래스를 로딩합니다.
<br><br>

여기서 `java.lang.String`, `java.lang.System`은 Main 클래스를 로딩하는 과정에서 로드타임에 동적으로 로딩됩니다.

#### 런타임 동적 로딩 (Run-time dynamic loading)

자바의 다형성은 프로그래머로 하여금 선언부와 구현부를 나누어 프로그래밍 할 수 있게 해줍니다. 
<br><br>
interface에서 선언한 메소드가 사용될 때 메소드가 실행되기 전까지는 어떤 클래스가 들어올지 알 수 없습니다. 즉, 로드타임에 로딩할 수 없습니다.
<br><br>

따라서 메서드가 실행되는 순간인 런타임에 동적으로 클래스가 로딩되게 됩니다! 이를 우리는 메소드의 동적 바인딩 이라고 부르죠.  
<br><br>


이처럼 자바는 동적 로딩을 통해 필요한 클래스를 실행중에 로드합니다. 
덕분에 시작이 빠르고 생성할 클래스를 선택할 수 있지만 실행 직후에는 누락 사실을 바로 알 수 없다는 문제가 있습니다. 
<br><br>

### runtime의 거대화

런타임이 거대해져 배포가 어려워집니다. 초기의 자바 플랫폼은 라이브러리의 모든 클래스를 `rt.jar`라는 단 하나의 파일에 통합하여 
배포했습니다. `rt`는 런타임(`Run Time`)이라는 뜻이며 자바 프로그램이 실행되기 위한 모든 클래스가 포함되어 있는 
자바 플랫폼 그 자체입니다. 단일 파일이라 배포하기 쉽고 별 문제가 없었죠. 
<br><br>

그러나 자바 버전이 올라 가면서 `rt.jar`는 용량 60M에 2만개의 클래스를 포함하는 초대형 런타임이 되었고 
앞으로는 더 늘어날 것이 자명하죠. 그나마 PC에서는 별 무리가 없지만 임베디드 장비에 설치하기에는 너무 커지게 됩니다. 
게다가 단일 파일이라 불필요한 것까지 한꺼번에 배포할 수밖에 없는 구조입니다. 꼭 필요한 클래스만 추려 원하는 런타임을 생성하고 
런타임과 프로그램을 같이 배포할 수 있어야 할 필요가 생긴것이죠.
<br><br>

---

## module의 특징

조금 자세히 모들을 알아보도록 하겠습니다. 모듈이란 서로 밀접하게 연관된 패키지들과 리소스들의 그룹입니다. 
<br><br>

즉, "자바 패키지들의 패키지(`package of Java Packages`)"라고도 볼 수 있는데, 이는 코드의 재사용성을 높이기 위해 
추상화 단계를 하나 더 추가한 것이라고 볼 수 있습니다. 
<br><br>

### package

모듈 내의 패키지들은 Java가 시작된 이후 사용해 온 Java 패키지와 동일합니다. 모듈을 만들 때, 우리는 이전에 다른 프로젝트에서 
했던 것처럼 똑같이 패키지를 구성합니다. 

### resource

각 모듈은 미디어 또는 configuration 파일과 같은 `resource`들을 관리합니다. 이전에는 모든 리소스를 프로젝트의 루트 레벨에 넣고 
애플리케이션의 각기 다른 부분들에 속한 리소스들을 수동으로 관리했었죠. 그런데 모듈을 사용하면, 모듈과 함께 필요한 이미지와 
XML 파일 등을 함께 모아놓을 수 있으므로 프로젝트를 훨씬 쉽게 관리할 수 있게됩니다.

### 모듈 설명자(Module Descriptor)

모듈을 만들 때, 모듈의 여러 가지 설정을 정의하는 설명자 파일(`module-info.java`)을 사용합니다. 
설정값들에는 다음과 같은 것들이 있습니다.
<br><br>

* 이름 : 모듈 이름
* 종속성 : 이 모듈이 의존하는 다른 모듈 목록
* 공용 패키지 : 모듈 외부에서 접근할 수 있는 모든 패키지 목록
* 제공되는 서비스 : 다른 모듈에서 사용할 수 있는 서비스 구현을 제공할 수 있다. (Service Provider)
* 사용된 서비스 : 현재 모듈이 서비스의 소비자가 될 수 있도록 한다. (Service Consumer)
* 리플렉션 허용 : 다른 클래스가 리플렉션을 사용하여 패키지의 private 멤버에 접근할 수 있는지의 여부를 명시

### 모듈 타입

새 모듈 시스템에는 4가지 유형의 모듈이 있습니다.
<br><br>

* 시스템 모듈(System Modules) : 
list-modules 명령어를 입력했을 때 조회되는 모듈들입니다. 여기에는 Java SE 및 JDK 모듈이 포함됩니다.
* 애플리케이션 모듈(Application Modules) : 이 모듈은 일반적으로 우리가 모듈을 사용하기로 결정했을 때 
빌드하고자 하는 모듈입니다. 이들은 `JAR`에 포함된 컴파일된 `module-info.class` 파일에 명명되고 정의됩니다.
* 자동 모듈(Automatic Modules) : 기존 JAR 파일을 모듈 경로(module path)에 추가하여 비공식 모듈을 포함시킬 수 있습니다.
모듈 이름은 JAR의 이름에서 파생됩니다. 자동 모듈은 모듈 경로에 로드된 다른 모든 모듈들에 대한 전체 읽기 액세스 권한을 가지게 됩니다.
* 이름 없는 모듈(Unnamed Module) : 클래스 또는 JAR가 모듈 경로(module path)가 아닌 클래스 경로(classpath)에 로드되면, 
이름이 지정되지 않은 모듈에 자동으로 추가됩니다. 이것은 이전에 작성된 Java 코드와의 하위 호환성을 유지하기 위함입니다.

### 모듈 배포

모듈은 JAR 파일 또는 컴파일된 여러 파일들로 이루어진 프로젝트 두 가지 방법 중 하나로 배포될 수 있습니다.\
물론 이는 다른 Java 프로젝트와 동일한 방법이죠. 우리는 "주 애플리케이션"과 여러 라이브러리 모듈로 구성된 
다중 모듈 프로젝트를 만들 수 있습니다. 이때 JAR 파일당 모듈을 하나만 가질 수 있기 때문에 주의해야 합니다.
빌드 파일을 설정할 때 프로젝트의 각 모듈을 별도의 JAR로 묶어야 합니다.

---

## 디폴트 모듈

Java 9를 설치하면 JDK가 새로운 구조를 가지고 있음을 알 수 있습니다. 
원래 패키지들이 모두 새 모듈 시스템으로 옮겨졌습니다. 커멘드 라인에 아래의 명령어를 입력하면 이러한 모듈들이 무엇인지 알 수 있습니다.

~~~txt

java --list-modules

~~~

이러한 모듈들은 `java`, `javafx`, `jdk` , `oracle`의 네 가지 주요 그룹으로 나뉩니다.
<br><br>

* java 모듈은 핵심 Java SE 언어에 대한 구현 클래스이다.
* javafx 모듈은 FX UI 라이브러리이다.
* jdk 모듈은 JDK 자체에 필요한 모든 것이 포함되어 있습니다.
* oracle 모듈은 오라클에 특화된(Oracle-specific) 것들이 포함되어 있습니다.
<br><br>

---

## 모듈 선언

모듈을 설정하려면 `module-info.java`라는 모듈 설명자 파일을 패키지 루트 레벨에 생성해야 합니다.
이 파일에는 모듈을 생성하는데 필요한 모든 데이터가 들어있습니다. 어떤 설정값도 없는 빈 모듈 설명자 파일을 만들어보죠.
<br><br>

Eclipse에서 새로운 project를 생성할 때 프로젝트명을 `계산기모듈`로 지정하고 
대화창 하단에 module 항목에 있는 `module-info.java` 파일을 생성한다고
체크를 한 후 모듈 이름을 `my.firstmodule`로 지정해서 프로젝트를 생성합니다.
<br><br>

처음 project 생성 시 `module-info.java`를 생성하지 않아도 추후에 프로젝트에서 오른쪽 클릭해서 
`Configure > Create module-info.java`를 선택해도 모듈을 생성할 수 있습니다. 
<br><br>

프로젝트가 생성된 후 `src` 폴더의 이름을 `mt.firstmodule`로 변경합니다. 이 폴더안에는 `module-info.java` 파일이 위치해야 합니다.
(이 폴더가 module root folder가 됩니다.)
<br><br>

그런다음 `com.test.calc` package를 생성한 후 `Calculator` class를 작성합니다. 

~~~java

package com.test.calc;

public class Calculator {
	
	public Calculator() {
		// TODO Auto-generated constructor stub
	}
	
	public int sum(int a, int b) {
		
		return a + b;
		
	}
}

~~~

생성된 `module-info.java` 파일의 내용을 보면 아래와 같습니다. `module` 키워드로 모듈 선언을 시작하고, 
그다음 모듈 이름이 명시되어 있습니다.  
이러한 선언만으로도 모듈은 동작하지만, 일반적으로 더 많은 정보(설정값들)가 필요합니다.

 
~~~java

module my.firstmodule {

    // 설정값들은 모두 optional입니다.
    
}

~~~

### module jar 파일 생성

모듈을 컴파일하기 위해서는 다음과 같이 수행합니다. `CMD` 창을 열어서 working directory를 
프로젝트의 root로 이동한 후 다음의 명령을 실행합니다.

~~~txt

javac -d target/classes 
      my.firstmodule/module-info.java 
      my.firstmodule/com/test/calc/Calculator.java 

~~~

이제 jar 명령을 이용하여 jar 파일로 압축합니다.

~~~txt

jar --create 
    --file target/my-module.jar 
    target/classes/module-info.class 
    target/classes/com/test/calc/*.class

~~~

이렇게 생성된 module jar파일을 이용해 보도록 하죠.
<br><br>

다시 새로운 Project를 생성합니다. 

### Requires

첫 번째 키워드는 `requires`입니다. 이 키워드는 이 모듈의 종속성을 나타냅니다.

~~~java

module my.module {
    requires module.name;
}

~~~

이제 'my.module'은 'module.name'에 대한 종속성을 갖게 된다(런타임, 컴파일 타임 둘 다). 이제 'my.module'는 'module.name'에서 외부로 공개(exports)하기로 한 모든 public type들에 대한 접근이 가능하다.


End.

{% include links.html %}
