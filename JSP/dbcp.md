# 커넥션 풀 (DBCP)
* 데이터베이스에 어떤 쿼리를 실행하기 위해서 가장 우선해야 할 작업 : 연결된 상태 - 이런 연결된 상태를 커넥션 이라고 부른다.

* 그러나 웹 페이지에 접속자가 많아지면 커넥션도 동일한 수 만큼 만들어 주어야 하기 때문에 서버에 과부하가 걸리게 되고 심한 경우 서버가 다운되는 현상(서버가 폭파 되었다고도 한다)이 발생한다.

* 이런 문제를 해결하기 위해서 나온 기법이 커넥션 풀 이다.

* 커넥션 풀(DBCP : Database Connection Pool)은 dbcp 매니저가 미리 연결 수량을 확보하고서 요청이 들어오면 확보된 연결(커넥션)을 제공하고 처리가 완료되면 다시 연결을 회수해서 재활용하는 방식이다.

* 커넥션 풀을 생성하기 위해서는 우선 프로젝트가 서버에 컨텍스트 패스가 등록되어야 한다. (server.xml)
    ```java
    // 예시
    <Context docBase="web09" path="/web09" reloadable="true" source="org.eclipse.jst.jee.server:web09">
        <Resource name="jdbc/myoracle" auth="Container"
            type="javax.sql.DataSource" driverClassName="oracle.jdbc.OracleDriver"
            url="jdbc:oracle:thin:@localhost:1521:XE"
            username="green" password="1234" maxTotal="20" maxIdle="10"
            maxWaitMillis="-1"/>
    </Context>
    ```

* DBCP의 요소
  - initialSize : 최초 시점에 커넥션 풀을 채워 넣을 커넥션 개수
  - maxTotal : 동시에 사용가능한 최대 커넥션 개수
  - maxIdle : Connection Pool에 반납할 때 최대로 유지될 수 있는 커넥션 개수
  - minIdle : 최소한으로 유지할 커넥션 개수
  - maxWaitMillis : pool이 고갈되었을 경우 최대 대기 시간
