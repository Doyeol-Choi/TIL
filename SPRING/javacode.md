## 자바 코드 설정 기초
* 자바를 이용해서 설정하는 것은 XML과 크게 다르지 않다.

* <span style="color:yellowgreen">자바코드가 스프링 설정이라는 것을 알려주기 위한 어노테이션(@Configuration)을 클래스에서 설정해두고 각각의 코드가 빈 객체임을 알려주기 위한 어노테이션(@Bean)을 설정해 둔다.</span>

* 차이점
  - 자바 코드를 이용해서 빈 객체를 생성
  - AnnotationConfigApplicationContext클래스를 이용해서 컨테이너를 생성함

    ```java
    @Configuration		// 스프링 설정 파일이다.
    public class JavaConfig {
        
        // <bean id="memberDao" class="spring.dao.MemberDao"></bean>
        
        // 자바 설정은 빈을 메서드로 등록
        @Bean	// 스프링 컨테이너에 빈을 등록
        public MemberDao memberDao() {
            return new MemberDao();
        }
        
        // <bean id="memberRegSvc" class="spring.service.MemberRegisterService"></bean>
        @Bean
        public MemberRegisterService memberRegSvc() {		// 생성자로 의존주입 받았던 경우
            return new MemberRegisterService(memberDao());	// Dao 객체 주입
        }
        
        @Bean
        public MemberPrinter printer() {
            return new MemberPrinter();
        }
        
        @Bean
        public MemberInfoPrinter infoPrinter() {		// setter로 의존 주입 받았던 경우
            MemberInfoPrinter infoPrinter = new MemberInfoPrinter();
            infoPrinter.setDao(memberDao());
            infoPrinter.setPrinter(printer());
            
            return infoPrinter;
        }
    }
    ```
    ```java
    public class Main01 {

	public static void main(String[] args) {
		// 자바 설정파일을 이용한 스프링 동작
		ApplicationContext ctx = new AnnotationConfigApplicationContext(JavaConfig.class);
		
		MemberRegisterService regSvc = ctx.getBean("memberRegSvc", MemberRegisterService.class);
		
		MemberInfoPrinter infoPrt = ctx.getBean("infoPrinter", MemberInfoPrinter.class);
		
		// 회원가입 진행
		RegisterRequest rr = new RegisterRequest();
		rr.setEmail("hong@naver.com");
		rr.setName("홍길동");
		rr.setPassword("1234");
		rr.setConfirmPassword("1234");
		
		regSvc.regist(rr);
		
		// 등록된 회원 정보 출력
		infoPrt.printMemberInfo("hong@naver.com");		
        }
    }
    ```

* 자바코드로 설정 하는 경우 별도의 작업이 없어도 자동 주입 기능을 사용할 수 있다.

* 자동 주입 대상이 되는 set메소드에는 @Autowired를 붙여야 한다.

* 생성자는 자동 주입 대상이 될수 없다.

## 두 개 이상의 클래스를 사용한 설정
* XML과 마찬가지로 자바 클래스도 2개 이상의 파일로 나눠서 활용이 가능하다.

* 다만 다른 클래스에 존재하는 객체를 메소드로 바로 불러오는 것은 불가능하므로 주입에 사용할 빈 객체를 주입 받은 다음 내 객체에 주입을 할 수 있다.

* 스프링은 @Configuration 어노테이션이 적용된 클래스는 자동 주입 적용 대상이 된다.
    ```java
    @Configuration
    public class JavaConf01 {
        
        @Bean	
        public MemberDao memberDao() {
            return new MemberDao();
        }
        
        @Bean
        public MemberRegisterService memberRegSvc() {
            return new MemberRegisterService(memberDao());
        }
    }
    ```
    ```java
    @Configuration
    public class JavaConf02 {
        
        @Autowired
        private JavaConf01 javaConf01;
        // 다른 설정파일을 주입받는다.
        
        @Bean
        public MemberPrinter printer() {
            return new MemberPrinter();
        }
        
        @Bean
        public MemberInfoPrinter infoPrinter() {
            MemberInfoPrinter infoPrinter = new MemberInfoPrinter();
            infoPrinter.setDao(javaConf01.memberDao());
            infoPrinter.setPrinter(printer());
            
            return infoPrinter;
        }
    }
    ```

* 또한 설정 객체를 가져올 때도 두 설정 클래스 모두 가져와야 한다.

* XML과 마찬가지로 Import방식으로 다른 설정 클래스를 가져오는 것도 가능하다.

* 단 설정파일이 여러 개인 경우 배열의 형태로 가져온다.
    ```java
    // 객체 방식
    ApplicationContext ctx = new AnnotationConfigApplicationContext(JavaConf01.class, JavaConf02.class);
    // Import 방식
    @Import({JavaConf01.class, JavaConf02.class, ….})
    ```

## 클래스 설정 파일과 XML 설정 파일의 혼합
* 스프링은 XML설정 과 자바 설정을 같이 지원한다.

* 어떤 때에는 XML이 어떤 때에는 자바 설정이 편할 수 있으므로 이런 경우 각각의 편한 방식으로 설정한 후 하나로 합쳐서 처리 할 수 있다.

1) XML을 기준으로 Java를 통합 : main-config.xml + JavaSubConfig.java
    - main-config.xml
    ```xml
    <context:annotation-config/>	<!-- 자동 주입 활성화 -->
	
	<!-- Java 설정 읽어와서 합치기 -->
	<bean class="spring.config.JavaSubConfig"/>
    <!-- 설정파일을 다른 곳에 주입할 것이 아니므로 id값은 불필요 -->
	
	<!-- DAO 빈 -->
	<bean id="dao" class="spring.dao.MemberDao"/>
	
	<!-- MemberRegistService 빈 -->
	<bean id="regSvc" class="spring.service.MemberRegisterService">
		<constructor-arg ref="dao"/>
	</bean>
    ```
    - JavaSubConfig.java
    ```java
    @Configuration
    public class JavaSubConfig {
        
        // 설정파일을 하나로 합치는 경우 주입대상만 Autowired 처리한다.
        @Autowired
        private MemberDao dao;
        
        @Bean
        public MemberPrinter printer() {
            return new MemberPrinter();
        }
        
        @Bean
        public MemberInfoPrinter infoPrinter() {
            MemberInfoPrinter info = new MemberInfoPrinter();
            info.setDao(dao);
            info.setPrinter(printer());
            
            return info;
        }
    }
    ```
    - 설정 가져오기
    ```java
    // 1) XML을 기준으로 Java를 합쳐서 처리
	ApplicationContext ctx = new GenericXmlApplicationContext("classpath:main-config.xml");
    ```

2) Java를 기준으로 XML을 통합 : JavaMainConfig.java + Sub-config.xml
    - JavaMainConfig.java
    ```java
    @Configuration
    @ImportResource("classpath:sub-config.xml")
    public class JavaMainConfig {
        
        @Bean
        public MemberDao memberDao() {
            return new MemberDao();
        }
        
        @Bean
        public MemberRegisterService memberRegSvc() {
            return new MemberRegisterService(memberDao());
        }
    }
    ```
    - Sub-config.xml
    ```xml
    <!-- MemberPrinter -->
	<bean id="printer" class="spring.printer.MemberPrinter"/>
	
	<!-- MemberInfoPrinter -->
	<bean id="infoPrinter" class="spring.printer.MemberInfoPrinter">
		<property name="dao" ref="memberDao"/>
		<property name="printer" ref="printer"/>
	</bean>
    ```
    - 설정 가져오기
    ```java
    // 2) Java를 기준으로 XML을 합쳐서 처리
	ApplicationContext ctx = new AnnotationConfigApplicationContext(JavaMainConfig.class);
    ```
