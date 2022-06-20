# MVC (Model, View, Controller)
* 정보를 보여주는 부분 (View), 정보를 처리하는 부분 (Controller), 정보를 저장하거나 꺼내오는 부분 (Model) 3 개의 파트로 나눠서 프로그래밍하는 방법을 MVC 패턴이라고
한다.

* 모델1 과 모델2를 볼 수 있는데, 모델1의 경우 View와 Controller의 경계가 없이 사용하는 것을 말하는데 현재는 소규모 프로젝트 정도만 사용된다.

* 모델2의 경우 VIew와 Controller를 나눠서 화면을 처리하기 위한 부분(프레젠테이션 로직)과 실제 업무를 처리하는 부분(비즈니스 로직)를 통제하기 위한 컨트롤러로 분업하는 형태를 의미한다.
  - 모델2는 하나의 컨트롤러가 모든 요청을 받아서 해당하는 서블릿에 연결해주는 형태를 가진다.

* MVC 패턴의 컨트롤러 : 서블릿
  - 웹 브라우저(클라이언트)의 요청을 받는다.
  - 웹 브라우저가 어떤 기능을 요청했는지 분석한다.
  - 분석된 요구사항을 바탕으로 필요한 비즈니스 로직을 처리하는 모델을 호출한다.
  - 모델로부터 전달 받은 결과물을 알맞게 가공한 다음, request 나 session 객체의 setAttribute 메소드를 사용하여 결과를 속성에 저장한다.
  - 웹 브라우저에 처리 결과를 보여 주기 위해 JSP 를 선택한다음 JSP 에 포워딩 한다.

* MVC 패턴의 뷰 : JSP
  - 일반 JSP 와 같은 역할을 하지만 대체로 컨트롤러로 부터 전달 받는 데이터를 사용해서 결과를 출력해주는 역할을 한다.

* MVC 패턴의 모델 : 자바클래스
  - 컨트롤러로부터 요청을 받는다.
  - 비즈니스 로직을 수행한다.
  - 수행 결과를 컨트롤러에게 전달한다.

---------------------------------------------------------
## 게시물 등록 시나리오
1. 리스트페이지
  - 게시물 등록 버튼을 누른다 요청  
  : boardservlet 매핑 주소 => command = board_write_form

2. boardservlet 서블릿 ----[command]----> ActionFactory

3. ActionFactory
  - 해당 기능을 동작시킬 객체 BoardWriteFormAction 생성
  - 객체를 boardservlet 서블릿에 반환

4. Boardservlet 서블릿은 Actionfactory 로부터 받은 객체의 메서드(execute)를 실행

5. execute 메서드 내부 :
  - 결과로 어떤 뷰페이지를 호출할 것인가?
  - DAO 를 이용해서 DB 에 어떤 쿼리를 보내고 어떤 결과를 받아 올것인가?
  - DB 에 받는 결과를 어떻게 처리해서 뷰에 보낼 것인가?

6. 메서드(execute)의 내용 - boardWrite.jsp 로 이동  
=> 해당 뷰페이지가 [응답]

7. 사용자가 폼의 내용을 채우고 submit 버튼 클릭 => [요청]  
: boardservlet 매핑 주소 => command = 'board_write'

8. boardservlet 서블릿 ----[command]----> ActionFactory

9. ActionFactory
  - 해당 기능을 동작시킬 객체 BoardWriteAction 생성
  - 객체를 boardservlet 서블릿에 반환

10. Boardservlet 서블릿은 Actionfactory 로부터 받은 객체의 메서드(execute)를 실행

11. execute 메서드 내부 :
  - 결과로 어떤 뷰페이지를 호출할 것인가?  
  => 목록페이지
  - DAO 를 이용해서 DB 에 어떤 쿼리를 보내고 어떤 결과를 받아 올것인가?  
  => 가져온 데이터를 DB 에 입력
  - DB 에 받는 결과를 어떻게 처리해서 뷰에 보낼 것인가?

12. 목록을 호출하는 요청으로 이동 => 서블릿에서 목록페이지 호출 => [응답]