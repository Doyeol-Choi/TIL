## 날짜를 이용한 회원 검색 기능
* 가입일을 기준으로 회원을 검색하는 기능을 구현하면서 몇몇 특징을 알아보자

* 기능을 구현하기 위한 순서
    1) DAO에 날짜를 이용한 데이터 조회 쿼리 메서드
    2) 날짜 두개를 전달 받을 폼 페이지
    3) 날짜 두개를 전달 할 커맨드 객체
    4) 컨트롤러
    5) 스프링 설정에 등록

* MemberDao에 selectByRegdate() 메서드를 추가한다.
    ```java
    // 날짜를 이용한 회원 조회 메서드
	public List<Member> selectByRegDate(Date from, Date to) {
		String sql = "SELECT * FROM members WHERE regDate BETWEEN ? AND ? ORDER BY regDate";
		
		List<Member> list = jdbcTemplate.query(sql, new RowMapper<Member>() {

			@Override
			public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
				Member m = new Member(
						rs.getString("email"),
						rs.getString("password"),
						rs.getString("name"),
						rs.getTimestamp("regDate"));
				m.setId(rs.getLong("id"));
				return m;
			}}, from, to);
		return list;
	}
    ```

* 가입 일시를 기준으로 회원을 검색할 수 있도록 하기 위해 시작과 끝 날짜를 파라미터로 전달받아야 한다.

* 이를 위해 커맨드 객체를 제작해본다.
    ```java
    public class ListCommand {
	
        @DateTimeFormat(pattern="yyyyMMddHH")
        private Date from;
        @DateTimeFormat(pattern="yyyyMMddHH")
        private Date to;
        
        public Date getFrom() {
            return from;
        }
        public void setFrom(Date from) {
            this.from = from;
        }
        public Date getTo() {
            return to;
        }
        public void setTo(Date to) {
            this.to = to;
        }
    }
    ```

* 그러나 문자열을 Date 타입으로 변환해야 하는데 스프링은 기본타입(int, long등)으로 변환은 지원하지만 Date타입으로 변환은 추가 설정을 해야 한다.
    ```java
    // 뷰에서 from, to에 해당하는 데이터를 커멘드 객체에 담아서 컨트롤러에 보낸다.
	// 뷰에서 보낸 데이터는 text 문자열, 커멘드 객체의 프로퍼티 타입은 Date
	// 문자열 => 기본타입으로 바꿀땐 자동 형변환을 지원(스프링), 그러나 date는 자동 형 변환 타입이 아니다.
	// 결론 문자열을 Date 타입으로 형변환 하도록 설정해줘야 한다.
	
	@DateTimeFormat(pattern="yyyyMMddHH")
	private Date from;
	@DateTimeFormat(pattern="yyyyMMddHH")
	private Date to;
    ```

* 컨트롤러를 제작한 후 빈 등록을 해본다.
    ```java
    @Controller
    public class MemberListController {

        private MemberDao dao;	
        // service 클래스 없이 바로 호출, 좋은 방법은 아님
        
        public void setDao(MemberDao dao) {
            this.dao = dao;
        }
        
        // RequestMapping방법과 get, post를 나누는 방법
        @GetMapping("/member/list")
        public String form(ListCommand listCommand) {
            return "member/memberList";
        }
        
        @PostMapping("/member/list")
        public String list(ListCommand listCommand, Model model) {
            
            if(listCommand.getFrom() != null && listCommand.getTo() != null) {
                // 날짜 데이터가 존재 할때만 조회한 데이터를 보낸다.
                List<Member> list = dao.selectByRegDate(listCommand.getFrom(), listCommand.getTo());
                
                model.addAttribute("members", list);
            }
            return "member/memberList";
        }
    }
    ```
    ```xml
    <!-- spring-controller.xml 빈 등록  -->
    <bean class="spring.controller.MemberListController">
		<property name="dao" ref="dao" />
	</bean>
    ```

* 이어서 뷰 코드를 작성해 본다.(jsp)
    ```html
    <title><spring:message code="member.select" /></title>
    </head>
    <body>
        <h2><spring:message code="member.select" /></h2>
        <form:form modelAttribute="listCommand">
            <p>
                <label><spring:message code="member.from" /> : <form:input path="from" /></label>
                ~
                <label><spring:message code="member.to" />: <form:input path="to" /></label>
                <input type="submit" value="<spring:message code="select" />">
            </p>
        </form:form>
        <c:if test="${!empty members}">
            <table>
                <tr>
                    <th>아이디</th>
                    <th>이메일</th>
                    <th>이름</th>
                    <th>가입일</th>
                </tr>
                <c:forEach var="member" items="${members}">
                    <tr>
                        <td>${member.id}</td>
                        <td><a href="#">${member.email}</a></td>
                        <td>${member.name}</td>
                        <td><fmt:formatDate value="${member.registerDate}" pattern="yyyy-MM-dd" /></td>
                    </tr>
                </c:forEach>
            </table>
        </c:if>
    </body>
    ```

## @PathVariable을 이용한 경로 변수 처리
* ID가 10 인 회원 정보를 조회하기 위한 URL 구성  
=> ?ID=10

* 위와 같이 해당 ID 값을 요청 경로에 포함시키는 방법으로 사용한다.

* 이렇게 경로의 특정 부분의 값이 고정되지 않고 달라 질 수 있는 것이 @PathVariable 이다.

* DAO 객체에 다음 메서드를 추가해본다.
    ```java
    // ID 번호를 통해 회원 조회를 하기 위한 메서드
	public Member selectById(Long id) {
		String sql = "SELECT * FROM members WHERE id=?";
		
		List<Member> result = jdbcTemplate.query(sql, new RowMapper<Member>() {

			@Override
			public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
				Member m = new Member(
						rs.getString("email"),
						rs.getString("password"),
						rs.getString("name"),
						rs.getTimestamp("regDate"));
				m.setId(rs.getLong("id"));
				return m;
			}}, id);
		
		return result.isEmpty()?null:result.get(0);
	}
    ```

* 컨트롤러를 만들어본다.
    ```java
    @Controller
    public class MemberDetailController {
        
        private MemberDao dao;
        
        public void setDao(MemberDao dao) {
            this.dao = dao;
        }

        @GetMapping("member/detail/{id}")
        public String detail(@PathVariable("id") Long memberId, Model model) {
            // 특정 누군가의 정보를 보고싶은 기능
            // 필요한 정보(누군가를 식별할 수 있는 정보) id => memberId
            // 보여주고 싶은 정보는 누군가의 정보 member => Model
            
            Member member = dao.selectById(memberId);
            
            if(member == null) {
                throw new MemberNotFoundException();
            }
            
            model.addAttribute("member", member);
            
            return "member/memberDetail";
        }
    }
    ```

* 컨트롤러를 빈으로 등록합니다.
    ```xml
    <bean class="spring.controller.MemberDetailController">
		<property name="dao" ref="dao" />
	</bean>
    ```

* memberList.jsp 수정
    ```html
    <c:forEach var="member" items="${members}">
        <tr>
            <td>${member.id}</td>
            <!-- email부분에 a태그 추가 -->
            <td><a href="<c:url value='/member/detail/${member.id}' />">${member.email}</a></td>
            <td>${member.name}</td>
            <td><fmt:formatDate value="${member.registerDate}" pattern="yyyy-MM-dd" /></td>
        </tr>
    </c:forEach>
    ```

* 뷰페이지를 생성한다.
    ```html
    <title><spring:message code="member" /></title>
    </head>
    <body>
        <p><spring:message code="member.id" /> : ${member.id}</p>
        <p><spring:message code="email" /> : ${member.email}</p>
        <p><spring:message code="name" /> : ${member.name}</p>
        <p><spring:message code="register.info" /> : <fmt:formatDate value="${member.registerDate}" pattern="yyyy-MM-dd HH:mm" /></p>
        <br>
        <p>
            <a href="<c:url value='/member/list' />">[날짜별 회원 정보 보기]</a>
        </p>
    </body>
    ```

## 컨트롤러 예외 처리하기
* ID 가 존재할 때는 정상적으로 뷰페이지가 출력이 되지만 ID 가 존재하지 않으면 500 번 에러페이지를 보여준다.

* 또한 ID 값은 long 타입인데 문자열 등을 입력하면 역시 형변환 불가로 인한 400 번 에러페이지를 보여준다.

* 이런 여러가지 에러가 발생했을 때 적절한 에러 화면을 보여 주는 방법이 존재한다.

* @ExceptionHandler 어노테이션을 사용하는 방법이다.

* @ExceptionHandler가 적용된 메서드가 존재하면 스프링 MVC 는 이 메서드가 예외를 처리하도록 한다.

* 컨트롤러에서 예외처리를 위한 메서드를 만든다.
    ```java
    @ExceptionHandler(TypeMismatchException.class)
	public String handlerTypeMismatchException(TypeMismatchException err) {
		// 형변환 오류(Long 타입이 아닐때)
		return "member/ivalidid";
	}
	
	@ExceptionHandler(MemberNotFoundException.class)
	public String handlerNotFoundException(MemberNotFoundException err) {
		// 없는 id조회 오류
		return "member/noMember";
	}
    ```

* 각 예외 페이지를 보여주기 위한 뷰페이지를 제작한다.
    ```html
    <title>오류</title>
    </head>
    <body>
        <p>잘못된 요청입니다.</p>
    </body>

    <title>회원 업음</title>
    </head>
    <body>
        <p> 존재하지 않는 회원입니다. </p>
    </body>
    ```

* 다수의 일반 예외를 모아서 처리하기 위한 컨트롤러를 만들 수 있다.
  - 컨트롤러 생성
    ```java
    @ControllerAdvice("spring.controller")	// 패키지를 적어준다.
    public class CommonExceptionHandler {	// 작동원리 AOP
        
        
        @ExceptionHandler(TypeMismatchException.class)
        public String handlerTypeMismatchException(TypeMismatchException err) {
            // 형변환 오류(Long 타입이 아닐때)
            return "member/ivalidid";
        }
        
        @ExceptionHandler(MemberNotFoundException.class)
        public String handlerMemberNotFoundException(MemberNotFoundException err) {
            // 없는 id조회 오류
            return "member/noMember";
        }
        
        @ExceptionHandler(RuntimeException.class)
        public String handlerRuntimeException(RuntimeException err) {
            return "error/commonException";
        }
    }
    ```
  - xml에 빈으로 등록
    ```xml
    <bean class="spring.controller.CommonExceptionHandler" />
    ```
  - 뷰페이지 생성
    ```html
    <title>일반적인 예외 화면</title>
    </head>
    <body>
        <p> 
            페이지를 이용하는 데 문제가 발생했습니다.<br>
            담당자(webmaster@green.co.kr)에게 문의하세요.
        </p>
    </body>
    ```
