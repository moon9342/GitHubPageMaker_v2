---
title: Java JDBC 활용예제
sidebar: java_sidebar
summary: "JavaFX를 이용한 JDBC 활용입니다."
permalink: java_jdbc-book-manager.html
folder: java
---

## VO, DO, Entity, DTO

활용예제를 보기 앞서 먼저 `VO`라고 불리는 객체에 대해서 알아보도록 하죠.
<br><br>

`VO`란 도메인에서 한 개 또는 그 이상의 속성들을 묶어서 특정 값을 나타내는 객체를 의미합니다.
무슨말인지 잘 모르겠네요. 아주 쉽게 생각하면 VO는 테이블의 한 줄(row) 데이터를 저장하기 위한 객체를
의미합니다. 
<br><br>

`VO`는 도메인 객체(`DO`)의 일종이고 보통 기본 키로 식별 값을 갖는 `Entity`와 구별해서 사용합니다.
또한 데이터의 전달을 주 목적으로 하는 `DTO`와도 의미가 다릅니다. 하지만 형태가 거의 비슷하기 때문에
모두 같은 의미로 사용합니다. 
<br><br>

아래의 JavaFX 예제를 위해 우리는 하나의 책 정보를 표현하는 `VO`를 생성하기 위한 class를 정의해서 사용할 예정입니다. 

{% include image.html
file='java-jdbc-entity.png'
%}

## 연습문제 1

도서검색에 대한 내용을 JavaFX를 이용해서 작성해보세요! 구현하기 위해 TableView에 대한 기본적인 내용은 같이
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
		bottomFlowPane.setPadding(new Insets(10, 10, 10, 10)); // padding(여백)설정
		bottomFlowPane.setColumnHalignment(HPos.CENTER); // 정렬
		bottomFlowPane.setPrefSize(700, 40);
		bottomFlowPane.setHgap(10);

		keywordField = new TextField();
		keywordField.setPrefSize(250, 40);

		ObservableList<Book> books = FXCollections.observableArrayList();
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
		tableView.getColumns().addAll(isbnColumn, titleColumn, authorColumn, priceColumn);

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

### JavaFX JDBC TableView

JDBC와 연동해 키워드를 이용한 도서검색을 구현해 보도록 하겠습니다.
<br><br>

구현된 결과 화면은 다음과 같습니다. 

{% include image.html
file='java-jdbc-jdbc-tableview.png'
%}
<br>

~~~java

package lecture.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

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

public class BookSearchJavaFXTableView extends Application {

	Scene scene;
	TextField keywordField;
	String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";

	// 사용하는 VO BOOK은 public class이어야 한다. 그렇지 않으면 RuintimeException
	TableView<Book> tableView;

	public BookSearchJavaFXTableView() {
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			System.out.println("MySQL Driver Loading 성공!");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	@Override
	public void start(Stage primaryStage) throws Exception {

		// BorderPane생성
		BorderPane root = new BorderPane();
		root.setPrefSize(700, 500);

		FlowPane bottomFlowPane = new FlowPane();
		bottomFlowPane.setPadding(new Insets(10, 10, 10, 10)); // padding(여백)설정
		bottomFlowPane.setColumnHalignment(HPos.CENTER); // 정렬
		bottomFlowPane.setPrefSize(700, 40);
		bottomFlowPane.setHgap(10);

		keywordField = new TextField();
		keywordField.setPrefSize(250, 40);
		keywordField.setOnAction(e -> {
			Connection con = null;
			PreparedStatement pstmt = null;
			ResultSet rs = null;
			ObservableList<Book> books = FXCollections.observableArrayList();
			try {
				con = DriverManager.getConnection(jdbcUrl, "root", "test1234");

				String keyword = keywordField.getText();

				String sql = "select bisbn, btitle, bauthor, bprice from book where btitle like ?";

				pstmt = con.prepareStatement(sql);

				pstmt.setString(1, "%" + keyword + "%");

				rs = pstmt.executeQuery();

				while (rs.next()) {
					books.add(new Book(rs.getString("bisbn"), 
					                   rs.getString("btitle"), 
					                   rs.getString("bauthor"),
					                   rs.getInt("bprice")));
				}

				tableView.setItems(books);

				keywordField.clear();

			} catch (SQLException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			} finally {
				try {
					rs.close();
					pstmt.close();
					con.close();
				} catch (SQLException e1) {
					// TODO Auto-generated catch block
					e1.printStackTrace();
				}
			}
		});

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
		tableView.getColumns().addAll(isbnColumn, titleColumn, authorColumn, priceColumn);

		bottomFlowPane.getChildren().add(keywordField);

		root.setCenter(tableView);
		root.setBottom(bottomFlowPane);

		Scene scene = new Scene(root);

		primaryStage.setScene(scene);
		primaryStage.setTitle("BookSearch JavaFX TableView");

		primaryStage.show();
	}

	public static void main(String[] args) {
		launch();
	}

}


~~~

## 연습문제 2

위의 내용에 추가적으로 삭제기능을 추가해 보도록 하겠습니다. 
<br><br>

아마 구현은 큰 문제없이 잘 될 듯 합니다. 하지만 구현한 코드를 보면 무언가 석연치 않은 부분도 많이
보일 듯 합니다. 구현이 끝난 후 어떤 문제가 있을지 생각해보도록 하죠.
<br><br>

일단 구현된 결과 화면은 다음과 같습니다.

{% include image.html
file='java-jdbc-delete-tableview.png'
%}
<br>

~~~java 

package lecture.jdbc.bookdelete;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableRow;
import javafx.scene.control.TableView;
import javafx.scene.control.TextField;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.FlowPane;
import javafx.stage.Stage;

public class BookSearchDeleteJavaFX extends Application {

	Scene scene;
	TextField keywordField;
	Button deleteBtn;
	String selectedISBN;
	String searchKeyword;
	String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";

	// 사용하는 VO BOOK은 public class이어야 한다. 그렇지 않으면 RuintimeException
	TableView<Book> tableView;

	public BookSearchDeleteJavaFX() {
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			System.out.println("MySQL Driver Loading 성공!");
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	@Override
	public void start(Stage primaryStage) throws Exception {

		// BorderPane생성
		BorderPane root = new BorderPane();
		root.setPrefSize(700, 500);

		FlowPane bottomFlowPane = new FlowPane();
		bottomFlowPane.setPadding(new Insets(10, 10, 10, 10)); // padding(여백)설정
		bottomFlowPane.setColumnHalignment(HPos.CENTER); // 정렬
		bottomFlowPane.setPrefSize(700, 40);
		bottomFlowPane.setHgap(10);

		deleteBtn = new Button("선택한 책 삭제");
		deleteBtn.setPrefSize(150, 40);
		deleteBtn.setDisable(true);
		deleteBtn.setOnAction(e -> {

			Connection con = null;
			PreparedStatement pstmt = null;
			ResultSet rs = null;
			try {
				con = DriverManager.getConnection(jdbcUrl, "root", "test1234");

				String sql = "delete from book where bisbn = ?";

				pstmt = con.prepareStatement(sql);

				pstmt.setString(1, selectedISBN);

				int count = pstmt.executeUpdate();

				if (count == 1) {
					// 정상삭제!
					ObservableList<Book> books = FXCollections.observableArrayList();

					sql = "select bisbn, btitle, bauthor, bprice from book where btitle like ?";

					pstmt = con.prepareStatement(sql);

					pstmt.setString(1, "%" + searchKeyword + "%");

					rs = pstmt.executeQuery();

					while (rs.next()) {
						books.add(new Book(rs.getString("bisbn"), 
						                   rs.getString("btitle"), 
						                   rs.getString("bauthor"),
						                   rs.getInt("bprice")));
					}

					tableView.setItems(books);

					deleteBtn.setDisable(true);
				}

			} catch (SQLException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			} finally {
				try {
					rs.close();
					pstmt.close();
					con.close();
				} catch (SQLException e1) {
					// TODO Auto-generated catch block
					e1.printStackTrace();
				}
			}

		});

		keywordField = new TextField();
		keywordField.setPrefSize(250, 40);
		keywordField.setOnAction(e -> {
			Connection con = null;
			PreparedStatement pstmt = null;
			ResultSet rs = null;
			ObservableList<Book> books = FXCollections.observableArrayList();
			try {
				con = DriverManager.getConnection(jdbcUrl, "root", "test1234");

				String keyword = keywordField.getText();

				String sql = "select bisbn, btitle, bauthor, bprice from book where btitle like ?";

				pstmt = con.prepareStatement(sql);

				pstmt.setString(1, "%" + keyword + "%");

				rs = pstmt.executeQuery();

				while (rs.next()) {
					books.add(new Book(rs.getString("bisbn"), 
					                   rs.getString("btitle"), 
					                   rs.getString("bauthor"),
					                   rs.getInt("bprice")));
				}

				tableView.setItems(books);

				searchKeyword = keywordField.getText();

				keywordField.clear();

			} catch (SQLException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			} finally {
				try {
					rs.close();
					pstmt.close();
					con.close();
				} catch (SQLException e1) {
					// TODO Auto-generated catch block
					e1.printStackTrace();
				}
			}
		});

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
		tableView.getColumns().addAll(isbnColumn, titleColumn, authorColumn, priceColumn);

		tableView.setRowFactory(e -> {
			TableRow<Book> row = new TableRow<>();
			row.setOnMouseClicked(event -> {
				if (event.getClickCount() == 1 && (!row.isEmpty())) {
					Book book = row.getItem();
					selectedISBN = book.getBisbn();
					deleteBtn.setDisable(false);
				}
			});
			return row;
		});

		bottomFlowPane.getChildren().add(keywordField);
		bottomFlowPane.getChildren().add(deleteBtn);

		root.setCenter(tableView);
		root.setBottom(bottomFlowPane);

		Scene scene = new Scene(root);

		primaryStage.setScene(scene);
		primaryStage.setTitle("BookSearch & Delete JavaFX TableView");

		primaryStage.show();
	}

	public static void main(String[] args) {
		launch();
	}

}


~~~


End.

{% include links.html %}
