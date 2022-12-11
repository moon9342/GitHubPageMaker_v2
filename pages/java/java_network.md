---
title: Java Network
sidebar: java_sidebar
summary: "Java Network에 대한 설명입니다."
permalink: java_network.html
folder: java
---

## Network 개요

### Internet

{% include image.html
file='java-internet.png'
%}

### IP Address

{% include image.html
file='java-ip-address.png'
%}

### Protocol

{% include image.html
file='java-protocol.png'
%}

### Port

{% include image.html
file='java-port.png'
%}

### TCP/IP Protocol

{% include image.html
file='java-tcp-ip.png'
%}

### Socket

{% include image.html
file='java-socket.png'
%}

{% include image.html
file='java-socket-1.png'
%}

{% include image.html
file='java-socket-2.png'
%}

{% include image.html
file='java-socket-3.png'
%}
<br>

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
아래에 "Create module-info.java file" 체크는 하지 않습니다. 에러를 유발할 수 있기때문입니다.
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

## 기본적인 절차

* 서버는 클라이언트가 접속을 요청하면, 이에 대한 응답을 하는 프로세스를 의미.

* 서버도 클라이언트와 마찬가지로 Socket class를 이용해야 네트워크 통신을 할 수 있다.

* 그러나, 서버는 클라이언트와 달리 클라이언트의 접속을 대기하고 있어야 한다.

* 서버 쪽에서는 client의 접속을 기다리기 위해 ServerSocket객체를 생성한 후 accept()를 호출

* accept() method는 blocking method

* 클라이언트에서는 Socket객체를 생성하면 서버에 접속할 수 있다.

* 이때 접속한 성공하면 실제로 Socket 객체가 반환

* 데이터 통신을 하기 위해 클라이언트와 서버가 각각 생성한 socket에 대해서 Stream을 생성한다.

* 이때, 서버의 OutputStream은 클라이언트의 InputStream과 연결되고 반대로 서버의 InputStream은 클라이언트의 OutputStream과 연결된다.

* 접속을 종료할 때는 socket에 대해서 close() method를 호출한다.

* 주의할 점은 socket으로 부터 얻은 Stream이 존재할 때는 해당 IO객체를 먼저 close() 해 주어야 한다.

{% include image.html
file='java-network-fig.png'
%}

---

## 연습문제 1

* 접속한 클라이언트에게 날짜를 전송하는 서버프로그램을 작성해 보자.

* 클라이언트가 서버에 접속하면 서버는 현재 시간을 구해서 클라이언트에게 전송하고 서버프로그램을 종료.

* 클라이언트는 서버가 보내준 데이터를 화면에 출력하고 프로그램 종료

~~~java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.Socket;
import java.net.UnknownHostException;

import javafx.application.Application;
import javafx.application.Platform;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.TextArea;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.FlowPane;
import javafx.stage.Stage;

public class Exam01_DateClient extends Application {

	TextArea textarea;
	Button connBtn;
	
	private void printMsg(String name) {
		Platform.runLater(()->{
			textarea.appendText(name + "\n");
		});
	}
	
	
	@Override
	public void start(Stage primaryStage) throws Exception {

		BorderPane root = new BorderPane();
		root.setPrefSize(700, 500);
		
		textarea = new TextArea();
		root.setCenter(textarea);
		
		connBtn = new Button("Date 서버 접속");
		connBtn.setPrefSize(150, 40);
		connBtn.setOnAction(e->{
			textarea.clear();
			try {
				Socket socket = new Socket("localhost",5555);
				BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
				
				String msg = br.readLine();
				
				printMsg(msg);
				
				br.close();
				socket.close();
				printMsg("서버와의 연결 종료");
			} catch (UnknownHostException e1) {
				e1.printStackTrace();
			} catch (IOException e1) {
				e1.printStackTrace();
			}
			
		});
		
		
		
		FlowPane flowPane = new FlowPane();
     	flowPane.setPadding(new Insets(10, 10, 10, 10));
		flowPane.setColumnHalignment(HPos.CENTER);
		flowPane.setPrefSize(700, 40);
		flowPane.setHgap(10);
		flowPane.getChildren().add(connBtn);
		
		root.setBottom(flowPane);
		
		Scene scene = new Scene(root);
		primaryStage.setScene(scene);
		primaryStage.setTitle("Date Server로 부터 현재 날짜 가져오기");
		primaryStage.setOnCloseRequest(e->{
		});
		primaryStage.show();
		
	}
	 
	public static void main(String[] args) {
		launch();
	}	
	

}


~~~


~~~java 

import java.io.IOException;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;
import java.text.DateFormat;
import java.util.Date;

public class Exam01_DateServer {

	public static void main(String[] args) {

		try {
			ServerSocket serverSocket = new ServerSocket(5555);
			System.out.println("Date Server 기동 - 5555");
			Socket socket = serverSocket.accept();
			
			PrintWriter pr = new PrintWriter(socket.getOutputStream());
			
			DateFormat dateFormat = DateFormat.getInstance();
			pr.println(dateFormat.format(new Date()));
			pr.flush();
			pr.close();
			socket.close();
			serverSocket.close();
			System.out.println("Date Server 종료");
		} catch (IOException e) {
			e.printStackTrace();
		}

	}

}

~~~

---

## 연습문제 2

* 간단한 Echo 클라이언트/서버 프로그램 작성

* Echo 프로그램이란 클라이언트가 보낸 문자열을 서버가 받아서 다시 클라이언트에게 재 전송해주는 프로그램

* 단, 프로그램은 클라이언트가 `/exit`를 입력하면 종료하는 것으로 작성

~~~java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.net.UnknownHostException;

import javafx.application.Application;
import javafx.application.Platform;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.TextArea;
import javafx.scene.control.TextField;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.FlowPane;
import javafx.stage.Stage;

public class Exam02_EchoClient extends Application {

	TextArea textarea;
	TextField textfield;
	Button connBtn;
	
	Socket socket;
	BufferedReader br;
	PrintWriter pr;
	
	private void printMsg(String name) {
		Platform.runLater(()->{
			textarea.appendText(name + "\n");
		});
	}
	
	
	@Override
	public void start(Stage primaryStage) throws Exception {

		BorderPane root = new BorderPane();
		root.setPrefSize(700, 500);
		
		textarea = new TextArea();
		root.setCenter(textarea);
		
		connBtn = new Button("Echo 서버 접속");
		connBtn.setPrefSize(150, 40);
		connBtn.setOnAction(e->{
			textarea.clear();
			try {
				socket = new Socket("localhost",5555);
				br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
				pr = new PrintWriter(socket.getOutputStream());		
				printMsg("Echo Server 연결 성공");
				textfield.setDisable(false);
			} catch (UnknownHostException e1) {
				e1.printStackTrace();
			} catch (IOException e1) {
				e1.printStackTrace();
			}			
		});

		textfield = new TextField();
		textfield.setPrefSize(200, 40);
		textfield.setDisable(true);
		textfield.setOnAction(e->{
			
			String msg = textfield.getText();
			pr.println(msg);
			pr.flush();
			textfield.clear();
			
			if(!msg.equals("/exit")) {
				try {
					String serverMsg = br.readLine();
					printMsg("[서버로부터 전달된 메시지] : " + serverMsg);
				} catch (IOException e1) {
					e1.printStackTrace();
				}				
			} else {
				printMsg("[서버와의 연결 종료]");
				textfield.setDisable(true);
			}
		});
		
		
		FlowPane flowPane = new FlowPane();
     	flowPane.setPadding(new Insets(10, 10, 10, 10));
		flowPane.setColumnHalignment(HPos.CENTER);
		flowPane.setPrefSize(700, 40);
		flowPane.setHgap(10);
		flowPane.getChildren().add(connBtn);
		flowPane.getChildren().add(textfield);
		
		root.setBottom(flowPane);
		
		Scene scene = new Scene(root);
		primaryStage.setScene(scene);
		primaryStage.setTitle("Echo Server에 접속");
		primaryStage.setOnCloseRequest(e->{
			try {
				if( br != null ) br.close();
				if( pr != null ) pr.close();
				if( socket != null ) socket.close();
			} catch (Exception e2) {
				e2.printStackTrace();
			}
		});
		primaryStage.show();		
	}
	 
	public static void main(String[] args) {
		launch();
	}	
	
}

~~~


~~~java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;

public class Exam02_EchoServer {

	public static void main(String[] args) {
		ServerSocket serverSocket = null;
		Socket socket = null;
		BufferedReader br = null;
		PrintWriter pr = null;
		try {
			serverSocket = new ServerSocket(5555);
			System.out.println("클라이언트 접속 대기");
			socket = serverSocket.accept();
			br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
			pr = new PrintWriter(socket.getOutputStream());
			String msg = null;
			while(true) {
				msg = br.readLine();
				if( (msg==null) || (msg.equals("/exit")) ) {
					break;
				}
				pr.println(msg);
				pr.flush();
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				if( br != null ) br.close();
				if( pr != null ) pr.close();
				if( socket != null ) socket.close();
				if( serverSocket != null ) serverSocket.close();
				System.out.println("Echo Server 종료");
			} catch (IOException e) {
				e.printStackTrace();
			}			
		}
	}

}

~~~

## 연습문제 3

* 이전의 code는 1명의 클라이언트에 대해서는 잘 동작하지만 다수의 클라이언트에 대해서는 서비스를 제공하지 못한다.

* 다수의 클라이언트에게 똑같은 Echo 서비스를 제공해 주기 위해서는 어떻게 해야 하는가?

* Multi Thread 이용

* 다수의 클라이언트에게 똑같은 Echo 서비스를 제공하는 프로그램 작성

{% include image.html
file='java-chatting.png'
%}

~~~java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.net.UnknownHostException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javafx.application.Application;
import javafx.application.Platform;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.TextArea;
import javafx.scene.control.TextField;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.FlowPane;
import javafx.stage.Stage;

public class Exam03_ChatClient extends Application {

	TextArea textarea;
	TextField idfield, msgfield;
	Button connBtn, disconnBtn;
	
	Socket socket;
	BufferedReader br;
	PrintWriter pw;
	ExecutorService executorService = Executors.newCachedThreadPool();
	
	
	private void printMsg(String name) {
		Platform.runLater(()->{
			textarea.appendText(name + "\n");
		});
	}
	
	class ReceiveRunnable implements Runnable {

		private BufferedReader br;
		
		public ReceiveRunnable(BufferedReader br) {
			this.br = br;
		}
		
		@Override
		public void run() {
			String msg = null;
			try {
				while(true) {
					msg = br.readLine();
					if( msg == null ) {
						break;
					}
					printMsg(msg);					
				}
			} catch (IOException e) {
				// e.printStackTrace();
			} finally {
				try {
					if( br != null ) br.close();
				} catch (Exception e2) {
					e2.printStackTrace();
				}
			}
		}
		
	}
	
	@Override
	public void start(Stage primaryStage) throws Exception {

		BorderPane root = new BorderPane();
		root.setPrefSize(700, 500);
		
		textarea = new TextArea();
		textarea.setEditable(false);
		root.setCenter(textarea);
		
		connBtn = new Button("Chat 서버 접속");
		connBtn.setPrefSize(150, 40);
		connBtn.setOnAction(e->{
			textarea.clear();
			disconnBtn.setDisable(false);
			try {
				socket = new Socket("localhost",5555);
				br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
				pw = new PrintWriter(socket.getOutputStream());		
				printMsg("Chat Server 연결 성공");
				
				idfield.setDisable(false);
				msgfield.setDisable(false);
				
				ReceiveRunnable receiver = new ReceiveRunnable(br);
				executorService.execute(receiver);
				connBtn.setDisable(true);
			} catch (UnknownHostException e1) {
				e1.printStackTrace();
			} catch (IOException e1) {
				e1.printStackTrace();
			}			
		});

		idfield = new TextField();
		idfield.setPrefSize(70, 40);
		idfield.setDisable(true);
		
		msgfield = new TextField();
		msgfield.setPrefSize(200, 40);
		msgfield.setDisable(true);
		msgfield.setOnAction(e->{
			
			String msg = "[" + idfield.getText() + "] : " + msgfield.getText();
			pw.println(msg);
			pw.flush();
			msgfield.clear();
			
		});
		
		disconnBtn = new Button("Chat 서버 접속 종료");
		disconnBtn.setPrefSize(150, 40);
		disconnBtn.setDisable(true);
		
		disconnBtn.setOnAction(e->{
			connBtn.setDisable(false);
			idfield.setDisable(false);
			msgfield.setDisable(false);
			try {
				printMsg("[서버연결 종료]");
				// br은 blocking중이므로 pw를 먼저 close.
				if( pw != null ) pw.close();
				if( br != null ) br.close();				
				if( socket != null ) socket.close();
			} catch (UnknownHostException e1) {
				e1.printStackTrace();
			} catch (IOException e1) {
				e1.printStackTrace();
			}	
			disconnBtn.setDisable(true);
		});
		
		FlowPane flowPane = new FlowPane();
     	flowPane.setPadding(new Insets(10, 10, 10, 10));
		flowPane.setColumnHalignment(HPos.CENTER);
		flowPane.setPrefSize(700, 40);
		flowPane.setHgap(10);
		flowPane.getChildren().add(connBtn);
		flowPane.getChildren().add(idfield);
		flowPane.getChildren().add(msgfield);
		flowPane.getChildren().add(disconnBtn);
		
		root.setBottom(flowPane);
		
		Scene scene = new Scene(root);
		primaryStage.setScene(scene);
		primaryStage.setTitle("Echo Server에 접속");
		primaryStage.setOnCloseRequest(e->{
			try {
				// br은 blocking중이므로 pw를 먼저 close.
				if( pw != null ) pw.close();
				if( br != null ) br.close();				
				if( socket != null ) socket.close();
				executorService.shutdownNow();
			} catch (Exception e2) {
				e2.printStackTrace();
			}
		});
		primaryStage.show();		
	}
	 
	public static void main(String[] args) {
		launch();
	}	
	
}

~~~

~~~java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javafx.application.Application;
import javafx.application.Platform;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.TextArea;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.FlowPane;
import javafx.stage.Stage;

public class Exam03_ChatServer extends Application {

	TextArea textarea;
	Button startBtn, stopBtn;
	ServerSocket serverSocket;
	ExecutorService executorService = Executors.newCachedThreadPool();
	SharedObject sharedObject = new SharedObject();
	
	private void printMsg(String name) {
		Platform.runLater(()->{
			textarea.appendText(name + "\n");
		});
	}
	
	class SharedObject {
		
		List<ClientRunnable> clients = new ArrayList<ClientRunnable>();
		
		public synchronized void broadcast(String msg) {
			clients.stream().forEach(t -> {
				t.pw.println(msg);
				t.pw.flush();
			});
		}		
	}
	
	
	class ClientRunnable implements Runnable {

		private SharedObject sharedObject;		
		private Socket socket;
		private BufferedReader br;
		private PrintWriter pw;

		public ClientRunnable(SharedObject sharedObject,Socket socket) throws IOException {
			this.sharedObject = sharedObject;
			this.socket = socket;
			br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
			pw = new PrintWriter(socket.getOutputStream());						
		}
		
		@Override
		public void run() {
			
			String msg = null;

			try {
				while(true) {
					msg = br.readLine();
					if( msg == null ) {
						break;
					}
					sharedObject.broadcast(msg);					
				}
			} catch (IOException e) {
				e.printStackTrace();
			}
			
			try {
				if( pw != null ) pw.close();
				if( br != null ) br.close();				
				if( socket != null ) socket.close();
			} catch (Exception e) {
				e.printStackTrace();
			}			
			sharedObject.clients.remove(this);
		}
		
	}
	
	@Override
	public void start(Stage primaryStage) throws Exception {

		BorderPane root = new BorderPane();
		root.setPrefSize(700, 500);
		
		textarea = new TextArea();
		textarea.setEditable(false);
		root.setCenter(textarea);
		
		
		startBtn = new Button("Chat Server 기동");
		startBtn.setPrefSize(150, 40);
		startBtn.setOnAction(e->{
			textarea.clear();
			printMsg("[ 채팅서버 기동 - 5555 ]");
			Runnable runnable = new Runnable() {
				
				@Override
				public void run() {
					try {
						serverSocket = new ServerSocket(5555);
						while(true) {
							printMsg("[ 클라이언트 접속 대기중 ]");
							Socket socket = serverSocket.accept();
							ClientRunnable cRunnable = new ClientRunnable(sharedObject,socket);
							synchronized (this) {
								sharedObject.clients.add(cRunnable);
							}
							printMsg("[ 클라이언트 접속 - 현재 클라이언트 수 : " + sharedObject.clients.size() + " ]");							
							executorService.execute(cRunnable);							
						}
					} catch (IOException e) {
						e.printStackTrace();
					}
				}
			};
			executorService.execute(runnable);
		});

		stopBtn = new Button("Chat Server 중지");
		stopBtn.setPrefSize(150, 40);
		stopBtn.setOnAction(e->{
			printMsg("[ 서버 중지 - 프로그램 재시작 필요 ]");
			executorService.shutdownNow();
		});
		
		
		FlowPane flowPane = new FlowPane();
     	flowPane.setPadding(new Insets(10, 10, 10, 10));
		flowPane.setColumnHalignment(HPos.CENTER);
		flowPane.setPrefSize(700, 40);
		flowPane.setHgap(10);
		flowPane.getChildren().add(startBtn);
		flowPane.getChildren().add(stopBtn);
		
		root.setBottom(flowPane);
		
		Scene scene = new Scene(root);
		primaryStage.setScene(scene);
		primaryStage.setTitle("Chat Server ( Java IO )");
		primaryStage.setOnCloseRequest(e->{
			executorService.shutdownNow();
		});
		primaryStage.show();	
		
		 
	}
	 
	public static void main(String[] args) {
		launch();
	}	
	
}

~~~


End.

{% include links.html %}
