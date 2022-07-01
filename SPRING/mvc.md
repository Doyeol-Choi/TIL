## 웹 프로젝트 생성하기
* spring legacy project로 프로젝트를 생성한다.

* 생성된 프로젝트 POM.xml 자동 생성되는 기존의 내용을 지우고 다음 내용을 추가한다.
  - Jsp-api
  - Servlet-api
  - Spring-webmvc
  - 등의 버전을 바꿔주거나 필요한 라이브러리를 메이븐 레포지트리에서 복사해온다.

* 이클립스 또는 STS 에 톰캣 서버를 등록한다.

* 스프링 MVC를 실행하기 위한 설정을 진행한다.
  - spring-mvc.xml 을 등록한다 (mvc를 체크)
    ```xml
    <mvc:annotation-driven />
	
	<mvc:default-servlet-handler/>
	
	<mvc:view-resolvers>
		<mvc:jsp prefix="/WEB-INF/views/" />
	</mvc:view-resolvers>
    ```

* 스프링 MVC를 실행하기 위한 설정을 진행한다.
  - 자동 생성된 web.xml을 수정한다.
    ```xml
    <servlet>
 		<servlet-name>appServlet</servlet-name>
 		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
 		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
				classpath:spring-mvc.xml
				classpath:spring-controller.xml
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

* 간단한 서블릿과 JSP로 스프링 프로젝트를 실행해본다.
  - HomeController 클래스 생성
  - spring controller.xml에 빈 등록
  - xml 설정파일을 web.xml에 등록
  - hello.jsp 생성
