# 스프링 MVC 자바 설정
* Com.green.ex00j로 새로운 프로젝트를 생성한다.

* 기존 프로젝트(MVC만들기)에서 모든 자바코드와 뷰페이지를 복사해온다.

* 기존 프로젝트에서 POM.xml의 내용을 복사한 후 메이븐 업데이트로 각종 라이브러리를 추가한다.

* 기존의 Web.xml설정에서 xml설정파일을 읽어오는 내용을 자바 클래스를 읽어오는 방식으로 변경한다.
    ```xml
    <!-- 기존 -->
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
    ```
    ```xml
    <!-- 변경 -->
    <servlet>
 		<servlet-name>appServlet</servlet-name>
 		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
 		<init-param>
 			<param-name>contextClass</param-name>
 			<param-value>
 				org.springframework.web.context.support.AnnotationConfigWebApplicationcontext
 			</param-value>
 		</init-param>
 		
 		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
				spring.config.MvcConfig
				spring.config.MemberConfig
				spring.config.ControllerConfig
			</param-value>
			<load-on-startup>1</load-on-startup>
		</init-param>
 	</servlet>
    ```
* 기존 xml 스프링 설정 파일을 자바 파일로 변환해보자
  - spring-mvc.xml => MvcConfig.java
  - spring-controller.xml =>ControllerConfig.java
  - spring-member.xml => MemberConfig.java

* spring.config 패키지에 ControllerConfig, MemberConfig, MvcConfig 클래스 파일을 만든다.

* 모든 클래스는 설정파일이라는 의미로 @Configuration 어노테이션을 붙인다.

## MvcConfig 클래스 설정
* 기존 spring-mvc.xml
    ```xml
    <mvc:annotation-driven />
	
	<mvc:default-servlet-handler/>
	
	<!-- return으로 String에 주소를 적어줄떄 앞뒤에 붙을 내용 -->
	<mvc:view-resolvers>
		<mvc:jsp prefix="/WEB-INF/views/" />
	</mvc:view-resolvers>
	
	<!-- /main, / 이라는 경로로 들어오는 경우 main.jsp로 바로 연결 시켜주세요 -->
	<mvc:view-controller path="/main" view-name="main"/>
	<mvc:view-controller path="/" view-name="main"/>
	
	<!-- JSP 라벨 추가 -->
	<bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
		<property name="basenames">
			<list>
				<value>classpath:message/label</value>
			</list>
		</property>
		<property name="defaultEncoding" value="UTF-8" />
	</bean>
	
	<!-- 인터셉터 적용 -->
	<mvc:interceptors>
		<mvc:interceptor>
			<!-- 1. 인터셉터를 어디에 적용할 것인가? -->
			<mvc:mapping path="/edit/**" />
			<!-- 2. 어떤 인터셉터를 사용할 것인가? -->
			<bean class="spring.intercepter.AuthCheckIntercepter" />
		</mvc:interceptor>
	</mvc:interceptors>
    ```

* MvcConfig
    ```java
    @Configuration
    @EnableWebMvc
    public class MvcConfig extends WebMvcConfigurerAdapter{

        @Override
        public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
            configurer.enable();
        }

        // return으로 String에 주소를 적어줄떄 앞뒤에 붙을 내용
        @Override
        public void configureViewResolvers(ViewResolverRegistry registry) {
            registry.jsp().prefix("/WEB-INF/views/").suffix(".jsp");
        }
        
        // /main, / 이라는 경로로 들어오는 경우 main.jsp로 바로 연결 시켜주세요
        @Override
        public void addViewControllers(ViewControllerRegistry registry) {
            registry.addViewController("/main").setViewName("main");
            registry.addViewController("/").setViewName("main");
        }
        
        // JSP 라벨 추가
        @Bean
        public MessageSource messageSource() {
            ResourceBundleMessageSource msgSrc = new ResourceBundleMessageSource();
            msgSrc.setBasename("message.label");
            msgSrc.setDefaultEncoding("UTF-8");
            return msgSrc;
        }

        // 인터셉터 적용
        @Override
        public void addInterceptors(InterceptorRegistry registry) {
            registry.addInterceptor(authCheckIntercepter()).addPathPatterns("/edit/**");
        }
        
        @Bean
        public AuthCheckIntercepter authCheckIntercepter() {
            return new AuthCheckIntercepter();
        }
    }
    ```

## MemberConfig 클래스 설정
* 기존 spring-member.xml
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
	
	<bean id="authService" class="spring.service.AuthService">
		<property name="memberDao" ref="dao" />
	</bean>
    ```

* MemberConfig
    ```java
    @Configuration
    @EnableTransactionManagement
    public class MemberConfig {

        // 트랜젝션 관리 객체
        @Bean
        public DataSourceTransactionManager transactionManager() {
            DataSourceTransactionManager txMgr = new DataSourceTransactionManager();
            txMgr.setDataSource(dataSource());
            return txMgr;
        }
        
        // DB 연결을 위해서 연결객체를 생성하기 위한 커넥션풀(DataSource)
        @Bean
        public DataSource dataSource() {
            ComboPooledDataSource ds = new ComboPooledDataSource();
            
            try {
                ds.setDriverClass("oracle.jdbc.OracleDriver");			
            } catch(PropertyVetoException e) {
                throw new RuntimeException(e);
            }
            ds.setJdbcUrl("jdbc:oracle:thin:@localhost:1521:xe");
            ds.setUser("green");
            ds.setPassword("1234");
            ds.setMaxPoolSize(20);
            
            return ds;
        }
        
        // jdbcTemplate
        @Bean
        public JdbcTemplate jdbcTemplate() {
            return new JdbcTemplate(dataSource());
        }
        
        // DAO
        @Bean
        public MemberDao dao() {
            return new MemberDao(jdbcTemplate());
        }
        
        @Bean
        public MemberRegisterService regSvc() {
            return new MemberRegisterService(dao());
        }
        
        @Bean
        public ChangePasswordService changePwdSvc() {
            return new ChangePasswordService(dao());
        }
        
        @Bean
        public AuthService authService() {
            // 생성자 주입과 setter주입의 차이 확인
            AuthService as = new AuthService();
            as.setMemberDao(dao());
            return as;
        }
    }
    ```

## ControllerConfig 클래스 설정
* 기존 spring-controller.xml
    ```xml
    <bean id="registerController" class="spring.controller.RegisterController">
		<property name="memberRegisterService" ref="regSvc" />
	</bean>
	
	<!-- <bean class="spring.controller.MainController"></bean> -->
	<bean class="spring.controller.SurveyController"></bean>
	
	<bean class="spring.controller.LoginController">
		<property name="authService" ref="authService" />
	</bean>
	
	<bean class="spring.controller.LogoutController"></bean>
	
	<bean class="spring.controller.ChangePwdController">
		<property name="changePasswordService" ref="changePwdSvc" />
	</bean>
	
	<bean class="spring.controller.MemberListController">
		<property name="dao" ref="dao" />
	</bean>
	
	<bean class="spring.controller.MemberDetailController">
		<property name="dao" ref="dao" />
	</bean>
	
	<bean class="spring.controller.CommonExceptionHandler" />
    ```

* ControllerConfig
    ```java
    @Configuration
    public class ControllerConfig {

        // MemberConfig에서 가져오기 위한 어노테이션
        @Autowired
        private MemberDao memberDao;
        
        @Autowired
        private ChangePasswordService changePwdSvc;
        
        @Autowired
        private AuthService authSvc;
        
        @Autowired
        private MemberRegisterService regSvc;
        
        @Bean
        public RegisterController registerController() {
            RegisterController rc = new RegisterController();
            rc.setMemberRegisterService(regSvc);
            return rc;
        }
        
        @Bean
        public SurveyController surveyController() {
            return new SurveyController();
        }
        
        @Bean
        public LoginController loginController() {
            LoginController lc = new LoginController();
            lc.setAuthService(authSvc);
            return lc;
        }
        
        @Bean
        public LogoutController logoutController() {
            return new LogoutController();
        }
        
        @Bean
        public ChangePwdController changePwdController() {
            ChangePwdController cpc = new ChangePwdController();
            cpc.setChangePasswordService(changePwdSvc);
            return cpc;
        }
        
        @Bean
        public MemberListController memberListController() {
            MemberListController mlc = new MemberListController();
            mlc.setDao(memberDao);
            return mlc;
        }
        
        @Bean
        public MemberDetailController memberDetailController() {
            MemberDetailController mdc = new MemberDetailController();
            mdc.setDao(memberDao);
            return mdc;
        }
        
        @Bean
        public CommonExceptionHandler commonExceptionHandler() {
            return new CommonExceptionHandler();
        }
    }
    ```