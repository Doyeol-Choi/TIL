## 프로젝트 준비
* 기존에 작업해봤던 ex02와 ex07에서 몇몇 파일을 복사해온다.

* spring legacy project로 프로젝트를 생성한다.

* com.green.ex00

* POM.xml 에 다음 내용을 채운다.

* 기존의 프로젝트에서 다음 코드를 복사한다.
  - spring.exception
    + AlreadyExistingMemberException.java
    + IdPasswordNotMatchingException.java
    + MemberNotFoundException java
  - spring.vo
    + Member.java
    + RegisterRequest.java
  - spring.dao
    + MemberDao.java
  - spring.service
    + MemberRegisterService.java
    + ChangePasswordService .java

* 데이터베이스 연결을 준비한다. 기존의 데이터베이스를 사용한다.

* spring-member.xml 을 작성한다.
    ```xml
    <!-- 트랜젝션 관리 객체 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>
	<tx:annotation-driven transaction-manager="transactionManager"/>
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
		<property name="maxPoolSize" value="20" />
	</bean>
	<!-- jdbcTemplate -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<!-- 연결객체 -->
		<constructor-arg ref="dataSource" />
	</bean>
	<!-- DAO -->
	<bean id="dao" class="spring.dao.MemberDao">
		<constructor-arg ref="jdbcTemplate" />
	</bean>
	<!-- 추가 Bean -->
	<bean id="regSvc" class="spring.service.MemberRegisterService">
		<constructor-arg ref="dao" />
	</bean>
	
	<bean id="changePwdSvc" class="spring.service.ChangePasswordService">
		<constructor-arg ref="dao" />
	</bean>
    ```
* spring-mvc.xml 을 작성한다.
    ```xml
    <mvc:annotation-driven />
	
	<mvc:default-servlet-handler/>
	
	<mvc:view-resolvers>
		<mvc:jsp prefix="/WEB-INF/views/" />
	</mvc:view-resolvers>
    ```
* web.xml 을 작성한다.
    ```xml
    <servlet>
 		<servlet-name>appServlet</servlet-name>
 		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
 		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
				classpath:spring-mvc.xml
				classpath:spring-controller.xml
				classpath:spring-member.xml
			</param-value>
			<load-on-startup>1</load-on-startup>
		</init-param>
 	</servlet>
 	
 	<servlet-mapping>
		<servlet-name>appServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	
	<!-- 서블릿이 동작하기 전에 거름망 같은 존재 filter -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>
			org.springframework.web.filter.CharacterEncodingFilter
		</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
    ```

## @RequestMapping을 이용한 경로 매핑
* 웹 어플리케이션을 개발한다는 것은 결국 다음을 위한 코드를 작성하는 것과 같다.
  1) 특정 요청의 URL 을 처리할 코드 작성
  2) 처리 결과를 HTML 과 같은 형식으로 응답

* (1)의 경우 @Controller 어노테이션을 사용한 컨트롤러 클래스를 이용해서 구한다. 컨트롤러 클래스는 @RequestMapping 어노테이션을 사용해서 메서드가 처리할 요청 경로를 지정한다.

* spring.controller.RegisterController.java
    ```java
    @Controller
    public class RegisterController {
        
        @RequestMapping("/register/step1")
        public String handlerStep1() {
            return "";
        }
    }
    ```

* views/register/step1.jsp
	```jsp
	<title>회원 가입</title>
	</head>
	<body>
		<h2>약관</h2>
		<p>약관 내용</p>
		<form action="step2" method="post">
			<label>
				<input type="checkbox" name="agree" value="true"> 약관 동의
			</label>
			<input type="submit" value="다음 단계">
		</form>
	</body>
	```

* spring-controller.xml
	```xml
	<bean id="registerController" class="spring.controller.RegisterController"></bean>
	```

## GET과 POST구분
* HTML폼 태그의 전송방식은 Get 방식과 Post 방식으로 구분된다.

* 스프링 MVC 는 get 과 Post 구분없이 처리를 한다.

* 그러나 특정 방식의 요청을 처리하고자 한다면 @RequestMapping 어노테이션을 사용해서 전송방식을 한정할 수 있다.

* RegisterController.java
	```java
	//	@RequestMapping(value="/register/step2", method=RequestMethod.POST)		// 속성을 2가지 이상 넣어줄땐 확실하게 선언해준다.
	@PostMapping("/register/step2")		// post방식의 맵핑을 PostMapping 어노테이션으로 사용해 줄 수도 있다. (스프링 5.버전 이상부터 사용가능)
	public String handlerStep2() {
		return "register/step2";
	}
	```

## 요청 파라미터 접근
* 클라이언트가 요청을 할 때는 단순히 경로만으로 요청하는 경우도 있지만 어떤 처리를 목적으로 데이터를 전달하는 경우도 있다.

* 이런 데이터를 요청 파라미터라고 하는데 컨트롤러는 이러한 파라미터는 얻어와서 처리 해야 한다.

* 이때 파라미터를 얻어오는 방법는 두가지 있다.
  1) httpServletRequest 객체에서 직접 얻어오는 방법
		```java
		@PostMapping("/register/step2")
		public String handlerStep2(HttpServletRequest request) {
			String agreeParam = request.getParameter("agree");
			
			if(agreeParam == null || !agreeParam.equals("true")) {
				return "register/step1";
			}
			return "register/step2";
		}
		```
  2) @RequestParam 어노테이션은 사용하는 방법
		```java
		// 클라이언트에서 값 얻어오기 2번 방법
		@PostMapping("/register/step2")
		public String handlerStep2(@RequestParam(value="agree", required=false, defaultValue="false")boolean agree) {	
			// @RequsetParam은 1번 방법과 달리 자동으로 형태변환 해줄 수 있다.
			if(!agree) {
				return "register/step1";
			}
			
			return "register/step2";
		}
		```

* @RequestParam(value = "agree", defaultValue = "false") Boolean agreeVal

* agree요청 파라미터의 값을 읽어와서 agreeVal 매개변수에 할당한다.

* defaultValue에 의해서 요청 파라미터가 없으면 false 로 처리한다.

* 파라미터를 읽어와서 자동으로 기본 타입의 형변환을 지원한다.
  - value (String) : HTTP 요청 파라미터의 이름을 지정한다.
  - required (Boolean) : 필수 여부를 지정한다 . 기본값은 true 이며 true 일 경우 해당 요청 파라미터에 값이 없으면 스프링 MVC 는 예외를 발생시킨다.
  - defaultValue (String) : 요청 파라미터의 값이 없을 경우 사용할 문자열 값을 지정한다.

## @RequestMapping을 이용한 경로 매핑
* HelloController 클래스의 @RequestMapping 어노테이션 적용 메서드를 한 개만 정의 했지만 @RequestMapping 어노테이션 적용 메소드를 두개 이상 정의 할 수도 있다.

	```java
	@Controller
	@RequestMapping("/register")
	// 공통의 경로를 생략하기 위한 어노테이션
	public class RegisterController {
		
		@RequestMapping("/step1")		// localhost:8085/ex00/register/step1
		public String handlerStep1() {
			return "register/step1";		// WEB-INF/views/register/step1.jsp 를 찾아가도록
		}
	```

## 리다이렉트 처리
* 서버를 구동하고 주소창에 직접 /register/step2 를 입력하면 404 에러 페이지를 보여준다.

* 이유는 주소창에 직접 요청하는 방식이 Get 방식이기 때문이다.

* 이렇듯 잘못된 전송방식으로 요청이 왔을 때 알맞은 경로로 리다이렉트 하는 것이 좋다.

* 이때 @RequestMapping 적용 메서드가 redirect: 로 시작되는 경로를 반환하면 나머지 경로를 이용해서 자동으로 리다이렉트 할 경로를 구한다.

	```java
	@GetMapping("/register/step2")
	public String handlerStep2Get() {	// 주소창에서 step2를 입력해서 페이지로 들어오는 것을 방지
	//	return "register/step1";	// 이것만 쓰면 주소창에는 step2로 보여지게 된다. 실제 보여지는 페이지는 step1
		return "redirect:regiset/step1";	// 주소창의 요청 경로를 register/step1으로 지정
	}
	```

## 커맨드 객체를 이용해서 요청 파라미터 사용하기
* 앞서 본 예제에서는 하나의 요청 파라미터를 가져오는 내용을 살펴 보았다.

* 일반적인 방법으로 여러 데이터를 읽어오기
	```java
	// 기존 request의 파라미터 받아오기
	@PostMapping("/step3")
	public String handlerStep3(HttpServletRequest request) {
		String email = request.getParameter("email");
		.....등등
		return "register/step3";
	}
	```

	```java
	// 어노테이션을 활용한 파라미터 받아오기
	@PostMapping("/step3")
	public String handlerStep3(@RequestParam("email") String email,
							   @RequestParam("name") String name,
							   @RequestParam("password") String password,
							   @RequestParam("confirmPassword") String confirmPassword) {
		
		return "register/step3";
	}
	```

* 그러나 요청 파라미터의 개수가 늘어나면 늘어날 수록 코드의 복잡도는 점점 늘어난다.

* 이런 불편함을 줄이고자 스프링은 커맨드 객체를 사용한다.

* 커맨드 객체는 파라미터를 전달하는 기능을 가지므로 set 메소드가 존재하는 클래스 여야한다.
	```java
	// 커맨드 객체를 통해 데이터 한번에 받아오기
	@PostMapping("/step3")
	public String handlerStep3(RegisterRequest regReq) {	//	setter 메서드 기준으로 받아온다.
		return "register/step3";
	}
	```

* 커맨드 객체를 활용해서 실제 회원 가입하는 코드를 작성해보자
  1) 서비스 객체를 주입받아서 사용하도록 처리한다.
		```java
		@PostMapping("/step3")
		public String handlerStep3(RegisterRequest regReq) {	//	setter 메서드 기준으로 받아온다.
			try {
				memberRegisterService.regist(regReq);
				return "register/step3";			
			} catch(AlreadyExistingMemberException e) {
				e.printStackTrace();
				return "register/step2";
			}			
		}
		```
  2) 객체 주입을 위한 설정 : spring-controller.xml
		```xml
		<bean id="registerController" class="spring.controller.RegisterController">
			<property name="memberRegisterService" ref="regSvc" />
		</bean>
		```
  3) 처리 후 이동할 페이지 작성 : step3.jsp
		```jsp
		<title>회원 가입</title>
		</head>
		<body>
			<p>회원 가입을 완료했습니다.</p>
			<p><a href="<c:url value='/main'/>">[첫 화면 이동]</a></p>
		</body>
		```

## 뷰 JSP 코드에서 커맨드 객체 사용하기