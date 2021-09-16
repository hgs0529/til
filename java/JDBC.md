# JDBC

Java DataBase Connectivity

자바와 DB를 연결하는 것

JDBCTest 라는 프로젝트를 만들어 데이터 베이스와 연동을 테스트

프로젝트 안에 MainClass(package: main)와 JDBCTest(package : database) 라는 클래스를 생성후

JDBCTest 에 아래와 같이 코드를 작성해보자

```java
public class JDBCTest {

	public JDBCTest() {
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver"); // 오라클 드라이버 로딩
			
			System.out.println("Driver Loading Success");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
}
```

우선 JDBC 에서 db와 연동을 해볼껀데 그러기 위해선 db 드라이버를 class.forName으로 드라이버를 불러와 로딩해주어야하는데 그러러면 프로젝트에 먼저 자바와 DB를 연결하기 위해 ojdc6.jar 이라는 드라이버를 추가해야함.

![image-20210916103153021](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916103153021.png)

![image-20210916103203176](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916103203176.png)

![image-20210916103207463](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916103207463.png)

![image-20210916103424786](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916103424786.png)

![image-20210916103428860](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916103428860.png)

위 사진처럼 추가하면 프로젝트 아래에 referenced Libraries 가 생기고 그안에 드라이버가 추가된다

![image-20210916105421517](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916105421517.png)

그 후에 로딩을 해주면 된다.

그리고 db와 연동할때 필요한 것중 하나가 Connection이라는 클래스를 이용해서 db의 정보를 가져와서 현재 상태를 파악한다.

위의 코드에서 아래처럼 코드를 추가해보자

```java
package database;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class JDBCTest {

	public JDBCTest() {
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver"); // 오라클 드라이버 로딩
			
			System.out.println("Driver Loading Success");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
	public Connection getConnection() { // 커넥션을 가져올 클래스를 생성
		
		Connection conn = null;
        // 먼저 리턴할 커넥션 인스턴스를 생성
		
		try {
			conn = DriverManager.getConnection(Url, user, password);
            // 커넥션에 DriverManager라는 클래스를 사용하여 커넥션을 해줌 
            // 첫번쨰 인자로 데이터베이스url, DB의 username, password 를 입력
            
			System.out.println("DB Connection Success");
		} catch (SQLException e) {
			e.printStackTrace();
		} 
		
		return conn;
	}
	
}
```

connection에 db의 정보를 넣어야하는데 `DriverManager.getConnection(Url, user, password);` 을 사용하면된다.

이떄 db의 url이 필요한데 아래와 같이 url을 구할수 있다.

![image-20210916105442743](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916105442743.png)

오른쪽 위에 database Development 를 클릭하시고 왼쪽위에 database가 연결된 곳에서 오른쪽 클릭후 propertise 클릭

![image-20210916105511606](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916105511606.png)

왼쪽에 Driver Propertise 텝으로 와서 Connection URL 부분을 복사해주면 됩니다.

```java
package database;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class JDBCTest {

	public JDBCTest() {
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver"); // 오라클 드라이버 로딩
			
			System.out.println("Driver Loading Success");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
	public Connection getConnection() { // 커넥션을 가져올 클래스를 생성
		
		Connection conn = null;
        // 먼저 리턴할 커넥션 인스턴스를 생성
		
		try {
			conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:xe", "hr", "hr");
            // 커넥션에 DriverManager라는 클래스를 사용하여 커넥션을 해줌 
            // 첫번쨰 인자로 데이터베이스url, DB의 username, password 를 입력
            
			System.out.println("DB Connection Success"); // 정상적으로 커넥션이 만들어지면 콘솔에출력
		} catch (SQLException e) {
			e.printStackTrace();
		} 
		
		return conn;
	}
	
}
```

가져온 url 과 db의 유저네임과 패스워드를 `conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:xe", "hr", "hr");`

이렇게 넣어주셔서 커넥션을 만들어주시고 메인클래스에서 실행해보겠습니다.

```java
package main;

public class MainClass {

	public static void main(String[] args) {
		 JDBCTest jt = new JDBCTest();
		 jt.getConnection();
	}
}
```

메인클래스에서  위 코드처럼 작성후 실행을 시켜보시면

![image-20210916130147906](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916130147906.png)

콘솔에 아래와 같이 잘 작동되는것을 보실수 있습니다.





이제 crud를 코딩 해보겟습니다

id, name, age 로 된 테이블을 하나 말들고

테이블과 데이터를 주고받아 보겠습니다.

우선 database development 로 가셔서

![image-20210916172906079](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916172906079.png)

왼쪽위에 open scrapbook to edit SQL statements 를 클릭하셔서 SQL쿼리를 작성 해봅시다!.

![image-20210916173227643](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916173227643.png)

위 창이 뜨셨으면 위에 type 과 name과 database를 본인 db에 맞게 선택해주시고 쿼리를 작성하시면 됩니다.

```sql

CREATE TABLE USERDTO(
	ID VARCHAR2(50),
	NAME VARCHAR2(50),
	AGE NUMBER(3),
	JOINDATE DATE
);
```

위에 처럼 작성하고 실행하면 테이블이 만들어집니다. 이클립스에서 쿼리문 실행은 art + c 로 실행하면 됩니다.

실행을 하고나서 저장을 누르시면 아래와 같은 창이 뜨시는데

![image-20210916174728915](C:/Users/user/AppData/Roaming/Typora/typora-user-images/image-20210916174728915.png)

저장할 프로젝트를 찾아서 저장해주면 프로젝트 아래에 저장이 됩니다.



## util class

우선 위에 작성했던 드라이버 로딩과 커넥션은 계속 재사용 해야하므로 따로 static 메소드로 만들겠습니다

```java
// DBConnection

package db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DBConnection {

	public static void initConnection() {
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver"); // 오라클 드라이버 로딩
			
			System.out.println("Driver Loading Success");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
	public static Connection getConnection() {
			
			Connection conn = null;
			
			try {
				conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:xe", "hr", "hr");// 다른컴퓨터를 연결할때는 아이피 주소
				System.out.println("DB Connection Success");
			} catch (SQLException e) {
				e.printStackTrace();
			} 
			
			return conn;
		}
}
```



그리고 만들어진 커넥션과 statement , resultSet을 닫아주기 위해 DBClose 라는 클래스도 만들어 주겠습니다.

여기서 statement 는 sql구문을 실행시켜주는 클래스로 crud 클래스에서 쿼리를 날릴때 필요합니다.

resultSet은 crud중 검색을 할 경우 디비에서 데이터를 받아와야 하는데 리턴된 데이터를 받아주는 클래스가 resultSet 입니다.

쿼리를 날린후에는 이 세가지를 꼭 닫아주어야 하는데 이것을 따로 스태틱 메소드로 만들어 놓았습니다.

```java
// DBClose

package db;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class DBClose {
	
	public static void close(Connection conn, Statement stmt, ResultSet rs) {
		
		try {
			if(conn != null) {
				conn.close();
			}
			if(stmt != null) {
				stmt.close();
			}
			if(rs != null) {
				rs.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		
		
	}
}
```





## Insert

```java
// InsertTest

package database;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class InsertTest {

	public InsertTest() {
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver"); // 오라클 드라이버 로딩
			
			System.out.println("Driver Loading Success");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
	public Connection getConnection() {
		
		Connection conn = null;
		
		try {
			conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:xe", "hr", "hr");// 다른컴퓨터를 연결할때는 아이피 주소
			System.out.println("DB Connection Success");
		} catch (SQLException e) {
			e.printStackTrace();
		} 
		
		return conn;
	}
	public int insert(String id, String name, int age) {
		
		String sql = " INSERT INTO USERDTO(ID, NAME, AGE, JOINDATE) "
				   + " VALUES( '" + id + "', '" + name + "', " + age + ", SYSDATE) ";
		
		Connection conn = getConnection(); // 쿼리를 날릴때 연결된 데이터베이스의 정보가 필요해서 커넥션을 가져옴
		Statement stmt = null;
		
		int count = 0; // 추가할때마다 카운트해서 0이면 추가가 안된것
		
		try {
			stmt = conn.createStatement(); // 커넥션 정보를 가지고 현재상태를 파악
			
			count = stmt.executeUpdate(sql); // sql작정한 것을 넣어 업데이트 실행
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				if(stmt != null) {			
					stmt.close();
				}
				if(conn != null) {
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		
		return count;
	}

}

```



## update

```java
// UpdateTest

package database;

import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;

import db.DBClose;
import db.DBConnection;

public class UpdateTest {
	public int update(String id, String name, int age) {
		
		String sql =  " UPDATE USERDTO "
				   + " SET NAME = '" + name + "', AGE = " + age + " "
				   + " WHERE ID = '" + id + "' ";

		Connection conn = DBConnection.getConnection();
		Statement stmt = null;
		
		int count = 0;
		
		try {
			stmt = conn.createStatement();
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			DBClose.close(conn, stmt, null);
		}
		return count;
	}
}
```



## delete

```JAVA
// DeleteTest

package database;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import db.DBClose;
import db.DBConnection;

public class DeleteTest {
public int Delete(String id) {
		
		String sql =  " DELETE FROM USERDTO WHERE ID = ? ";
		Connection conn = null;
		PreparedStatement psmt = null;
		ResultSet rs = null;
		
		int count = 0;
		
		try {
			conn = DBConnection.getConnection();
			psmt = conn.prepareStatement(sql);
			psmt.setString(1, id);
			count = psmt.executeUpdate();
			
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			DBClose.close(conn, psmt, null);
		}
		return count;
	}
}

```





## select

```java
// selectTest

package database;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.List;

import db.DBClose;
import db.DBConnection;
import dto.UserDto;

public class SelectTest {
	
	// 하나의 데이터만을 취득
	public UserDto search(String id) {
		String sql = " SELECT ID, NAME, AGE, JOINDATE "
				   + " FROM USERDTO "
				   + " WHERE ID = '" + id + "' ";
		
		Connection conn = DBConnection.getConnection();
		Statement stmt = null;
		ResultSet rs = null; // 결과값을 받아주는 클래스
		
		UserDto dto = null;
		
		try {
			stmt = conn.createStatement();
			
			rs = stmt.executeQuery(sql); // select 는 executeQuery 로 실행함 결과값을 리턴해줌
			
			if(rs.next()) { // next()는 DB로부터 넘어온 데이터가 있다면 트루를 반환
				String _id = rs.getString("id");
				String _name = rs.getString("name"); // 뒤에있는 id 나 name 은 컬럼명
				int _age = rs.getInt("age");
				String _joindate = rs.getString("joindate");
				
				dto = new UserDto(_id, _name, _age, _joindate);		
			}
			
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			DBClose.close(conn, stmt, rs);
		}
		
		return dto;
	}
	
	public UserDto search2(String id) {
		
		String sql = " SELECT ID, NAME, AGE, JOINDATE "
				   + " FROM USERDTO "
				   + " WHERE ID = ? ";
		
		Connection conn = DBConnection.getConnection();
		PreparedStatement psmt = null; // statement 보다 나중에 나옴 좀더 편함
		ResultSet rs = null; // 결과값을 받아주는 클래스
		
		UserDto dto = null;
		
		try {
			psmt = conn.prepareStatement(sql);
			psmt.setString(1, id);
			
			rs = psmt.executeQuery();
			
			if(rs.next()) { // next()는 DB로부터 넘어온 데이터가 있다면 트루를 반환
				String _id = rs.getString("id");
				String _name = rs.getString("name"); // 뒤에있는 id 나 name 은 컬럼명
				int _age = rs.getInt("age");
				String _joindate = rs.getString("joindate");
				
				dto = new UserDto(_id, _name, _age, _joindate);		
			}
			
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			DBClose.close(conn, psmt, rs);
		}
		
		return dto;
		
	}
```



statement 를 쓰면 위 코드에서 search 처럼 쿼리문 안에 작은 따음표들을 쿼리 짜듯이 작성해줘야 하지만

PreparedStatement 쓰면 따로 작은 따옴표를 쓰지 않고 변수를 ? 처리후 아래에서 `psmt.setString(1, id);` 이렇게 처리를 해줌

변수가 여러가지라면 전부 ? 처리하고 setString을 ? 갯수에 맞게 늘려주면 됨

이제 메인 클래스에서 crud 클래스들을 하나씩 불러와서 db와 연동해보겠습니다

```java
 
InsertTest it = new InsertTest();
		
		String id = "aaa";
		String name = "황길성";
		int age = 33;
		
		int count = it.insert(id, name, age);
		if(count > 0) {
			System.out.println("정상적으로 추가되었습니다.");
		}
```

위에서 필요한 클래스들을 불러와 위 코드처럼 작상할수 있다.







