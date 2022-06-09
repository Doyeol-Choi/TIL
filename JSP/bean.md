# 자바 빈
* 회원 가입을 할 때 회원 가입페이지에 받은 정보를 데이터베이스에 전달을 해야 한다.

* 이때 두가지 방법으로 서버에 전송된다.
  - 이름, 아이디, 비밀번호등 개별적으로 전송하는 방법
  - 회원정보를 하나로 묶어서 전송하는 방법

* 이중에서 하나로 묶어서 전송하는 방법이 보다 효율적일 것이다.

* 이렇게 관련 있는 정보를 하나로 묶어 관리 하기 위해 나온 매커니즘이 바로 자바 빈(java bean) 이다.

* 자바 빈은 정보의 덩어리로 필요한 정보를 객체로 구성해서 한꺼번에 접근해서 사용가능 하다.

* 단순 정보의 저장뿐 아니라 데이터 은닉 개념을 사용한다.

* 자바 빈도 클래스이다. 필드와 메소드 내용이 구성된다.

* JSP에서 자바빈을 활용하는 액션태그는 3 가지로 구성된다.
  - &lt; jsp:useBean &gt; : 자바 빈을 생성한다.
  - &lt; jsp:getProperty &gt; : 자바 빈에서 정보를 얻어 온다.
  - &lt; jsp:setProperty &gt; 자바 빈에 정보를 저장한다.

## 자바 빈 생성하기
* 액션 태그 없이 생성하는 법
    ```java
    <%@ page import=“패키지명.자바빈클래스” %>
    // import를 통해 편하게 객체 생성
    <% MemberBean member = new MemberBean(); %>
    ```

*  임포트 없이 자바빈 객체를 생성하는 법
    ```java
    <% 패키지명.MemberBean member = new 패키지명. MemberBean(); %>
    ```

* JSP에서 자바 빈 객체를 액션 태그로 가져오자
    ```java
    <jsp:useBean class=“패키지명. MemberBean” id=“member” scope=“page”/>
    // 이 내용을 액션 태그로 표현
    <% 패키지명.MemberBean member = new 패키지명. MemberBean(); 
    pageContext.setAttribute(“member”,member); %>
    ```

* scope 속성은 자바빈 태그에 의해 생성된 객체가 사용될 수 있는 유효 범위를 결정한다.
  - page : 자바 빈은 생성된 페이지 내에서만 사용 할 수 있다.
  - request : 자바 빈은 생성된 요청을 수행하는 페이지들 에서만 사용 할 수 있다.
  - session : 자바 빈은 생성된 세션에서 요청을 처리하는 페이지들에서 사용될 수 있다.
  - application : 자바 빈은 생성된 응용 프로그램에 포함된 모든 페이지들에서 사용 될 수 있다.

* 액션태그를 통해서 프로퍼티에 값을 얻거나 저장할 수 있다.
    ```java
    // 값 불러오기
    <jsp:getProperty name=“빈 이름” property=“프로퍼티 이름” />
    // 값 저장
    <jsp:setProperty name=“빈 이름” property=“프로퍼티 이름” value=“프로퍼티 값”/>
    // 위의 액션태그는 아래와 같다
    Member.setName('request.getParameter("name")')

    // 한꺼번에 데이터를 가져오는 것도 가능하다.
    <jsp:setProperty name=“member” property=“*” />
    ```
