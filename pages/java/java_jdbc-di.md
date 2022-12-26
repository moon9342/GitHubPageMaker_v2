---
title: Java JDBC Dependency Injection
sidebar: java_sidebar
summary: "Dependency Injection에 대한 설명입니다."
permalink: java_jdbc-di.html
folder: java
---

## Dependency Injection

이전 Java에서 `interface`의 특징만 살펴보고 왜 이런 interface가 존재하고 이걸 사용했을 때의 이점에
대해서는 자세하게 알아보지 않고 넘어갔었습니다. 
<br><br>

`DAO`에 대해서 이제 알았을테니 이 `DAO` 예제를 이용해서 interface를 사용하는 장점에 대해 비로소 알아볼 수 있을 듯
합니다. 상당히 유명한 예제인데 천천히 단계별로 알아보도록 하죠.

### 잘못 설계된 DAO - step 1

잘못 설계된 DAO를 작성해 보고 문제가 무엇인지를 생각해 보도록 하죠.

* User.java

~~~java

package lecture.jdbc.di.step1;

public class User {

	private String id;
	private String name;
	private String password;
	
	public User() {
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}
		
}


~~~

* UserDAO.java

~~~java

package lecture.jdbc.di.step1;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class UserDAO {

	public void insert(User user) throws ClassNotFoundException, SQLException {
		
		Class.forName("com.mysql.cj.jdbc.Driver");
		System.out.println("MySQL Driver Loading 성공!");
		String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
		Connection con = DriverManager.getConnection(jdbcUrl,"root","test1234");
		System.out.println("데이터베이스 연결 성공");
		
		String sql = "INSERT INTO users VALUES(?,?,?)";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, user.getId());
		pstmt.setString(2, user.getName());
		pstmt.setString(3, user.getPassword());
		
		pstmt.executeUpdate();
		
		pstmt.close();
		con.close();
	}
	
	public User select(String id) throws ClassNotFoundException, SQLException {
		
		Class.forName("com.mysql.cj.jdbc.Driver");
		System.out.println("MySQL Driver Loading 성공!");
		String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
		Connection con = DriverManager.getConnection(jdbcUrl,"root","test1234");
		System.out.println("데이터베이스 연결 성공");
		
		String sql = "SELECT * FROM users WHERE id = ?";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, id);
		
		ResultSet rs = pstmt.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));

		rs.close();
		pstmt.close();
		con.close();
		
		return user;
	}
	
}


~~~

* Main.java

~~~java

package lecture.jdbc.di.step1;

public class Main {

/*
 * 테이블 생성
	
CREATE TABLE USERS (
 id        VARCHAR(10) NOT NULL PRIMARY KEY,
 name      VARCHAR(20) NOT NULL,
 password  VARCHAR(20) NOT NULL
);
 
*/

	public static void main(String[] args) throws Exception {

		UserDAO dao = new UserDAO();

		User user = new User();
		user.setId("HGD");
		user.setName("홍길동");
		user.setPassword("test1234");

		dao.insert(user);

		System.out.println("새로운 사용자 등록 성공!");

		User user2 = dao.select("HGD");
		
		if(user2 != null) {
			System.out.println(user2.getName() + ", " + user2.getPassword());
			System.out.println("조회 성공!");			
		} else {
			System.out.println("조회 실패");
		}
	}
}


~~~

상당히 어려운 주제인데 개발자가 class를 설계할 때 가장 주의를 기울어야 하는 점은 바로 유지보수성, 
즉 변경에 얼마나 능동적으로 대처할 수 있는가입니다.
<br><br>

즉, 프로그램에 변경이 일어날 때 필요한 작업을 최소화 하고 그 변경이 다른 곳에 문제를 일으키지 말아야 한다는 것이죠.
<br><br>

#### Separation of Concerns

프로그래밍의 기초 개념 중 `Separation of Concerns`(관심사의 분리)라는 개념이 있습니다.
<br><br>

주제, 성격이 같은 것끼리 모아두고 관심사항이 다른 것들은 가능한 따로 떨어뜨려 
서로 영향을 주지 않도록 해야 한다는 것입니다.
<br><br>

하나의 관심사가 방만하게 중복되어 있고, 여기저기 흩어져 있어서 다른 관심사와 중복되게 되면 결국 
변경이 일어날 때 많은 문제를 일으키게 된다는 의미로 해석하면 됩니다. 
<br><br>

위의 코드에서 가장 먼저 해야 할 일은 Database `connection`을 가져오는 관심사에 대한 중복 코드를 분리하는
것입니다. 
<br><br>

중복된 DB 연결 코드를 `getConnection()`이라는 이름의 독립적인 method로 만들어서 코드를 분리해 보도록 하죠.
이와 같은 작업을 `Refactoring`이라고 하는데 기존의 code를 외부의 동작방식에는 변화 없이 내부 구조를 변경해서 
재구성하는 작업 기술을 의미합니다. 
<br><br>

getConnection()이라는 method를 이용하여 중복된 code를 뽑아내는 것과 같은 기법을 `refactoring`에서는 
`extract method`(메소드 추출)기법이라고 합니다. 
<br><br>

코드를 수정하면 `step 2`와 같은 코드형태가 될 듯 합니다. 

### 잘못 설계된 DAO - step 2

* UserDAO.java

~~~java 

package lecture.jdbc.di.step2;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;


public class UserDAO {

	private Connection getConnection() throws ClassNotFoundException, SQLException {

		Class.forName("com.mysql.cj.jdbc.Driver");
		System.out.println("MySQL Driver Loading 성공!");
		String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
		Connection con = DriverManager.getConnection(jdbcUrl, "root", "test1234");
		System.out.println("데이터베이스 연결 성공");

		return con;
	}

	public void insert(User user) throws ClassNotFoundException, SQLException {

		Connection con = getConnection();

		String sql = "INSERT INTO users VALUES(?,?,?)";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, user.getId());
		pstmt.setString(2, user.getName());
		pstmt.setString(3, user.getPassword());

		pstmt.executeUpdate();

		pstmt.close();
		con.close();
	}

	public User select(String id) throws ClassNotFoundException, SQLException {

		Connection con = getConnection();

		String sql = "SELECT * FROM users WHERE id = ?";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, id);

		ResultSet rs = pstmt.executeQuery();
        rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
				
		rs.close();
		pstmt.close();
		con.close();

		return user;
	}

}

~~~

기본적인 `Refactoring`과정을 통해 변화에 좀 더 유연하게 대처할 수 있는 유지보수성이 있는 DAO로 변경했습니다. 
<br><br>

이번에는 좀 더 나아가서 유지보수성 뿐만 아니라 재활용이 가능한 DAO로 수정해 보도록 하죠. 
<br><br>

이런 DAO를 만들기 위해 우리의 시나리오를 약간 바꾸도록 하겠습니다. 우리가 만든 DAO를 `naver`과 `kakao`에 판매하는 
경우를 가정합니다. 문제는 이 두 회사가 각기 다른 DBMS를 사용하고 있고 DB Connection을 가져오는 방식 역시 
독자적인 방법(예를 들면, Connection Pool)을 적용하고 싶어한다는 것입니다.
<br><br>

가장 쉬운 방법은 소스코드를 제공하고 변경이 필요한 부분을 수정해서 사용하라고 하는 것입니다. 하지만 우리의 독자 기술이 들어
있는 어마어마한 DAO이기 때문에 source code를 공개하고 싶지 않습니다. 대부분 그러하죠.
<br><br>

즉, 고객에게는 미리 compile된 DAO의 class 파일만을 제공하고 고객 스스로 원하는 방식의 DB connection 생성 방식을
적용해 가면서 우리의 DAO를 사용하게 하려면 어떻게 해야 하는 것일까?
<br><br>

이 문제의 1차적인 해답은 바로 `Inheritance`(상속)를 이용하는 것입니다. 그럼 우리의 UserDao class code를 어떻게 
수정해야 할까요? 
<br><br>

`getConnection()`은 `abstract method`로 작성하고 만들어서 각각의 고객이 자신이 원하는 형태로
overriding해서 사용할 수 있도록 제공하면 될 듯 합니다. 
<br><br>

그림으로 표현하면 다음과 같습니다. 

{% include image.html
file='java-dao-inheritance.png'
%}
<br>

코드를 수정하면 step 3와 같은 코드형태가 될 듯 합니다.

### 잘못 설계된 DAO - step 3

* UserDAO.java

~~~java 

package lecture.jdbc.di.step3;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public abstract class UserDAO {

	protected abstract Connection getConnection() throws ClassNotFoundException, SQLException;
	
	public void insert(User user) throws ClassNotFoundException, SQLException {
			
		Connection con = getConnection();
		
		String sql = "INSERT INTO users VALUES(?,?,?)";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, user.getId());
		pstmt.setString(2, user.getName());
		pstmt.setString(3, user.getPassword());
		
		pstmt.executeUpdate();
		
		pstmt.close();
		con.close();
	}
	
	public User select(String id) throws ClassNotFoundException, SQLException {
		
		Connection con = getConnection();
		
		String sql = "SELECT * FROM users WHERE id = ?";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, id);
		
		ResultSet rs = pstmt.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		pstmt.close();
		con.close();
		
		return user;
	}
	
}

~~~


* KUserDAO.java

~~~java

package lecture.jdbc.di.step3;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class KUserDAO extends UserDAO {

	@Override
	protected Connection getConnection() throws ClassNotFoundException, SQLException {
		// 자신의 고유한 Database Connection 획득방법으로 변경
		Class.forName("com.mysql.cj.jdbc.Driver");
		System.out.println("MySQL Driver Loading 성공!");
		String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
		Connection con = DriverManager.getConnection(jdbcUrl,"root","test1234");
		System.out.println("데이터베이스 연결 성공");
		return con;
	}
	
}

~~~

* Main.java

~~~java 

package lecture.jdbc.di.step3;

public class Main {

	public static void main(String[] args) throws Exception {
		
		UserDAO dao = new KUserDAO();
		
		User user = new User();
		user.setId("HGD");
		user.setName("홍길동");
		user.setPassword("test1234");
		
		dao.insert(user);
		
		System.out.println("새로운 사용자 등록 성공!");
		
		User user2 = dao.select("HGD");
		System.out.println(user2.getName() + ", " + user2.getPassword());
		
		System.out.println("조회 성공!");
	}
}

~~~

DAO의 핵심기능인 데이터를 등록하고 데이터를 검색하는 기능(`insert`, `select`)은 `UserDAO`에 구현되었고,
DBMS 연결방법은 `NUserDAO`와 `KUserDAO`에 아예 class 레벨에서 분리하여 구분하고 있습니다.
<br><br>

이제 우리의 `UserDAO`는 코드의 수정없이 DBMS의 연결을 새롭게 정의한 class를 이용해 자유롭게 사용할 수 있게 되었습니다. 
우리의 `UserDao`는 단순히 변경이 용이하다는 측면을 넘어 `손쉽게 확장된다` 라고도 말할 수 있게 된거죠.
<br><br>

이렇게 super class에 기본적인 로직의 흐름을 만들고 그 기능의 일부를 `abstract method`나 overriding이 가능한 
`protected method`로 만든 후 subclass에서 이런 method를 필요에 맞게 구현해서 사용하는 방법을 디자인 패턴에서 
`Template Method Pattern`이라고 합니다.
<br><br>

즉, `Template Method Pattern`은 상속을 통해 super class를 확장할 때 사용하는 가장 대표적인 방법으로
변하지 않는 기능은 super class에 만들어 두고 자주 변경되어야 할 기능은 subclass에서 만들도록 하는 방법입니다.
<br><br>

부연설명을 하자면, `super class`에서는 abstract method 같은 것을 이용하여 코드의 기본 알고리즘을 담고 있는 
`temnplate method`를 만듭니다. super class에서 default 기능을 정해두거나 비워두었다가 `sub class`에서 
선택적으로 overriding 할 수 있는 method를 `hook method`라고 하는데 sub class에서는 추상 메소드를 
`overriding`하거나 hook method를 overriding하여 기능의 일부를 확장하는 개념입니다.

{% include image.html
file='java-dao-factory-method-pattern.png'
%}
<br>

조금 다른 관점에서 바라보자면 `UserDAO`의 `getConnection()` 메소드는 `Connection` 타입의 객체를 생성한다는 기능을
정의해 놓은 추상 메소드입니다. 그리고 `UserDAO`의 sub class인 `NUserDAO`와 `KUserDAO`의 `getConnection()` 메소드는 
어떤 Connection객체를 어떻게 생성할지를 구체적으로 결정하는 메소드입니다.
<br><br>

이렇게 하위 클래스에서 구체적인 객체의 생성 방법을 결정하는 디자인 패턴을 `Factory Method Pattern`이라고 부릅니다.
<br><br>

즉, `UserDAO`는 `Connection`객체를 생성할 수 있다는 interface만 제공하고 있고 어떻게 만들어지는지에 대한 관심이 없습니다.
실제로 `Connection`객체가 만들어지는 방식은 `KUserDAO`와 `NUserDAO`의 관심사항입니다. 이런 디자인 패턴은 중요한 설계 
전략이기도 하지만 커뮤니케이션 수단으로 사용됩니다. 
<br><br>

예를 들면, `UserDAO`에 `Factory Method Pattern`을 적용해서 `getConnection()`을 분리해서 구현합니다. !!! 라는 식으로
서로 의사소통한다는 말이죠. 
<br><br>

언뜻보면 좋은것 같지만 사실 위의 방법은 상속을 사용했다는 단점(?)이 있다. 
상속은 간단하고 편리하지만 사실 많은 한계점을 가지고 있습니다. 
<br><br>

잘 알다시피 Java는 단일 상속만 지원합니다. 이미 `KUserDAO`나 `NUserDAO`가 다른 목적을 위해 다른 class로 부터
상속을 이미 받고 있다면 어떻게 해야 할까요? 난감한 상황에 봉착하게 됩니다. 
<br><br>

또한 상속을 통한 상위 클래스와 하위 클래스의 관계는 생각보다 밀접합니다. 관심사항을 다른 클래스로 분리하고
`Factory method pattern`같은 것을 이용하여 필요에 따라 다양한 변신이 가능하도록 확장성을 부여했지만 여전히
상속관계라는 끈끈한 관계를 유지하고 있습니다.
<br><br>

이제 이 문제를 해결할 수 있는 방법을 생각해봐야 합니다. 
<br><br>

이번에는 이렇게 한번 해보죠. 
<br><br>

처음에는 독립적인 method를 만들어서 분리했고 다음에는 상속을 이용해 상위 클래스, 하위 클래스로 관심사를 분리했는데
이번에는 상속관계도 아닌 완전히 독립적인 class로 만들어서 사용해 보겠습니다. 
<br><br>

즉, DB connection과 관련된 부분을 sub class가 아닌 상속관계가 없는 별도의 class를 만들어서 사용해 보자는 거죠.
<br><br>

그림으로 표현하면 다음과 같습니다.
<br>

{% include image.html
file='java-dao-class-seperated.png'
%}
<br>

위의 그림처럼 `SimpleMakeConnection` 이라는 새로운 class를 만들고 DB 생성 기능을 그 안에 넣습니다.
그리고 `UserDAO`는 new keyword를 이용해 instance를 만들고 이를 select(), insert()에서 사용하면 됩니다.
<br><br>

코드를 수정하면 step 4와 같은 코드형태가 될 듯 합니다.

### 잘못 설계된 DAO - step 4

* SimpleConnectionMaker.java

~~~java 

package lecture.jdbc.di.step4;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class SimpleConnectionMaker {
	
	public Connection makeNewConnection() throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.cj.jdbc.Driver");
		System.out.println("MySQL Driver Loading 성공!");
		String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
		Connection con = DriverManager.getConnection(jdbcUrl,"root","test1234");
		System.out.println("데이터베이스 연결 성공");
		return con;		
	}

}

~~~

* UserDAO.java

~~~java 

package lecture.jdbc.di.step4;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class UserDAO {

	private SimpleConnectionMaker simpleConnectionMaker;
	
	public UserDAO() {
		simpleConnectionMaker = new SimpleConnectionMaker();
	}
	
	public void insert(User user) throws ClassNotFoundException, SQLException {
			
		Connection con = simpleConnectionMaker.makeNewConnection();
		
		String sql = "INSERT INTO users VALUES(?,?,?)";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, user.getId());
		pstmt.setString(2, user.getName());
		pstmt.setString(3, user.getPassword());
		
		pstmt.executeUpdate();
		
		pstmt.close();
		con.close();
	}
	
	public User select(String id) throws ClassNotFoundException, SQLException {
		
		Connection con = simpleConnectionMaker.makeNewConnection();
		
		String sql = "SELECT * FROM users WHERE id = ?";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, id);
		
		ResultSet rs = pstmt.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		pstmt.close();
		con.close();
		
		return user;
	}
	
}

~~~

* Main.java

~~~java

package lecture.jdbc.di.step4;

public class Main {

	public static void main(String[] args) throws Exception {
		
		UserDAO dao = new UserDAO();
		
		User user = new User();
		user.setId("HGD");
		user.setName("홍길동");
		user.setPassword("test1234");
		
		dao.insert(user);
		
		System.out.println("새로운 사용자 등록 성공!");
		
		User user2 = dao.select("HGD");
		System.out.println(user2.getName() + ", " + user2.getPassword());
		
		System.out.println("조회 성공!");
	}
	
}

~~~

실행은 잘 됩니다. 그런데 이제 naver와 kakao에 class를 제공해서 상속을 통해 DB Connection 기능을 확장해서 사용하려 했던것이
다시 불가능해집니다. 
<br><br>

즉, naver와 kakao가 다른 방식으로 connection을 제공하는 class를 사용하기 위해서는 source code를 직접 수정해야 합니다.
코드에서 확인할 수 있듯이 우리의 UserDao는 SimpleConnectionMaker라는 특정 class에 종속됩니다. 
<br><br>

이번에 작성한 code의 근본적인 문제는 UserDao가 바뀔 수 있는 정보, 다시 말해서 DB connection을 가져오는 class와 
`tightly coupled`되어 있다는 것입니다. 
<br><br>

즉, 어떤 class가 사용되고 그 class에서 connection을 가져오는 method의 정확한 이름에 대한 내용까지 일일이 알고 있고,
결국 UserDao는 DB connection을 가져오는 구체적인 방법에 종속되어 버리게 된다. 오히려 상속을 사용할 때 보다 더 좋지 
않은 경우라고 볼 수 있습니다. 
<br><br>

그럼 class를 분리하면서 이전과 같이 문제를 해결할 수 있을까요? 이런 해결책이 과연 존재하는 것일까요?
<br><br>

가장 좋은 해결책은 두 개의 class가 서로 긴밀하게 연결되어 있지 않도록 중간에 추상적인 느슨한 연결고리를 만들어 주는 것입니다.
<br><br>

이러한 작업을 위해 Java가 제공하는 가장 유용한 도구가 바로 `interface`입니다.  interface를 통해 접근하게 되면 실제 구현 
class를 바꿔도 신경 쓸 일이 없습니다. 말로는 이해하기 힘드니 코드를 보면서 이해해보도록 하죠.
<br>

{% include image.html
file='java-dao-interface.png'
%}
<br>

### 잘못 설계된 DAO - step 5

* ConnectionMaker.java

~~~java

package lecture.jdbc.di.step5;

import java.sql.Connection;
import java.sql.SQLException;

public interface ConnectionMaker {

	public Connection makeConnection() throws ClassNotFoundException, SQLException;
	
}

~~~

* KConnectionMaker.java

~~~java

package lecture.jdbc.di.step5;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class KConnectionMaker implements ConnectionMaker {

	@Override
	public Connection makeConnection() throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.cj.jdbc.Driver");
		System.out.println("MySQL Driver Loading 성공!");
		String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
		Connection con = DriverManager.getConnection(jdbcUrl,"root","test1234");
		System.out.println("데이터베이스 연결 성공");
		return con;
	}

}


~~~

* UserDAO.java

~~~java

package lecture.jdbc.di.step5;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class UserDAO {
private ConnectionMaker connectionMaker;
	
	public UserDAO() {
		connectionMaker = new KConnectionMaker();
	}
	
	public void insert(User user) throws ClassNotFoundException, SQLException {
			
		Connection con = connectionMaker.makeConnection();
		
		String sql = "INSERT INTO users VALUES(?,?,?)";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, user.getId());
		pstmt.setString(2, user.getName());
		pstmt.setString(3, user.getPassword());
		
		pstmt.executeUpdate();
		
		pstmt.close();
		con.close();
	}
	
	public User select(String id) throws ClassNotFoundException, SQLException {
		
		Connection con = connectionMaker.makeConnection();
		
		String sql = "SELECT * FROM users WHERE id = ?";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, id);
		
		ResultSet rs = pstmt.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		pstmt.close();
		con.close();
		
		return user;
	}
}

~~~

* Main.java

~~~java

package lecture.jdbc.di.step5;

public class Main {
	public static void main(String[] args) throws Exception {

		UserDAO dao = new UserDAO();

		User user = new User();
		user.setId("HGD");
		user.setName("홍길동");
		user.setPassword("test1234");

		dao.insert(user);

		System.out.println("새로운 사용자 등록 성공!");

		User user2 = dao.select("HGD");
		System.out.println(user2.getName() + ", " + user2.getPassword());

		System.out.println("조회 성공!");
	}
}

~~~

naver와 kakao에게 UserDao class와 ConnectionMaker interface를 함께 전달한 후, kakao의 개발자가 
ConnectionMaker interface를 구현한 class를 만들고, 자신들의 DB connection기술을 이용해 DB connection을 
가져오도록 method를 overriding해 주면 됩니다. 
<br><br>

이렇게 interface를 이용하면 class간의 밀접한 연관성을 많은 부분 제거할 수 있습니다. 
하지만!!  UserDao class의 code를 보면 `KConnectionMaker`라는 class의 이름을 볼 수 있습니다. 
<br><br>

interface를 이용하여 다른 정보는 모두 제거했지만 초기에 한 번 어떤 class의 object를 사용할지를 결정하는
constructor의 code는 제거되지 않고 남아있는 것을 확인할 수 있습니다. 제거하고 싶지만 간단하게 할 수 있는 방법이
현재로는 없습니다. 
<br><br>

그러면 결국, UserDao의 source code를 함께 제공해서 처리해야 하는 것인가요? interface까지 이용했음에도 불구하고 
UserDao와 ConnectionMaker는 완벽히 분리가 되지 못했습니다. 소스코드에 클래스의 이름이 명시되어 있다는 말이죠.
<br><br>

그 이유는 UserDao class안에 분리되지 않은 또 다른 관심사항이 존재하기 때문입니다. 조금 이해하기 힘들지만
`new NConnectionMaker()` 라는 code는 매우 간단하지만 그 자체로 충분히 독립적인 관심사를 가지고 있습니다. 
바로 UserDao가 어떤 ConnectionMaker 구현 class의 object를 이용하게 할지를 결정하는 것이죠.
<br><br>

결국, 이 관심사를 담은 code를 UserDao에서 분리하지 않으면 UserDao는 결코 독립적으로 확장 가능한 class가 될 수
없습니다. 그러면 관심사항인 UserDao와 ConnectionMaker 구현 class의 관계를 결정해 주는 기능을 분리해 보도록 
하겠습니다. 그런데 문제는 그 분리된 code를 어디에 두어야 하는가 입니다. 
<br><br>

결론적으로 이 code는 UserDao를 사용하는 client에 넣은 것이 좋습니다. 좀 더 정확히 표현하자면 class간의 관계를 
설정하는 것이 아니고 `object`와 `object`사이의 관계를 설정하면 된다는 것이죠.
<br><br>

class사이의 관계는 code에 다른 class의 이름이 나타나지만 object 사이의 관계는 그렇지 않습니다. 객체지향 프로그램의 
polymorphism 특성으로 인해 code에서 특정 class를 전혀 알지 못하더라도 해당 class가 구현한 interface를 이용했다면 
그 class의 object를 interface type으로 받아서 사용할 수 있습니다. 
<br><br>

결국 별도의 client가 두 object 사이에 runtime 사용관계(의존관계)를 맺어주면 됩니다. 마지막 구현 내용을 보도록 하죠.
<br>

### 잘못 설계된 DAO - step 6

* ConnectionMaker.java

~~~java

package lecture.jdbc.di.step6;

import java.sql.Connection;
import java.sql.SQLException;

public interface ConnectionMaker {

	public Connection makeConnection() throws ClassNotFoundException, SQLException;
	
}


~~~

* UserDAO.java

~~~java 

package lecture.jdbc.di.step6;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class UserDAO {
	
private ConnectionMaker connectionMaker;
	
	public UserDAO(ConnectionMaker connectionMaker) {
		this.connectionMaker = connectionMaker;
	}
	
	public void insert(User user) throws ClassNotFoundException, SQLException {
			
		Connection con = connectionMaker.makeConnection();
		
		String sql = "INSERT INTO users VALUES(?,?,?)";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, user.getId());
		pstmt.setString(2, user.getName());
		pstmt.setString(3, user.getPassword());
		
		pstmt.executeUpdate();
		
		pstmt.close();
		con.close();
	}
	
	public User select(String id) throws ClassNotFoundException, SQLException {
		
		Connection con = connectionMaker.makeConnection();
		
		String sql = "SELECT * FROM users WHERE id = ?";
		PreparedStatement pstmt = con.prepareStatement(sql);
		pstmt.setString(1, id);
		
		ResultSet rs = pstmt.executeQuery();
		rs.next();
		
		User user = new User();
		user.setId(rs.getString("id"));
		user.setName(rs.getString("name"));
		user.setPassword(rs.getString("password"));
		
		rs.close();
		pstmt.close();
		con.close();
		
		return user;
	}
}


~~~

* KConnectionMaker.java

~~~java

package lecture.jdbc.di.step6;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class KConnectionMaker implements ConnectionMaker {
	
	@Override
	public Connection makeConnection() throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.cj.jdbc.Driver");
		System.out.println("MySQL Driver Loading 성공!");
		String jdbcUrl = "jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true";
		Connection con = DriverManager.getConnection(jdbcUrl,"root","test1234");
		System.out.println("데이터베이스 연결 성공");
		return con;
	}
}


~~~

* Main.java

~~~java

package lecture.jdbc.di.step6;

public class Main {
	
	public static void main(String[] args) throws Exception {

		ConnectionMaker connectionMaker = new KConnectionMaker();

		UserDAO dao = new UserDAO(connectionMaker);

		User user = new User();
		user.setId("HGD");
		user.setName("홍길동");
		user.setPassword("test1234");

		dao.insert(user);

		System.out.println("새로운 사용자 등록 성공!");

		User user2 = dao.select("HGD");
		System.out.println(user2.getName() + ", " + user2.getPassword());

		System.out.println("조회 성공!");
	}
}

~~~

Main class는 UserDao와 ConnectionMaker 구현 class와의 runtime object 의존 관계를 설정하는 책임을 담당하고 있습니다.
결국 client인 Main이 수고해준 덕분에 UserDao의 변경 없이 자유롭게 naver와 kakao는 자신들을 위한 DB connection class를 
만들어서 UserDao가 사용하게 할 수 있게 되었습니다.
<br><br>

이렇게 `interface`를 도입하고 client의 도움을 받는 방법은 `inheritance`를 사용해 비슷한 시도를 했을 경우에 비해서 훨씬
유연한 결과를 보여줍니다. 
<br><br>

결과를 다시 한번 보면, UserDao가 ConnectionMaker interface에 의존하고 있는 형태입니다. 
이렇게 interface에 대해서만 의존관계를 만들어두면 interface 구현 class와의 관계는 느슨해지면서 변화에 영향을 
덜 받는 상태 다시말해 결합도가 낮게 상태로 연결되게 됩니다. 
<br><br>

모델의 관점에서 보자면 UserDao는 NconnectionMaker class에는 의존하지 않습니다. 하지만 모델이나 code에서 
class와 interface를 통해 드러나는 의존관계 말고, `runtime`시에 object 사이에서 만들어지는 의존관계도 있습니다. 
<br><br>

이런 것을 runtime 의존관계 혹은 object 의존관계라고 하는데, 설계 시점의 의존관계가 실체화 된 것이라고 볼 수 있습니다.
이렇게 구체적인 의존 object와 그것을 사용할 주체를 runtime시에 연결해 주는 작업을 우리는
`DI`(의존관계 주입)라고 합니다. 

## BookSearch 수정

위에서 배운 내용을 기반으로 기존 `BookSearchMVC`를 수정해보도록 하죠. Transaction 단위로 connection을 사용해야 
한다는 점에 유의하면 될 듯 합니다. 
<br><br>

완성된 프로젝트는 다음의 링크에서 받을 수 있습니다.

* [BookSearchMVC_DI.zip](https://drive.google.com/file/d/18BBblh2L6kuhvqMMervnJNKXuby8Gk0z/view?usp=share_link){: target="_blank" }
<br><br>

기존 코드에서 변함이 없는 코드는 다음과 같습니다. 
<br>

* VO(Book.java)
* View(BookSearchView.java)
* Controller(BookDeleteByISBNController.java, BookSearchByKeywordController.java)
<br><br>

코드의 변화가 생기는 부분은 service, dao 부분이 될 것입니다. 

### dao.DBCPConnectionPool

~~~java 

package booksearch.dao;

import javax.sql.DataSource;

import org.apache.commons.dbcp2.BasicDataSource;

public class DBCPConnectionPool {

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

	private Connection con;

	public BookDAO() {
		// TODO Auto-generated constructor stub
	}

	public BookDAO(Connection con) {
		super();
		this.con = con;
	}

	public ObservableList<Book> select(String keyword) {
		ObservableList<Book> books = FXCollections.observableArrayList();
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {

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
			} catch (SQLException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			}
		}

		return books;
	}

	public int delete(String selectedISBN) {

		PreparedStatement pstmt = null;
		int result = 0;
		try {

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
			} catch (SQLException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			}
		}

		return result;
	}

}

~~~

### service.BookService

~~~java

package booksearch.service;

import java.sql.Connection;
import java.sql.SQLException;

import booksearch.dao.BookDAO;
import booksearch.dao.DBCPConnectionPool;
import booksearch.vo.Book;
import javafx.collections.ObservableList;

public class BookService {

	public ObservableList<Book> searchBooksByKeyword(String keyword) {

		ObservableList<Book> result = null;
		Connection con = null;
		try {
			con = DBCPConnectionPool.getDataSource().getConnection();
			// BookDAO를 이용한 데이터 추출
			con.setAutoCommit(false);
			BookDAO dao = new BookDAO(con);
			result = dao.select(keyword);
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		// 결과 리턴
		if (result != null) {
			try {
				con.commit();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		} else {
			try {
				con.rollback();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}

		return result;
	}

	public ObservableList<Book> deleteBooksByISBN(String selectedISBN, String searchKeyword) {

		ObservableList<Book> result = null;
		Connection con = null;

		try {
			con = DBCPConnectionPool.getDataSource().getConnection();
			con.setAutoCommit(false);

			BookDAO dao = new BookDAO(con);
			int deleteResult = dao.delete(selectedISBN);
			if (deleteResult == 1) {
				result = dao.select(searchKeyword);
				con.commit();
			} else {
				con.rollback();
			}

		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		return result;
	}
}


~~~

End.

{% include links.html %}
