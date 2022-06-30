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
        <!-- 1. 연결 드라이버 -->
		<property name="driverClass" value="oracle.jdbc.OracleDriver"/>
        <!-- 2. DB서버 주소 -->
		<property name="jdbcUrl" value="jdbc:oracle:thin:@localhost:1521:xe"/>
        <!-- 3. 계정 ID -->
		<property name="user" value="green"/>
        <!-- 4. 계정 PW -->
		<property name="password" value="1234"/>
	</bean>
    ```

* DAO 객체에 jdbcTemplate 생성 코드를 추가한다.
    ```java
    public class MemberDao {
	
        private JdbcTemplate jdbcTemplate;
        
        // 생성자를 통해서 주입
        public MemberDao(JdbcTemplate jdbcTemplate) {
            this.jdbcTemplate = jdbcTemplate;
        }
    ```

* 그후에 DAO 객체를 스프링 설정 파일에 등록한다.
    ```xml
    <!-- jdbcTemplate -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<!-- 연결객체 -->
		<constructor-arg ref="dataSource" />
	</bean>
	
	<!-- DAO -->
	<bean id="dao" class="spring.dao.MemberDao">
		<constructor-arg ref="jdbcTemplate" />
	</bean>
    ```

* JdbcTemplate 클래스는 SELECT 쿼리 실행을 위한 query() 메서드를 제공한다.

* 그중 자주 사용되는 쿼리는 다음과 같다.
    ```java
    List<T> query(String sql, RowMapper<T> rowMapper)
    List<T> query(String sql, Object[] args, RowMapper<T> rowMapper)
    List<T> query(String sql, RowMapper<T> rowMapper, Object… args)
    ```

* 복잡하게 보이지만 간단히 보면, sql 쿼리를 매개로 받아서 실행하고 RowMapper를 이용해서 ResultSet 의 결과를 자바 객체로 변환한다.

* sql 쿼리가 PreparedStatement인 경우 args 파라미터를 통해서 각 ?를 채울 값을 전달한다. 즉 query()는 리스트를 반환하고 sql문과 rowMapper를 매개변수로 사용하며 쿼리문의 ? 개수 만큼 매개변수가 추가된다.

* RowMapper의 mapRow 메서드는 SQL 실행 결과로 구한 ResultSet 으로부터 한 행의 데이터를 읽어와서 자바 객체로 변환해 주는 매퍼 기능을 구현한다.

* 보통 익명구현 클래스를 이용해서 RowMapper객체를 생성해 query() 메서드에 전달해주는 방법이 일반적이다.
    ```java
    // 보통 익명구현객체로 만드는게 아래와 같이 클래스로 만드는 것과 동일
    public class MapperSqlToMember implements RowMapper<Member> {

        @Override
        public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
            Member m = new Member(
                    rs.getString("email"),
                    rs.getString("password"),
                    rs.getString("name"),
                    rs.getTimestamp("regdate")
                    );
            m.setId(rs.getLong("id"));
            
            return m;
        }
    }

    // 익명구현클래스로 만들었을때
    // 1. 이메일을 통한 멤버 "조회"
	public Member selectByEmail(String email) {
		String sql = "SELECT * FROM members WHERE email = ?";
		
		// 각 레코드의 데이터를 읽어오는데 도움을 주는 클래스 : RowMapper(인터페이스) => 레코드 1차 포장
		
		// 2차 포장 list에 담는 역할
        // 1. RowMapper를 구현한 클래스를 활용한 방법
		// List<Member> list = jdbcTemplate.query(sql, new MapperSqlToMember(), email);

        // 2. 익명 구현 객체를 활용한 방법
		List<Member> list = jdbcTemplate.query(sql, new RowMapper<Member>() {
			
			@Override
			public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
				Member m = new Member(
						rs.getString("email"),
						rs.getString("password"),
						rs.getString("name"),
						rs.getTimestamp("regdate"));
				
				m.setId(rs.getLong("id"));
				
				return m;
			}
			
		}, email);
		
		return list.isEmpty()?null:list.get(0);	// 삼항 연산자
	}
    ```

* 결과가 무조건 1 개인 조회 쿼리문 : queryForObject()

* 조회 결과가 다수의 행이 나온 경우 query() 메서드를 사용할 수 있다.

* 단 하나의 조회 결과만 나온다면 List 로 처리할 것이 아니라 Integer 형으로 처리하는 것이 편리하다.

* 테이블의 레코드 개수를 구하는 메서드를 작성해보자.
    ```java
    public int count() {
		String sql = "SELECT count(*) FROM members";
		
		Integer cnt = jdbcTemplate.queryForObject(sql, Integer.class);
		
		return cnt;
	}
    ```

* JdbcTemplate 클래스는 데이터 변경 관련 쿼리 실행을 위한 update() 메서드를 제공한다.
    ```java
    update(String sql)
    update(String sql, args)
    ```

* update() 메서드의 반환값은 변경된 행의 개수을 반환한다.
    ```java
    public void insertMember(Member member) {
		String sql = "INSERT INTO members VALUES (members_seq.nextval,?,?,?,?)";
		
		jdbcTemplate.update(sql, 
				member.getEmail(), 
				member.getPassword(), 
				member.getName(), 
				new Timestamp(member.getRegisterDate().getTime()));
		
	}
    ```

* PreparedStatementCreator 를 이용한 쿼리 실행, 자주 사용하진 않지만 특별한 경우 사용해야 할 때가 있다.

* 앞서 살펴본 update() 메서드는 쿼리에 사용할 값을 매개값으로 전달해 주었다.

* 그러나 경우에 따라서 Servlet 때 사용했던 set 메서드를 사용해서 직접 파라미터 값을 설정해 주어야 할 때도 있다.

* PreparedStatementCreator 객체를 사용하면 connection 을 가져와서 직접
PreparedStatement 객체를 만들고 쿼리를 완성해서 반환한다.

* 기본키로 사용되는 컬럼은 대체로 시퀀스를 이용한 자동 증가값을 사용한다.

* 그런데 생성된 시퀀스의 값을 알고 싶을 때는 어떻게 할까?

* 이때는 KeyHolder를 사용한다.

* keyHolder를 사용하면 insert() 메서드를 구현할 때 Member 객체의 ID 값을 구할 수 있다.
    ```java
    public void insertMember(Member member) {
		String sql = "INSERT INTO members VALUES (members_seq.nextval,?,?,?,?)";
		
		// ? 부분을 채우기 위한 기능을 집접 제어해야 하는 경우 => key(시퀀스) 값 가져오기
		KeyHolder keyholder = new GeneratedKeyHolder();
		
		jdbcTemplate.update(new PreparedStatementCreator() {
			
			@Override
			public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
				PreparedStatement psmt = con.prepareStatement(sql, new String[] {"id"});
				
				psmt.setString(1, member.getEmail());
				psmt.setString(2, member.getPassword());
				psmt.setString(3, member.getName());
				psmt.setTimestamp(4, new Timestamp(member.getRegisterDate().getTime()));
				
				return psmt;
			}
		}, keyholder);
		
		Number keyValue = keyholder.getKey();
		member.setId(keyValue.longValue());	// 시퀀스로 자동 생성된 id값 읽어오기
	}
    ```

* main메서드에서 실행해보기
    ```xml
    <!-- 추가 Bean -->
	<bean id="printer" class="spring.printer.MemberPrinter">
	</bean>
	<bean id="infoPrinter" class="spring.printer.MemberInfoPrinter">
		<property name="dao" ref="dao" />
		<property name="printer" ref="printer" />
	</bean>
	<bean id="listPrinter" class="spring.printer.MemberListPrinter">
		<constructor-arg ref="dao" />
		<constructor-arg ref="printer" />
	</bean>
	<bean id="regSvc" class="spring.service.MemberRegisterService">
		<constructor-arg ref="dao" />
	</bean>
	<bean id="changePwdSvc" class="spring.service.ChangePasswordService">
		<constructor-arg ref="dao" />
	</bean>
    ```
    ```java
    public static void main(String[] args) {
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:appCtx01.xml");
		
		// SELECTALL
		MemberListPrinter listPrt = ctx.getBean("listPrinter", MemberListPrinter.class);
		listPrt.printAll();
		
		System.out.println("-----------------------------------------------------------");
		
		// SelectByEmail
		MemberInfoPrinter infoPrt = ctx.getBean("infoPrinter", MemberInfoPrinter.class);
		infoPrt.printMemberInfo("park@naver.com");
		
		// InsertMember
		MemberRegisterService regSvc = ctx.getBean("regSvc", MemberRegisterService.class);
		insert(regSvc);
		
		// Dao
		MemberDao dao = ctx.getBean("dao", MemberDao.class);
		
		// UpdateMember
		String email = "park@naver.com";
		ChangePasswordService changePwdSvc = ctx.getBean("cangePwdSvc", ChangePasswordService.class);
		update(changePwdSvc, email, dao);
		
		// Insert / Update가 잘 되는지 확인
		listPrt.printAll();
		
	}

	private static void update(ChangePasswordService changePwdSvc, String email, MemberDao dao) {
		String oldPwd = dao.selectByEmail(email).getPassword();	//	이메일로 기존 비밀번호 알아오기
		String newPwd = "";
		
		if(oldPwd.equals("1234")) {
			newPwd = "4321";
		} else if (oldPwd.equals("4321")) {
			newPwd = "1234";
		}
		
		changePwdSvc.changePassword(email, oldPwd, newPwd);
	}

	private static void insert(MemberRegisterService regSvc) {
		RegisterRequest rr = new RegisterRequest();
		
		// 매번 달라지는 이름과 이메일을 저장하기
		SimpleDateFormat dateFmt = new SimpleDateFormat("YYYYMMdd-HHmmss");
		String name = dateFmt.format(new Date());
		
		rr.setEmail(name+"@naver.com");
		rr.setPassword("1234");
		rr.setName(name);
		rr.setConfirmPassword("1234");
		
		regSvc.regist(rr);
	}
    ```

## 트랜젝션
* 트랜젝션이란 하나의 논리적인 작업의 단위를 의미한다.

* 여러 물리적인 작업을 하나로 묶어 주며, 중간에 하나의 쿼리라도 실패하면 전체 쿼리 실패로 간주하고 실패 이전의 모든 쿼리를 취소한다.

* 이렇게 쿼리 실행 결과를 취소하고 기존 상태로 되돌리는 것을 rollback(롤백) 라고 하고, 반대로 모든 쿼리가 성공적으로 작동해서 쿼리의 내용을 실제 DB 에 반영하는 것을 commit(커밋) 이라고 한다.

* 트랜젝션이 한번 시작되면 커밋하거나 롤백할 때 까지 실행되는 모든 쿼리가 하나의 작업의 단위가 된다.
    ```java
    Connection conn = null;
    try{
        conn =DriverManager.getConnection(url,user,pwd);
        conn.setAutoCommit(false); //트랜젝션 시작
    
    //  ….쿼리 실행
    
    conn.commit();  // 커밋 , 트랜젝션 종료
    }catch(SQLException err){
        if(conn!=null){
            try{
                conn.rollback(); // 롤백 , 트랜젝션 종료
            }
        }
    }
    ```

* 위와 같은 방식으로 직접 트랜젝션으로 관리하면 실수의 가능성이 높아진다.

