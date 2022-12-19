---
title: Java JDBC Connection Pool
sidebar: java_sidebar
summary: "JDBC Connection Pool 활용입니다."
permalink: java_jdbc-connection-pool.html
folder: java
---

## Connection Pool

일반적으로 WAS에서 사용되며(다중 thread 환경) Database와 미리 connection(연결)을 해놓은 객체들을 pool에 저장해두었다가 
클라이언트 요청이 오면 connection을 빌려주고, 처리가 끝나면 다시 connection을 반납받아 pool에 저장하는 방식을
통해 connection을 재활용하는 방법입니다.
<br><br>

Database와 Java program에서 가장 부하가 많이 걸리는 부분이 바로 이 connection을 새로 생성하는 작업인데
이 생성 작업을 대여 및 반납의 개념으로 바꿔서 사용하는 방법이라고 생각하시면 됩니다. 
<br><br>

이런 Connection Pool을 사용하면 다음과 같은 장점이 있습니다. 
<br>

* 속도를 향상시킬 수 있습니다. 
* 자원공유로 인한 resource를 효율적으로 사용할 수 있습니다. 
* Connection의 개수를 제어할 수 있기 때문에 서버의 부하를 조절할 수 있습니다.
<br>

{% include image.html
file='java-jdbc-connection-pool-1.png'
%}

{% include image.html
file='java-jdbc-connection-pool-3.png'
%}

{% include image.html
file='java-jdbc-connection-pool-4.png'
%}

위의 그림처럼 우리가 Connection Pool을 직접 만들어 사용할 수도 있지만 그런 경우는 거의 없습니다. 
이미 잘 만들어져서 제공되는 Connection Pool을 가져다가 사용하는 경우가 대부분이고 상용 혹은 무료로
제공되는 상당히 많은 Connection Pool library가 존재합니다. 
<br><br>

우리는 이 중 가장 흔하게 사용되는 `Apache DBCP`를 사용해 보도록 하겠습니다. 
<br><br>

`DAO`에 관한 내용은 뒤에 `MVC Pattern`에서 다룰것이기 때문에 여기에서는 DBCP를 다루는 방법에 대해서만
간단히 알아보도록 하겠습니다. 

---

## download & 프로젝트 생성

아래에 있는 3개의 링크로 들어가서 `Java 8` 버전의 binary 압축파일을 다운로드 한 후 압축을 풀면 
각각 jar 파일을 하나씩 얻을 수 있습니다. 
<br><br>

* https://commons.apache.org/proper/commons-dbcp/download_dbcp.cgi
* https://commons.apache.org/proper/commons-pool/download_pool.cgi
* https://commons.apache.org/proper/commons-logging/download_logging.cgi
<br><br>

파일은 아래의 링크에서도 얻을 수 있습니다. 
<br>

* [commons-dbcp](https://drive.google.com/file/d/1gctGBu-jKUxW9g1Ev_0w9bpN4QCEt4Ig/view?usp=share_link){: target="_blank" }
* [commons-pool](https://drive.google.com/file/d/1SsXCZOE2moFeYyNiceED9ikl2W58LoFx/view?usp=share_link){: target="_blank" }
* [commons-logging](https://drive.google.com/file/d/17XLIXBWnNQkxp90eN1gVxHkVoR_yiYfU/view?usp=share_link){: target="_blank" }
<br><br>

이제 새로운 project를 하나 생성한 후 3개의 `jar` 파일을 우리 project의 `Java Build Path` 부분에
`Classpath`에 포함시킵니다. 추가적으로 MySQL에 대한 `connector/j`도 포함시켜야 합니다.
<br><br>

---

## 기본코드구현

DBCP 사용법을 알아보기 위해 아래의 간단한 코드를 실행시켜보죠.

~~~java

package lecture.jdbc.pool;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

import javax.sql.DataSource;
import org.apache.commons.dbcp2.BasicDataSource;

public class Main {

	private static BasicDataSource basicDS;

	static {
		try {
			basicDS = new BasicDataSource();
			basicDS.setDriverClassName("com.mysql.cj.jdbc.Driver");
			basicDS.setUrl(
					"jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true");
			basicDS.setUsername("root");
			basicDS.setPassword("test1234");
			// Parameters for connection pooling
			basicDS.setInitialSize(10);
			basicDS.setMaxTotal(10);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	public static DataSource getDataSource() {
		return basicDS;
	}

	public static void main(String[] args) {
		Connection con = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;

		DataSource ds = getDataSource();
		try {
			// getting connection
			con = ds.getConnection();
			String sql = "select bisbn, btitle, bauthor, bprice from book where btitle like ?";

			pstmt = con.prepareStatement(sql);
			pstmt.setString(1, "%" + "파이썬" + "%");

			rs = pstmt.executeQuery();

			while (rs.next()) {
				String tmp = "";
				tmp += rs.getString("bisbn") + "  ";
				tmp += rs.getString("btitle") + "  ";
				tmp += rs.getString("bauthor") + "  ";
				tmp += rs.getString("bprice") + "  ";
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

				if (ds != null) {
					((BasicDataSource) ds).close();
				}
				System.out.println("connection pool close()");
			} catch (Exception e2) {
				// TODO: handle exception
			}
		}
	}
}


~~~

---

## DBCP를 적용한 BookSearch 구현

위의 예제코드를 이용해서 우리의 BookSearch 프로그램을 수정해보도록 하겠습니다. 수정한 후에
어떠한 문제가 남아있는지를 다시 한번 고민해 보도록 하죠.

~~~java

package lecture.jdbc.pool.booksearch;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.sql.DataSource;

import org.apache.commons.dbcp2.BasicDataSource;

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

public class BookSearchJavaFXConnectionPool extends Application {

	Scene scene;
	TextField keywordField;
	Button deleteBtn;
	String selectedISBN;
	String searchKeyword;

	private static BasicDataSource basicDS;

	static {
		try {
			basicDS = new BasicDataSource();
			basicDS.setDriverClassName("com.mysql.cj.jdbc.Driver");
			basicDS.setUrl(
					"jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true");
			basicDS.setUsername("root");
			basicDS.setPassword("test1234");
			// Parameters for connection pooling
			basicDS.setInitialSize(10);
			basicDS.setMaxTotal(10);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	public static DataSource getDataSource() {
		return basicDS;
	}

	// 사용하는 VO BOOK은 public class이어야 한다. 그렇지 않으면 RuintimeException
	TableView<Book> tableView;

	public BookSearchJavaFXConnectionPool() {
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
				DataSource ds = getDataSource();
				con = ds.getConnection();

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
				DataSource ds = getDataSource();
				con = ds.getConnection();

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
