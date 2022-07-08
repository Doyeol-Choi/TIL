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

* 날짜를 입력해야 하는 뷰에 형식에 맞지 않는 날짜를 입력하면 오류가 발생한다.