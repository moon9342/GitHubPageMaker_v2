---
title: Java BookSearch MyBatis
sidebar: java_sidebar
summary: "Java BookSearch(MyBatis) 프로그램입니다."
permalink: java_jdbc-booksearch-mybatis.html
folder: java
---

## BookSearch(MyBatis)

지금까지 배운 내용을 토대로 간단한 로그인 및 도서검색을 구현해보도록 하겠습니다. 천천히 한번 해 보도록 하죠.
<br><br>

일단 구현한 화면은 다음과 같습니다.

{% include image.html
file='java-jdbc-fx-mybatis-1.png'
%}
<br>

{% include image.html
file='java-jdbc-fx-mybatis-2.png'
%}
<br>

{% include image.html
file='java-jdbc-fx-mybatis-3.png'
%}
<br>

---

## 테이블 생성

사용할 테이블은 2개입니다. `memberTBL` table과 `book` table입니다.
<br><br>

`book` table은 제공된 script를 이용해 생성하시면 됩니다. memberTBL은 다음과 같은 코드를 이용해
간단히 작성하고 데이터 역시 1개의 row만 삽입합니다.

~~~sql

CREATE TABLE memberTBL (
    member_id        varchar(20) NOT NULL PRIMARY KEY,
    member_pw        varchar(20) NOT NULL,
    member_name      varchar(20) NOT NULL
);

insert into memberTBL values('123','456','홍길동');

~~~

---

## 프로젝트 생성

프로젝트 명은 `Java Final Example`이라고 하죠. 프로젝트 생성 후 필요한 library 및 module을 프로젝트에 
포함시킵니다. (`JavaFX module`, `MySQL Connector/J`, `MyBatis`)
<br><br>

`resources` 폴더가 있어야 하기 때문에 폴더를 하나 생성한 후 `Java Build Path > Soruce` 탭에서 해당 폴더를
찾아 추가합니다.

---

## LoginView 구현

제일 먼저 구현해야 하는 코드는 `LoginView` 입니다. 우리의 첫 화면인 로그인 화면입니다. 화면 구현은 `Scene Builder`를
이용해서 구현하도록 하겠습니다. `example.view` package에 LoginView class를 작성합니다. 

~~~java

package example.view;

import javafx.application.Application;
import javafx.stage.Stage;

public class LoginView extends Application {

	@Override
	public void start(Stage primaryStage) throws Exception {
		
	}
	
	public static void main(String[] args) {
		launch();
	}
}


~~~

JavaFX 창을 실행시키기 위한 기본 코드입니다. 화면 구현을 해야 하니 위의 그림과 같은 화면을 
`Scene Builder`를 이용하여 작성한 후 `XML`로 저장합니다. 저장되는 위치는 
편의상 `example.view` package 위치에 저장하도록 하겠습니다.

~~~xml

<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.*?>
<?import javafx.scene.control.*?>
<?import java.lang.*?>
<?import javafx.scene.layout.*?>


<FlowPane maxHeight="-Infinity" maxWidth="-Infinity" minHeight="-Infinity" minWidth="-Infinity" prefHeight="400.0" prefWidth="600.0" xmlns="http://javafx.com/javafx/8" xmlns:fx="http://javafx.com/fxml/1"
          fx:controller="example.controller.LoginViewController">
   <children>
      <GridPane prefHeight="249.0" prefWidth="607.0">
        <columnConstraints>
          <ColumnConstraints hgrow="SOMETIMES" minWidth="10.0" prefWidth="100.0" />
          <ColumnConstraints hgrow="SOMETIMES" minWidth="10.0" prefWidth="100.0" />
        </columnConstraints>
        <rowConstraints>
          <RowConstraints minHeight="10.0" prefHeight="30.0" vgrow="SOMETIMES" />
          <RowConstraints minHeight="10.0" prefHeight="30.0" vgrow="SOMETIMES" />
        </rowConstraints>
         <children>
            <Label alignment="CENTER" text="ID" GridPane.halignment="CENTER" />
            <Label alignment="CENTER" text="PW" GridPane.halignment="CENTER" GridPane.rowIndex="1" />
            <TextField maxHeight="-Infinity" maxWidth="-Infinity" prefHeight="30.0" prefWidth="150.0" GridPane.columnIndex="1" 
                       fx:id="idField" />
            <TextField maxHeight="-Infinity" maxWidth="-Infinity" minHeight="-Infinity" minWidth="-Infinity" prefHeight="30.0" prefWidth="150.0" GridPane.columnIndex="1" GridPane.rowIndex="1" 
                       fx:id="pwField" />
         </children>
      </GridPane>
      <Button mnemonicParsing="false" prefHeight="107.0" prefWidth="600.0" text="Button" 
              fx:id="loginBtn" />
   </children>
</FlowPane>


~~~

위의 코드에서 주의깊게 봐야하는 곳은 최상위 `FlowPane` 태그의 속성으로 지정된 
`fx:controller="example.controller.LoginViewController"` 부분입니다. 이 `View`의 `Controller` 역할을 하는
클래스를 명시하는 부분입니다. View가 생성되면 자동적으로 `Controller` 객체 역시 생성되게 됩니다.
<br><br>

그리고 두 개의 `TextField`에 `fx:id`를 이용하여 각각 id를 부여했습니다. (idField, pwField) 마찬가지로 버튼에 대한
id 역시 `fx:id="loginBtn"`을 이용해 설정했습니다.
<br><br>

이제 이 XML 파일을 불러들여 `Scene`을 생성하고 Stage를 이용해 화면에 창을 띄우는 코드를 마저 작성해 보겠습니다.

~~~java 

package example.view;

import java.io.IOException;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class LoginView extends Application {

	@Override
	public void start(Stage primaryStage) throws Exception {
		
		Parent root = null;
		FXMLLoader loader = 
				new FXMLLoader(getClass().getResource("LoginView.fxml"));		
		try {
			root = loader.load();
		} catch (IOException e) {
			e.printStackTrace();
		}
		
		Scene scene = new Scene(root);
		primaryStage.setScene(scene);
		primaryStage.setTitle("JavaFX Login");
		primaryStage.show();		
	}
	
	public static void main(String[] args) {
		launch();
	}
}


~~~

JavaFX module 이용하여 실행시키기 위해서 `VM args`설정을 잡아야 합니다. (우리 프로젝트는 모듈로 만들지 않았기 때문입니다.)
또한 `Controller` class를 생성하지 않았기 때문에 Exception이 발생하게 됩니다.
<br><br>

`example.controller` package에 `LoginViewController` class를 생성합니다. 해당 클래스는 다음과 같이 작성해야 합니다.

~~~java

package example.controller;

import java.net.URL;
import java.util.ResourceBundle;

import javafx.fxml.Initializable;

public class LoginViewController implements Initializable {

	@Override
	public void initialize(URL arg0, ResourceBundle arg1) {
		// TODO Auto-generated method stub
		
	}
}


~~~

MVC pattern에서 Controller는 사용자의 이벤트를 처리하고 Service에게 처리해야 할 로직을 지시하며 Service의 수행결과에 
따라 결과 View를 선택하거나 만들어내는 역할을 수행합니다. 즉, View와 Service의 연결고리 역할을 수행하는 것이죠.
<br><br>

여기까지 작성했으면 우리의 첫번째 로그인 화면이 나타나게 됩니다. 

---

## 로그인 처리

로그인 화면에서 ID와 PW를 입력하고 로그인 버튼을 누르면 로그인 처리가 진행되어야 합니다. 만약 성공하면 MainView가 실행되어야
하며 로그인에 실패하면 간단하게 경고창을 실행하면 됩니다.
<br><br>

이벤트 처리는 `Controller`가 담당합니다. XML에서 `fx:id`로 지정된 각 component를 Controller class의 field와 연결시키기
위해 다음과 같이 코드를 작성합니다.

~~~java

package example.controller;

import java.net.URL;
import java.util.ResourceBundle;

import javafx.fxml.FXML;
import javafx.fxml.Initializable;
import javafx.scene.control.Button;
import javafx.scene.control.TextField;

public class LoginViewController implements Initializable {

	@FXML private TextField idField;
	@FXML private TextField pwField;
	@FXML private Button loginBtn;
	
	@Override
	public void initialize(URL arg0, ResourceBundle arg1) {
		// TODO Auto-generated method stub
		
	}
}


~~~

`@FXML` annotation을 이용한다는 것과 `fx:id`로 XML에 설정한 id가 field로 설정되어야 한다는 것에 주의합니다.
<br><br>

event처리는 `initialize` 메소드에 작성합니다. 버튼을 클릭했을 때 ID와 PW를 가져와서 로그인 처리를 해야 합니다.
여기서부터는 MVC Pattern과 MyBatis에 맞게 각각의 class와 파일을 작성해나가야 합니다. 

* LoginViewController.java (Controller)

~~~java

package example.controller;

import java.net.URL;
import java.util.ResourceBundle;

import example.service.MemberService;
import example.view.MainView;
import example.vo.Member;
import javafx.fxml.FXML;
import javafx.fxml.Initializable;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.TextField;
import javafx.scene.control.Alert.AlertType;
import javafx.stage.Stage;

public class LoginViewController implements Initializable {

	@FXML
	private TextField idField;
	@FXML
	private TextField pwField;
	@FXML
	private Button loginBtn;

	@Override
	public void initialize(URL arg0, ResourceBundle arg1) {
		loginBtn.setOnAction(e -> {
			// 로그인 버튼이 클릭되면 로그인 처리 후 로그인이 성공하면 새창을 띄워요!
			
			// VO가 있어야 해요. VO class를 작성해야 해요!
			Member member = new Member();
			member.setMemberId(idField.getText());
			member.setMemberPw(pwField.getText());

            // Service가 있어야 해요. Service class를 작성해야 해요!
			MemberService service = new MemberService();

            // Service에 적절한 메소드가 있어야 해요!
			member = service.login(member);

			if (member != null) {
				// 로그인 성공! 새로운 View를 생성한 후 member 주입
				// controller는 클라이언트에 보여줄 새로운 View를 선택, 생성하는 역할 수행
				new MainView(member);
				Stage stage = (Stage) loginBtn.getScene().getWindow();
				stage.close();

			} else {
				// 로그인 실패! 경고창 실행
				Alert alert = new Alert(AlertType.WARNING);
				alert.setTitle("로그인 실패!");
				alert.setHeaderText("ID와 PW를 확인하세요!");
				alert.setContentText("로그인이 실패했습니다. 다시 시도해주세요!");
				alert.showAndWait();
			}
		});
	}
}


~~~

* Member.java (VO)

~~~java

package example.vo;

public class Member {

	private String memberId;
	private String memberPw;
	private String memberName;
	
	public Member() {
		// TODO Auto-generated constructor stub
	}

	public Member(String memberId, String memberPw, String memberName) {
		super();
		this.memberId = memberId;
		this.memberPw = memberPw;
		this.memberName = memberName;
	}

	public String getMemberId() {
		return memberId;
	}

	public void setMemberId(String memberId) {
		this.memberId = memberId;
	}

	public String getMemberPw() {
		return memberPw;
	}

	public void setMemberPw(String memberPw) {
		this.memberPw = memberPw;
	}

	public String getMemberName() {
		return memberName;
	}

	public void setMemberName(String memberName) {
		this.memberName = memberName;
	}
	
}

~~~

* MemberService.java (Service)

~~~java 

	public Member login(Member member) {

		// 로직처리를 해야 해요!
		// 별다른 로직이 없네!! -> 바로 DB처리를 합시다.
		// 여기에서 SqlSession을 얻어와야 Transaction 처리를 할 수 있어요!
		// 이쯤에서 MyBatis 설정에 들어가야 합니다. 
		
		SqlSession session = 
				MyBatisConnectionFactory.getSqlSessionFactory().openSession();
		
		// DAO가 있어야 해요. DAO class를 작성해야 해요!
		MemberDAO dao = new MemberDAO(session);
		
		try {
		    // 적절한 형태의 method가 있어야 해요!
			member = dao.select(member);			
		} finally {
			session.close();
		}
		
		return member;
	}


~~~

* MyBatisConnectionFactory.java (MyBatis)

~~~java

package example.mybatis;

import java.io.IOException;
import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

public class MyBatisConnectionFactory {

	private static SqlSessionFactory sqlSessionFactory;

	static {

		String resource = "./SqlMapConfig.xml";
		try {
			Reader reader = Resources.getResourceAsReader(resource);

			if (sqlSessionFactory == null) {
				sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
			}
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	public static SqlSessionFactory getSqlSessionFactory() {
		return sqlSessionFactory;
	}

}


~~~

* SqlMapConfig.xml

~~~xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration 
          PUBLIC "-//mybatis.org/DTD Config 3.0//EN" 
          "http://mybatis.org/dtd/mybatis-3-config.dtd" >
<configuration>

    <!-- 
        property는 하나만 설정할꺼예요!
        외부파일을 이용해서 데이터베이스 접속정보를 가져올꺼예요!
        외부파일을 하나 만들꺼구요. => driver.properties 파일을 만들꺼예요!
     -->
	<properties resource="./driver.properties"/>
	
	<!-- 
		MyBatis에 대한 기본 세팅정보가 나와요!
		MyBatis가 동작하는 기본적인 방식에 대한 세팅정보가 있어요!
	 -->
	 <settings>
	 	<setting name="jdbcTypeForNull" value="NULL"/>
	 	<!-- Data가 없을경우 NULL로 처리할 꺼예요! -->
	 </settings>
	 
	 <!-- 별명지정하는게 나와요! 타이핑 줄이기위해서 사용해요! -->
	 <typeAliases>
	 	<typeAlias type="example.vo.Book" alias="Book"/>
		<typeAlias type="example.vo.Member" alias="Member"/>
	 </typeAliases>

	<!-- Database 연결 정보 선언 -->
	<environments default="development">
		<environment id="development">
			<!-- 연결 환경에 대한 세부사항이 나오면 되요 -->
			<transactionManager type="JDBC">
			    <!--  
			    	type="JDBC" 이면..수동으로 transaction을 처리
			    	지금 우리가 하고 있는 방식이예요. commit(), rollback()명령어를
			    	직접 처리하는 방식이예요!
			    	type="MANAGED" 이면.. 자동으로 transaction을 관리
			    	Container가 transaction을 관리해요!
			    -->
			</transactionManager>
			<dataSource type="POOLED">
				<!-- Connection Pool을 사용하지 말지를 결정.
					 일반적으로 POOLED를 명시해서 connection pool을 사용!
				 -->
				 <!-- 실제 데이터베이스 연결정보가 들어가요! -->
				 <property name="driver" value="${driver}"/>
				 <property name="url" value="${url}"/>
				 <property name="username" value="${user}"/>
				 <property name="password" value="${password}"/>
				 
				 <!-- 데이터베이스 connection pool에 대한 설정이 나올 수 있어요! -->
				 
			</dataSource>
		</environment>		
	</environments>
	
	<!-- mapping된 SQL 구문이 있는 XML 파일에 대한 설정! -->
	<mappers>
		<mapper resource="./sqlmap/Member.xml"/>
		<mapper resource="./sqlmap/Book.xml"/>
	</mappers>


</configuration>


~~~

* driver.properties

~~~text 

driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true
user=root
password=test1234

~~~

* sqlmap/Member.xml

~~~xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org/DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="example.myMember">

    <resultMap id="member" type="Member">
    	<result column="member_id" property="memberId" />
    	<result column="member_pw" property="memberPw" />
    	<result column="member_name" property="memberName" />
    </resultMap>
    
	<select id="selectMember" parameterType="Member"
	        resultMap="member">
		SELECT member_id, 
		       member_pw, 
		       member_name
		FROM memberTBL
		WHERE member_id = #{memberId} AND member_pw = #{memberPw}      
	</select>
	 
</mapper>


~~~

* sqlmap/Book.xml

~~~xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper 
          PUBLIC "-//mybatis.org/DTD Mapper 3.0//EN" 
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
          
<mapper namespace="example.myBook">

	<select id="selectBookByKeyword" parameterType="String"
	        resultType="Book">		
		<![CDATA[
			SELECT bisbn, btitle, bauthor, bprice
	        FROM book 
	        WHERE btitle LIKE CONCAT('%', #{keyword}, '%')	
		]]>
	</select>

</mapper>


~~~

* MemberDAO (DAO)

~~~java 

package example.dao;

import org.apache.ibatis.session.SqlSession;

import example.vo.Member;

public class MemberDAO {

	private SqlSession session;

	public MemberDAO() {
		// TODO Auto-generated constructor stub
	}

	public MemberDAO(SqlSession session) {
		super();
		this.session = session;
	}

	public Member select(Member member) {

		member = session.selectOne("example.myMember.selectMember", member);

		return member;
	}
}

~~~

---

## MainView 생성

로그인에 성공하면 사용자 정보를 가지고 있는 Member VO를 MainView에 넘겨서 새로운 창을 실행시킵니다.
MainView class를 작성해야 하며 새로운 창을 띄울것이기 때문에 `Stage` class를 상속해서 클래스를 작성합니다. 

* MainView.java

~~~java 

package example.view;

import java.io.IOException;

import example.controller.MainViewController;
import example.vo.Member;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class MainView extends Stage {

	public MainView(Member member) {

		Parent root = null;
		FXMLLoader loader = new FXMLLoader(getClass().getResource("MainView.fxml"));
		try {
			root = loader.load();
		} catch (IOException e1) {
			e1.printStackTrace();
		}

		MainViewController controller = loader.getController();
		controller.setMember(member);

		Scene scene = new Scene(root);
		this.setScene(scene);
		this.setTitle("JavaFX Main");
		this.show();
	}
}

~~~

여기에서 주의해야 하는 부분은 

~~~java

		MainViewController controller = loader.getController();
		controller.setMember(member);

~~~

이 부분인데 controller 객체에게 인자로 넘어온 member VO를 전달해 주기 위해서
저렇게 처리해야 합니다. Controller 객체에게 VO가 전달되어야 Controller에서 사용할 수 있습니다.
<br><br>

사용되는 XML은 다음과 같습니다. 

* MainView.xml

~~~xml

<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.*?>
<?import javafx.scene.control.*?>
<?import java.lang.*?>
<?import javafx.scene.layout.*?>


<BorderPane maxHeight="-Infinity" maxWidth="-Infinity" minHeight="-Infinity" minWidth="-Infinity" prefHeight="400.0" prefWidth="600.0" xmlns="http://javafx.com/javafx/8" xmlns:fx="http://javafx.com/fxml/1"
            fx:controller="example.controller.MainViewController">
   <center>
      <TableView prefHeight="200.0" prefWidth="200.0" BorderPane.alignment="CENTER"
                 fx:id="myTableView" >
        <columns>
          <TableColumn prefWidth="75.0" text="isbn" 
                       fx:id="isbnColumn"/>
          <TableColumn prefWidth="75.0" text="title" 
                       fx:id="titleColumn"/>
            <TableColumn prefWidth="75.0" text="author" 
                       fx:id="authorColumn"/>
            <TableColumn prefWidth="75.0" text="price" 
                       fx:id="priceColumn"/>
        </columns>
      </TableView>
   </center>
   <top>
      <Label text="환영메시지" BorderPane.alignment="CENTER" 
             fx:id="memberStatus"/>
   </top>
   <bottom>
      <FlowPane prefHeight="35.0" prefWidth="600.0" BorderPane.alignment="CENTER">
         <children>
            <TextField prefHeight="32.0" prefWidth="190.0" 
                       fx:id="searchText" />
            <Button mnemonicParsing="false" prefHeight="23.0" prefWidth="72.0" text="검색"
                    fx:id="searchBtn">                    
               <FlowPane.margin>
                  <Insets left="30.0" />
               </FlowPane.margin>
            </Button>
         </children>
      </FlowPane>
   </bottom>
</BorderPane>


~~~

설정되어 있는 `fx:id`에 주의해서 보셔야 합니다. 
<br><br>

당연히 Controller class도 작성해야 합니다. 

* MainViewController.java 

~~~java 

package example.controller;

import java.net.URL;
import java.util.List;
import java.util.ResourceBundle;

import example.service.BookService;
import example.vo.Book;
import example.vo.Member;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.fxml.FXML;
import javafx.fxml.Initializable;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.TextField;
import javafx.scene.control.cell.PropertyValueFactory;

public class MainViewController implements Initializable{

	private Member member;
	
	@FXML private Label memberStatus;
	@FXML private Button searchBtn;
	@FXML private TextField searchText;
	
	@FXML private TableColumn<Book, String> isbnColumn;
	@FXML private TableColumn<Book, String> titleColumn;
	@FXML private TableColumn<Book, String> authorColumn;
	@FXML private TableColumn<Book, String> priceColumn;
	
	@FXML private TableView myTableView;
	
	private void updateTableView() {
		
		isbnColumn.setMinWidth(150);
		isbnColumn.setCellValueFactory(new PropertyValueFactory<>("bisbn"));
		titleColumn.setMinWidth(150);
		titleColumn.setCellValueFactory(new PropertyValueFactory<>("btitle"));
		authorColumn.setMinWidth(150);
		authorColumn.setCellValueFactory(new PropertyValueFactory<>("bauthor"));
		priceColumn.setMinWidth(150);
		priceColumn.setCellValueFactory(new PropertyValueFactory<>("bprice"));
		
	}
	@Override
	public void initialize(URL arg0, ResourceBundle arg1) {		
		searchBtn.setOnAction(e -> {
			
			String keyword = searchText.getText();
			
			BookService service = new BookService();
			
			List<Book> list = service.searchByKeyword(keyword);
			
			if(list != null) {						
				ObservableList<Book> result = FXCollections.observableArrayList();
				for(Book book : list) {
					result.add(book);
				}
				updateTableView();
				myTableView.setItems(result);
			} 
			
		});
	}


	public Member getMember() {
		return member;
	}


	public void setMember(Member member) {
		this.member = member;
		memberStatus.setText(member.getMemberName());
	}
	
}


~~~

* Book.java (VO)

~~~java

package example.vo;

public class Book {
	
	private String bisbn;
	private String btitle;
	private String bauthor;
	private int bprice;
	
	public Book() {
		// TODO Auto-generated constructor stub
	}
	
	public Book(String bisbn, String btitle, String bauthor, int bprice) {
		super();
		this.bisbn = bisbn;
		this.btitle = btitle;
		this.bauthor = bauthor;
		this.bprice = bprice;
	}

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

* BookService.java (Service)

~~~java 

package example.service;

import java.util.List;

import org.apache.ibatis.session.SqlSession;

import example.dao.BookDAO;
import example.mybatis.MyBatisConnectionFactory;
import example.vo.Book;

public class BookService {

	public List<Book> searchByKeyword(String keyword) {

		List<Book> list = null;

		SqlSession session = 
				MyBatisConnectionFactory.getSqlSessionFactory().openSession();

		BookDAO dao = new BookDAO(session);

		try {
			list = dao.select(keyword);
		} finally {
			session.close();
		}

		return list;
	}
}


~~~

* BookDAO.java (DAO)

~~~java

package example.dao;

import java.util.List;

import org.apache.ibatis.session.SqlSession;

import example.vo.Book;

public class BookDAO {
	
	private SqlSession session;

	public BookDAO(SqlSession session) {
		super();
		this.session = session;
	}

	public BookDAO() {
		// TODO Auto-generated constructor stub
	}

	public List<Book> select(String keyword) {

		List<Book> list = null;

		list = session.selectList("example.myBook.selectBookByKeyword", keyword);

		return list;

	}
}


~~~


End.

{% include links.html %}
