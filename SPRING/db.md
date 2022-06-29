# DB연동

## JDBC 프로그래밍의 단점을 보완하는 스프링
* 스프링 이전에 데이터베이스에 연동하기 위해서는 다음과 같은 코드를 작성해야 했다.
    ```java
    Connection conn = null;
    Statement stmt = null;
    ResultSet rs = null;
    String url = "jdbc:oracle:thin:@localhost:1521:XE";
    String uid = "system";
    String upw = "1234";
    try {
        Class.forName("oracle.jdbc.driver.OracleDriver");
        conn = DriverManager.getConnection(url , uid , upw);
        stmt = conn.createStatement();
    
    // 핵심 코드

    } catch (Exception e) {
    e.printStackTrace
    } finally {
    try {
        if (rs != null) rs.close();
        if (stmt != null) stmt.close();
        if (conn != null) conn.close();
    } catch (Exception e) {
        e.printStackTrace();
    }
    }//finally 의 끝
    ```

* 위 코드에서 }로 표시 되는 부분은 실제 데이터 처리와는 관계없는 단순 연결을 위한 코드이다.

* 이런 구조적인 반복을 줄이기 위해 많이 사용되는 기법이 템플릿 메서드 패턴과 전략 패턴을 함께 사용하는 것인데 스프링은 이 두가지 기법을 하나로 엮은 jdbcTemplate 클래스를 제공한다.

* 또 얻을 수 있는 장점 트랜젝션 관리 부분

* 일반적으로 JDBC를 통해서 트랜젝션을 관리하려면 conn.setAutoCommit(false) 를 통해서 자동 커밋을 비활성화하고 conn.commit() 메서드나 conn.rollback() 메서드를 통해서 트랜젝션을 통제해야 한다.

* 그러나 스프링에서는 트렌젝션을 적용하고 싶은 메서드에 @Transaction 어노테이션을 붙이면 된다.

## 프로젝트 준비
* 메이븐 레포지트리에서 POM.xml에 다음관련 기능을 추가하고 메이븐 업데이트를 진행한다.  
  - spring-jdbc : JDBCTemplate 등 JDBC 연동에 필요한 기능  
  (spring-context와 같은 버전 사용!)  
  - c3p0 : DB 커넥션 풀 기능을 제공  
  - ojdbc8 : 오라클 DB 에 연결하기 위한 JDBC 드라이버를 제공  
  - log4j : 쿼리문이 실행됐을때 어떤 쿼리문이 실행됐었는지를 기록

  - 추가로 트랜젝션 기능을 활용하려면 spring-tx도 필요하지만 spring-jdbc와 의존 관계이므로 자동으로 다운로드가 된다.

## DataSource설정
* 자바 JDBC는 데이터베이스와 연결을 위해서 DriverManager이외에도 DataSource를 이용해서 연결하는 방법을 정의한다.

* 스프링이 제공하는 DB연동 기능은 DataSource를 활용해서 DBConnection은 구하도록 구현되어 있다.

* DataSource기능을 제공하는 모듈로 dbcp나 c3p0등이 존재하는 데 이번에는 c3p0 모듈을 활용해본다.

* c3p0모듈은 DataSource를 구현한 ComboPooledDataSource클래스를 제공하므로 이 클래스를 스프링 빈으로 등록해서 사용한다.

* c3p0의 주요 프로퍼티
  - initalPoolSize : 초기 커넥션 풀의 크기, 기본값 3
  - maxPoolSize : 커넥션 풀의 최대 크기, 기본값 15
  - minPoolSize : 커넥션 풀의 최소 크기, 기본값 3
  - maxIdleTime : 지정한 시간동안 사용되지 않는 커넥션을 제거한다. 단위는 초, 기본값은 0
  - checkoutTimeout : 풀에서 커넥션을 가져올 때 대기 시간, 1/1000초 기본값 0 (0은 무한히 대기) 지정한 시간동안 커넥션을 가져오지 못하는 경우 SQLException 발생
  - idleConnectionTestPeriod : 풀 속에 있는 커넥션 테스트 주기, 단위는 초,기본값은 0 (0 인 경우 검사하지 않는다)

* DataSource 설정 후 스프링이 제공하는 jdbcTemplate클래스를 이용해서 DB연동을 처리한다.
    ```xml
    <!-- xml설정파일 만들때 tx 체크 -->
    <!-- DB 연결을 위해서 연결객체를 생성해야 함 => 커넥션풀(DataSource) -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource" destroy-method="close">
		<property name="driverClass" value="oracle.jdbc.OracleDriver"/>
		<property name="jdbcUrl" value="jdbc:oracle:thin:@localhost:1521:xe"/>
		<property name="user" value="green"/>
		<property name="password" value="1234"/>
	</bean>
    ```



