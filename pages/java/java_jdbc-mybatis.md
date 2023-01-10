---
title: Java JDBC MyBatis
sidebar: java_sidebar
summary: "Java JDBC MyBatis입니다."
permalink: java_jdbc-mybatis.html
folder: java
---

## MyBatis

`MyBatis`를 한마디로 정의하자면 Database 연동을 도와주는 Framework이라고 할 수 있습니다. Controller에서 
Service를 호출하고 Service가 DAO를 이용하여 Database처리를 할 때 DAO에서 이 MyBatis를 이용하여 Database처리를
하게 됩니다. 
<br><br>

기존의 JDBC는 연동 과정도 복잡하고 SQL문장이 코드내에 섞여 있어서 사용하고 유지보수 하기가 상당히 불편했는데
이런점을 개선하여 편의성(유지보수성)을 높이기 위한 목적으로 만들어졌다고 생각하시면 됩니다. 
<br><br>

간단하게 특징을 살펴보면 다음과 같습니다.
<br>

* JDBC의 모든 기능을 MyBatis가 대부분 제공해줍니다. 따라서 복잡한 JDBC 코드를 걷어낼 수 있으며
깔끔한 소스코드를 유지할 수 있습니다.
* SQL 실행결과를 Map객체에 mapping해 줍니다.
* SQL 문장을 소스코드가 아닌 XML로 분리하여 작성합니다.
따라서 SQL의 변경이 있을 때마다 source code를 수정하거나 컴파일하지 않아도 됩니다. 
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

### XML(SqlMapConfig.xml) 파일 생성

이제 `xml` 파일을 만들 차례입니다. 
<br><br>

`resources`폴더에서 `New > Other > MXL > XML File`을 누른 뒤 파일명은 `SqlMapConfig.xml`로 하고 
Next에서 `DTD`를 선택하고 `Select XML Catalog Entry`를 선택해 위에서 설정한 `config` DTD를 선택한 후 
`Next > Finish`를 클릭합니다.
<br><br>

파일에 아래의 내용을 작성합니다.

~~~xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration 
        PUBLIC "-//mybatis.org/DTD Config 3.0//EN" 
        "http://mybatis.org/dtd/mybatis-3-config.dtd" >
<configuration>

    <!-- driver property 파일 -->
    <!-- 이 설정은 외부 파일에 작성할 수 있습니다. 
         일반적으로 자바 프로퍼티 파일에 설정합니다. 
    -->
    <properties resource="./driver.properties" />

    <!-- MyBatis 설정 관련 기본 세팅은 <settings></settings>에 설정합니다. 
           런타임시 MyBatis의 행위를 조정하기 위한 중요한 값들입니다. 
           상당히 많은 설정관련 정보가 있어요. 
           관련된 설정은 MyBatis Reference를 참조해야 합니다.
           설정하지 않은 정보들은 default 값으로 설정됩니다. -->
    <settings>
        <setting name="jdbcTypeForNull" value="NULL" />
        <!-- Data가 Null값일 경우 처리하는 방식 설정(NULL로 인식) -->
    </settings>

    <!-- 타입 별칭은 자바 타입에 대한 좀더 짧은 이름을 설정하기 위해 사용합니다. 
         오직 XML 설정에서만 사용되며, 타이핑을 줄이기 위해 사용합니다. -->
    <typeAliases>
        <typeAlias alias="Book" type="example.vo.Book" />
    </typeAliases>

    <!-- DB 연결 정보 선언 부분 -->
    <!-- MyBatis는 여러개의 환경으로 설정될 수 있습니다. 
         예를들어, 개발, 테스트, 운영 환경을 위해 별도의 설정을 가지는 경우 입니다. 
         중요한 점은 다중 환경을 설정할 수는 있지만 SqlSessionFactory는 
         각 환경마다 한개만 사용할 수 있다는 것입니다. 
         두개의 데이터베이스에 연결하고 싶다면, SqlSessionFactory 인스턴스를 
         두개 만들어야 합니다. 
         즉, 데이터베이스별로 하나의 SqlSessionFactory가 필요합니다. -->
    <!-- 디폴트 환경(Environment)의 ID(default="development")와 
         각각의 환경을 정의한 환경(Environment) ID(id="development") 설정에 
         주의해야 합니다. 
         또한 TransactionManager 설정(type="JDBC") 
         DataSource 설정(type="POOLED")에 유의해야 합니다. -->
    <environments default="development">
        <!-- DB 연결 설정 정보 선언 -->
        <environment id="development">
            <transactionManager type="JDBC">
                <!-- TR을 누가 관리할 것인지를 결정합니다.
                     JDBC : 수동 commit
                     MANAGED : 자동 commit(Container가 TR 생명주기를 관리합니다) 
                -->
            </transactionManager>
            <dataSource type="POOLED">
                <!-- Connection Pool을 사용할건지 안 할건지 설정합니다. 
                     UNPOOLED : DB 관련 요청이 들어올 때마다 실시간으로 Connection 
                     객체를 open/close합니다. 미리 커넥션 객체를 만들어놓는 방식이 
                     아니므로 조금 느릴 수 있습니다. 
                     POOLED : Connection 객체를 미리 만들어놓고 관리합니다. 
                     맨 처음 객체를 만드는 데 시간이 걸립니다. 사용자 요청 시마다 DB에 
                     연결하여 명령을 실행하고, 명령이 종료되더라도 연결 종료하지 않고 
                     Pool에 저장하여 요청 시마다 Pool에서 Connection을 다시 꺼내 
                     재사용합니다. 
                     재사용할 Connection 없는 경우 새로운 Connection 생성합니다.
                -->
                <property name="driver" value="${db.driver}" />
                <property name="url" value="${db.url}" />
                <property name="username" value="${db.username}" />
                <property name="password" value="${db.password}" />

                <!-- 만약 type="POOLED"로 지정되었다면 더 많은 property들이 
                     설정될 수 있습니다. 
                -->
                <property name="poolMaximumActiveConnections" value="20" />
                <!-- poolMaximumActiveConnections : 주어진 시간에 존재할 수 있는 
                     활성화된 커넥션의 수. 디폴트는 10 
                -->
            </dataSource>
        </environment>
    </environments>

    <!-- 매핑된 SQL구문이 있는 XML 파일에 대한 설정입니다. 
         자바는 자동으로 리소스를 찾기 위한 좋은 방법을 제공하지 않기 때문에 
         어디서 찾으라고 직접 지정합니다. -->
    <mappers>
        <mapper resource="./sqlmap/Book.xml" />
    </mappers>

</configuration>


~~~

위의 코드에서 `${}`로 되어 있는 부분이 보이는데 MyBatis는 `${}`와 `#{}` 두 가지로 `parameter`를 표현합니다.
<br><br>

`${}`
* parameter가 바로 출력됩니다. SQL문에서는 해당 컬럼의 자료형에 맞추어서 parameter의 자료형이 자동으로 변경됩니다.
* 쿼리 주입을 예방할 수 없어서 보안적 측면에서는 좋지 않습니다. 따라서 사용자 입력값을 전달할 때는 사용하지 않습니다.
* 대신 table명이나 컬럼명을 parameter로 전달할 때 주로 사용됩니다.
<br><br>

`#{}`
* parameter가 SQL 문자열 형태로 들어와서 `'문자열'` 형태가 됩니다. 즉, `single quotation`이 자동으로 붙습니다.  
* 쿼리 주입을 예방할 수 있기 때문에 보안적 측면에서 유리합니다. 일반적으로 사용자 입력값을 전달할때는 이 방식을 사용합니다.
<br><br>

### driver.properties 파일 생성

`resouces/driver.properties` 파일을 생성한 후 다음의 내용을 작성합니다. 
<br><br>

~~~text

driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/library?characterEncoding=UTF-8&serverTimezone=UTC&useSSL=false&allowPublicKeyRetrieval=true
username=root
password=test1234

~~~

### Book.xml 파일 생성(Mapper)

`resouces/sqlmap` 폴더를 생성한 후 그 안에 `Book.xml` 파일을 생성합니다. 파일의 내용은 다음과 같습니다.
<br><br>

~~~xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper 
        PUBLIC "-//mybatis.org/DTD Mapper 3.0//EN" 
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="example.myBook">

    <select id="selectByISBNHashMap" parameterType="String"
            resultType="HashMap">
        <![CDATA[
	        SELECT bisbn, btitle, bauthor, bprice 
	        FROM book 
	        WHERE bisbn = #{bisbn}
	    ]]>
    </select>

    <select id="selectAllHashMap" resultType="HashMap">
        <![CDATA[
        	SELECT bisbn, btitle, bauthor, bprice
        	FROM book
        ]]>
    </select>

    <select id="selectByISBNBookVO" parameterType="String"
            resultType="Book">
        <![CDATA[
	        SELECT bisbn, btitle, bauthor, bprice 
	        FROM book 
	        WHERE bisbn = #{bisbn}
	    ]]>
    </select>

    <resultMap id="result_1" type="Book">
        <result property="bisbn" column="my_bisbn" />
        <result property="btitle" column="my_btitle" />
        <result property="bauthor" column="my_bauthor" />
        <result property="bprice" column="my_bprice" />
    </resultMap>

    <select id="selectByISBNResultMap" parameterType="String"
            resultMap="result_1">
        <![CDATA[
	        SELECT bisbn AS my_bisbn, 
	               btitle AS my_btitle, 
	               bauthor AS my_bauthor, 
	               bprice AS my_bprice 
	        FROM book 
	        WHERE bisbn = #{bisbn}
	    ]]>
    </select>

    <update id="update" parameterType="Book">
        <![CDATA[
	        UPDATE book 
	        SET btitle = #{btitle} 
	        WHERE bisbn = #{bisbn}
	    ]]>
    </update>

    <!-- resultMap의 일반적인 사용법 -->
    <resultMap id="result" type="Book">
        <result property="bisbn" column="bisbn" jdbcType="VARCHAR"
                javaType="String" />
        <result property="btitle" column="btitle" jdbcType="VARCHAR"
                javaType="String" />
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

    <!-- 일단 아래의 코드는 동작하지는 않습니다. 
         아래와 같이 ${table}를 사용하는 코드는 SQL Injection에 취약해지기 때문에 
         이렇게 사용하지 않는게 좋습니다. 
         <if> 같은 태그를 이용해 처리하는게 좋은 방식입니다. 
    -->
    <delete id="delete_NG" parameterType="Book">
        <![CDATA[
	        DELETE 
	        FROM ${table} 
	        WHERE bprice >= #{bprice}
	    ]]>
    </delete>

    <!-- 동적 SQL -->
    <delete id="deleteCondition" parameterType="Book">
        DELETE
        FROM book
        WHERE btitle like CONCAT('%', #{btitle}, '%')
        <if test="bprice != 0">
            AND bprice >= #{bprice}
        </if>
    </delete>

</mapper>


~~~

추후에 하나씩 실행시켜 보면서 이해해 보도록 하죠. 일단 먼저 `ResultMap`에 대해서만 
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

아래와 같은 User 객체(VO)가 있다고 가정할때

~~~java

public class User {
  private int id;
  private String username;
}

~~~

MyBatis의 `resultType`을 `User` 객체로 바꾸어서 사용할 수 있습니다.
<br><br>

~~~xml 

<select id="selectUsers" resultType="User">
  select id, username
  from member
  where id = #{id}
</select>

~~~

위와 같은 경우 `User`객체의 필드명과 id가 `selectUsers`의 조회 컬럼명이 일치하므로 쉽게 매핑됩니다.
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
이 때 사용하게 되는 것이 `resultMap`입니다.
<br><br>

이와 같은 경우 다음과 같이 `resultMap`을 정의해서 사용할 수 있습니다.

~~~xml

<resultMap id="testMap" type="User">
  <result column="user_id" 
          property="id" 
          jdbcType="NVARCHAR" 
          javaType="String"/>
  <result column="user_name" 
          property="username" 
          jdbcType="NVARCHAR" 
          javaType="String"/>
</resultMap>

<select id="selectUsers" resultMap="testMap">
  select user_id, user_name
  from member
  where user_id = #{user_id}
</select>

~~~

`user_id` 컬럼은 `resultMap`을 통해 User 객체의 id에 매칭되게 됩니다. 
<br><br>

{% raw %}<![CDATA[ ]]>{% endraw %}는 `>`,`<`,`>=`,`<=`같은 특수문자를 인식하지 못할수도 
있어서 이를 방지하기 위해 사용됩니다. <if>나 <foreach>와 같은 태그를 사용할 경우 사용할 수 없습니다. 
<br><br>

### SqlSessionFactory 생성 java 파일

다음은 `MyBatisConnectionFactory.java` 파일을 작성하면 됩니다.

~~~java

package example.mybatis;

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
			String resource = "./SqlMapConfig.xml";
			Reader reader = Resources.getResourceAsReader(resource);

			if (sqlSessionFactory == null) {
				sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
				// SqlSessionFactory factory = 
				// new sqlSessionFactoryBuilder.build(reader, environment); 
				// environment 파라미터가 없으면, 디폴트 환경이 로드됩니다.
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

### Book VO 생성

다음은 `vo` 객체를 생성하기 위한 class를 작성하면 됩니다.

~~~java

package example.vo;

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

### Book DAO 생성

이제 `DAO`를 작성해 보도록 하죠.

~~~java

package example.dao;

import java.util.HashMap;
import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;

import example.vo.Book;

public class BookDAO {

	private SqlSessionFactory sqlSessionFactory = null;

	public BookDAO(SqlSessionFactory sqlSessionFactory) {
		this.sqlSessionFactory = sqlSessionFactory;
	}

	// 1. bisbn을 이용한 책 1권 select, return type: HashMap
	public HashMap<String, Object> selectByISBNHashMap(String bisbn) {

		HashMap<String, Object> map = null;
		SqlSession session = sqlSessionFactory.openSession();

		try {
			map = session.selectOne("example.myBook.selectByISBNHashMap", bisbn);
		} finally {
			session.close();
		}

		return map;
	}

	// 2. 모든 책 조건없이 select, return type: List<HashMap>
	public List<HashMap<String, Object>> selectAllHashMap() {

		List<HashMap<String, Object>> list = null;
		SqlSession session = sqlSessionFactory.openSession();

		try {
			list = session.selectList("example.myBook.selectAllHashMap");
		} finally {
			session.close();
		}

		return list;
	}

	// 3. bisbn을 이용한 책 1권 select, return type: Book
	public Book selectByISBNBookVO(String bisbn) {

		Book book = null;
		SqlSession session = sqlSessionFactory.openSession();

		try {
			book = session.selectOne("example.myBook.selectByISBNBookVO", bisbn);
		} finally {
			session.close();
		}

		return book;
	}

	// 4. bisbn을 이용한 책 1권 select, return type: Book
	// resultMap을 이용한 경우
	public Book selectByISBNResultMap(String bisbn) {

		Book book = null;
		SqlSession session = sqlSessionFactory.openSession();

		try {
			book = session.selectOne("example.myBook.selectByISBNResultMap", bisbn);
		} finally {
			session.close();
		}

		return book;
	}

	// 5. bisbn을 이용한 책 1권 update, return type: int
	// parameterType으로 BookVO를 이용한 경우
	public int updateByISBN(Book book) {

		int reuslt = 0;
		SqlSession session = sqlSessionFactory.openSession();

		try {
			reuslt = session.update("example.myBook.update", book);
		} finally {
			session.commit();
			session.close();
		}

		return reuslt;
	}

	// 6. 모든 책 조건없이 select, return type: List<Book>
	public List<Book> selectAll() {
		List<Book> list = null;
		SqlSession session = sqlSessionFactory.openSession();

		try {
			list = session.selectList("example.myBook.selectAll");
		} finally {
			session.close();
		}

		return list;
	}

	public List<Book> selectByKeyword(String keyword) {
		List<Book> list = null;
		SqlSession session = sqlSessionFactory.openSession();
		try {
			list = session.selectList("example.myBook.selectByKeyword", keyword);
		} finally {
			session.close();
		}

		return list;
	}

	public Book selectByISBN(int bisbn) {
		Book book = null;
		SqlSession session = sqlSessionFactory.openSession();
		try {
			book = session.selectOne("example.myBook.selectByISBN", bisbn);
		} finally {
			session.close();
		}

		return book;
	}

	public void delete(int bisbn) {

		SqlSession session = sqlSessionFactory.openSession();

		try {
			session.delete("example.myBook.delete", bisbn);
		} finally {
			session.commit();
			session.close();
		}
	}

	public int deleteCondition(Book book) {

		SqlSession session = sqlSessionFactory.openSession();
		int count = 0;

		try {
			count = session.delete("example.myBook.deleteCondition", book);
		} finally {
			// session.commit();
			session.close();
		}

		return count;
	}

}

~~~

### Main class & main method

최종적으로 Test를 하기 위한 Main 클래스를 작성하면 다음과 같습니다. 

~~~java

package example;

import java.util.HashMap;
import java.util.List;

import example.dao.BookDAO;
import example.mybatis.MyBatisConnectionFactory;
import example.vo.Book;

public class Main {
	public static void main(String[] args) {

		BookDAO bookDAO = new BookDAO(MyBatisConnectionFactory.getSqlSessionFactory());

		// 1.
		// 특정 ISBN을 이용하여 책 1권을 가져와서 출력(resultType=HashMap)
		// 89-7914-063-0 : C로 구현한 알고리즘
		HashMap<String, Object> map1 = bookDAO.selectByISBNHashMap("89-7914-063-0");
		for (String keys : map1.keySet()) {
			System.out.println(keys + ", " + map1.get(keys));
		}

		// 2.
		// 모든 책을 가져와서 출력(resultType=HashMap)
		List<HashMap<String, Object>> list1 = bookDAO.selectAllHashMap();
		for (HashMap<String, Object> map : list1) {
			for (String keys : map.keySet()) {
				System.out.println(keys + ", " + map.get(keys));
			}
			System.out.println();
		}

		// 3.
		// 특정 ISBN을 이용하여 책 1권을 가져와서 출력(resultType=Book)
		// 현재 BookVO의 field(property)와 table의 column이 동일한 상태이기
		// 때문에 resultMap을 사용할 필요가 없다.
		// 89-7914-063-0 : C로 구현한 알고리즘
		Book book1 = bookDAO.selectByISBNBookVO("89-7914-063-0");
		System.out.println(book1.getBisbn() + ", " + book1.getBtitle());

		// 4.
		// 특정 ISBN을 이용하여 책 1권을 가져와서 출력(resultMap 활용)
		// 현재 BookVO의 field(property)와 결과집합의 column의 이름이 다른 상태이기
		// 때문에 resultMap을 사용
		// 89-7914-063-0 : C로 구현한 알고리즘
		Book book2 = bookDAO.selectByISBNResultMap("89-7914-063-0");
		System.out.println(book2.getBisbn() + ", " + book2.getBtitle());

		// 5.
		// 특정 ISBN을 이용하여 책 1권을 Update(parameterType을 Book으로 설정)
		Book book3 = new Book("89-7914-063-0", "변경완료!", "홍길동", 3000);
		int count = bookDAO.updateByISBN(book3);
		System.out.println("영향받은 행의 개수 : " + count);

		// 6.
		// 모든 책을 가져와서 출력(resultType=Book)
		List<Book> bookList = bookDAO.selectAll();
		for (Book book : bookList) {
			System.out.println("제목: " + book.getBtitle());
			System.out.println("저자: " + book.getBauthor());
		}

		// 7.
		// 동적 SQL 예제
		// BookVO를 인자로 받아 만약 bprice의 값이 0이 아니면
		// btitle안의 키워드와 같고 bprice의 값보다 가격이 큰 책들을 삭제
		// 만약 bprice의 값이 0이면 btitle안의 키워드를 가지고 있는 책을 삭제

		Book book4 = new Book("", "java", "", 25000);
		int count1 = bookDAO.deleteCondition(book4);
		System.out.println("영향받은 행의 개수 : " + count1);

	}

}

~~~

구현된 프로젝트는 다음의 링크에서 다운로드 하실 수 있습니다. 

* [MyBatis Example Project](https://drive.google.com/file/d/1Cp138SINtAiC8-XRTH5cmgJJv6jC3gUj/view?usp=share_link){: target="_blank" }
<br><br>


End.

{% include links.html %}
