## JDBC를 통해 데이터 조작하기
* 자바는 JDBC를 통해서 데이터베이스에 접근하는 데 JDBC는 자바 프로그램에서 데이터베이스에 일관된 방식으로 접근할 수 있도록 API를 제공하는 클래스의 집합을 말합니다.

* JDBC를 이용해서 데이터베이스에 연결하는 방법은 4단계를 거쳐서 진행된다.
    1. DriverManager (JDBC 드라이버 로드)
    2. Connection (데이터베이스 연결)
    3. Statement (쿼리문 실행)
    4. ResultSet (데이터베이스와 연결 종료)

* JDBC에 연결하기 위해서 사용하는 클래스는 java.sql. 패키지에 포함되어 있다.
  - import java.sql.*

* 데이터베이스 연결에 사용된 인터페이스는 Connection

* 질의, 갱신과 관련된 인터페이스는 Statement

* 결과물을 가져오는 인터페이스는 ResultSet

* Class.forName()메소드를 통해서 JDBC 드라이버를 로딩한다.
    ```java
    Class.forName(“oracle.jdbc.driver.OracleDriver”)
    ```

* 로딩된 드라이버는 DriverManager에 등록된다.

* DriverManager객체에서 getConnection()메소드를 통해서
오라클 데이터베이스에 연결 객체를 생성한다.
    ```java
    Connection conn = DriverManager.getConnection(url, uid, upw);
    ```

* 이때 매개값으로 url과 uid와 upw를 전달하는데 각각의 의미는 다음과 같다.
  - url : 데이터 베이스 주소
  - uid : 데이터베이스에 접속할 아이디
  - upw : 접속할 아이디의 비밀번호

* URL 주소는 다음과 같은 형식에 맞춰서 작성한다.
    ```java
    jdbc:oracle:thin:[hostname][:port]:dbname
    “jdbc:oracle:thin:@localhost:1521:XE”
    ```

* 다음은 커넥션 객체를 얻는 예제이다.
    ```java
    Connection conn = DriverManager.getConnection(
        ‘jdbc:oracle:thin:@localhost:1521:XE’,
        ‘계정명’,
        ‘비밀번호’);
    ```

* 연결이 종료되면 연결을 끊어 주어야 한다.
    ```java
    conn.close();
    ```

* 연결이 완료되면 표준 쿼리문을 수행시키기 위한 객체(Statement)를 생성해야한다.
    ```java
    Statement stmt = conn.createStatement();
    ```

* 모든 작업이 완료되면 해당 객체도 닫아 주어야 한다.
    ```java
    stmt.close();
    ```

* 쿼리를 작성하면 쿼리를 실행할 메소드를 호출해야 한다.
  -  executeQuery() : select문과 같이 결과값이 여러 개의 레코드로 구해지는 경우에 사용
  - executeUpdate() : insert,update,delete문과 같은 내부적으로 테이블의 내용이 변경만 되고 결과값이 없는 경우에 사용

* select쿼리를 실행하면 결과를 받아줄 객체가 필요하게 되는데 이때 사용하는 객체가 ResultSet 객체이다.
    ```java
    String sql =“select * from member”;
    ResultSet rs = stmt.executeQuery(sql);
    ```

* ResultSet객체는 여러 레코드를 저장하는데 한번에 하나의 레코드만 처리할 수 있다.(cursor)

* ResultSet객체에서 각 레코드를 선택하기 위한 메소드를 제공한다.
  - <span style="color:yellowgreen">next() : 현재 행에서 다음 행으로 이동</span>
  - previous() : 현재 행에서 이전 행으로 이동
  - first() : 현재 행에서 첫번째 행으로 이동
  - last() : 현재 행에서 마지막 행으로 이동
  - 위 메소드는 성공적으로 실행시 true 반환, 실패시 false를 반환한다.

* 보통 모든 레코드를 순서대로 검색하기 위해서 while문과 next()메소드를 사용한다.
    ```java
    while(rs.next()){
    }
    ```

* next()메소드는 레코드 단위로 데이터를 가져온다. 하나의 레코드를 선택했으면 이제 레코드에서 컬럼의 데이터를 꺼내 와야 한다.

* 컬럼의 데이터를 꺼내 오기 위해서는 get타입(“컬럼명”)메소드를 사용한다. (컬럼명에 숫자를 적을시 몇번째 컬럼을 뜻하지만 이름을 적어주는것이 좋다.)
    ```java
    rs.getString(컬럼명)
    int admin = rs.getInt(“admin”);
    String name = rs.getString(“name”);
    ```
* 모든 컬럼의 데이터를 꺼내오는 예제(컬럼 이름만 일치하면 순서는 상관없다.)
    ```java
    String name = rs.getString(“name”);
    String userid = rs.getString(“userid”);
    String userpwd = rs.getString(“userpwd”);
    String email = rs.getString(“email”);
    String phone = rs.getString(“phone”);
    int admin = rs.getInt(“admin”);
    ```

* ### <span style="color:yellowgreen">데이터베이스에서 자료를 가져오거나 편집하는 부분은 에러가 자주 발생하기 때문에 예외처리문 안에 작성을 해주자.
    ```java
    try {

    } catch(Exception e) {
        e.printStackTrace();
    } finally{
        try{
            if(rs != null){
                rs.close();}
            if(stmt != null){
                stmt.close();}
            if(conn != null){
                conn.close();}
        }catch(Exception e){
            e.printStackTrace();    // 오류 확인
        }
	}
    ```
### 데이터 저장하는 방법
* 데이터를 저장하기 위해서는 다음과 같은 insert문을 사용해야 한다.
    ```java
    insert into member
    values (‘최길동’,‘choi','1234’,’choi@naver.com','010-1234-5678',0);
    ```

* 위 쿼리를 전송하기 위해서는 Statement객체의 executeUpdate()메소드를 사용하면 된다.
    ```java
    String sql = “insert into member values (‘최길동’,‘choi','1234’,’choi@naver.com','010-1234-5678’,0)”;
    stmt.executeUpdate(sql);
    ```

* 저장해야 하는 값는 고정이 아니라 유동적으로 바뀐다. 그러므로 쿼리문을 다음과 같이 바꿔 주어야 한다.
    ```java
    String name =“정길동”;
    String userid = “jeong”;
    String userpwd = “1234”;
    String email = “jeong@naver.com”;
    String phone = “010-1234-5678”;
    String admin = “0”;
    String sql = “insert into member values (‘”+name+”’,‘”+userid
    +”’,’”+userpwd+”’,’”+email
    +”’,’”+phone+”’,”+admin+”)”;
    stmt.executeUpdate(sql);
    ```

* 기존의 방법은 sql문을 작성하기 대단히 복잡하므로 좀더 개선된 방법으로 preparedStatement인터페이스를 활용한 방법을 사용한다. (쿼리문의 오타 등을 줄이기 위해서)

* preparedStatement객체를 활용하기 위해 preparedStatement()메소드를 호출한다.
    ```java
    PreparedStatement psmt = conn. preparedStatement(sql);
    ```

* preparedStatement에서 사용하는 쿼리 문은 다음과 같이 작성한다.
    ```java
    String sql = “insert into member values(?,?,?,?,?,?)”;
    ```

* 위 쿼리문에서 ?부분은 바인드 변수라고 해서 나중에 값을 넣어서 쿼리를 완성합니다.
    ```java
    // ? 부분을 채우는 방법
    psmt.setString(인덱스,문자열);
    psmt.setInt(인덱스,숫자);
    ```

* 바인드 변수를 모두 채운후 쿼리를 실행하는 메소드를 호출하면 된다.
    ```java
    psmt.executeUpdate();
    psmt.close();
    ```