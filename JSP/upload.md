# 파일 업로드
* 자바에는 기본적으로 파일 업로드 기능을 제공하고 있지 않다.

* 직접 만들 수도 있지만 이미 만들어진 라이브러리를 사용하는 편이 도움이 된다.

* http://www.servlets.com/ 에서 cos.jar파일을 다운 받는다. (메이븐에서도 다운 가능)

* 파일을 서버로 업로드 하기 위해서는 폼 태그의 전송방식을 Post로만 사용해야 한다.

* <span style="color:yellowgreen">또한 폼태그에 다음과 같은 속성을 추가해야 한다.</span>
    ```html
    enctype="multipart/form-data"
    ```

* 위와 같은 속성을 지정하지 않는 경우 파일 이름만 전송되고 파일객체는 전송되지 않는다.

* cos.jar에는 파일 업로드를 위한 핵심 클래스 MultipartRequest 가 담겨 있다

* MultipartRequest 객체를 생성하기 위해서는 다음과 같은 정보가 필요하다.
  - request : MultipartRequest와 연결할 request 객체
  - saveDirectory : 서버에 저장할 경로
  - maxPostSize : 최대 파일 크기
  - encoding : 파일 인코딩방식 파일 이름이 한글인 경우 매개변수값을 UTF-8 로 준다
  - policy : 파일 중복 처리를 위한 매개변수

* MultipartRequest 객체에 속한 메소드
  - getParameterNames() : 폼에서 전송된 파라미터의 이름을 Enumeration 타입으로 반환한다.
  - getParameterValues() : 폼에서 전송된 파라미터들을 배열로 받아온다.
  - getParameter() : 객체에 있는 해당 파라미터의 값을 가져온다.
  - getFileNames() : 파일을 여러 개 업로드 할 경우 그 값들을 Enumeration 타입으로 반환한다.
  - getFilesystemName() : 서버에 실제로 업로드 된 파일의 이름을 의미한다.
  - getOriginalFileName() : 클라이언트가 업로드한 파일의 원본 이름을 의미한다.
  - getContentType() : 업로드 파일의 컨텐트 타입을 얻을 때 사용한다.
  - getFile() : 서버에 업로드 된 파일의 정보를 객체로 얻어 낼 때 사용한다.

* 여러 파일을 업로드 받기 위해서는 getFileNames 메소드를 통해서 Enumeration 타입으로 전달 받는다.
  - BooleanhasMoreElements() : 데이터가 존재한다면 true 반환 , 그렇지 않으면 false 반환
  - E nextElements() : 데이터(Elements) 를 얻어 낸다.

