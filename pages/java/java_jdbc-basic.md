---
title: Java JDBC Basic
sidebar: java_sidebar
summary: "Java JDBC 기본문법입니다."
permalink: java_jdbc-basic.html
folder: java
---

## JDBC 개요

`JDBC`(Java Database Conectivity)는 Java를 이용하여 Database에 있는 값을 활용하기 위한 API를
의미합니다. 
<br><br>

왜 이런 JDBC를 이용하여 데이터베이스를 연결해서 사용해야 할까요? 
<br><br>

먼저 알아두어야 할 것은 `JDBC Driver`라는 것입니다. JDBC Driver는 Java 응용 프로그램이 
데이터베이스와 상호 작용할 수 있도록 하는 소프트웨어 구성 요소(class & interface)입니다.
<br><br>

아시다시피 DBMS는 굉장히 다양한 종류가 존재합니다. 당연히 각 DBMS vendor는 Java program을 이용해 자사의
DBMS를 사용할 수 있도록 특별히 제작된 class와 interface들을 제공합니다. (vendor들 마다 구현한 내용이
각기 다릅니다.) 굉장히 많은 class와 interface를 제공하는데 이 중 가장 중요한 역할을 담당하는게
JDBC Driver입니다. (DBMS와의 통신을 담당하는 class가 Driver class입니다.)
<br><br>

위에서 언급했듯이 이런 class와 interface는 DBMS마다 사용하는 방법이 다릅니다. 아래 그림처럼요.

{% include image.html
file='java-jdbc-need.png'
%}
<br>

위의 그림에서 만약 데이터베이스를 변경한다고 가정하면 어떻게 될까요? 소스코드를 수정해서 관련된 코드들을 일일이
변경해줘야 합니다. 그런데 만일 공통된 Interface를 제공해서 일관적인 사용이 가능하도록 제공한다면 이런 변경에
쉽게 대응할 수 있지 않을까요? 아래 그림처럼 말이죠.

{% include image.html
file='java-jdbc-need-1.png'
%}
<br>

JDBC를 그림으로 살펴보면 아래 그림과 같이 표현할 수 있습니다. 

{% include image.html
file='java-jdbc-concept.png'
%}
<br>

---

## JDBC Driver 등록

MySQL 홈페이지에서 다운로드 받거나 `C:\Program Files (x86)\MySQL\Connector J 8.0` 폴더안의 
`mysql-connector-j-8.0.31.jar`파일을 Java Project의 `build path`에 추가합니다. 
(`Tomcat`의 경우 Tomcat Home 아래의 `lib` 폴더에 파일을 복사합니다.)

---

## JDBC 활용 전체 과정

{% include image.html
file='java-jdbc-procedure.png'
%}

---

## JDBC 드라이버 등록

{% include image.html
file='java-jdbc-driver-register.png'
%}

~~~java

package lecture.jdbc;

public class Main {

	public static void main(String[] args) {
		
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			System.out.println("MySQL Driver Loading 성공!");
		} catch (Exception e) {
			System.out.println(e);
		}
	}
}


~~~

---

## 테이블 생성

제공된 Script file(`_BookTableDump.sql`)을 실행해 테이블을 생성합니다. 사용하는 데이터베이스는
`library` 입니다. 파일은 아래의 링크에서 받을 수 있습니다.
<br><br>

* [SQL Script file](https://drive.google.com/file/d/1zhibjEpn4dhB_W9ddqE-yfR3a0z7IaPo/view?usp=sharing){: target="_blank" }
<br>

---

## 데이터베이스 연결

{% include image.html
file='java-jdbc-connection.png'
%}

~~~java

package lecture.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;

public class Main {

	public static void main(String[] args) {
		Connection con = null;
		
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			System.out.println("MySQL Driver Loading 성공!");
			String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
			con = DriverManager.getConnection(jdbcUrl,"root","test1234");
			System.out.println("데이터베이스 연결 성공");
		} catch (Exception e) {
			System.out.println(e);
		} finally {
			try {
				if( con != null ) con.close();
				System.out.println("connection close()");
			} catch (Exception e2) {
				// TODO: handle exception
			}
		}
	}
}

~~~

---

## statement 생성

{% include image.html
file='java-jdbc-statement.png'
%}

~~~java

String sql = "select * from book";

// 기본적인 Statement - 실제로 사용되지는 않아요!			
Statement stmt = con.createStatement();

// 일반적으로 사용되는 PreparedStatement - 편리성과 효율이 좋아요!
PreparedStatement pstmt = con.prepareStatement(sql);

~~~

## Query 실행

{% include image.html
file='java-jdbc-statement-execute.png'
%}

~~~java

ResultSet rs = pstmt.executeQuery();

~~~

---

## 결과 받기

{% include image.html
file='java-jdbc-statement-execute-result.png'
%}

---

## 연결 끊기

{% include image.html
file='java-jdbc-statement-execute-close.png'
%}

---

## ResultSet 활용

{% include image.html
file='java-jdbc-result-set-1.png'
%}

{% include image.html
file='java-jdbc-result-set-2.png'
%}

{% include image.html
file='java-jdbc-result-set-3.png'
%}

{% include image.html
file='java-jdbc-result-set-4.png'
%}

{% include image.html
file='java-jdbc-result-set-5.png'
%}

---

## 데이터의 입력 수정 삭제

{% include image.html
file='java-jdbc-ddl.png'
%}

{% include image.html
file='java-jdbc-ddl-1.png'
%}

{% include image.html
file='java-jdbc-ddl-2.png'
%}

---

## 동적 SQL

{% include image.html
file='java-jdbc-prepared-statement.png'
%}
<br>

---

## 연습문제 1

책 제목에 특정 keyword가 들어있는 책을 검색해서 출력하는 프로그램을 작성해보세요!
<br><br>

* 일반 Java application으로 작성합니다.
* 책ISBN, 책제목, 책저자, 책가격 순으로 출력합니다.  

<br><br>

~~~java

package lecture.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class BookSearchCommand {

	public static void main(String[] args) {
		
		Connection con = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;

		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			System.out.println("MySQL Driver Loading 성공!");
			String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
			con = DriverManager.getConnection(jdbcUrl, "root", "test1234");
			System.out.println("데이터베이스 연결 성공");
			
			String keyword = "여행";
			
			String sql = "select bisbn, btitle, bauthor, bprice from book where btitle like ?";
			
			pstmt = con.prepareStatement(sql);
			
			pstmt.setString(1, "%" + keyword + "%");
			
			rs = pstmt.executeQuery();
			
			while(rs.next()) {
				String tmp = "";
				tmp += rs.getString("bisbn") + "  ";
				tmp += rs.getString("btitle") + "  ";
				tmp += rs.getString("bauthor") + "  ";
				tmp += rs.getInt("bprice") + "  ";
				System.out.println(tmp);
			}
			
		} catch (Exception e) {
			System.out.println(e);
		} finally {
			try {
				rs.close();
				pstmt.close();
				if (con != null)
					con.close();
				System.out.println("connection close()");
			} catch (Exception e2) {
				
			}
		}

	}
}

// MySQL Driver Loading 성공!
// 데이터베이스 연결 성공
// 978-89-98756-21-5  IT CookBook, C++ 하이킹 : 객체지향과 만나는 여행  성윤정, 김태은  25000  
// 979-11-85933-01-6  게스트하우스 창업 A to Z : 청춘여행자의 낭만적 밥벌이  김아람  15000  
// 979-11-85933-10-8  크로아티아의 작은 마을을 여행하다 : 자다르의 일몰부터 두브로브니크의 붉은 성벽까지  양미석  15800  
// 979-11-951538-1-7  도쿄의 오래된 상점을 여행하다 : 소세키의 당고집부터 백 년 된 여관까지  여지영, 이진숙  15000  
// connection close()

~~~

---

## 연습문제 2

위와 같은 내용을 JavaFX를 이용해서 작성해보세요! 구현하기 위해 TableView에 대한 기본적인 내용은 같이
공부하고 그 내용을 바탕으로 구현해보도록 하겠습니다. 
<br><br>

### JavaFX Simple TableView

아래와 같은 화면을 만들어보자.

{% include image.html
file='java-jdbc-simple-tableview.png'
%}
<br>

~~~java

package lecture.jdbc;

public class Book {
	
	public Book() {
		
	}
	
	
	public Book(String bisbn, String btitle, String bauthor, int bprice) {
		super();
		this.bisbn = bisbn;
		this.btitle = btitle;
		this.bauthor = bauthor;
		this.bprice = bprice;
	}


	private String bisbn;
	private String btitle;
	private String bauthor;
	private int bprice;
	
	public String getBisbn() {
		return bisbn;
	}


	public void setBisbn(String bisbn) {
		this.bisbn = bisbn;
	}


	public String getBtitle() {
		return btitle;
	}


	public void setBtitle(String btitle) {
		this.btitle = btitle;
	}


	public String getBauthor() {
		return bauthor;
	}


	public void setBauthor(String bauthor) {
		this.bauthor = bauthor;
	}


	public int getBprice() {
		return bprice;
	}


	public void setBprice(int bprice) {
		this.bprice = bprice;
	}

}


~~~

~~~java

package lecture.jdbc;

import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.TextField;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.FlowPane;
import javafx.stage.Stage;

public class SimpleJavaFXTableView extends Application {
	
    Scene scene;
    TextField keywordField;
    
    // 사용하는 VO BOOK은 public class이어야 한다. 그렇지 않으면 RuintimeException    
    TableView<Book> tableView;
	
	@Override
	public void start(Stage primaryStage) throws Exception {

		// BorderPane생성
		BorderPane root = new BorderPane();
		root.setPrefSize(700, 500);
					
		FlowPane bottomFlowPane = new FlowPane();
		bottomFlowPane.setPadding(new Insets(10,10,10,10)); // padding(여백)설정
		bottomFlowPane.setColumnHalignment(HPos.CENTER); // 정렬
		bottomFlowPane.setPrefSize(700, 40);
		bottomFlowPane.setHgap(10);	
		
		keywordField = new TextField();
		keywordField.setPrefSize(250, 40);

		ObservableList<Book> books = 
				FXCollections.observableArrayList();
		books.add(new Book("1-234", "Java 30일 완성", "홍길동", 20));
		books.add(new Book("1-567", "Java 여기까지인가", "김길동", 70));
		books.add(new Book("3-7789", "Java 이젠 그만", "신사임당", 45));
		books.add(new Book("3-56", "Java 너무 어렵..", "강감찬", 30));
		books.add(new Book("27-123455", "Java 쉬움 ㅋ", "김연아", 40));
        
		// 컬럼 객체를 생성합니다.
		
		// 책 ISBN
        TableColumn<Book, String> isbnColumn = new TableColumn<>("ISBN");
        isbnColumn.setMinWidth(150);
        isbnColumn.setCellValueFactory(new PropertyValueFactory<>("bisbn"));

        // 책제목
        TableColumn<Book, String> titleColumn = new TableColumn<>("Title");
        titleColumn.setMinWidth(150);
        titleColumn.setCellValueFactory(new PropertyValueFactory<>("btitle"));

        // 책저자
        TableColumn<Book, String> authorColumn = new TableColumn<>("Author");
        authorColumn.setMinWidth(100);
        authorColumn.setCellValueFactory(new PropertyValueFactory<>("bauthor"));
        
        // 책가격
        TableColumn<Book, Integer> priceColumn = new TableColumn<>("Price");
        priceColumn.setMinWidth(100);
        priceColumn.setCellValueFactory(new PropertyValueFactory<>("bprice"));
        
        tableView = new TableView<Book>();
        tableView.setItems(books);
        tableView.getColumns().addAll(isbnColumn, titleColumn,  
        		authorColumn, priceColumn);
        
		bottomFlowPane.getChildren().add(keywordField);

		root.setCenter(tableView);
		root.setBottom(bottomFlowPane);
		
		Scene scene = new Scene(root);
		
		primaryStage.setScene(scene);
		primaryStage.setTitle("Simple JavaFX TableView");
		
		primaryStage.show();
	}

	public static void main(String[] args) {
		launch();
	}
}


~~~

### JavaFX TableView BookSearch

JDBC와 연동해 키워드를 이용한 도서
End.

{% include links.html %}
