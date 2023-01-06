---
title: JavaFX
sidebar: java_sidebar
summary: "JavaFX에 대한 설명입니다."
permalink: java_fx.html
folder: java
---

## JavaFX

우리는 Java Network Program을 하기 위해 JavaFX를 이용하도록 하겠습니다. 비록 JavaFX가 실무에서는 그다지
많이 사용되지는 않지만 Network 프로그램을 작성할 때 상당히 편한 interface를 제공해 주기 때문에 이용해보도록 하겠습니다. 

### JavaFX란

JavaFX는 표준 GUI 라이브러리였던 Swing을 대체하기 위해 만들어진 GUI 라이브러리입니다.

### JavaFX 설치

다음의 링크에 접속합니다. 
<br><br>

https://gluonhq.com/products/javafx/
<br><br>

위 링크에 접속하면 하단에 Downloads 목록이 있습니다. `Include older versions`을 체크한 후
사용 중인 Java 버전 및 OS에 맞는 JavaFX를 다운로드합니다. `SDK` 타입 다운로드를 클릭합니다.
<br><br>

혹은 다음의 링크에서 다운로드 받을 수 있습니다. 
<br><br>

[JavaFX 11 version Download - Google Drive](https://drive.google.com/file/d/1fNgkfnHIGzB_9ANFVVj4330JWzEgDr2j/view?usp=share_link){: target="_blank" }
<br><br>

다운로드 후 편의를 위해 Java 설치폴더로 복사한 후 압축을 풀어줍니다. (사실 어디에서 압축을 풀어도 상관없습니다.)
<br><br>

### JavaFX 설정

Eclipse의 상단 메뉴바에서 File > New > Java Project 차례로 클릭해서 프로젝트를 하나 생성합니다. 이 때
아래에 "Create module-info.java file" 체크는 하지 않습니다. 
<br><br>

이제 프로젝트에서 JavaFX를 사용하기 위한 세팅을 진행합니다. 프로젝트 익스플로러에서 생성한 프로젝트 폴더를 우측 클릭하고,
맨 아래에 `Properties`를 클릭합니다.
<br><br>

좌측에 Java Build Path를 선택한 후 가운데 Libraies를 선택합니다.  그 후 아래에 Modulepath를 선택한다음
우측에 Add External JARs 클릭하고 압축을 해제했던 JavaFX 폴더 내 lib 폴더 접근해서 jar 파일 8개를 모두 선택합니다.
<br><br>

이제 `VM arguments`를 수정해야 합니다. 
프로젝트 마우스 우측 클릭 > Run As > Run Configurations를 차례로 클릭합니다. 
<br><br>

좌측에 Java Application 선택한 후 본 프로젝트 내 Class를 선택합니다. 
그런다음 우측에 Arguments 선택하고 VM arguments 내 JavaFX SDK 폴더 내 lib 폴더의 full 경로를 입력해 줍니다.
<br><br>

아래처럼 입력하면 됩니다.

> `--module-path "C:\Program Files\Java\jdk-11.0.16.1\javafx-sdk-11\lib" --add-modules javafx.controls,javafx.fxml`

<br><br>

이제 기본적인 설정은 모두 마쳤습니다. 

### 기본적인 코드수행

JavaFX를 활용하여 버튼 GUI를 만들고, 버튼을 클릭했을 때 간단한 콘솔창 내 출력문이 나타나도록 구현한 코드입니다.
<br><br>

~~~java

package javafxexam;

import javafx.application.Application;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.layout.StackPane;
import javafx.stage.Stage;

public class JavafxExample extends Application {

    @Override
    public void start(Stage primaryStage) {
        // 버튼 생성
        Button btn = new Button();
        // 버튼에 text 설정
        btn.setText("Hello World");
        // 버튼에 핸들러 지정
        btn.setOnAction((ActionEvent event) -> {
            // printing Hello World! to the console
            System.out.println("Hello World!");
        });
        // Layer 구성요소(node)가 추가된 순서대로 덧붙여 시각화하는 레이아웃인 StackPane 초기화
        StackPane root = new StackPane();
        
        // Adding all the nodes to the FlowPane
        root.getChildren().add(btn);
        
        //Creating a scene object
        Scene scene = new Scene(root, 300, 250);
        
        //Adding the title to the window (primaryStage)
        primaryStage.setTitle("Hello World!");
        primaryStage.setScene(scene);
        
        // show the window(primaryStage)
        primaryStage.show();
    }

    public static void main(String[] args) {
        launch(args);
    }
}


~~~

---

## JavaFX Scene Builder

JavaFX를 사용할 때 가장 문제가 되는 것은 화면 구성입니다. 일일이 코드를 작성하고 확인하면서 화면을 구성하는
작업은 대단히 비효율적입니다. 이 화면 작업을 쉽게 할 수 있도록 도와주는 도구가 있는데 바로 
`Scene Builder`라는 툴입니다.  
<br><br>

JavaFx Scene Builder 는 우리가 이전까지 했던 자바코드로 레이아웃을 코딩하고 빌드해보고 다시 수치를 맞추고하는 
과정을 사용자가 신속하게 설계할 수 있도록 도와주는 시각적 레이아웃 도구이며 `FXML` 파일을 생성해줍니다.
<br><br>

### JavaFx Scene Builder 설치

먼저 Java Fx Scene Builder를 설치해야합니다. 아래 링크를 이용하여 파일을 다운로드하고 설치합니다.

[JavaFX Scene Builder](https://www.oracle.com/java/technologies/javafxscenebuilder-1x-archive-downloads.html){: target="_blank" }
<br><br>

[JavaFX Scene Builder - Google Drive](https://drive.google.com/file/d/11PrUBGUnT0RLyJv5oEiqky0iQl0wVvmq/view?usp=share_link){: target="_blank" }
<br><br>

### JavaFx Scene Builder 시작

Scene Builder를 실행하고 컴포넌트를 배치해 화면을 구성하면 됩니다. 
<br><br>

아래 그림과 같이 `FlowPane`에 `Button` 두 개를 배치했습니다. 이제 이 FlowPane을 `FXML`로 저장합니다.
파일명은 `example.fxml`로 바탕화면같은 곳에 저장한 후 추후에 eclipse project안에 복사합니다. 

{% include image.html
file='java-fx-scene-builder.png'
%}
<br>

### JavaFx 코드 작성

간단한 JavaFX코드를 아래와 같이 작성합니다.

* Main.java : 첫번째 창. 버튼을 클릭하면 두번째 창이 뜨게됩니다.

~~~java

package example;

import javafx.application.Application;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.layout.BorderPane;
import javafx.stage.Stage;

public class Main extends Application {

	Button btn;
	
	@Override
	public void start(Stage primaryStage) throws Exception {
		
		// BorderPane생성
		BorderPane root = new BorderPane();
		root.setPrefSize(300, 300);

		btn = new Button("새창을 띄워요!!");
		btn.setPrefSize(300, 50);

		btn.setOnAction(e -> {
			Stage myStage = new MyStage();
			myStage.show();
		});

		root.setBottom(btn);

		Scene scene = new Scene(root);

		primaryStage.setScene(scene);
		primaryStage.setTitle("JavaFX First Window");

		primaryStage.show();	
		
	}
	
	public static void main(String[] args) {
		launch();
	}
}


~~~

* MyStage.java : 두번째 창. Stage class를 상속받아서 작성. 이 안에서 FXML을 로드합니다.

~~~java

package example;

import java.io.IOException;

import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class MyStage extends Stage {

	public MyStage() {
		Parent root = null;

		FXMLLoader loader = 
				new FXMLLoader(getClass().getResource("example.fxml"));
		
		try {
			root = loader.load();
		} catch (IOException e) {
			e.printStackTrace();
		}

		Scene scene = new Scene(root);
		this.setScene(scene);
		this.setTitle("JavaFX Second Window");
	}
}


~~~

* example.fxml : FXML파일입니다. 버튼에 대한 id를 설정합니다. 이 fxml과 연동하는
class인 `MyStageController`를 연결하는 부분에 주의합니다.

~~~xml 
{% raw %}

<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.*?>
<?import javafx.scene.control.*?>
<?import java.lang.*?>
<?import javafx.scene.layout.*?>


<FlowPane fx:controller="example.MyStageController" 
          alignment="CENTER" hgap="100.0" maxHeight="-Infinity" maxWidth="-Infinity" 
          minHeight="-Infinity" minWidth="-Infinity" prefHeight="199.0" prefWidth="600.0" 
          xmlns="http://javafx.com/javafx/8" xmlns:fx="http://javafx.com/fxml/1">
   <children>
      <Button mnemonicParsing="false" prefHeight="76.0" prefWidth="115.0" 
              text="첫번째 버튼" 
              fx:id="firstBtn">
         <opaqueInsets>
            <Insets />
         </opaqueInsets>
      </Button>
      <Button mnemonicParsing="false" prefHeight="76.0" prefWidth="115.0" 
              text="두번째 버튼"  
              fx:id="secondBtn" />
   </children>
   <opaqueInsets>
      <Insets />
   </opaqueInsets>
</FlowPane>

{% endraw %}
~~~

* MyStageController.java : 이벤트를 처리하기 위한 Controller class입니다. `Initializable` interface를
반드시 구현해야 하며 `initialize()` 메소드내에서 이벤트 등록을 해줍니다. FXML과 어떻게 연동되는지를 잘 확인해야 합니다.

~~~java

package example;

import java.net.URL;
import java.util.ResourceBundle;

import javafx.fxml.FXML;
import javafx.fxml.Initializable;
import javafx.scene.control.Button;

public class MyStageController implements Initializable {

	@FXML private Button firstBtn;
	@FXML private Button secondBtn;
	
	@Override
	public void initialize(URL arg0, ResourceBundle arg1) {
		
		System.out.println("Controller 생성!!");
		
		firstBtn.setOnAction(e -> {
			System.out.println("첫번째 버튼이 눌렸어요!");
		});
		
		secondBtn.setOnAction(e -> {
			System.out.println("두번째 버튼이 눌렸어요!");
		});
		
	}

}


~~~

End.

{% include links.html %}
