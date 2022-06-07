# Servlet
* 웹 서버는 다양한 서비스를 제공할 수 있다.

* 이런 서비스는 각각의 웹 애플리케이션으로 작성되어야 하며

* 웹 애플리케이션당 하나의 프로젝트를 생성한다.

* http://localhost:8085/web02/HS 분석
  - http:// = 프로토콜 : 어떤 정보를 주고받을 지에 대한 약속, http는 웹 문서를 주고 받는다.
  - localhost
    + 어떤 컴퓨터에게 => 컴퓨터 주소
    + IP주소로 나타내야 => 도메인 네임
    + DNS를 통해서 IP를 얻어간다.
    + 127.0.0.1(localhost) => 나 자신을 가르키는 키워드
  - :8085 = 어떤 프로그램인가?? => 톰캣
  - web02
    + 톰캣 내에 동작하는 어플리케이션 정보
    + 컨텍스트 패스 라 부른다.
    + 웹 어플리케이션을 구분해주는 정보, 톰캣 서버에 등록
    + 네이버(www.naver.com)의 컨텍스트 패스는 => /
  - HS = Servlet 매핑 주소
    + 방법 1 : web.xml 에 직접 작성
    + 방법 2 : 어노테이션
        ```java
        @WebServlet("/HS")
        ```
## 만들기
* 서블릿 클래스 정의하기  
반드시 public 으로 선언되야어야 하고 HttpServlet 을 상속받아야 한다.

* HttpServlet 클래스를 상속받으면 doGet 과 doPost 를 구현할 수 있게 된다.

* HttpServletRequest request 는 요청 처리를 위한 객체이다.

* HttpServletResponse response 응답 처리를 위한 객체이다.

* doGet, doPost 메소드를 모두 구현해야 두가지 방식에 모두 대응할 수있다.

* 응답객체 - response (중요도 낮음)
  - setContentType 메소드는 응답할 페이지의 환경을 설정해 준다.
  - 가장이 기본이 되는 응답방식 text/html
  - 문자 인코딩 방식 charset=UTF 8

* getWriter() 결과 출력을 위한 출력 스트림을 만들어 준다.

* 출력 스트림으로 만들어진 문자열을 응답해준다.

* Servlet 라이프 사이클
  1. Instance 생성 - 서블릿 객체 생성
  2. init() - 최초 한번만 호출
  3. doGet() or doPost() - 요청이 있을 때 마다 호출
  4. destroy() - 톰캣 해제시 실행

## 데이터 통신
* 데이터를 어떻게 서블릿에 전달할 것인가?
  - 사용자가 어떻게 자기 정보를 서버에 전달할 것인가?

1. 어떻게 사용자에게 입력을 받을 것인가?
    - 클라이언트로부터 정보를 전달 받기 위해 폼태그를 사용한다.
        ```html
        <form method=“get/post” action="호출할 서블릿">
        ```

    - form 태그의 주요 속성은 두가지 이다.
        + method : 정보를 어떤 방식으로 전달 할지 결정 Get, Post 두가지중 선택
        + action : 해당 정보를 어디에 전달 할지 결정 서블릿에서는 서블릿 매핑 주소를 명기한다.

    - form 태그에 적용된 내용을 전달하기 위해서는 반드시 전송 버튼이 있어야 한다.
        ```html
        <input type=“submit” value="전송">
        ```

2. 전달받을 데이터를 어떻게 꺼낼 것인가?
    - get은 쿼리스트링으로 받는다.
    - 서버에서는 클라이언트가 보낸 정보를 읽어 들이기 위해서 쿼리 스트링이라는 기술을 사용한다.
    - 쿼리스트링은 '리소스?이름=값&이름=값' 형태로 구성된다.
      + http://localhost:8085/web02/LS?userid=asdf&userpwd=1234
      + 위의 주소와 똑같은 형태로 id와 pwd 값을 입력하고 주소창에 입력해도 데이터가 전송된다.
    - 쿼리스트링을 사용하는 이유는 웹 페이지의 특성 때문인데 웹 페이지는 특성상 현재 페이지의 데이터를 다음 페이지가 인지 하지 못하고 전부 잃어버리기 때운이다.
    - 그래서 페이지간의 정보를 교환할 필요를 쿼리스트링을 통해서 처리한다.
    - 클라이언트에서 서버로 데이터를 전송하기 위해서는 기본적으로 데이터를 입력할 수 있는 텍스트 박스가 필요하다.
        ```html
        <input type=“text” name=“id”>
        <input type=“text” name=“age”>
        ```
    - 위 데이터는 서버로 데이터를 전송하는 데 name부분의 데이터를 ‘이름’ 입력된 값을 ‘값’ 형태의 쿼리 스트링 형태로 전송된다.

    - 서버에서는 클라이언트로부터 오는 모든 데이터를 요청 객체(request)에 담아서 활용한다.
    - 필요한 데이터는 request객체로 부터 얻을 수 있다.
        ```java
        request.getParameter(‘이름’);
        ```
        + 위 메소드로 얻어온 데이터는 문자열 형태로 사용가능 하다.
        + 숫자가 필요하다면 문자열을 숫자로 변환해서 사용한다.

    - post는 데이터 조각으로 받을 수 있는데 chrome의 툴에서 확인 가능하다.
* 입력 양식
  - form 태그는 일반 텍스트 뿐 아니라 다양한 형식의 데이터를 입력받는다.
    + 암호
    + 라디오 버튼
    + 체크박스
    + 목록 상자
  - 여러 데이터를 동시에 받는 경우 배열 형태로 받는다.
    + 체크박스와 같은경우
        ```html
        <input type="checkbox" name="hobby" value="readBook">독서
		<input type="checkbox" name="hobby" value="watchMovie">영화
		<input type="checkbox" name="hobby" value="fishing">낚시
		<input type="checkbox" name="hobby" value="golf">골프
        ```
        ```java
        // 체크박스등 다수의 데이터가 하나의 이름으로 넘어올때 => 배열로 처리
		String[] hobbys = request.getParameterValues("hobby");
        ```