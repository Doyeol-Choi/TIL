## Project 만들기

* Spring Tool Suite4를 이용해서 Spring Project를 생성하고 Dependencies를 추가하거나

* spring initializr 사이트에 접속해서 필요한 항목을 추가하고 Dependencies를 추가한다.
  - https://start.spring.io/

* 웹 개발에서 자주 사용하는 Dependencies
  - Spring Web
  - Lombok (Dependencies 추가 후 따로 설치 필요)
  - Spring Security
  - JDBC API
  - JDBC Driver (자신이 사용중인 DB의 Driver)
  - Thymeleaf

* pom.xml을 통해 dependencies를 확인하고 업데이트 한다.

* application.properties를 통해 설정을 잡아준다.(MavenProject 기준)
    ```
    # tomcat
    server.port=8080
    server.compression.enabled=true

    # oracle
    #spring.datasource.url=jdbc:oracle:thin:@localhost:1521:xe
    #spring.datasource.username= [아이디]
    #spring.datasource.password= [비밀번호]
    #spring.datasource.driver-class-name=oracle.jdbc.OracleDriver

    #thymeleaf auto refresh
    #thymeleaf 변경시 오토 refresh
    spring.thymeleaf.cache=false

    # mybatis mapping
    # mapper.xml
    # .xml Mapping 시 resultType에 풀패키지명 대신 간략화 하는 설정
    mybatis.type-aliases-package:com.demo.model
    
    # mapper.xml location
    # mapper.xml 파일의 경로를 설정해주는 부분
    mybatis.mapper-locations: mybatis/mapper/**/*.xml

    # security
    spring.security.user.name = [아이디]
    spring.security.user.password = [비밀번호]
    ```


