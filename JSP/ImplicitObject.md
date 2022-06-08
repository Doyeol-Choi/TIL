# JSP 내장객체
* 내장 객체란 개발자가 직접 생성한 일이 없이 바로 사용 가능한 객체를 이야기한다.

* JSP가 서블릿으로 만들어 질 때 자동으로 생성해 주기 때문에 별도의 객체 생성이 필요없다.

* JSP를 학습한다는 것은 내장 객체을 학습한다는 것과 동일한 의미이다.
    ```java
    <% out.print(“Hello World!”); %>
    // out : 내장객체의 예시
    ```

* JSP가 서블릿으로 변환될 때 생성되는 서블릿 파일을 살펴 보면 자동으로 생성되는 내장객체의 종류를 확인 할 수 있다.
    - 입출력 관련 객체
        + request
        + response
        + out  
    <br>
    - 서블릿 관련 객체
        + page
        + config  
    <br>
    - 외부 환경 정보를 제공하는 객체
        + pageContext
        + session
        + application  
    <br>
    - 예외 관련 객체
        + exception

## out
* JSP의 실행결과를 클라이언트의 브라우저로 출력할 때 가장 효과적인 객체이다.
* 서블릿에서는 다음과 같은 객체를 직접 생성해야 했지만 JSP 내장 객체로 제공된다.
    ```java
    PrintWriter out = response.getWriter();
    ```

## request
* getContextpath() : JSP페이지가 속한 웹 애플리케이션의 컨텍스트 패스를 구한다.
* getMethod() : 요청 방식이 GET 방식인지 POST 방식인지 알려준다.
* getRequestURL() : 요청 URL을 구한다.
* getRequestURI() : 요청 URL에서 쿼리 스트링 부분을 제외한 부분을 구한다.
* getQueryString() : 요청 URL 다음에 오는 쿼리 스트링 부분을 구한다.
* getSession() : 요청에 관련된 세션 객체를 구한다.
* getRequestDispatcher() : 지정 로컬 URL 에 대한 RequestDispatcher 객체를 구한다.
* getRemoteHost() : 요청한 호스트의 완전한 이름을 구한다.
* getRemoteAddr() : 요청한 호스트의 네트워크 주소를 구한다.
* getRemoteUser() : 요청한 사용자의 이름을 구한다.
* getSession() : 세션 객체를 구한다.
* getServerName() : 서버의 이름을 구한다.
* getProtocol() : 사용중인 프로토콜을 구한다.
* getParameter(String name) : 지정한 이름의 파라미터를 구한다.
* getParameterNames() : 모든 파라미터의 이름을 구한다.
* getParameterValues(String Name) : 지정한 이름의 파라미터가 여러 개인 경우 사용하며 지정한 이름을 가진 파
라미터의 모든 값을 배열로 구한다.
    
## response
* 실행 결과를 브라우저로 되돌려 줄 때 사용하는 내장 객체

* resoponse 에서 가장 많이 사용되는 메소드는 sendRedirect 이다.

* 리다이렉트는 웹 서버가 브라우저에게 지정된 페이지로 이동하도록 지시한다.

* 로그인 => 로그인 성공 실패를 결정후 해당 페이지로 리다이렉트 한다.

* 웹 사이트에서 페이지를 이동시키는 방법은 두 가지 이다.  
(여기서 이동은 클라이언트의 의도와 상관없는 이동이다.)
    - 리다이렉트 방식
        + 리다이렉트 방식은 response 객체의 sendRedirect 리다이렉트 방식이다.
        + 리다이렉트 방식은 브라우저의 URL을 변경하여 페이지를 이동하는 방식으로 request, response 객체가 유지되지 않는다.
        + 리다이렉트로 다른 페이지의 데이터를 전송하고 싶다면 쿼리스트링을 이용해야 한다.
    - 포워드 방식
        + 포워드 방식은 서버상으로 페이지가 이동되므로 브라우저에서는 페이지 이동을 알 수 없다.
        + URL 변경이 없고 기존 request, response 객체가 유지된다.
        + 포워드 방식은 RequestDispatcher 객체를 통해서 호출이 가능하다.
        + 포워드로 다른 페이지에 데이터를 전송하고 싶다면 request 객체가 유지되므로 해당 객체에 데이터를 담아서 보낼 수 있다.

## 내장 객체의 영역
* 내장 객체의 영역은 객체가 얼마 동안이나 살아 있는가를 보여주는 영역이다.

* 영역은 총 4 개가 있다.
    - page : 하나의 JSP 페이지를 처리할 때 사용되는 영역
        + page영역은 한번의 클라이언트 요청에 하나의 JSP 페이지를 범위로 갖는다.
        + 요청을 처리하는 JSP 페이지 하나당 새로운 page 영역을 가지며 pageContext 객체 하나가 생성된다.
        + pageContext 객체에 정보가 저장되면 해당 페이지 내에서만 사용이 가능하다.
    - request : 하나의 요청을 처리할 때 사용되는 영역
        + request 영역은 한번의 클라이언트 요청과 관련이 된다.
        + 클라이언트가 페이지를 요청하면 요청한 페이지와 요청 받은 페이지 사이의 request 내장 객체에 정보를 저장 할 수 있다.
        + 브라우저가 응답 페이지를 받으면 request 객체는 사라진다.
        + page 와의 차이점은 page 는 오직 하나의 JSP 페이지만 포함하는데 반해 request는 하나의 요청이 끝나기 까지 모든 JSP 페이지가 포함된다.
        + 포워드로 page를 이동하면 요청은 여전히 한번만 한것이기때문에 객체가 유지되나, 리퀘스트로 page를 이동하면 재요청이 이루어진 것이기 때문에 객체가 지워진다. 
    - session : 하나의 브라우저와 관련된 영역
        + 로그인에서 많이 사용한다.
        + session 영역은 웹 브라우저를 닫기 전까지 페이지를 이동하더라도 정보를 저장할 수 있는 객체
        + 보통 사용자 정보를 담아서 사용하는 session 객체는 로그인 인증 처리를 통해 회원 전용 페이지를 사용할 수 있게 해준다.
        + session은 하나의 브라우저와 관련된 영역이다.
    - application : 하나의 웹 애플리케이션과 관련된 영역
        + application 영역은 하나의 웹 애플리케이션과 관련된 전체 영역을 포함한다.
        + 하나의 웹 애플리케이션에 속한 모든 페이지, 페이지에 대한 요청, 세션 전부 application 영역에 속한다.

* 각각의 영역 객체에 정보를 저장하기 위한 메소드 : setAttribute 속성 이름 속성 값
* 각각의 영역 객체에 정보를 꺼내 오기 위한 메소드 : getAttribute 속성 이름
* 각각의 영역 객체에 모든 속성 이름을 가져오기 위한 메소드 : getAttributeNames
* 각각의 영역 객체에 지정된 속성을 삭제하는 메소드 : removeAttribute 속성 이름