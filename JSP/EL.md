# 표현 언어(Expression Language)
* 값을 웹 페이지에 표시하는데 사용하는 태그이다.
    ```html
    <!-- 기존 표현식 -->
    <%= 변수 %>
    <!-- 표현 언어 -->
    ${변수}
    ```
* 표현 언어에서 사용 가능한 데이터 타입으로 문자열, 정수, 실수, 논리형, null 이 있다. 다만 null 은 공백으로 출력한다.

* 표현언어 내부에서 사용가능 한 연산자
  - 산술 : +, --, *, /(div) ,% (mod)
  - 관계형 : ==(eq), !=(ne), <(lt), >(gt), <=(le), >=(ge)
  - 조건형 : a ? b : c
  - 논리형 : && (and), || (or), ! (not)
  - null검사 : empty

* 연산자는 기호와 텍스트 둘 다 사용 가능하다.

* empty는 객체가 비었는지 확인할 때 사용=> 비어있다면 true 반환

* jstl와 함께 사용하면 보다 가독성 높은 코드를 작성할 수 있다.

* 요청 처리는 request.getParameter를 사용한다. 다만 표현언어에서는 param 객체를 사용한다.
  - param : JSP의 내장 객체인 request 의 getParameter 와 동일한 역할인 파라미터의 값을 알려준다.
  - paramValues : 동일한 이름으로 전달되는 파라미터 값들을 배열 형태로 얻어오는 데 사용하는 request 의 getParameterValues 와 동일한 역할을 한다.

* param 객체는 . 또는 [" "]로 사용자의 입력값을 가져온다.

* 표현 언어에서 null 처리는 공백으로 사용된다.

* 또한 기존 Java 에서 객체 비교를 위해선 equals 메소드를 사용해야 하는 반면 표현언어로는 으로 객체에 저장된 값을 비교할 수 있다.

* 그리고 표현언어는 문자열을 숫자로 변환할 필요도 없다.

## 표현언어로 내장객체 접근하기
* JSP에서 웹 애플리케이션을 구현하는 데 필요한 정보를 JSP의 내장 객체에 속성값으로 저장해서 사용했다.

* 각 속성에 저장된 값을 표현언어에서는 다음과 같은 형태로 사용할 수 있다.
  - pageScope : page 기본 객체에 저장된 속성의<속성,값> 매핑을 저장한 Map객체
  - requestScope : request 기본 객체에 저장된 속성의<속성,값> 매핑을 저장한 Map객체
  - sessionScope : session 기본 객체에 저장된 속성의<속성,값> 매핑을 저장한 Map객체
  - applicationScope : application 기본 객체에 저장된 속성의<속성,값> 매핑을 저장한 Map객체

* 표현식에서 작성된 이름은 자바의 변수로 인식하지만 표현언어에서는 속성의 이름으로 인식한다.

* 표현언어에서 내장 객체에 데이터를 접근할 때 어느 객체에 접근할지 생략할 수 있다. 이때 num1 속성의 값을 가져올 때 어느 내장 객체인지 알 수 없으므로 표현언어에서 사용할 때는 다음 순서로 자동으로 검색해서 해당 속성이 있을 때 가져온다.  
pageScope -> requestScope -> sessionScope -> applicationScope

## 표현언어로 자바 빈에 접근하기
* 자바 빈에 저장된 객체의 필드에 저장된 값을 사용하는 법
    ```html
    ${자바빈즈.프로퍼티이름}
    ${자바빈즈[“프로퍼티 이름”]}
    ```

# JSTL
* JSTL이란 JSP Standard Tag Library의 약어로 JSP에 사용가능한 표준 태그 라이브러리이다.

* 기존 JSP의 스크립트릿에서 코드를 보다 간결하고 가독성 높게 처리가 가능해진다.

* JSP는 스크립트릿과 자바코드가 한데 어우러져 복잡한 구조로 되어있다. 이것을 보다 간결하게 사용하기 위해서 자신만의 태그를 추가 할 수 있는데 이런 태그를 커스텀 태그라고 한
다.

* 이런 커스텀 태그를 모아서 배포하면 이를 커스텀 태그 라이브러리라고 한다.

* 다만 이런 커스텀 태그 라이브러리는 말 그대로 개발자 개개인마다 다르기 때문에 이것을 표준화 한 것이 JSTL 이다.

* JSTL에서 제공하는 기능
  - 간단한 프로그램 로직의 구현가능 변수 선언, 조건(if), 반복(for) 등에 해당하는 로직
  - 다른 JSP 페이지 호출
  - 날짜, 시간, 숫자의 포맷
  - JSP 페이지 하나를 가지고 여러 가지 언어의 웹 페이지 생성
  - 데이터베이스로의 입력, 수정, 삭제, 조회
  - XML문서의 처리
  - 문자열을 처리하는 함수 호출

* JSTL은 크게 core, format, xml, sql , functions 5 가지 커스텀 태그로 나누어서 제공한다.
  - 기본 기능(core) : 일반적인 프로그램이 제공하는 것과 유사한 기능을 제공 (주로 공부할 내용)
  - 형식화(format) : 숫자, 날짜, 시간을 포맷팅하는 기능
  - 데이터베이스(sql) : 데이터베이스의 데이터를 입력, 수정, 삭제, 조회하는 기능
  - XML처리(xml) : XML 문서를 처리할 때 필요한 기능
  - 함수 처리(functions) : 문자열을 처리하는 함수를 제공

## core 태그
* 5가지의 태그 분류중에서 가장 많이 사용되는 태그는 core 태그이다. 기본 접두어는 c 를 사용한다.
  - &lt; c:set &gt; : 변수에 값을 설정한다.
  - &lt; c:remove &gt; : 변수에 설정된 값을 제거한다.
  - &lt; c:if &gt; : 조건에 따라 처리를 달리 할 때 사용한다.
  - &lt; c:choose &gt; : 여러 조건에 따라 처리를 달리 할 때 사용한다.
  - &lt; c:forEach &gt; : 반복 처리를 위해서 사용한다.
  - &lt; c:forTokens &gt; : 구분자로 분리된 각각의 토큰을 처리할 때 사용한다.
  - &lt; c:import &gt; : 외부의 자원을 url 을 지정하여 가져다 사용한다.
  - &lt; c:redirect &gt; : 지정한 경로로 이동한다.
  - &lt; c:url &gt; : url을 재 작성한다.
  - &lt; c:out &gt; : 데이터를 출력할 때 사용하는 태그로 표현식인 <%= %> 을 대체할 수 있다. 보안상의 이유로 사용된다.
  - &lt; c:catch &gt; : 예외 처리에 사용한다.

* core 태그를 사용하기 위해서 지시자를 등록해야 한다.
    ```html
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    ```

### &lt; c:set &gt;
* 변수에 값을 저장할 때 사용한다.
  - var : 변수 이름을 String형으로 지정한다.
  - value : 변수에 저장할 값을 지정한다.
  - scope : 변수가 효력을 발위할 영역으로 생략될 경우 기본 값은 page이다.
    ```java
    // 사용법
    <c:set var=“msg2” value=“Hello2” scope=“request” />
    // 같은 표현의 리퀘스트
    request.setAttribute(“msg2”,”Hello2”)
    // value 값을 다음과 같이 넣을 수도 있다.
    <c:set var=“msg2” scope=“request” >
        Hello2
    </c:set>
    // 액션 태그의 setProperty 대체
    <jsp:setProperty name=“자바빈 객체” property=“프로퍼티이름” value=“저장할 값” />
    // 위의 액션 태그를 c:set 으로
    <c:set target=“${자바빈 객체}” property=“프로퍼티 이름” value=“저장할 값” />
    // 산술연산이나 비교연산등도 가능하다.
    <c:set var=“변수” value=“${10>5}” />
    ```

### &lt; c:remove &gt;
* 변수를 삭제할 때 사용한다.
    ```java
    <c:remove var=“변수이름” scope=“범위”/>
    ```

### &lt; c:if &gt;
* c:if문은 if문과 비슷한 기능을 제공한다. 단, if~else문은 제공하지 않는다.
    ```java
    <c:if test=“조건식”>
    조건이 참일 경우 실행할 영역
    </c:if>
    ```

### &lt; c:choose &gt;
* c:if문의 경우 else기능을 제공하지 않는다. 그러므로 else 기능을 하기 위해서는 c:if를 여러 개 나열할 수밖에 없는데 이때 choose를 사용하면 여러 조건을 보다 간결하게 처리할 수 있다.
    ```java
    <c:choose>
    <c:when test=“조건식1”> 조건식1이 참일 경우 실행할 영역 </c:when>
    <c:when test=“조건식2”> 조건식2이 참일 경우 실행할 영역 </c:when>
    <c:when test=“조건식3”> 조건식3이 참일 경우 실행할 영역 </c:when>
    <c:otherwise> 모든 조건이 만족하지 않을 때 실행 영역 </c:otherwise>
    </c:choose>
    ```

### &lt; c:forEach &gt;
* c: forEach 문의 경우 배열, 컬렉션등의 집합체에 저장된 값을 순차적으로 처리할 때 사용하는 반복문이다. (향상된 for문과 유사)
    ```java
    <c:forEach var=“원소 하나를 저장할 변수” items=“반복처리할 집합체”>
        반복할 코드
    </c: forEach >
    ```

* 프로퍼티
  - varStatus : 각 항목의 Index를 사용해야 할 때 반복 상태등을 저장하는 변수
  - index : items에 지정한 집합체의 현재 반복중인 항목의 index를 알려준다.(0부터 시작)
  - count : 반복을 할 때 몇 번째 반복 중인지 알려준다 (1부터 시작)
  - first : 현재 반복이 처음인지 여부를 알려준다(boolean타입)
  - last : 현재의 반복이 마지막인지 여부를 알려준다.(boolean타입)

* c: forEach 문의 경우 일반적인 사용법은 집합체에 저장된 값을 순차적으로 꺼내 올 때 사용하지만 단독으로 숫자를 이용해서 횟수 반복으로도 사용가능하다.
  - begin : 반복에 사용할 것으로 첫번째 항목의 Index 값
  - end : 반복에 사용할 것으로 마지막 항목의 Index 값
  - step :증가 값

### &lt; c:forTokens &gt;
* c: forTokens 문의 경우 문자열을 구분자로 쪼개고 각각 쪼개진 문자열 하나하나의 집합체로서 순차적으로 반복해서 사용하는 반복문이다.
    ```java
    <c:forTokens var=“토큰을 저장할 변수” items=“토큰으로 나눌 문자열” delims=“구분자”>
        반복할 코드
    </c:forTokens>
    ```

### &lt; c:import &gt;
* 다른 페이지를 포함하거나 이동할 때 사용하는 태그

* c:import 문의 경우 지정된 페이지를 불러와서 변수에 저장해 두고 해당 변수를 호출할 때 해당 페이지에서 가져온 결과를 출력한다.
    ```java
    <c:import var=“저장할 변수” url=“URL” scope=“변수를 저장할 영역” charEncoding=“UTF-8”>
    </c:import>
    ```

### &lt; c:redirect &gt;
* c:redirect 문의 경우 지정한 페이지로 이동할 때 사용한다.

* response.sendRedirect와 같다.
    ```java
    <c:redirect url=“URL” />
    ```

### &lt; c:url &gt;
* c:url 문의 경우 URL을 생성해서 적절한 위치에 사용할 수 있다.
    ```java
    <c:url var=“저장할 변수” value=“URL” scope=“변수를 저장할 영역>
    </c:url>
    ```

### &lt; c:out &gt;
* c:out 문은 출력을 위한 태그이다.

* 표현식이나 표현언어와 동일한 역할을 하기 때문에 표현언어보다 자주 사용되지 않는다.

* 보안이 필요할때 사용하면 좋다.
    ```java
    < c:out value=“출력할 값” [defalut=“기본값”] / >
    ```

### &lt; c:catch &gt;
* c:catch 문은 예외 처리을 위한 태그이다.

* 예외가 발생하면 잡아내는 역할을 한다.
    ```java
    <c:catch var=“발생한 예외가 저장될 변수” >
        예외가 발생할 가능성이 있는 코드
    </c:catch>
    ```

## Fmt 태그
* fmt태그는 포맷팅에 관련된 태그 모음이다.

* 주로 숫자, 날짜, 시간의 형식을 다루는데 사용되며 다양한 언어를 지원한다.

* fmt 태그를 사용하기 위해서 다음 지시자를 등록한다.
    ```java
    <%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>
    ```

* 주로 숫자나 날짜 형식 지정에 사용된다.

* 숫자, 날짜 형식
  - &lt; fmt:formatNumber &gt; : 숫자를 양식에 맞춰서 출력한다.
  - &lt; fmt:formatDate &gt; : 날짜 정보를 담고 있는 객체를 포맷팅하여 출력할 때 사용한다.
  - &lt; fmt:parseDate &gt; : 문자열을 날짜로 파싱한다.
  - &lt; fmt:parseNumber &gt; : 문자열을 수치로 파싱한다.
  - &lt; fmt:setTimeZone &gt; : 시간대 별로 시간을 처리할 수 있는 기능을 제공한다.
  - &lt; fmt:timeZone &gt; : 시간대 별로 시간을 처리할 수 있는 기능을 제공한다.

* 로케일 지정
  - &lt; fmt:setLocale &gt; : 국제화 태그들이 사용할 로케일을 지정한다.
  - &lt; fmt:requestEncoding &gt; : 요청 파라미터의 인코딩을 지정한다.

* 메시지 처리
  - &lt; fmt:bundle &gt; : 태그 몸체에서 사용할 리소스 번들을 지정한다.
  - &lt; fmt:message(param) &gt; : 메시지를 출력한다.
  - &lt; fmt:setBundle &gt; : 특정 리소스 번들을 사용할 수 있도록 로딩한다.


