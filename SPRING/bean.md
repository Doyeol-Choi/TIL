# 빈 라이프사이클과 범위
* 스프링
컨테이너는 초기화와 종료라는 라이프사이클을 가진다
  - 컨테이너의 초기화 => 빈 객체의 생성과 의존 객체 주입 및 초기화
  - 컨테이너의 종료 => 빈 객체의 소멸

    ```java
    // 컨테이너 초기화
    ApplicationContext
    ctx = new GenericXmlApplicationContext("classpath:appCtx.xml");

    // 컨테이너 사용
    Greeter g = ctx.getBean ("greeter", Greeter.class);

    // 컨테이너 종료
    ctx.close();
    ```

* 스프링 컨테이너는 빈 객체의 라이프 사이클을 관리한다.
  - 객체 생성 => 의존 설정 => 초기화 => 소멸

* 일반적으로 서버 컴퓨터는 항상 켜져있도록 유지하기 때문에 빈이 소멸될 일이 없다.

* 스프링 컨테이너가 초기화 할 때
  - 빈 객체 생성
  - 의존 설정
  - 빈 객체의 초기화 수행

* 스프링 컨테이너가 종료 될 때
  - 빈 객체 소멸

* 빈 객체가 생성되고 소멸하기 위해서 빈 객체의 지정한 메서드를 호출하는데 그 내용을 스프링에서는 다음 두 인터페이스에 정의하고 있다.
    ```java
    // 빈 생성
    public interface InitializingBean {
        void afterPropertiesSet() throws Exception;
    }
    // 빈 소멸
    public interface DisposableBean {
        void destroy() throws Exception;
    }
    ```

* 빈 객체가 생성되고 소멸하기 위해서 빈 객체의 지정한 메서드를 호출하는데 그 내용을 스프링에서는 다음 두 인터페이스에 정의하고 있다.
    ```java
    public class Client1 implements InitializingBean, DisposableBean{

        private String host;

        public void setHost(String host) {
            this.host = host;
            System.out.println("Client.setHost() 실행");
        }
        
        public void send() {	// 실제 사용할 메서드
            System.out.println("Client.send() to + " + host);
        }

        @Override
        public void destroy() throws Exception {
            // 빈 소멸시 작동할 메서드
            System.out.println("빈 소멸 : Client.destory() 실행");
        }

        @Override
        public void afterPropertiesSet() throws Exception {
            // 빈 초기화시 작동할 메서드
            System.out.println("빈 초기화 : Client.afterPropertiesSet() 실행");
        }
    }
    ```
    ```xml
    <bean class="spring.bean.Client1" id="c1">
		<property name="host" value="웹서버1"/>
	</bean>
    ```
    ```java
    public static void main(String[] args) {
		
		// 스프링 빈 생성 안하고 객체 생성
		Client1 c0 = new Client1();
		c0.setHost("웹서버1");
		c0.send();
		
		System.out.println("------------------------------------------");
		// 스프링 빈으로 등록하고 객체 생성
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:appCtx.xml");	// 빈 초기화
		
		Client1 c1 = ctx.getBean("c1", Client1.class);
		
		c1.send();
		
		ctx.close();	// 빈 소멸
	}
    ```

* 상황에 따라 위 두 인터페이스를 사용할 수 없을 때가 존재한다.

* 이런 경우 스프링 설정에서 직접 메서드를 지정해 줄 수 있다.
    ```java
    public class Client2 {
	
        private String host;

        public void setHost(String host) {
            this.host = host;
            System.out.println("Client2.setHost() 실행");
        }
        
        public void send() {	// 실제 사용할 메서드
            System.out.println("Client2.send() to + " + host);
        }
        
        // 빈 초기화 및 소멸시 사용할 메서드를 임의로 생성
        public void open() throws Exception {	// 초기화시 사용할 메서드
            System.out.println("빈 초기화 : Client2.open() 실행");
        }
        
        public void close() throws Exception {	// 소멸시 사용할 메서드
            System.out.println("빈 소멸 : Client2.close() 실행");
        }
    }
    ```
    - xml 설정 파일 방식
    ```xml
    <bean class="spring.bean.Client2" id="c2" init-method="open" destroy-method="close"> <!-- 초기화시 사용할 메서드와 소멸시 사용할 메서드 -->
		<property name="host" value="웹서버2"/>
	</bean>
    ```
    - xml 설정 파일 가져와서 동작
    ```java
    public static void main(String[] args) {
		
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:appCtx.xml");	// 빈 초기화
		
		//임의의 초기화, 소멸 메서드
		Client2 c2 = ctx.getBean("c2", Client2.class);
		
		c2.send();
		
		ctx.close();
	}
    ```
    - java 설정 파일 방식
    ```java
    @Configuration
    public class JavaConfig {
        
        @Bean(initMethod = "open", destroyMethod = "close")
        public Client2 c2() {
            Client2 c2 = new Client2();
            c2.setHost("웹 서버2");
            return c2;
        }
    }
    ```
    - java 설정 파일 가져와서 동작
    ```java
    public static void main(String[] args) {
		AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(JavaConfig.class);	// 빈 초기화
		
		//임의의 초기화, 소멸 메서드
		Client2 c2 = ctx.getBean("c2", Client2.class);
		
		c2.send();
		
		ctx.close();
	}
    ```

## 객체의 범위
* 앞서 살펴본 바로 스프링 컨테이너의 빈 객체는 동일한 이름의 경우 싱글톤 범위를 갖는다고 배웠다.

* 사용 빈도가 낮지만 예외적으로 빈 객체를 프로토 타입으로 설정할 수 있는데 이 경우 일반적인 라이프 사이클을 따르지 않는다.
  - -> 스프링 컨테이너에 의해 빈 객체를 생성하고 의존 설정후 초기화까지 수행하지만 컨테이너가 종료한다고 소멸 메서드를 실행하지는 않는다. => 직접 소멸 처리를 해야한다

* 빈 객체의 범위를 프로토타입으로 지정하면 매번 빈 객체를 구할 때마다 새로운 객체를 생성해 낸다.
    ```java
    public class Client3 {
        private String host;

        public void setHost(String host) {
            this.host = host;
            System.out.println("Client3.setHost() 실행");
        }
        
        public void send() {	// 실제 사용할 메서드
            System.out.println("Client3.send() to + " + host);
        }
    }
    ```
    - xml 설정 파일
    ```xml
    <!-- 범위의 기본값 scope="singleton" -->
	<bean class="spring.bean.Client3" id="c3" scope="prototype">
		<property name="host" value="프로토 서버"/>
	</bean>
    ```
    - java 설정 파일
    ```java
    @Bean
	@Scope("prototype")	// 범위가 프로토타입으로 작동
	public Client3 c3() {
		Client3 c3 = new Client3();
		c3.setHost("프로토 서버");
		return c3;
	}
    ```
    - main에서 사용하기
    ```java
    public static void main(String[] args) {
		
		useXml();
		System.out.println("=====================================");
		useJava();	
	}

	private static void useJava() {
		AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(JavaConfig.class);
		
		// 사용할 빈 객체
		Client3 c3_1 = ctx.getBean("c3", Client3.class);
		Client3 c3_2 = ctx.getBean("c3", Client3.class);
		
		System.out.println("(c3_1 == c3_2) : " + (c3_1 == c3_2));	// 범위 프로토타입인 경우 => false
		ctx.close();
	}

	private static void useXml() {
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:appCtx.xml");
		
		// 사용할 빈 객체
		Client3 c3_1 = ctx.getBean("c3", Client3.class);
		Client3 c3_2 = ctx.getBean("c3", Client3.class);
		
		System.out.println("(c3_1 == c3_2) : " + (c3_1 == c3_2));
		ctx.close();
	}	
    ```