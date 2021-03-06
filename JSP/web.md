# 웹개발 개요
## 웹 프로그래밍
* 기본적인 인터넷 동작원리
  - [ 클라이언트 ] 사이트 주소를 입력하고 웹 페이지 요청
  - [ 서버 ] 요청받은 웹페이지를 검색
  - [ 서버 ] 검색된 웹페이지를 응답
  - [ 클라이언트 ] 웹페이지를 본다

* 요청과 응답은 HTTP 라는 약속된 절차를 통해서 진행된다.

* 요청하고 응답하는 페이지는 HTML 문서로 이루어진 웹 문
서이다.

* HTML 문서로 이루어진 문서는 정보만 보여주는 정적인 페이지
이다.

* 우리가 사용하는 인터넷은 새로운 정보를 제공해야 하
는데 정적 페이지인 HTML 문서만으로는 이런 역할을 하기에
불충분하다.

* 이런 단점을 보완하기 위해서 나온 것이 동적 웹페이지이다.

* 다양하고 새로운 정보를 제공하기 위해서는 방대한 양의 정보
가 필요한데 이를 위한 정보 저장소 => 데이터 베이스가 필요
하게 되고 이런 정보를 가져와서 동적 으로 보여 줄 기술이 필
요하다.

* 이런 의미로 등장한 언어가 PHP, ASP, 서블릿 /JSP 이다. 이런 언어로 개발된 애플리케이션이 웹 애플리케이션이다.

* 서버는 클라이언트의 요청을 처리해서 결과를 보여 주기 위한
웹서버 (WS)

* 실질적으로 페이지 로직이나 데이터베이스 연동을 위한 비즈니
스 로직이 구현된 웹 애플리케이션 서버 (WAS)로 구성된다.

* 웹서버의 종류 : Apache, Nginx, IIS 등

* 웹 애플리케이션 서버의 종류 : WebSphere, iPlanet, 9iAS, Jeus, Tomcat 등

----------------------------------------------------------
## html과 자바 연결 (Servlet)

* 서블릿(Servlet)이란 Server+Applet 의 합성어로 서버에서 실행되는 Applet 란 의미로 웹에서 실행하는 프로그램을 작성하는 기술을 의미한다.

* 보통 서블릿은 자바 클래스 형태의 웹 애플리케이션이다

* 이것을 브라우저를 통해서 실행되도록 하기 위해서는 javax.servlet.http 패키지에서 제공하는 httpSevlet 클래스를 상속받아서 구현해야 한다.

* 위 클래스를 상속받은 서브 클래스를 서블릿 클래스라고 한다

* JDK 에는 웹 애플리케이션을 제작할 수 있는 클래스를 제공하지 않고 톰캣을 설치하면 해당 클래스를 제공한다.

## 서블릿 응답방식
* 서블릿의 응답 방식을 보면 doGet 과 doPost 방식이 있는데 이것은 클라이언트의 요청방식에 대응한다.
  - Get 방식 : 데이터를 주소창으로 넘어간다. 보안에 취약하고 255 이하의 적은 양의 데이터를 전송한다.
  - Post 방식 : Html header 에 데이터를 담아서 넘긴다. 255 자 이상 대용량 데이터를 전송한다.

* 서블릿 입장에서는 Post 로 전송되나 Get 방식으로 전송되나 처리방식은 같다.

* 단지 doGet 이냐 doPost 에서 처리하느냐의 차이이다.

* 요청정보는 HttpServletRequest 객체에 담아서 전달된다.

* 그리고 응답정보는 HttpServletResponse 객체를 사용한다.

* HttpServletResponse 객체를 이용해서 HTML 문서로 응답하기 위해서는 출력 객체를 사용해야 한다.

* PrintWriter 출력 스트림 객체로 HTML 문서를 출력한다.

```md
# Get 방식
* 클라이언트가 서버에게 요청 (요청 수단 : 주소창 => Get)

* 서버는 요청에 대응하는 서블릿을 찾아야 한다. (둘 중 하나 사용)
  1. web.xml에 요청과 서블릿 연결(매핑)
  2. @WebServlet 어노테이션을 이용한 방법

* 찾아낸 서블릿의 doGet 메서드 동작
```

## html과 자바 연결 (JSP)
* JSP는 JAVA SERVER PAGE 의 준말로 자바로 웹 페이지를 작성하기 위한 언어이다.

* 서버 페이지를 실행되는 로직을 구현하기 위해 프로그래밍언어가 필요한데 JSP 는 그중에서 Java 를 사용하고 있다.

## JSP와 Servlet의 차이
* 서블릿은 자바 코드 내부에 HTML 코드가 들어가는 구조이지만 JSP는 HTML 코드 내부에 자바 코드를 삽입하는 형태이다.
  - Servlet은 JAVA 친화적
  - JSP는 HTML 친화적

* 서블릿 보다 JSP 만 사용하는 것이 적합 할 것 같지만 복잡한 로직을 구현하는데 JSP 에 작성하면 프론트 엔드 개발자가 실수로 코드를 건드려 문제가 발생하는 경우가 있으므로 적절한 조화가 필요하다. (복합적으로 사용)