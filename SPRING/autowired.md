# 자동 주입
* 기존의 의존관계를 명시해서 의존 주입 기능을 사용할 수도 있지만 스프링은 보다 편리하게 자동 주입 기능을 제공해 준다.

* 자동 주입 기능을 사용하기 위해서
  - 자동 주입 대상이 되는 클래스에 @Autowired 어노테이션을 사용
  - 스프링 컨피그 파일 생성시 context를 체크한다.
  - XML 설정에 &lt;context:annotation-config /&gt; 태그 추가

    ```xml
    <!-- 설정파일에서 property나 constructor를 쓰지 않고 -->
    <bean id="infoPrinter" class="spring.printer.MemberInfoPrinter">
		<!-- <property name="dao" ref="memberDao"/> -->
		<!-- <property name="printer" ref="printer"/> -->
	</bean>
    ```
    ```java
    // 어노테이션을 통해 입력해준다.
    @Autowired
	private MemberPrinter printer;
	
	@Autowired
	public void setDao(MemberDao dao) {	
		this.dao = dao;
	}
    ```

## 빈 객체가 2개일 때
* @Autowired 애노테이션을 적용시 자동으로 주입할 객체를 선택해준다.

* 그러나 동일한 타입의 주입할 객체가 두개 이상이라면 에러를 발생시킨다.

* @Qualifier 애노테이션을 사용한다.
  - 빈의 한정자를 지정한다.
  - @Autowired 로 주입된 대상에 @Qualifier의 값으로 한정자를 지정해준다.

    ```xml
    <bean id="printer1" class="spring.printer.MemberPrinter">
		<qualifier value="chk01"/>
	</bean>
	
	<bean id="printer2" class="spring.printer.MemberPrinter"></bean>
    ```

    ```java
    @Autowired
	@Qualifier("chk01")     // 우선순위 설정
	private MemberPrinter printer;
    ```

### @Autowired의 필수 여부 지정
* @Autowired 애노테이션을 적용하면 반드시 주입할 의존 객체가 있어야 한다

* 주입할 의존객체가 없는 경우 에러가 발생한다.

* @Autowired뒤에 (required=false)를 붙인다. => 필수요건을 완화시킴

* 임시로 스킵 진행하는 것으로 후에 결국 에러가 발생할 확률이 높다.

## @Autowired 애노테이션 적용 순서
* @Autowired 애노테이션 적용 순서
    1) 타입이 같은 빈 객체를 검색
    2) 타입이 같은 빈 객체가 두개 이상 존재 시 @Qualifier로 지정한 빈 객체를 검색
    3) @Qualifier가 없는 경우 이름이 같은 빈 객체 검색

* 위 3가지 경우가 아닌 경우는 예외를 발생 시킨다.

## @Autowired 애노테이션과 파라미터 개수
* @Autowired 애노테이션의 적용 대상 파라미터가 여러 개인 경우에도 사용이 가능하다.

* 여러 개의 파라미터 중 개별적으로 의존 객체를 지정해줄 수도 있다.
    ```java
    private MemberDao memDao;
    private MemberPrinter printer;
    @Autowired
    public void injectDependency(MemberDao memberDao, @Qualifier("sysout") MemberPrinter printer) {
        this.memDao = memberDao;
        this.printer = printer;
    }
    ```

## 자동 주입과 명시적 주입간의 관계
* 자동 주입과 명시적 주입을 함께 사용하는 것이 가능하다.

* 다만 두개가 동시에 사용되는 경우 명시적 주입을 우선 사용한다.