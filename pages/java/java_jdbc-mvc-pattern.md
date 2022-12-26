---
title: Java JDBC MVC Pattern
sidebar: java_sidebar
summary: "MVC pattern에 대한 설명입니다."
permalink: java_jdbc-mvc-pattern.html
folder: java
---

## Layered Architecture

`Layered Architecture`는 소프트웨어 개발에서 가장 일반적으로 널리 사용되는 아키텍처입니다. 가장 기본이 되는 구조란 말이죠.
구성되는 계층의 숫자에 따라 N 계층 아키텍처 (N-tier Architecture) 라고도 하는데 일반적으로는 4계층을 많이 사용합니다.  
<br><br>

각 계층은 어플리케이션 내에서의 특정 역할과 관심사-`concern`(화면 표시, 비즈니스 로직 수행, DB 작업 등)별로 구분됩니다. 
이러한 Layered Architecture의 구조는 객체지향의 주요 개념 중 하나인 관심사의 분리(`Separation of Concern`)와 
맞닿아 있습니다. 
<br><br>

당연히 특정 계층의 구성요소는 해당 계층에 관련된 기능만 수행하며 이러한 특징은 높은 유지보수성과 쉬운 테스트를 제공한다는
장점을 제공합니다.
<br>

### 4-tier Architecture

가장 많이 언급되고 사용되는 architecture입니다. 그림으로 표현하면 다음 그림과 같습니다. 
<br>

{% include image.html
file='java-jdbc-layered-architecture.png'
%}
<br>

각 layer에 대해 간단히 알아보도록 하죠.

### Presentation Layer

사용자가 데이터를 전달하기 위해 화면에 정보를 표시하는 것을 주 관심사로 둡니다.  
`Presentation Layer` 는 비즈니스 로직이 어떻게 수행되는지 알 필요가 없다. 
대표적인 구성요소는 `View`와 `Controller`가 있습니다. 

### Business Layer

비즈니스 로직을 수행하는 것을 주 관심사로 돕니다. 
화면에 데이터를 출력하는 방법이나 혹은 데이터를 어디서, 어떻게 가져오는지에 대한 내용은 알고있지 않습니다.
<br><br>

그저 `Persistence Layer`에서 데이터를 가져와 비즈니스 로직을 수행하고 그 결과를 `Presentation Layer`로 전달하면 됩니다. 
대표적인 구성요소는 `Service`와 `Domain Model` 등이 있습니다.

경우에 따라 `Service`와 `Domain Model`을 별개의 계층으로 나누거나, 
아예 Domain Model을 Layered Architecture 와 별개의 것으로 분리하는 경우도 있습니다.

### Persistence Layer

어플리케이션의 영속성을 구현하기 위해, 데이터 출처와 그 데이터를 가져오고 다루는 것을 주 관심사로 둡니다. 
대표적인 구성요소는 `Repository`, `DAO` 등이 있습니다.

### Database Layer

MySQL, MariaDB, PostgreSQL, MongoDB 등 데이터베이스가 위치한 계층을 의미합니다.

### Layers of isolation 

일반적으로 수직적으로 격리된 layer라는 표현을 사용합니다. 
<br><br>

아래 그림처럼 `Layered Architecture`에서 각각의 나뉘어진 계층은 수직적으로 배치되는데 이는 
Layered Architecture의 주요 특징 중 하나입니다. 당연히 이런 구조에서 특정 레이어는 바로 하위 레이어에만 연결되게 됩니다.
<br><br>

만약 `Presentation Layer`에서 그냥 `Database Layer`에 연결해서 정보를 가져오면 어떻게 될까요?
구현은 더 쉬울지 모르지만 추후 SQL에 대한 변경사항이 Presentation Layer에 직접적인 영향을 미치게 됩니다.
즉, 과도한 의존성이 발생하며 이는 어플리케이션의 변경을 매우 어렵게 만들고 유지보수가 힘들어지게 만드는 주요 요인이 됩니다.
<br><br>

Layered Architecture 에서 각 레이어는 격리되어 있습니다.
각 레이어가 다른 레이어와 독립적이므로 특정 레이어는 다른 레이어의 내부 동작을 당연히 알 수 없습니다. 
쉽게 말해 각 계층은 캡슐화되어 있고, 단일 책임을 갖게 됩니다. 객체지향의 중요한 특징이죠.
<br><br>

따라서 특정 레이어는 다른 레이어에 영향을 주지 않고 변경될 수 있고 유지보수를 좀 더 원할히 할 수 있도록 해 줍니다.

{% include image.html
file='java-jdbc-layered-architecture-isolation.png'
%}
<br>

---

## MVC Pattern

모델-뷰-컨트롤러(model–view–controller, `MVC`)는 소프트웨어 공학에서 사용되는 소프트웨어 디자인 패턴입니다.
<br><br>

어플리케이션을 세 개의 영역으로 분할하고 각 구성 요소에게 고유한 역할을 부여하는 개발 방식입니다. 어디서 많이 들어본듯한
내용이죠. 바로 위에서 언급한 layered architecture 내용과 유사합니다. 
<br><br>

MVC 패턴을 도입하면 도메인(비즈니스 로직 & 데이터) 영역과 UI 영역이 분리되므로 서로 영향을 주지 않고 유지보수가 가능합니다. 
MVC 패턴의 구조를 살펴보면서 각 컴포넌트가 무슨 역할을 수행하는지 알아보도록 하겠습니다.

{% include image.html
file='java-web-servlet-mvc-1.png'
%}

### Model

`model`은 크게 `Domain Model`과 `Service Model`로 구분됩니다. 

#### 도메인 모델(Domain Model)

비즈니스 로직에서 처리하는 데이터를 의미합니다. 도메인 모델 역할을 하는 클래스를 
도메인 객체(Domain Object), VO(Value Object), DTO(Data Transfer Object) 라고 하는거죠. 이것 역시
어디에서 많이 본듯한 용어입니다. 
<br><br>

당연히 도메인 객체는 비즈니스 로직을 처리하지 않습니다. 

#### 서비스 모델(Service Model)

도메인 객체를 이용해서 비즈니스 로직을 처리합니다. 즉, 해당 데이터에 대한 액세스를 제공하는 역할을 수행하며
뷰나 컨트롤러에 대해서 어떤 정보도 알지 말아야 합니다.

### View

사용자에게 보여지는 부분, 즉 유저 인터페이스(User interface)를 의미합니다.
<br><br>

MVC 패턴은 여러 개의 뷰(View)가 존재할 수 있으며, Model에게 질의하여 데이터를 전달받습니다. 
<br><br>

View는 받은 데이터를 화면에 표시해주는 역할을 가지고 있습니다. 
모델에게 전달받은 데이터를 별도로 저장하지 않아야 합니다. 이 역할은 다른 component가 담당하기 때문이죠. 
사용자가 화면에 표시된 내용을 변경하게 되면 모델에게 전달하여 모델을 변경하게 됩니다.  

### Controller

Model과 View 사이를 이어주는 브릿지(Bridge) 역할을 수행합니다.
<br><br>

모델이나 뷰는 서로의 존재를 모르고 있습니다. 변경 사항을 외부로 알리고 수신하는 방법만 있습니다. 
컨트롤러(Controller)는 이를 중재하기 위해 Model과 View에 대해 알고 있어야 합니다.
<br><br>

사용자가 어플리케이션을 조작하여 발생하는 변경 이벤트들을 처리하는 역할을 수행합니다.

---

## MVC를 사용하는 이유

결국 '유지보수의 편리성'이라는 하나의 결론으로 수렴합니다.
<br><br>

최초 설계를 꼼꼼하게 진행한 시스템이라도 유지보수가 발생하기 시작하면 각 기능간의 결합도(coupling)가 
높아지는 경우가 발생합니다. 이는 최초 설계 이념을 정했던 사람들의 부재 혹은 비즈니스 요건 변경으로 인해 필연적으로 
발생하게 됩니다. 
<br><br>

결합도가 높아진 시스템은 유지보수 작업 시 다른 비즈니스 로직에 영향을 미치게 되므로 사소한 코드의 변경이 의도치 않은 
버그를 유발할 수 있습니다. 이런 문제점을 해결하기 위해 만들어진 소프트웨어 디자인 패턴입니다. 

---

## MVC의 단점

당연히 MVC 패턴에도 한계가 존재합니다. 
<br><br>

복잡한 대규모 프로그램의 경우 다수의 View와 Model이 컨트롤러를 통해 연결되기 때문에 컨트롤러가 불필요하게 
커지는 현상이 발생합니다. 복잡한 화면을 구성하는 경우에도 동일한 현상이 발생하는데 이를 `Massive-View-Controller` 라고 합니다.
<br><br>

이런 문제를 보완하기 위해 또 다양한 pattern들이 파생되게 됩니다. 대표적인 예로 `MVP(Model-View-Presenter)`, 
`MVVM(Model-View-ViewModel)`, `Redux` 등이 있습니다. 

---

## BookSearch MVC 구현

위에서 설명한 내용을 기반으로 우리의 BookSearch 프로그램을 MVC 구조로 구현해 보도록 하겠습니다. 추가적으로
Database를 처리하는 `DAO` 역시 포함해서 구현해 보도록 하죠. 

완성된 프로젝트는 다음의 링크에서 받을 수 있습니다. 

* [BookSearchMVC.zip](https://drive.google.com/file/d/1ciBULHNmsxMNgwMK-2IjXBIeM-pIpQNJ/view?usp=share_link){: target="_blank" }

### view.BookSearchView

~~~java

package booksearch.view;

import booksearch.controller.BookDeleteByISBNController;
import booksearch.controller.BookSearchByKeywordController;
import booksearch.vo.Book;
import javafx.application.Application;
import javafx.collections.ObservableList;
import javafx.geometry.HPos;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.ButtonBar.ButtonData;
import javafx.scene.control.ButtonType;
import javafx.scene.control.Dialog;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableRow;
import javafx.scene.control.TableView;
import javafx.scene.control.TextField;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.FlowPane;
import javafx.stage.Stage;

public class BookSearchView extends Application {
	Scene scene;
	TextField keywordField;
	Button deleteBtn;
	String selectedISBN;
	String searchKeyword;

	// 사용하는 VO BOOK은 public class이어야 한다. 그렇지 않으면 RuintimeException
	TableView<Book> tableView;

	public BookSearchView() {
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
			BookDeleteByISBNController controller = new BookDeleteByISBNController();

			ObservableList<Book> result = controller.getResult(selectedISBN, searchKeyword);

			Dialog<String> dialog = new Dialog<String>();
			dialog.setTitle("Dialog");
			ButtonType type = new ButtonType("Ok", ButtonData.OK_DONE);

			if (result != null) {
				dialog.setContentText("정상적으로 삭제되었습니다.");
				tableView.setItems(result);
				deleteBtn.setDisable(true);
			} else {
				dialog.setContentText("삭제에 실패했습니다.");
				deleteBtn.setDisable(true);
			}
			dialog.getDialogPane().getButtonTypes().add(type);
			dialog.showAndWait();

		});

		keywordField = new TextField();
		keywordField.setPrefSize(250, 40);
		keywordField.setOnAction(e -> {

			BookSearchByKeywordController controller = new BookSearchByKeywordController();

			ObservableList<Book> books = controller.getResult(keywordField.getText());

			tableView.setItems(books);
			searchKeyword = keywordField.getText();
			keywordField.clear();

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
		primaryStage.setTitle("BookSearch MVC");

		primaryStage.show();
	}

	public static void main(String[] args) {
		launch();
	}

}

~~~

### vo.Book

~~~java

package booksearch.vo;

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

### controller.BookSearchByKeywordController

~~~java

package booksearch.controller;

import booksearch.service.BookService;
import booksearch.vo.Book;
import javafx.collections.ObservableList;

public class BookSearchByKeywordController {
	
public ObservableList<Book> getResult(String keyword) {
		
		BookService service = new BookService();
		ObservableList<Book> result = 
				service.searchBooksByKeyword(keyword);
		
		return result;
	}

}


~~~

### controller.BookDeleteByISBNController

~~~java 

package booksearch.controller;

import booksearch.service.BookService;
import booksearch.vo.Book;
import javafx.collections.ObservableList;

public class BookDeleteByISBNController {

	public BookDeleteByISBNController() {
	}

	public ObservableList<Book> getResult(String selectedISBN, String searchKeyword) {
		
		BookService service = new BookService();
		ObservableList<Book> result = 
				service.deleteBooksByISBN(selectedISBN, searchKeyword);
		
		return result;
	}
}


~~~

### service.BookService

~~~java

package booksearch.service;

import booksearch.dao.BookDAO;
import booksearch.vo.Book;
import javafx.collections.ObservableList;

public class BookService {

	public ObservableList<Book> searchBooksByKeyword(String keyword) {
		
		// BookDAO를 이용한 데이터 추출
		BookDAO dao = new BookDAO();
		ObservableList<Book> result = dao.select(keyword);
		
		// 결과 리턴
		return result;
	}

	public ObservableList<Book> deleteBooksByISBN(String selectedISBN, String searchKeyword) {

		ObservableList<Book> result = null;
		
		BookDAO dao = new BookDAO();
		int deleteResult = dao.delete(selectedISBN);
		
		if(deleteResult == 1) {
			result = dao.select(searchKeyword);
		}
		
		return result;
	}
}


~~~

### dao.BookDAO

~~~java

package booksearch.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.sql.DataSource;

import org.apache.commons.dbcp2.BasicDataSource;

import booksearch.vo.Book;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;

public class BookDAO {

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

	public ObservableList<Book> select(String keyword) {
		ObservableList<Book> books = FXCollections.observableArrayList();
		Connection con = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			DataSource ds = getDataSource();
			con = ds.getConnection();

			String sql = "select bisbn, btitle, bauthor, bprice from book where btitle like ?";

			pstmt = con.prepareStatement(sql);

			pstmt.setString(1, "%" + keyword + "%");

			rs = pstmt.executeQuery();

			while (rs.next()) {
				books.add(new Book(rs.getString("bisbn"), rs.getString("btitle"), rs.getString("bauthor"),
						rs.getInt("bprice")));
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

		return books;
	}

	public int delete(String selectedISBN) {

		Connection con = null;
		PreparedStatement pstmt = null;
		int result = 0;
		try {
			DataSource ds = getDataSource();
			con = ds.getConnection();

			String sql = "delete from book where bisbn = ?";

			pstmt = con.prepareStatement(sql);

			pstmt.setString(1, selectedISBN);

			result = pstmt.executeUpdate();

		} catch (SQLException e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		} finally {
			try {
				pstmt.close();
				con.close();
			} catch (SQLException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			}
		}

		return result;
	}

}


~~~

End.

{% include links.html %}
