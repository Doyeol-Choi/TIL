# DI (Dependency Injection)
* DI는 Dependency Injection의 약자로 ‘의존 주입’ 이라고 해석된다. 여기서 의존이란 객체간의 의존을 의미한다.

* 한 클래스가 동작하기 위해서 반드시 다른 클래스를 필요로 하는 것
이것을 ‘의존한다’라고 표현한다.

    ```java
    public class MemberRegisterService {	// 회원 가입 기능
	
	private MemberDao dao = new MemberDao();
	
	public void regist(RegisterRequest req) {
		Member member = dao.selectByEmail(req.getEmail());
		
		if(member != null) {
			throw new AlreadyExistingMemberException("중복 : " + req.getEmail());
		}
		Member newMember = new Member(req.getEmail(), req.getPassword(), req.getName(), new Date());
		
		dao.insertMember(newMember);
	    }
    }
    ```

* 여기서 눈여겨 봐야 할 부분은 MemberRegisterService 클래스가 DB 처리를 위해서 memberDao의 메소드를 사용한다는 점이다.
  - regist메서드를 동작시키려면 반드시 memberDao클래스의 메서드가 필요하다.

* MemberRegisterService 클래스가 memberDao 클래스에 의존한다고 볼 수 있다. 이때 의존하는 대상이 있다면 그 대상을 구하는 방법이 필요하게 된다.

* <span style="color:greenyellow">첫번째 방법은 의존 대상을 ‘직접 생성’하는 것이다.</span>
    ```java
    private MemberDao memberdao = new MemberDao();
    ```

* MemberRegisterService의 객체가 생성된다면 내부적으로 MemberDao객체가 자동으로
함께 생성된다. <span style="color:greenyellow">다만 이렇게 직접 생성하는 경우 유지보수의 관점에서 문제점이 생길 수 있다.</span>

* <span style="color:greenyellow">의존 객체를 구하는 두번째 방법 – DI(의존 주입)이다.<span>

* 의존 주입은 의존하는 객체를 직접 생성하지 않고 전달 받는 방식을 사용한다.

* 앞서 살펴본 예시를 다음과 같이 바꿔 본다.
    ```java
    public class MemberRegisterService {	// 회원 가입 기능
	
    //private MemberDao dao = new MemberDao();	// 의존 객체를 직접 생성
	
	private MemberDao dao;
	
	public MemberRegisterService(MemberDao memberDao) {
		this.dao = memberDao;   // 생성자를 통한 주입
	}
    ```

* memberdao 객체를 생성자를 통해서 ‘주입’받고 있다. <span style="color:greenyellow">이러한 방법이 유지 보수 관점에서 대단히 중요하다.</span>

### 객체 조립기
* 객체를 생성하기 위해서 의존을 주입한다고 배웠는데 그러면 실제 객체를 생성하는 곳은
어디일까??

* 보통 자바에서는 main()을 생성할 수 있는데 보통 객체를 생성하고 주입하는 클래스를 따
로 작성한다.

* 이런 클래스를 서로 다른 객체를 주입한다고 해서 이런 클래스를 조립기라고 한다.
    ```java
    public class Assembler {

        private MemberDao dao;
        private MemberRegisterService regSvc;
        private ChangePasswordService pwdSvc;
        
        public Assembler() {
            dao = new MemberDao();          // 서비스 클래스 밖
            regSvc = new MemberRegisterService(dao);    // 생성된 인스턴스를 주입
            pwdSvc = new ChangePasswordService(dao);
        }

        public MemberDao getDao() {
            return dao;
        }

        public MemberRegisterService getRegSvc() {
            return regSvc;
        }

        public ChangePasswordService getPwdSvc() {
            return pwdSvc;
        }
    }
    ```

--------------------------------------------------------------------------------------
## 스프링 DI
* 스프링은 앞서 본 조립기와 동일한 기능을 가진다.

* 적절히 필요한 객체를 생성하고 생성한 객체에 의존을 주입해 준다.

* 다만 앞서 본 Assembler와의 차이점은 스프링이 좀 더 범용적으로 사용 가능한 조립기
라는 점이다.

* 우선 스프링이 어떤 객체를 생성하고 의존을 어떻게 주입하는지 설정 정보를 작성해 본
다.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- 스프링이 만들게 될 객체를 작성 -->
        <bean id="memberDao" class="spring.dao.MemberDao">	
        </bean>

        <bean id="memberRegSvc" class="spring.service.MemberRegisterService">
            <constructor-arg ref="memberDao"/>
        </bean>
        
        <bean id="changePwdSvc" class="spring.service.ChangePasswordService">
            <constructor-arg ref="memberDao"/>
        </bean>
        
        <!-- 추가 빈 등록 -->
        <bean id="printer" class="spring.printer.MemberPrinter"></bean>
        
        <bean id="listPrinter" class="spring.printer.MemberListPrinter">
        <!-- 생성자 매개값이 두개 이상인 상황 -->
            <constructor-arg ref="memberDao"/>
            <constructor-arg ref="printer"/>
        </bean>
        
        <!-- setter 메서드를 통해서 주입 -->
        <bean id="infoPrinter" class="spring.printer.MemberInfoPrinter">
            <property name="dao" ref="memberDao"/>
            <property name="printer" ref="printer"/>
        </bean>
        
        <!-- 기본 타입 데이터 주입 -->
        <bean id="versionPrinter" class="spring.printer.VersionPrinter">
            <!-- 두가지 방법 다 똑같다 -->
            <constructor-arg value="5"/>
            <constructor-arg>
                <value>3</value>
            </constructor-arg>
        </bean>
        
        <bean id="versionPrinter2" class="spring.printer.VersionPrinter">
            <property name="majorVersion" value="5"/>
            <property name="minerVersion">
                <value>3</value>
            </property>
        </bean>
    </beans>
    ```
* 위와 같이 설정파일을 생성하면 설정파일을 통해 스프링 컨테이너를 생성해야 한다.
    ```java
    ApplicationContext ctx = new GenericXmlApplicationContext("classpath:appCtx.xml");
    ```

* 생성 된 컨테이너로부터 객체를 가져다 사용할 수 있다.
    ```java
    MemberRegisterService regSvc = ctx.getBean("memberRegSvc", MemberRegisterService.class);
    ```

## DI설정 : 생성자 방식
* 앞서 살펴 본 바는 생성자를 통해서 의존 객체를 주입 받았다.
    ```java
    public class MemberRegisterService {
        private MemberDao memberDao;
        public MemberRegisterService(MemberDao memberDao) {
            this.memberDao = memberDao;
        }
    ```
* 스프링 XML 설정에서도 생성자를 이용해서 의존 객체를 주입받기 위해 사용한 태그는
&lt;constructor-arg ref="memberDao" /&gt; 이다.

* 생성자를 통해 전달할 의존 객체가 2개 이상이면 &lt;constructor-arg&gt;태그를 순서대로 사용하면
된다.

## DI설정 : 설정 메소드 방식
* 스프링은 생성자 뿐 아니라 setter 메소드를 통해서 의존 객체를 주입 받는 방식을 지원 한다.

* 프로퍼티 설정 메소드는 다음과 같다.
  - 메소드의 이름은 set으로 시작한다.
  - set 뒤에 붙는 이름은 프로퍼티 이름이며 첫글자는 대문자로 한다.
  - 한 개의 파라미터를 가지며, 파라미터 타입이 곧 프로퍼티의 타입이다.
* 스프링 설정 xml파일에서는 &lt;property name="memberDao" ref="memberDao" /&gt; 형태로 객체를 주입받는다.

## DI 설정 : 기본 데이터 타입 값 설정
* 스프링 설정으로 다른 객체를 주입받기 위해서 &lt;constructor-arg&gt;태그나 &lt;property&gt; 태그의 ref 속성을 사용했다.

* 그러나 객체를 주입받는 것이 아닌 기본 타입의 값을 주입받을 때는 어떻게 설정해야 할까?
    ```xml
    <!-- 기본 타입 데이터 주입 -->
	<bean id="versionPrinter" class="spring.printer.VersionPrinter">
		<!-- 두가지 방법 다 똑같다 -->
		<constructor-arg value="5"/>
		<constructor-arg>
			<value>3</value>
		</constructor-arg>
	</bean>
	
	<bean id="versionPrinter2" class="spring.printer.VersionPrinter">
		<property name="majorVersion" value="5"/>
		<property name="minerVersion">
			<value>3</value>
		</property>
	</bean>
    ```
--------------------------------------------------------------------------------------
## 2개의 설정 파일 사용하기
* 스프링 설정파일을 XML에 작성했다. 그러나 모든 설정을 하나의 XML에 작성하면 설정이 늘
어남에 따라 관리하기가 어려워 질 것이다.

* 스프링은 이런 XML을 효율적으로 관리하기 위해서 두개 이상의 xml로 나눠서 관리 할 수 있
는 기능을 제공한다.

* 나눠진 두개 이상의 XML을 사용하는 방법
  - 첫번째 : 하나의 xml에 import 태그로 합치는 방법
    ```xml
    <import resource="classpath:Ctx02.xml"/>
    ```
  - 두번째 : GenericXmlApplicationContext의 생성자 매개변수를 여러 개의 xml 주소를 담은 배열로 가져오는 방법
    ```java
    ctx = new GenericXmlApplicationContext({"classpath:Conf01.xml","classpath:Conf02.xml"});
    ```