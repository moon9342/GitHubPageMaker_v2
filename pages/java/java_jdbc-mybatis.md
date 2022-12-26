---
title: Java JDBC MyBatis
sidebar: java_sidebar
summary: "Java JDBC MyBatis입니다."
permalink: java_jdbc-mybatis.html
folder: java
---

## MyBatis

`MyBatis`를 한마디로 정의하자면 Database 연동을 도와주는 Framework이라고 할 수 있습니다. 
<br><br>

기존의 JDBC는 연동 과정도 복잡하고 SQL문장이 코드내에 섞여 있어서 사용하고 유지보수 하기가 상당히 불편했는데
이런점을 개선하여 편의성(유지보수성)을 높이기 위한 목적으로 만들어졌다고 생각하시면 됩니다. 
<br><br>

간단하게 특징을 살펴보면 다음과 같습니다.
<br>

* SQL 실행결과를 Map객체에 mapping해 줍니다.
* SQL 문장을 소스코드가 아닌 XML로 분리하여 작성합니다.
* DataSource기능과 Transaction처리기능을 제공해줍니다.
<br><br>

사용하기 위해서는 먼저 설치를 해야 하는데 우리는 아직 Spring과 같은 Framework을 사용하는게 아니기 때문에
직접 다운로드하여 설치한 후 기존 예제를 이용하여 사용해보도록 하겠습니다. 
<br><br>

아래의 링크를 이용하여 `MyBatis` Homepage로 이동합니다. 

* [MyBatis Homepage](https://blog.mybatis.org/){: target="_blank" }
<br><br>

Products 링크를 이용하여 `MyBatis 3` GitHub로 이동한 후 `mybatis-3.5.11.zip`을 다운로드 합니다. 아래에 링크를 이용해도 됩니다. 
<br><br>

* [mybatis-3.5.11.zip (Googel Drive)](https://drive.google.com/file/d/1V3wm9k4NIv1JxVks0J_Q9oq5a2GmRuQR/view?usp=share_link){: target="_blank" }
<br><br>

다운로드 한 후 압축을 해제합니다. 
<br><br>

Eclipse에서 간단한 프로젝트를 생성 한 후 `Build Path > Classpath`에 라이브러리를 추가합니다.
`MySQL Connector/J`도 함께 추가합니다.
<br><br>

프로젝트의 root에서 `resources`라는 이름으로 폴더를 하나 생성한 후 프로젝트의 `Build Path > Source` 탭으로 
이동한 후 `Add Folder`를 선택해 `resources` 폴더를 선택합니다.

{% include image.html
file='java-project-resources.png'
%}
<br>

Eclipse 상단 탭의 `Window > Preference > XML > XML Catalog` 메뉴를 선택합니다. 
<br><br>

`XML Catalog`에서 `User Specified Entries`를 선택한 뒤 Add...를 클릭한 후 다음과 같은 내용을 삽입합니다.
각각의 항목을 모두 추가합니다. (2개)
<br><br>

* Location : http://mybatis.org/dtd/mybatis-3-config.dtd
* Key Type : Public ID (Default)
* Key : -//mybatis.org/DTD Config 3.0//EN
<br><br>

* Location : http://mybatis.org/dtd/mybatis-3-mapper.dtd
* Key Type : Public ID (Default)
* Key : -//mybatis.org/DTD Mapper 3.0//EN
<br><br>

이제 `xml` 파일을 만들 차례입니다. 
<br><br>

`resources`폴더 아래에 `config`폴더를 생성한 후 `New > Other > MXL > XML File`을 누른 뒤
파일명은 `config.xml`로 하고 Next에서 `DTD`를 선택하고 `Select XML Catalog Entry`를 선택해 
위에서 설정한 `config` DTD를 선택한 후 `Next > Finish`를 클릭합니다.
<br><br>

파일에 아래의 내용을 작성합니다.

~~~xml


<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration 
        PUBLIC "-//mybatis.org/DTD Config 3.0//EN" 
        "http://mybatis.org/dtd/mybatis-3-config.dtd" >
<configuration>

    <!-- driver property 파일 -->
    <properties resource="./config/driver.properties" />

    <!-- MyBatis 설정 관련 기본 세팅 -->
    <settings>
        <setting name="jdbcTypeForNull" value="NULL" />
        <!-- Data가 Null값일 경우 처리하는 방식 설정 (빈칸이 아니라 NULL로 인식) -->
    </settings>

    <typeAliases>
        <typeAlias alias="Book" type="vo.Book" />
    </typeAliases>

    <!-- DB 연결 정보 선언 부분 -->
    <environments default="development">
        <!-- DB 연결 설정 정보 선언 -->
        <environment id="development">
            <transactionManager type="JDBC">
                <!-- TR을 누가 관리할 것인지 선택 
                     JDBC : 수동 commit, JDBC가 Commit, RollBack을 직접 처리
                     MANAGED : 자동 commit, TR에 대해 직접적인 영향을 행사하지 않음. Container가 TR 생명주기 관리 -->
            </transactionManager>
            <dataSource type="POOLED">
                <!-- 실제 DB접속에 관한 정보들을 넣는 태그. type 속성은 Connection Pool을 사용할건지 안 할건지 설정 
                    type = "UNPOOLED" / "POOLED" / "JDNI" 
                    UNPOOLED : DB 관련 요청이 들어올 때마다 실시간으로 Connection 객체를 open/close함. 
                               미리 커넥션 객체를 만들어놓는 방식이 아니므로 조금 느릴 수 있다 
                    POOLED : Connection 객체를 미리 만들어놓고 관리. 객체를 만드는 데 시간이 걸림.
                             사용자 요청 시마다 DB에 연결하여 명령을 실행하고, 명령이 종료되더라도 연결 종료하지 않고 
                             Pool에 저장하여 요청 시마다 Pool에서 Connection을 다시 꺼내 재사용. 
                             재사용할 Connection 없는 경우 새로운 Connection 생성 -->

                <property name="driver" value="${driver}" />
                <property name="url" value="${url}" />
                <property name="username" value="${username}" />
                <property name="password" value="${password}" />
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="./sqlmap/Book.xml" />
    </mappers>
</configuration>


~~~

`resouces/config/driver.properties` 파일을 생성한 후 다음의 내용을 작성합니다. 
<br><br>

~~~text

driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true
username=root
password=test1234

~~~

`resouces/sqlmap` 폴더를 생성한 후 그 안에 `Book.xml` 파일을 생성합니다. 파일의 내용은 다음과 같습니다.
<br><br>

~~~xml
{% raw %}
        
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org/DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

<mapper namespace="myBook">

    <resultMap id="result" type="Book">
        <result property="bisbn" column="bisbn" />
        <result property="btitle" column="btitle" />
        <result property="bauthor" column="bauthor" />
        <result property="bprice" column="bprice" />
    </resultMap>

    <select id="selectAll" resultMap="result">
        <![CDATA[
        	SELECT bisbn, btitle, bauthor, bprice
        	FROM book
        ]]>
    </select>

    <select id="selectByKeyword" parameterType="String"
            resultMap="result">
        <![CDATA[
	        SELECT bisbn, btitle, bauthor, bprice
	        FROM BOOK 
	        WHERE btitle LIKE CONCAT('%', #{Keyword}, '%')
	    ]]>
    </select>

    <select id="selectByISBN" parameterType="String"
            resultMap="result">
        <![CDATA[
	        SELECT bisbn, btitle, bauthor, bprice 
	        FROM book 
	        WHERE bisbn = #{bisbn}
	    ]]>
    </select>

    <delete id="delete" parameterType="String">
        <![CDATA[
	        DELETE 
	        FROM book 
	        WHERE bisbn = #{bisbn}
	    ]]>
    </delete>

</mapper>


{% endraw %}
~~~

간단하게 설명하자면,
<br><br>

`ResultMap`은 복잡한 결과 매핑을 간편하게 만들어주기 위해 사용하는 태그입니다. 
<br><br>

일반적인 MyBatis Mapping 구문은 다음과 같습니다.

~~~xml

<select id="selectUsers" resultType="map">
  select id, username
  from member
  where id = #{id}
</select>

~~~

`resultType`이 `map`인 것을 보아 `{key, value}` 형태로 
{"id",id값}, {"username",username값}이 매핑될 것을 예상할 수 있습니다.
<br><br>

아래와 같은 User 객체가 있다고 가정할때

~~~java

public class User {
  private int id;
  private String username;
}

~~~

Mybatis의 `resultType`을 `User` 객체로 바꾸어서 사용할 수 있습니다.
<br><br>

~~~xml 

<select id="selectUsers" resultType="User">
  select id, username
  from member
  where id = #{id}
</select>

~~~

위와 같은 경우 `User`객체의 필드명과 selectUsers의 조회 컬럼명이 일치하므로 쉽게 매핑됩니다.
<br><br>

하지만 쿼리문이 아래와 같이 달라진다면 상황이 조금 애매해집니다.

~~~xml

<select id="selectUsers" resultType="User">
  select user_id, user_name
  from member
  where user_id = #{user_id}
</select>

~~~

위의 예처럼 필드명이 바뀐다거나, 매칭되는 타입(number -> String)이 다른 경우에는 매핑할 수 없게 되는데
이 때 사용하게 되는 것이 `ResultMap`입니다.
<br><br>

이와 같은 경우 다음과 같이 `ResultMap`을 정의해서 사용할 수 있습니다.

~~~xml

<resultMap id="testMap" type="User">
  <result column="user_id" property="id" jdbcType="NVARCHAR" javaType="String"/>
  <result column="user_name" property="username" jdbcType="NVARCHAR" javaType="String"/>
</resultMap>

<select id="selectUsers" resultMap="testMap">
  select user_id, user_name
  from member
  where user_id = #{user_id}
</select>

~~~

`user_id`는 `resultMap`을 통해 User 객체의 id에 매칭되게 됩니다. 
<br><br>

{% raw %}<![CDATA[ ]]>{% endraw %}는 `>`,`<`,`>=`,`<=`같은 특수문자를 인식하지 못할수도 
있어서 이를 방지하기 위해 사용됩니다.
<br><br>

다음은 `MyBatisConnectionFactory.java` 파일을 작성하면 됩니다.

~~~java

package mybatis;

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

public class MyBatisConnectionFactory {

	private static SqlSessionFactory sqlSessionFactory;

	static {
		try {
			String resource = "./config/config.xml";
			Reader reader = Resources.getResourceAsReader(resource);

			if (sqlSessionFactory == null) {
				sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
			}
		} catch (FileNotFoundException fileNotFoundException) {
			fileNotFoundException.printStackTrace();
		} catch (IOException iOException) {
			iOException.printStackTrace();
		}
	}

	public static SqlSessionFactory getSqlSessionFactory() {
		return sqlSessionFactory;
	}

}


~~~

다음은 `vo` 객체를 생성하기 위한 class를 작성하면 됩니다.

~~~java

package vo;

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

이제 `DAO`를 작성해 보도록 하죠.

~~~java

package dao;

import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;

import vo.Book;

public class BookDAO {

	private SqlSessionFactory sqlSessionFactory = null;

	public BookDAO(SqlSessionFactory sqlSessionFactory) {
		this.sqlSessionFactory = sqlSessionFactory;
	}

	public List<Book> selectAll() {
		List<Book> list = null;
		SqlSession session = sqlSessionFactory.openSession();

		try {
			list = session.selectList("myBook.selectAll");
		} finally {
			session.close();
		}

		return list;
	}

	public List<Book> selectByKeyword(String keyword) {
		List<Book> list = null;
		SqlSession session = sqlSessionFactory.openSession();
		try {
			list = session.selectList("myBook.selectByKeyword", keyword);
		} finally {
			session.close();
		}

		return list;
	}

	public Book selectById(int id) {
		Book book = null;
		SqlSession session = sqlSessionFactory.openSession();
		try {
			book = session.selectOne("myBook.selectById", id);
		} finally {
			session.close();
		}

		return book;
	}

	public int insert(Book book) {
		int id = -1;
		SqlSession session = sqlSessionFactory.openSession();

		try {
			id = session.insert("myBook.insert", book);
		} finally {
			session.commit();
			session.close();
		}

		return id;
	}

	public void update(Book book) {

		SqlSession session = sqlSessionFactory.openSession();

		try {
			session.update("myBook.update", book);
		} finally {
			session.commit();
			session.close();
		}
	}

	public void delete(int id) {

		SqlSession session = sqlSessionFactory.openSession();

		try {
			session.delete("myBook.delete", id);
		} finally {
			session.commit();
			session.close();
		}
	}

}

~~~

최종적으로 Test를 하기 위한 Main 클래스를 작성하면 다음과 같습니다. 

~~~java

package main;

import java.util.List;

import dao.BookDAO;
import mybatis.MyBatisConnectionFactory;
import vo.Book;

public class Main {
	public static void main(String[] args) {

		BookDAO bookDAO = 
				new BookDAO(MyBatisConnectionFactory.getSqlSessionFactory());

		List<Book> bookList = bookDAO.selectByKeyword("자바");
		for (Book book : bookList) {
			System.out.println("제목: " + book.getBtitle());
			System.out.println("저자: " + book.getBauthor());
		}

	}

}

~~~

구현된 프로젝트는 다음의 링크에서 다운로드 하실 수 있습니다. 

* [MyBatis Example Project](https://drive.google.com/file/d/1C5pHnh3sWcFjsyVeL8PM2wforTSLZqXO/view?usp=share_link){: target="_blank" }
<br><br>

---

## BookSearch(MyBatis)

자 이제 MyBatis에 대한 기본적인 사용법을 알았으니 
기존에 작성한 BookSearch 프로그램을 MyBatis를 이용한 코드로 변환해 보도록 하죠. 


End.

{% include links.html %}
