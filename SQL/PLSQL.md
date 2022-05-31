# PLSQL - Oracle
* PL/SQL(Oracle's Procedural Language extension to SQL) 은 오라클에서 지원하는 프로그래밍 언어의 특성을 수용해서 기존
SQL 에서는 사용할 수 없는 절차적 프로그래밍 기능으로 SQL 의
단점을 보완하기 위해 만든 언어이다.  

* 변수와 상수를 선언하여 SQL 과 절차적 언어에서 사용
* IF 문을 사용하여 조건에 따라 문장을 분기
* LOOP 문을 사용하여 일련의 문장을 반복적으로 실행
* 커서를 사용하여 여러 행을 검색

* 구조
  - DECLARE : 선언부(변수나 상수 선언)
  - BEGIN : 실행부(제어문 , 반복문 , 함수 정의 등 로직을 기술)
  - EXCEPTION : 예외처리부
  - END

* 작성 방법
  - PL/SQL 블록 내에서는 한 문장이 종료할 때마다 세미콜론 을 쓴다
  - END 뒤에는 을 사용하여 하나의 블록이 끝났음을 명시
  - PL/SQL 블록의 작성은 편집기를 통해 파일로 작성할 수도 있고 , 프롬프트에서 바로 작성 가능
  - SQL*PLUS 환경에서는 DECLARE 나 BEGIN 이라는 키워드로 PL/SQL의 시작을 알린다
  - 블록 내부에서 단일 행 주석은 다중 행 주석은 /* */ 이다
  - 쿼리문 끝에 가 붙으면 종결된 것으로 간주

* <span style="color:greenyellow">반드시 출력하기 위해서는 환경변수 SERVEROUTPUT 을 ON 으로 바꾸어 주어야 한다. (워크시트당 한번만 실행)</span>
* 웹에서만 제어문을 처리하면 서버에 부담이 가기 때문에 sql에서도 제어문을 처리해서 부하를 분산시키는 목적으로 주로 사용된다.

----------------------------------------------------------
## 변수 선언
* PL/SQL 블록 내부에 변수를 사용하려면 선언부(DELCARE)에서
선언해야 하며 변수명 다음에 데이터 타입을 기술해야 한다.
  - 형식 : identifier(변수명) datatype := expression(표현식)
  - 변수명에 CONSTANT를 붙이면 상수로서 작동 => 값을 변경할 수 없다.
  - 타입 뒤에 NOT NULL을 붙이면 반드시 초기값을 지정하도록 제약한다.
  - 보통 한중에 하나의 변수를 정의한다.
* 기본적으로 사용되는 데이터 타입은 스칼라와 레퍼런스 타입으
로 나뉜다.
  - 스칼라 : SQL에서 사용하던 데이터 타입과 일치
  - 레퍼런스 : 기존 칼럼에 사용된 타입을 선언하기 위해서 사용하는 타입
    + 테이블명.컬럼명%type => 해당 컬럼 타입 가져오기
    + 테이블명%rowtype => 해당 레코드 전체 타입 가져오기  
    이렇게 가져온 테이블 변수는 <span style="color:skyblue">_변수명.컬럼명_</span> 으로 사용
    + 개발자는 테이블에 정의된 컬럼의 타입이나 크기를 알 수 없으므로 유용하다
  - 대입문 (:=) => 변수 := 값
* PL/SQL에서도 테이블의 조회를 위해서 SELECT문을 사용한다.
  - 다만 일반 SQL 문과 차이점은 INTO라는 절이 추가적으로 필요
한다.
  - INTO절의 역할은 조회된 데이터를 저장하기 위한 변수를 연결
하기 위함이다.

----------------------------------------------------------
## 제어문
* IF문 (조건문)
  - 조건의 값이 참과 거짓을 판단해서 실행문을 실행할지 판단하는 문장이다. (IF 조건 THEN 실행문;)
  - ELSIF 절은 여러 번 (ELSIF 조건 THEN 실행문;)
  - ELSE는 한번만 사용가능 (ELSE 실행문; ENDIF;)
    ```sql
    DECLARE
        변수 선언
    BEGIN
        IF 조건 THEN 실행문;
        ELSIF 조건 THEN 실행문;
        ELSE 실행문;
        END IF;
    END;
    ```

* LOOP문 (반복문)
  - 어떤 반복적으로 수행되는 문장이 있을 경우 LOOP 문을 사용합니다.
  - LOOP문의 종류
    + BASIC LOOP문 : 가장 간단한 형태의 루프문으로 LOOP~END LOOP사이를 반복한다.
    ```sql
    DECLARE
    dan number(1) := 5;
    i number := 1;
    BEGIN
    dbms_output.put_line('***구구단 5단***');
    LOOP
        dbms_output.put_line(dan||' X '||i||' = '||(dan*i));
        i := i+1;
        IF i>9 THEN
            EXIT;
        END IF;
    END LOOP;
    END;
    ```
    + FOR LOOP문 : 반복 횟수가 정해진 반복문을 처리한다.
      * 수행할 반복횟수를 정하기 위해 LOOP앞에 제어문을 갖는다.

    ```sql
    DECLARE
        dan number(1) := 3;
        i number := 1;
    BEGIN
        dbms_output.put_line('***구구단 3단***');
        FOR i IN 1..9 LOOP
        dbms_output.put_line(dan||' X '||i||' = '||(dan*i));
        END LOOP;
    END;
    ```
    + WHILE LOOP문 : 제어 조건이 참인 동안만 문장을 반복한다.
      * 조건은 반복이 시작될 때 체크하므로 LOOP내의 문장이 한번도 수행 되지 않을 수 있다.
    ```sql
    DECLARE
    dan number(1) := 9;
    i number := 1;
    BEGIN
    dbms_output.put_line('***구구단 9단***');
    WHILE i < 10 LOOP
    dbms_output.put_line(dan||' X '||i||' = '||(dan*i));
    i := i + 1;
    END LOOP;
    END;
    ```

----------------------------------------------------------
## 커서
* SELECT문의 수행 결과가 여러 개의 레코드로 이루어진 경우 각
각 레코드에 대한 처리를 하기 위해서 커서를 사용해야 한다.
* 커서는 CURSOR, OPEN, FETCH, CLOSE 4 단계 명령에 의해서 사용된다.
* 형태
```sql
DECLARE
    CURSOR 커서이름 IS select 쿼리 (INTO 미포함)
BEGIN
    OPEN 커서이름
    FECTCH 커서이름 INTO 저장할 변수들
    
    - 주로 반복문을 통해서 여러 개의 레코드를 FETCH 한다
    - 커서이름%NOTFOUND 조건으로 반복을 종료한다

    CLOSE 커서이름
END;
```
* 커서의 상태
  - %NOTFOUND : 커서 영역의 자료가 모두 FETCH 되었다면 TRUE
  - %FOUND : 커서 영역에 FETCH 되지 않은 자료가 있다면 TRUE
  - %ISOPEN : 커서가 OPEN 된 상태이면 TRUE
  - %ROWCOUNT : 커서가 얻어온 레코드의 개수

* for루프문은 명시적으로 커서를 처리한다.
  - 즉 open, fetch, close 없이 FOR 루프문 내부에서 처리를 한다.
  ```sql
    DECLARE
        v_emp employee%rowtype;
        CURSOR c_emp IS SELECT * FROM employee;
    BEGIN
        dbms_output.put_line('사원번호  이름  급여  부서번호');
        dbms_output.put_line('------------------------------------------');
    
        FOR v_emp IN c_emp LOOP
        dbms_output.put_line(v_emp.eno||'   '||v_emp.ename||'   '||v_emp.salary||'   '||v_emp.dno);
    END LOOP;
    END;  
  ```
