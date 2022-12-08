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
