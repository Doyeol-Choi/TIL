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
	```html
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
		```html
		<title>회원 가입</title>
		</head>
		<body>
			<p>회원 가입을 완료했습니다.</p>
			<p><a href="<c:url value='/main'/>">[첫 화면 이동]</a></p>
		</body>
		```

## 뷰 JSP 코드에서 커맨드 객체 사용하기
* 가입 완료 화면에서 가입할 때 사용한 이메일 주소와 이름을 보여주면 좋을 것이다.

* 이때 커맨드 객체를 사용해서 JSP 를 통해 정보를 표시할 수 있다.
	```html
	<p><strong>${registerRequest.name}님! </strong>회원 가입을 완료했습니다.</p>
	```

* 커맨드 객체의 속성명을 변경하고 싶다면 @ModelAttribute 어노테이션을 사용하면 된다.
	```java
	public String handlerStep3(@ModelAttribute("formData")RegisterRequest regReq)
	```
	```jsp
	<p><strong>${formData.name}님! </strong>회원 가입을 완료했습니다.</p>
	```

## 커맨드 객체와 스프링 폼 연동
* 회원 정보 입력 폼에서 중복된 이메일을 입력할 경우 회원 정보를 입력하면 안되고 다시 정보를 요구해야 한다.

* 이때 앞서 본 예제는 기존의 입력 내용이 전부 사라지고 텅 빈 폼을 보게 되는데 이때 기존에 입력 내용을 커맨드 객체의 값을 보여 줄 수 있다.
	```html
	<form action="step3" method="post">
		<p>
			<label> 이메일 : 
				<input type="text" name="email" id="email" value="${formData.email}">
			</label>
		</p>
		<p>
			<label> 이름 : 
				<input type="text" name="name" id="name" value="${formData.name}">
			</label>
		</p>
	```

* 커맨드 객체를 사용하는 경우 스프링 MVC 가 제공하는 커스텀 태그를 사용하면 좀 더 간 편하게 커맨드 객체의 값을 출력할 수 있다.

* 스프링 MVC 가 제공하는 커스텀 태그
  - &lt;form:form&gt;
  - &lt;form:input&gt;
  - &lt;form:password&gt;

	```html
	<!-- taglib 추가후 -->
	<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
	<!-- body에 내용을 바꿔준다 -->
	<!-- spring버전 4.xx까진 commandName을 사용했지만 5.xx부터는 modelAttribute을 사용한다. -->
	<form:form action="step3" modelAttribute="formData">
		<p>
			<label> 이메일 : <br>
				<form:input path="email"/>
				<!-- <input type="text" name="email" id="email" value="${formData.email}"> -->
			</label>
		</p>
		<p>
			<label> 이름 : <br>
				<form:input path="name"/>
			</label>
		</p>
		<p>
			<label> 비밀번호 : <br>
				<form:input path="password"/>
			</label>
		</p>
		<p>
			<label> 비밀번호 확인 : <br>
				<form:input path="confirmPassword"/>
			</label>
		</p>
		<input type="submit" value="가입 완료">
	</form:form>
	```

* &lt;form:form&gt; 태그는 form 을 생성한다
  - action : form태그의 action 과 같은 역할 => submit 클릭시 이동할 페이지
  - commandName : 커맨드 객체 속성 이름을 지정한다. 미설정시 command 로 기본값 사용

* &lt;form:input&gt; 태그는 input 태그를 생성한다.
  - &lt;form:input path=“name” /&gt; 의 경우 커맨드 객체의 name 프로퍼티 값을 value 에 속성으로 사용한다.
  - 예를 들어 커맨드 객체에서 name = 홍길동 이라고 한다면 &lt;input id=“name” name=”name” type=“text” value=“홍길동"&gt; 이 생성된다.

* &lt;form:form&gt; 태그를 사용하려면 반드시 커맨드 객체가 필요하다.
  - step1 에서 step2 로 넘어오는 단계에서 커맨드 객체를 모델에 담아 주어야 한다.

## 컨트롤러 구현 없는 경로 매핑
* 컨트롤러 중에서는 별도의 처리없이 단순히 뷰 이름만 반환하는 컨트롤러의 경우
	```java
	@Controller
	public class MainController {

		@RequestMapping(value={"/main","/"})
		public String main() {
			return "main";
		}
	}
	```
	```xml
	<bean class="spring.controller.MainController"></bean>
	```

* 컨트롤러 중에서는 별도의 처리없이 단순히 뷰 이름만 반환하는 컨트롤러의 경우
특별한 로직도 없는 컨트롤러를 만드는 성가심을 해소하기 위해 스프링 MVC 는
&lt;mvc:view controller&gt; 태그를 제공한다.
	```xml
	<!-- /main, / 이라는 경로로 들어오는 경우 main.jsp로 바로 연결 시켜주세요 -->
	<mvc:view-controller path="/main" view-name="main"/>
	<mvc:view-controller path="/" view-name="main"/>
	```

## 커맨드 객체 : 중첩, 콜렉션 프로퍼티
* 예 : 간단한 설문 조사를 한다, 3 가지 설문항목이 있고, 응답자의 지역과 나이를 입력받아야 한다고 가정한다.

* 관련 클래스를 제작한다.
  - spring.survey.Respondent.java
		```java
		public class Respondent {
			private int age;
			private String location;
			
			public int getAge() {
				return age;
			}
			public void setAge(int age) {
				this.age = age;
			}
			public String getLocation() {
				return location;
			}
			public void setLocation(String location) {
				this.location = location;
			}
		}
		```
  - spring.survey.AnsweredData.java
		```java
		public class AnswerData {	// 설문 조사지
			private List<String> responses;	// 설문 답변
			private Respondent res;			// 답변자 정보
			
			public List<String> getResponses() {
				return responses;
			}
			public void setResponses(List<String> responses) {
				this.responses = responses;
			}
			public Respondent getRes() {
				return res;
			}
			public void setRes(Respondent res) {
				this.res = res;
			}
		}
		```

* 위 클래스는 이전 커맨드 객체와 2 가지 다른 점이 존재한다.
  - -> 리스트타입의 프로퍼티
  - -> 중첩 프로퍼티

* 스프링은 위와 같은 프로퍼티를 가진 경우에도 요청 파라미터의 값을 알맞게 설정해준다.
  - -> HTTP 요청 파라미터 이름이 프로퍼티이름 인덱스 형식이면 List 타입의 프로퍼티 값으로
  - -> HTTP 요청 파라미터 이름이 프로퍼티이름 프로퍼티이름 형식으면 중첩 프로퍼티 값으로

* 컨트롤러 SurveyController.java
	```java
	@Controller
	@RequestMapping("/survey")
	public class SurveyController {
		
		@GetMapping
		public String form() {
			return "survey/surveyForm";
		}
		
		@PostMapping
		public String submit(@ModelAttribute("ansData") AnswerData data) {
			return "survey/submitted";
		}
	}
	```

* 빈 등록
	```xml
	<bean class="spring.controller.SurveyController"></bean>
	```

* 매핑에 대응되는 jsp 생성 : surveyForm.jsp
	```html
	<form method="post">
		<p>
			1. 당신의 역할은? <br>
			<label> <input type="radio" name="responses[0]" value="프론트"> 프론트 개발자 </label>
			<label> <input type="radio" name="responses[0]" value="백엔드"> 백엔드 개발자 </label>
			<label> <input type="radio" name="responses[0]" value="풀스택"> 풀스택 개발자 </label>
		</p>
		<p>
			2. 가장 많이 사용하는 개발 도구는? <br>
			<label> <input type="radio" name="responses[1]" value="Eclipse"> Eclipse </label>
			<label> <input type="radio" name="responses[1]" value="IntelliJ"> IntelliJ </label>
			<label> <input type="radio" name="responses[1]" value="NetBeans"> NetBeans </label>
		</p>
		<p>
			3. 하고 싶은 말 <br>
			<input type="text" name="responses[2]">
		</p>
		<p>
			<label> 응답자 위치 : 
				<input type="text" name="res.location">
			</label>
		</p>
		<p>
			<label> 응답자 나이 : 
				<input type="text" name="res.age">
			</label>
		</p>
		<input type="submit" value="전송">
	</form>
	```

* 매핑에 대응되는 jsp 생성 : submitted.jsp
	```html
	<h2>응답 내용</h2>
	<p>
		<ul>
			<c:forEach items="${ansData.responses}" var="response" varStatus="status">
				<li>${status.index+1}번 문항 : ${response}</li>
			</c:forEach>
		</ul>
	</p>
	
	<p>응답자 위치 : ${ansData.res.location}</p>
	<p>응답자 나이 : ${ansData.res.age}</p>
	```

## Model을 통해 컨트롤러에서 뷰에 데이터 전달하기
* 컨트롤러는 뷰가 응답 화면을 구성하는 데 필요한 데이터를 생성해서 보내 주어야 한다. 이때 사용되는 것이 Model 이다.

* 이전 설문조사 코드에서 설문 제목과 답변 옵션을 객체로 만들어서 전달하는 예제를 만들어 본다.

  - Question.java
	```java
	public class Question {
		private String title;
		private List<String> option;
		
		public Question(String title) {
			this.title = title;
		}
		public Question(String title, List<String> option) {
			this.title = title;
			this.option = option;
		}
		public boolean isChoice() {
			return option!=null && !option.isEmpty();
		}
		
		public String getTitle() {
			return title;
		}
		public void setTitle(String title) {
			this.title = title;
		}
		public List<String> getOption() {
			return option;
		}
		public void setOption(List<String> option) {
			this.option = option;
		}
	}
	```

  - ServeyController.java 추가
	```java
  	private List<Question> createQuestion() {
		Question q1 = new Question("당신의 역할은 무엇입니까?", Arrays.asList("백엔드","프론트","풀스택"));
		Question q2 = new Question("많이 사용하는 개발 도구는 무엇입니까?", Arrays.asList("이클립스","인텔리제이","넷빈즈"));
		Question q3 = new Question("하고 싶은 말을 적어주세요");
		return Arrays.asList(q1,q2,q3);
	}
	
	@GetMapping
	public String form(Model model) {
		List<Question> qList = createQuestion();
		model.addAttribute("qList", qList);
		return "survey/surveyForm";
	}
	```

  - surveyForm.jsp 변경
	```html
	<c:forEach items="${qList}" var="q" varStatus="s">
		<p>
			${s.index+1}.${q.title}<br>
			<c:if test="${q.choice}">
				<c:forEach items="${q.option}" var="op">
					<label>
						<input type="radio" name="responses[${s.index}]" value="${op}"> ${op}
					</label>
				</c:forEach>
			</c:if>
			<c:if test="${!q.choice}">
				<label>
					<input type="text" name="responses[${s.index}]">
				</label>
			</c:if>
		</p>
	</c:forEach>
	<p>
		<label> 응답자 위치 : 
			<input type="text" name="res.location">
		</label>
	</p>
	<p>
		<label> 응답자 나이 : 
			<input type="text" name="res.age">
		</label>
	</p>
	<input type="submit" value="전송">
	```

* 컨트롤러의 기본 기능은 두가지 특징을 가진다.
  - Model을 이용해서 뷰에 전달할 데이터 설정
  - 결과를 보여줄 뷰 이름을 반환

* 위 두가지 기능을 한번에 처리 해줄 ModelAndView 객체를 사용할 수 있다.