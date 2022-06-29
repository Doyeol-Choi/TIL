# AOP (aspect-oriented programming, 관점 지향 프로그래밍)
* 준비한 예제의 두 구현 클래스의 실행 시간을 출력하고자 한다.

* 일반적인 방법은 메서드 시작과 끝에 시간을 구하고 이것을 출력하는 것이다.

* 예제 파일
    ```java
    public class ImpeCalculator implements Calculator {
        // 재귀 없이 팩토리얼 구하는 방법
        // 동작 시간 측정 코드
        @Override
        public long factorial(long num) {
            long start = System.currentTimeMillis();
            long result = 1;
            for(long i=1; i<=num; i++) {
                result *= i;
            }
            long end = System.currentTimeMillis();
            System.out.printf("ImpeCalculator.factorial(%d) 실행시간 : %d \n", num, (end-start));    
            return result;
        }
    }
    ```
    ```java
    public class RecCalculator implements Calculator {
        // 재귀함수를 통해서 팩토리얼 구하는 방법	(함수 내에서 자기 자신을 다시 호출 하는것)
        // 동작 시간 측정 코드
        @Override
        public long factorial(long num) {
            
            long start = System.currentTimeMillis();
            
            try {
                if(num==0) {
                    return 1;
                } else {
                    return num * factorial(num*-1);			
                }
            } finally {
                long end = System.currentTimeMillis();	
                System.out.printf("RecCalculator.factorial(%d) 실행시간 : %d \n", num, (end-start));
            }
        }

    }
    ```

* 위 예제를 main에서 실행시켜보면
    ```java
    public static void main(String[] args) {
		// 두가지 방법의 팩토리얼을 구해서 시간을 측정해 봅시다.
		
		ImpeCalculator impeCal = new ImpeCalculator();
		long sevenFactorial1 = impeCal.factorial(7);
		
		RecCalculator recCal = new RecCalculator();
		long sevenFactorial2 = recCal.factorial(7);
		
		System.out.println("sevenFactorial1의 결과 : " + sevenFactorial1);
		System.out.println("sevenFactorial2의 결과 : " + sevenFactorial2);
	}
    // 결과가 ms보다 낮은값이 나와서 각 단계 실행값이 0~1ms로 표시된다.
    ```

* 위 예제에서 실행시간을 밀리초가 아닌 나노초로 구하고자 한다면 어떻게 해야할까?

* 모든 코드에서 시간을 구하는 부분을 모두 바꿔주어야 할 것이다.

* 이때 기존의 코드는 수정하지 않고 코드 중복을 해결하는 방법이 프록시 객체이다.
    ```java
    public class ExeTimeCalculator implements Calculator {
        // 공통의 기능(시간)을 구현하는 프록시 객체
        
        private	Calculator calc;
        
        public ExeTimeCalculator(Calculator calc) {
            this.calc = calc;
        }

        @Override
        public long factorial(long num) {
            
            //long start = System.currentTimeMillis();
            long start = System.nanoTime();
            
            /////////////////////////////////////////////////////////////
            // 핵심 코드 : 각 클래스(Calculator를 구현받은 객체)의 핵심 기능
            long result = calc.factorial(num);
            /////////////////////////////////////////////////////////////
            
            //long end = System.currentTimeMillis();
            long end = System.nanoTime();
            System.out.printf("%s.factorial(%d) 실행시간 : %d \n", calc.getClass().getSimpleName(), num, (end-start));
            
            return result;
        }
    }
    ```
* 프록시 객체를 사용할 수 있도록 처음 두 예제의 시간을 구하는 코드는 삭제하거나 주석처리 해준다.

* 메인을 통해 실행시켜본다.
    ```java
    public static void main(String[] args) {
		// 팩토리얼을 구해봅시다 => 프록시를 거쳐서
		
		ExeTimeCalculator impeCal = new ExeTimeCalculator(new ImpeCalculator());
		long impeResult = impeCal.factorial(7);
		
		ExeTimeCalculator recCal = new ExeTimeCalculator(new RecCalculator());
		long recResult = recCal.factorial(7);
		
		System.out.println("impeResult 계산 결과 : " + impeResult);
		System.out.println("recResult 계산 결과 : " + recResult);
	}
    /*
    ImpeCalculator.factorial(7) 실행시간 : 2000 
    RecCalculator.factorial(7) 실행시간 : 1800 
    impeResult 계산 결과 : 5040
    recResult 계산 결과 : 5040
    */
    ```

* 위 결과로 알수있는 점
  - 기존 코드를 변경하지 않고 실행시간을 출력할 수 있다.
  - 실행 시간을 구하는 코드의 중복을 제거했다 . 변경하고 싶다면 해당 코드만 바꿔주면 된다.  
  <br>

* 이와 같이 핵심 기능의 실행은 다른 객체에 위임하고 부가적인 기능을 제공하는 객체를 프록시 객체라고 한다.

* 실제 핵심 기능을 실행하는 객체를 대상 객체라고 한다.

* 프록시 객체는 핵심 기능을 구현하지 않는 대신 여러 객체에 공통으로 적용해야 할 기능을 구현한다.

* <span style="color:yellowgreen">핵심코드와 여러 객체에 공통으로 적용가능한 객체를 구분함으로써 재사용성을 높이는 프로그래밍 기법을 AOP(Aspect Oriented Programming, 관점 지향 프로그래밍 기법) 이라고 한다.</span>

* AOP의 기본 개념은 핵심 기능에 공통의 기능을 삽입하는 것이다.
  - 컴파일 시점에 코드에 공통의 기능을 추가하는 방법
  - 클래스 로딩 시점에 바이트 코드에 공통의 기능을 추가하는 방법
  - <span style="color:yellowgreen">런타임에 프록시 객체를 생성해서 공통의 기능을 추가하는 방법</span>  
  <br>

* 스프링에서는 프록시 객체를 활용한 세번째 방식을 채택한다.

* 실제 스프링 AOP 는 프록시 객체를 자동으로 생성해 준다. 그러므로 개발자는 공통의 기능을 구현하기 위한 클래스만 알맞게 구현해 주면 된다.

* AOP의 주요 용어
  - Aspect : 여러 객체에 공통으로 적용되는 기능을 Aspect 라고 한다. 트랜젝션이나 보안등이 Aspect 의 좋은 예시이다.
  - Advice : 언제 공통 관심 기능을 핵심 로직에 적용할 지를 정의하고 있다. 예를 들어 메서드 호출전에 언제 트랜젝션 시작 기능(공통기능)을 적용 같은 경우이다.
  - Weaving : Advice를 핵심 로직 코드에 적용하는 것을 Weaving라고 한다.
  - Joinpoint : Advice를 적용 가능한 지점을 의미한다. 메서드 호출, 필드 값 변경 등이 Joinpoint에 해당한다. 스프링은 프록시를 이용해서 AOP 를 구현하기 때문에 메서드 호출에 대한 Joinpoint만 지원한다.
  - Pointcut : Joinpoint의 부분집합으로 실제로 Advice 가 적용되는 Joinpoint 를 나타낸다. 스프링에서는 정규 표현식이나 AspectJ의 문법을 이용하여 Pointcut을 정의할 수 있다.  
  <br>

* Aspect(기능), Advice(시점), Pointcut(누구에게)가 중요하다.

* Advice의 종류
  - Before Advice : 대상 객체의 메서드 호출 전에 공통의 기능을 실행한다.
  - After Return Advice : 대상 객체의 메서드가 익셉션이 없이 실행된 이후에 공통의 기능을 실행한다.
  - After Throwing Advice : 대상 객체의 메서드를 실행하는 도중에 익셉션이 발생한 경우에 공통의 기능을 실행한다.
  - After Advice : 대상 객체의 메서드를 실행하는 도중에 익셉션이 발생 했는지 여부에 상관없이 메서드 실행 후 공통의 기능을 실행한다.
  - Around Advice : 대상 객체의 메서드 실행 전, 후 또는 익셉션 발생 시점에 공통의 기능을 실행하는데 사용된다.

* 이중 가장 많이 사용되는 것은 Around Advice이다.

* 스프링에서 AOP 를 이용해서 공통의 기능을 구현하는 방법은 다음과 같은 절차를 따르면 된다.
  - 공통의 기능을 제공하는 Aspect 를 구현한다.
  - Aspect를 어디(Pointcut)에 적용할 지를 설정한다 . 즉 (Advice)를 설정한다.

* Aspect를 구현하는 방법에는 두가지가 있다.
  - XML 스키마 기반의 자바 POJO클래스(기본 자바 클래스)를 이용하는 방법
  - @Aspect 애노테이션을 이용하는 방법

## 스프링 AOP 구현 XML 스키마 기반
* XML 스키마를 이용하는 경우 XML 설정을 이용해서 Aspect를 어디에 적용할 지를 설정한다.

* 우선 적용할 Aspect 객체를 만든다.

    ```java
    public class ExeTimeAspect01 {	// 공통의 기능을 담을 Aspect 객체 : 어떤 핵심 기능(m(X))이 실행될때 구동되는가?
        // POJO 클래스        
        public Object m(ProceedingJoinPoint jp) throws Throwable {	// 스프링 프록시가 핵심기능을 가로채서 먼저 수행하도록 만들 메서드 m()
            // jp에 핵심기능에 관련된 정보가 들어있다.
            long start = System.nanoTime();
            
            ///////////////////////////////////////////////////////////
            Object result = jp.proceed();	// 핵심기능을 수행하세요.
            ///////////////////////////////////////////////////////////
            
            long end = System.nanoTime();
            System.out.println("실행 시간 : " + (end-start));
            
            return result;
        }
    }
    ```

* 위에서 구한 Aspect객체를 적용시키기 위해 XML스키마 설정을 한다.
    ```xml
    <!-- 공통의 기능을 제공한 객체를 빈으로 등록 -->
	<bean class="spring.aspect.ExeTimeAspect01" id="exeTimeAspect"/>
	
	<!-- AOP 설정 -->
	<aop:config>
		<aop:aspect id="mAspect" ref="exeTimeAspect">
			<!-- 한 덩어리의 공통 기능 묶음 -->
			<!-- 1. 공통 기능을 적용할 대상이 되는 핵심 기능 -->
			<aop:pointcut expression="execution(public * spring.calc..*(..))" id="pMethod"/>
			<!-- 2. 공통 기능을 언제 적용할 시기 -->
			<aop:around method="m" pointcut-ref="pMethod"/>
		</aop:aspect>
	</aop:config>
	
	<!-- 사용할 객체를 빈으로 등록 -->
	<bean class="spring.calc.ImpeCalculator" id="impeCalc"/>
	<bean class="spring.calc.RecCalculator" id="recCalc"/>
    ```

* Main 메서드에서 동작시켜본다.
    ```java
    public static void main(String[] args) {
		
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:appCtx01.xml");
		
		Calculator impeCalc = ctx.getBean("impeCalc", Calculator.class);
		long impeResult = impeCalc.factorial(7);
		System.out.println("impeCalc.factorial(7) = " + impeResult);
		
		Calculator recCalc = ctx.getBean("recCalc", Calculator.class);
		long recResult = recCalc.factorial(7);
		System.out.println("recCalc.factorial(7) = " + recResult);	
	}
    ```

## 스프링 AOP 구현 – @Aspect 애노테이션 이용
* @Aspect 어노테이션을 이용하는 방법도 POJO방식과 크게 다르지 않다.

* 차이점은 @Aspect 어노테이션을 적용할 클래스에 공통 기능과 PointCut을 설정한다는 점이다.

    ```java
    @Aspect		// 공통 기능을 수행하는 클래스
    public class ExeTimeAspect02 {
        
        @Pointcut("execution(public * spring.calc..*(..))")
        public void targetMethod() {}	// 핵심기능
        
        @Around("targetMethod()")
        public Object m(ProceedingJoinPoint jp) throws Throwable {	
            
            long start = System.nanoTime();
            
            Object result = jp.proceed();
            
            long end = System.nanoTime();
            System.out.println("실행 시간 : " + (end-start));
            
            return result;
        }
    }
    ```

* 앞서 본 POJO 객체와 유사하지만 다음 내용이 추가 되어있다.
  - 클래스에 @Aspect 어노테이션 추가
  - @Pointcut 어노테이션을 이용해서 Pointcut설정
  - @Around 어노테이션을 사용해서 메서드가 Around Advice로 사용됨을 설정

* @Aspect 어노테이션을 적용해 공통의 기능을 추가한 경우 XML 에서 이것을 인식할 수 있도록 설정해 주어야 한다.
  - &lt;aop:aspectj-autoproxy&gt; 태그를 사용하면 @Aspect 어노테이션이 적용된 빈 객체를 Advice로 사용한다.

    ```xml
    <aop:aspectj-autoproxy />
	
	<!-- 공통의 기능을 제공한 객체를 빈으로 등록 -->
	<bean class="spring.aspect.ExeTimeAspect02" id="exeTimeAspect"/>
	
	<!-- 사용할 객체를 빈으로 등록 -->
	<bean class="spring.calc.ImpeCalculator" id="impeCalc"/>
	<bean class="spring.calc.RecCalculator" id="recCalc"/>
    ```

* Main 메서드에서 동작시켜본다.
    ```java
    public static void main(String[] args) {
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:appCtx02.xml");
		
		Calculator impeCalc = ctx.getBean("impeCalc", Calculator.class);
		long impeResult = impeCalc.factorial(7);
		System.out.println("impeCalc.factorial(7) = " + impeResult);
		
		Calculator recCalc = ctx.getBean("recCalc", Calculator.class);
		long recResult = recCalc.factorial(7);
		System.out.println("recCalc.factorial(7) = " + recResult);	
	}
    ```

* XML 설정이 아닌 Java 설정을 사용한다면 Java 설정 클래스에 @EnableAspectJAutoProxy 어노테이션을 적용하면 XML 의 &lt;aop:aspectj autoproxy /&gt; 태그와 같은 효과를 볼수 있다.
    ```java
    @Configuration
    @EnableAspectJAutoProxy
    public class JavaConfig {
        @Bean
        public ExeTimeAspect02 exeTimeAspect() {
            return new ExeTimeAspect02();
        }
        @Bean
        public ImpeCalculator impeCalc() {
            return new ImpeCalculator();
        }
        @Bean
        public RecCalculator recCalc() {
            return new RecCalculator();
        }
    }

    // 자바 설정 파일 => 메인에서 불러오는 방식 바꿔준다.
    AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(JavaConfig.class);
    ```

## ProceedingJoinPoint 객체
* 보통의 경우 Around Advice 에서 사용할 공통의 기능 메서드는 대부분 파라미터로 전달 받은 ProceedingJoinPoint 의 proceed() 메서드만 호출하면 된다.
    ```java
    public Object m(ProceedingJoinPoint jp) throws Throwable {	
		long start = System.nanoTime();
	
		Object result = jp.proceed();	// 핵심기능을 수행하세요.

        return result;
    }
    ```

* 이 경우에도 호출되는 대상 객체의 정보 , 실행되는 메서드에 대한 정보, 메서드를 호출할 때 전달되는 매개값에 대한 정보가 필요할 때가 있다.

* 이때 ProceedingJoinPoin 객체에는 다음과 같은 메서드를 제공한다.
  - Signature getSignature() - 호출되는 메서드에 대한 정보를 구한다.
  - Object getTarget() - 대상 객체를 구한다.
  - Object[] getArgs() - 파라미터 목록을 구한다.

* 또한 Signature 인터페이스는 다음과 같은 메서드를 제공한다.
  - String getName() - 메서드의 이름을 구한다.
  - String toLongString() - 메서드를 완전하게 표현한 문장을 구한다. (리턴타입, 파라미터 타입이 모두 표시)
  - String toShortString() - 메서드를 축약해서 표현한 문자을 구한다.

    ```java
    Signature sig = jp.getSignature();	// 핵심기능을 가진 메서드의 정보를 꺼내보는 기능
    String methodName = sig.getName();	// 메서드의 이름
    String methodAllInfo = sig.toLongString();	// 메서드의 정보(매개정보, 반환정보)
    jp.getTarget();	// 대상 객체 => object타입
    String className = jp.getTarget().getClass().getSimpleName();	// 대상 객체의 이름정보
    jp.getArgs();	//매개값의 정보 => 배열
    String argName = Arrays.toString(jp.getArgs());	// 배열을 문자열로 전환
    ```

## 프록시 생성 방식
* 기존의 MainXMLPOJO 객체에는 다음과 같은 내용이 있다.
    ```java
    Calculator impeCal = ctx.getBean("impeCal", Calculator.class);
    ```
* 위 내용을 다음과 수정을 해도 사용이 가능할 것인가?
    ```java
    ImpeCalculator impeCal = ctx.getBean("impeCal", ImpeCalculator.class);
    ```

* 일반적인 자바 클래스는 사용이 가능해야 하지만 실행해 보면 에러가 발생한다.

* 그 이유는 getBean 으로 생성되는 객체가 실제 객체가 아닌 프록시 객체인데 이 프록시객체는 ImpeCalculator 클래스가 구현받은 Calculator 인터페이스를 구현받아 생성되기 때문이다.

* 빈 객체가 인터페이스를 구현 받고 있을 때 인터페이스가 아닌 클래스를 이용해서 프록시를 생성하고 싶다면 다음과 같은 설정을 해주면 된다.
  - POJO 방식의 XML 설정
    ```xml
    <aop:config proxy target class="true">
    ```
  - @Aspect 방식의 XML 설정
    ```xml
    <aop:aspectj autoproxy proxy target class="true"/>
    ```
  - @Aspect 방식의 자바 설정
    ```java
    @Configuration
    @EnableAspectJAutoProxy(proxyTargetClass=true)
    public class
    JavaConfig {...}
    ```

## execution 명시자 표현식
* Aspect를 적용할 위치를 지정할 때 사용한 Pointcut 설정을 보면 다음과 같이 execution명시자를 사용했다.
    ```java
    @Pointcut("execution(public * chap07..*(..))")
    private void publicTarget () {}
    ```

* execution 명시자는 Advice를 적용할 메서드를 지정할 때 사용되며 형식은 다음과 같다.
    ```
    execution(수식어패턴? 리턴타입패턴 클래스이름패턴? 메서드이름패턴 (파라미터패턴))
    ```

* ‘수식어패턴'은 생략이 가능한 부분으로 public,protected 등이 온다. 스프링 AOP의 경우 public만 적용이 가능하므로 무의미하다.

* ‘리턴타입패턴'은 리턴타입을 명시한다.

* ‘클래스이름패턴'과 메서드이름패턴 은 클래스 이름 및 메서드 이름을 패턴으로 명시한다.

* ‘파라미터 패턴' 부분은 매칭될 파라미터에 대해서 명시한다.

* *로 표기하는 경우 모든 값을 표기할 수 있다.

* ..을 이용해서 0개 이상이라는 의미도 표기할 수 있다.

* 명시자 표현식 => 메서드를 찾는 방법(메서드의 정보가 필요)
  1) 대상 객체
  2) public void method(int a, int b){...}
      1. public
      2. void, int, long, String, double[], List&lt;m&gt;.....
      3. 메서드의 이름
      4. 매개변수의 정보 => 타입, 개수, 순서

* publuc * spring... * (...) => AOP프록시에서 잡아내야할 메서드
  - public => public이 아닌 메서드는 통과
  - 반환타입 => 어떤 반환 타입을 가지든 프록시에 잡아냄
  - spring.. => spring 패키지의 모든 클래스를 프록시에 잡아냄
  - \* 메서드 이름 => 어떤 이름의 메서드이든 프록시에 잡아냄
  - (..)매개변수 => 매개변수의 개수가 0 개 이상이면 프록시에 잡아냄

* execution 명시자의 몇가지 예
  - execution(public void set*(..))  
  -> 반환타입이 void이고 메서드 이름은 set으로 시작하며 파라미터가 0개 이상인 메서드 호출

  - execution(* chap07.*.*())  
  -> chap07 패키지의 타입에 속한 파라미터가 없는 모든 메서드 호출

  - execution (* chap07..*.*(..))  
  -> chap07 패키지및 하위 패키지에 있는 파라미터가 0 개 이상인 메서드 호출

  - execution(Long chap07.Calculator.factorial(..))  
  -> 반환 타입이 Long 인 Calculator 타입의 factorial 메서드를 호출

  - execution(* get*(*))  
  -> 이름이 get 으로 시작하고 1 개의 파라미터를 가지는 메서드 호출

  - execution (* get*(*,*))  
  -> 이름이 get 으로 시작하고 2 개의 파라미터를 가지는 메서드 호출

  - execution (* read*(Integer,..))  
  -> 메서드 이름이 read로 시작하고, 첫번째 파라미터 타입이 Integer이며, 1개 이상의 파라미터를 갖는 메서드 호출

## Advice 적용 순서
* CacheAspect는 일종의 캐시를 구현한 Aspect로 실행 결과를 Map에 보관했다가 다음 동일한 요청이 들어오면 Map에 저장된 결과를 반환한다.

* 동일한 작업을 수행하지 않으므로 결과적으로 시간을 줄여준다.

* 캐시의 효과를 보려면 다음과 같은 순서로 실행되어야 한다.
  - 실행시간 측정 프록시 -> 캐시 프록시 -> 실행 대상 객체

* 이렇듯 Aspect의 적용 순서가 중요한 경우 순서를 직접 정해 주어야 한다.

* 다음 CacheAspect 클래스를 만들자
    ```java
    public class CacheAspect {

        private Map<Long, Object> cache = new HashMap<>();	// 정보를 임시로 저장할 공간
        
        public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
            Long num = (Long) joinPoint.getArgs()[0];
            
            if(cache.containsKey(num)) {
                System.out.println("캐시Aspect에서 구함 : " + num);
                return cache.get(num);
            }
            
            Object result = joinPoint.proceed();
            cache.put(num, result);	// 핵심 기능의 결과를 캐시에 저장
            System.out.println("캐시Aspect에 저장 : " + num);
            
            return result;
        }
    }
    ```

* XML스키마 기반 AOP설정을 사용하는 경우 &lt;aop:aspect&gt; 태그에 order 속성을 이용한다.
    ```xml
    <!-- 공통의 기능을 제공한 객체를 빈으로 등록 -->
	<bean class="spring.aspect.ExeTimeAspect01" id="exeTimeAspect"/>
	<bean class="spring.aspect.CacheAspect" id="cacheAspect"/>
	
	<!-- AOP 설정 -->
	<aop:config>
		<aop:aspect id="mAspect" ref="exeTimeAspect" order="0">
			<aop:pointcut expression="execution(public * spring.calc..*(..))" id="pMethod"/>
			<aop:around method="m" pointcut-ref="pMethod"/>
		</aop:aspect>
		
		<aop:aspect id="cAspect" ref="cacheAspect" order="1">
			<aop:pointcut expression="execution(public * spring.calc..*(..))" id="cMethod"/>
			<aop:around method="execute" pointcut-ref="cMethod"/>
		</aop:aspect>
	</aop:config>
	
	<!-- 사용할 핵심 기능을 가진 객체를 빈으로 등록 -->
	<bean class="spring.calc.ImpeCalculator" id="impeCalc"/>
	<bean class="spring.calc.RecCalculator" id="recCalc"/>
    ```

* Main 클래스로 확인해 본다.
    ```java
    public static void main(String[] args) {
		
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:appCtx03.xml");
		
		Calculator calc = ctx.getBean("impeCalc", Calculator.class);
		
		calc.factorial(10);
		
		calc.factorial(10);
	}
    ```

* @Aspect 어노테이션을 사용하는 경우
    ```java
    @Aspect
    @Order(1)   // 여러개의 Aspect객체에서 우선순위를 정하는 방법
    public class ExeTimeAspect {….}
    ```

