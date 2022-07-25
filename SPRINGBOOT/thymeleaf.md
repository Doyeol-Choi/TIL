## 템플릿 엔진(Template Engine)
* 웹 서비스를 만들 떄에는 서버의 데이터와 정적자원(html, css, image)을 조합해야 한다.
* 서버에서 데이터를 보내 웹 서비스를 만드는 방법에는 크게 2가지가 있다.SPA(Single Page Application)
* 최초 한번 전체페이지를 다 불러오고 응답데이터만 페이지 특정부분 렌더링.SSR(Server Side Rendering)
* 전통적인 웹 애플리케이션 방식. 요청시마다 서버에서 처리한 후 새로고침으로 페이지에 대한 응답.

# Thymeleaf(타임리프)
* 타임리프는 템플릿 엔진 중 하나로, Spring Boot에서는 JSP가 아닌 Thymeleaf 사용을 권장하고 있다.

* 타임리프 자주 사용 문법
  - 타임리프는 th 키워드를 사용해서 문법을 사용한다.

  -  th 키워드를 사용하기 위해서는 <html> 부분에 th 설정을 해줘야한다.
    ```html
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    ```

  - 주요 th 종류 설명
    + th:text - 텍스트 지정  
      [[...]] - 텍스트 지정
    + th:fragment - 다른 HTML에서 include 또는 replace 속성을 사용해서 적용할 수 있다.
    + th:block - layoutL:fragment 속성에 이름을 지정해서 실제 Content 페이지의 내용을 채우는 기능이다.
    + th:href - 웹 애플리케이션을 구분하는 콘텍스트 경로(Context Path)를 포함한다.
    + th:value - value 값 지정 (폼 양식)
    + th:each - for 반복문 (값이 하나 일 경우 단순 변수 지정으로도 사용가능)
    + th:if / th:unless - if , else 구문
    + th:switch / th:case - switch 분기 처리 구문

  - 주요 표현식 설명
    + ${} - key 값을 지정할 때 사용하는 변수
    + #{} - 메세지 표현식
    + @{} - 링크 표현식

  - 주요 연산자 설명 (th:타입 = "구문내에서 연산자 사용")
    + and , or , not , 크기 비교 연산자 (< (gt) , > (lt) , >= (ge) , <= (le))
    + 문자 합치기 : 'String' + ${name.name}
    + 리터럴 대체 : |Thenameis ${name} | 

* th URI GET 파라미터 추가 방식
    ```html
    /board/view.do(idx=${idx},page=${page})
    또는
    /board/view.do/${idx}/${page}
    ```

* 추가 자료
  - https://catsbi.oopy.io/32a9458e-f452-4733-b87c-caba75f98e2d

## 타임리프에서 security 사용
* Dependency 추가
    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <!-- 아래부분 추가 / 타임리프랑 붙여서 넣어주니 작동? -->
    <dependency>
        <groupId>org.thymeleaf.extras</groupId>
        <artifactId>thymeleaf-extras-springsecurity5</artifactId>
    </dependency>
    ```

* xmlns:sec 추가
    ```html
    <html lagn="ko"
      xmlns:th="http://www.thymeleaf.org" 
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
    ```

* 자주 사용하는 명령어
  - sec:authorize="isAuthenticated()" : 세션에 계정정보가 있을때 출력
  - sec:authentication="principal.user_nickname" : 세션 계정정보에서 특정 값 출력
  - th문 안에 세션 계정정보 사용하고 싶을때
    ```html
    <img th:src="@{'/image/profile/'+${#authentication.principal.user_picture}}">
    <!-- ${#authentication.principal.xx} -->
    ```