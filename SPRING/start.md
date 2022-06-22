# 스프링
## 스프링이란?
* 스프링에 대해 가장 잘 알려진 정의는 다음과 같다
  - 자바 엔터프라이즈 개발을 편하게 해주는 오픈 소스 경량급 애플리케이션 프레임워크

*  일반적으로 프레임워크란 전체 프로그램의 특정 부분을 손쉽게 만들거나 한가지 기능에 대해 간단한 설정만으로 해당 기술을 제공하는 일련의 구조체를 의미한다.

* 그러나 스프링 프레임워크는 프로그램의 특정 부분이나 기술에 국한하지 않고 프로그램의 전 영역을 포괄하는 범용 프레임워크이다.

* 스프링은 수많은 프로젝트의 집합체로 모든 것을 배우는 것은 시간상 불가능하며 프레임워크를 관통하는 핵심 기술과 웹 MVC 의 기본에 대해서만 살펴본다.

* 스프링의 주요 특징
  - <span style="color:yellowgreen">DI(의존 주입) 지원</span>
  - <span style="color:yellowgreen">AOP(관점 지향 프로그래밍) 지원</span>
  - <span style="color:yellowgreen">IOC(제어의 역전)</span>
  - MVC 웹 프레임워크 제공
  - JDBC, JPA 연동 기술 및 선언적 트랜젝션 처리 등 DB 연동 기술 지원

---------------------------------------------------------
## 스프링 준비
* 방법1) 이클립스 + STS 플러그인
* 방법2) STS tool

* Spring Tools => 이클립스+STS 플러그인

* Spring Tools 4 => spring boot만

* Spring Tools 3 => spring legacy 공부

* JDK
  - 썬 마이크로시스템즈에서 만들고 오라클이 인수
  - JAVA 컨퍼런스에 속한 다양한 회사에서 개발
  - 업데이트 => openJDK (업계 표준)
  - oracleJDK는 openJDK를 오라클의 기능을 추가해서 나온 버전 (관행적 업계 표준)
  - JDK 1.8 이후부터는 openJDK와 oracleJDK가 거의 일치

* SPRING 5.X 버전부터는 JDK 1.8이후로 사용가능하다. (우리 수업은 스프링4.X 버전을 사용할 예정이며 학습하는데는 스프링 버전과 관련없이 아무거나 사용가능하다.)

* 메이븐도 필요한 모듈이지만 이클립스 EE 4.3버전 이후로는 이클립스에 플러그인이 내장되어있으므로 4.3.이후 버전을 사용하면 굳이 설치할 필요는 없다.

* 이클립스에 STS(spring tool suite)플러그인을 직접 설치할 수도 있고 스프링 사이트에서 STS가 내장된 이클립스를 직접 다운 받을 수도 있다.

* 메이븐
  - 라이브러리 관리 및 배포 도구
  - POM.xml에 필요한 라이브러리 작성 (의존성이 있는 라이브러리에 필요한 라이브러리까지 다 메이븐 리포지트리에서 다운받아 적용시켜준다.)

---------------------------------------------------------
## 이클립스에서 스프링 시작
* 메이븐 프로젝트를 생성한다.
  - Group id는 주로 도메인을 역순으로 쓴다. ex)com.green
  - Artifact id는 프로젝트 이름
  - src/main/java : 자바 소스 위치 폴더
  - src/main/resource : 자원 파일 위치 폴더
  - test는 말 그대로 test 소스 저장 배포는 위의 두 폴더

* POM.xml에 내용 추가
  ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.22.RELEASE</version>
        </dependency>
    </dependencies>
    <!-- dependency 부분은 메이븐 리포지트리에서 가져오자 -->
    <build>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>utf-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
  ```

* 작성 후 프로젝트 우클릭해서 메이븐 업데이트를 진행한다.

* 메이븐 의존 설정(dependencies)
  -  의존을 추가한 다는 것은 자바 애플리케이션에 클래스 패스를 추가한다는 것을 의미한다.
  - 각 모듈은 아티팩트 단위로 관리 되는데 이 아티팩트의 완전한 이름은 “아티팩트이름-버전.jar”로 되어있다.
  - 앞서 본 내용에 의하면 spring-context-4.1.0.RELEASE.jar 아티팩트 파일을 추가한 다는 것을 의미한다.

* POM.xml에 위 내용을 추가하면 메이븐 로컬 레포지토리를 검색하고 없으면 원격 레포지토리로 부터 해당 의존 파일을 다운받는다.
  - 로컬 위치 : [사용자폴더]\.m2\repository
  - 보통 메이븐 컴파일할때 해당 파일을 다운로드 받는다.
  - 해당 파일을 다운 받은때는 spring-context-4.1.0.RELEASE.pom을 먼저 받는데 여기에는 해당 아티팩트를 실행하기 위해서 필요한 파일들의 목록이 기록되어서 이파일들을 먼저 받는다.(이런 관계를 의존 관계라고 한다.)

* web.xml is missing 에러 발생시 직접 web.xml파일을 작성한다.

* Java에서는 특정 객체의 기능을 실행하기 위해서는 클래스의 인스턴스를 생성해야 한다.

* 기본 자바 프로그램과 다르게 스프링은 스프링 설정파일에서 객체를 만든 다음 만들어진 객체를 불러오는 방법으로 기능을 동작시킨다.

* 스프링 설정 파일을 만들어 보자
  - Resources위치에 우클릭후 new -> spring bean configuration 을 선택
  - xml파일내부에 객체를 등록한다
    + id : 빈 객체를 구분할 때 사용할 이름
    + class : 빈 객체를 생성할 때 사용할 클래스
    + property태그는 set메서드를 호출한다.
      + name 속성은 set메서드가 호출할 클래스의 필드 이름이다.
      + value 속성은 해당 필드에 넣을 값이다.

    ```xml
    <!-- 스프링이 관리해줄 객체를 작성해 주면 된다. -->
	<bean id="greeter" class="ex01.Greeter">
		<property name="format" value="%s, 안녕하세요."/>
	</bean>
    ```

## 스프링은 객체 컨테이너
* 스프링의 핵심기능은 객체를 생성하고 초기화 하는 것이다.

* 이와 관련된 기능은 ApplicationContext라는 인터페이스에 정의 되어있으며 GenericXmlApplicationContext클래스는 이 인터페이스를 구현한 객체이다.

    ```java
    public class Main02 {

	public static void main(String[] args) {
		// 스프링으로 자바 객체 제어하기
		
		// 1. 스프링 설정 파일 읽어오기
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:AppContext.xml");
		
		// 2. 스프링이 만든 객체 읽어오기
		Greeter g = ctx.getBean("greeter", Greeter.class);
		
		// 3. 가져온 객체 이용하기
		String msg = g.greet("스프링");
		
		System.out.println(msg);
		
		ctx.close();
	    }
    }
    ```

* 스프링은 별도의 설정이 없는 경우 한 개의 빈 객체만 생성한다. => 싱글톤
    ```java
    public class Main03 {

	public static void main(String[] args) {
		GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:appContext.xml");
		
		Greeter g1 = ctx.getBean("greeter", Greeter.class);
		Greeter g2 = ctx.getBean("greeter", Greeter.class);
		Greeter g3 = ctx.getBean("greeter1", Greeter.class);
		
		// 주소 비교
		System.out.println("(g1 == g2) : " + (g1==g2));
		System.out.println("(g2 == g3) : " + (g2==g3));
        // (g1==g2) 값은 true
        // (g2==g3) 값은 false
        ctx.close();
	    }
    }
    ```

* 위의 예제와 같이 다른 객체를 만들고 싶다면 Context.xml 파일에 Bean을 추가해준다.