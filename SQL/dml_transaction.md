# 데이터 조작

## DML 
 * 데이터를 추가하고, 삭제하고, 변경하는 명령어 쿼리의 집합체
   - 데이터추가 : insert
   - 데이터변경 : update
   - 데이터삭제 : delete

### INSERT
 * Insert는 테이블에 데이터를 삽입할 때 사용하는 명령어이다.
 * 사용법
    ```sql
    INSERT INTO 테이블명(컬럼명1,컬럼명2…) VALUES (데이터1, 데이터2…);
    ```
 * 테이블의 모든 컬럼을 채우면 컬럼명을 생략할 수도 있다.  
  INSERT INTO 테이블명 VALUES (데이터1, 데이터2…);  
  이때 데이터 순서는 테이블에 명시된 순서여야 한다.
 * 데이터의 영구적인 저장을 위해서 COMMIT를 해주고 조회를 해본다.
 * 컬럼에 어떤 값을 넣어야 할지 모를 때 NULL을 입력한다.
   - 컬럼을 생략하고 데이터로 생략하면 NULL값으로 입력이 된다.
   - 명시적으로 NULL이라고 입력해도 NULL값으로 입력이 된다.
   - 테이블 만들 때 NOT NULL 선언하면 NULL 입력시 에러 발생
 * 현재 날짜를 이용할 때는 sysdate를 사용한다.

----------------------------------------------------------
### UPDATE
 * UPDATE는 기존 데이터를 변경할 때 사용하는 명령어이다.
 * 사용법
    ```sql
    UPDATE 테이블명 SET 컬럼명1=데이터1, 컬럼명2=데이터2 … WHERE 조건;
    -- 조건절을 생략하면 해당 컬럼의 모든 데이터가 변경된다.
    ```
 * UPDATE 나 DELETE 는 조건절이 없는게 매우 위험하다.

----------------------------------------------------------
### DELETE
 * DELETE는 기존 데이터를 삭제할 때 사용하는 명령어이다.
 * 사용법
    ```sql
    DELETE FROM 테이블명 WHERE 조건;
    -- 조건에 해당하는 레코드를 삭제한다.
    -- 조건절을 생략하면 모든 레코드가 삭제된다.
    ```
 * 조건절 없이 데이터를 삭제하는 것을 매우 주의하자!

----------------------------------------------------------
# 트랜잭션
 * 트랜젝션이란 데이터 처리를 위한 논리적인 작업의 단위를 의미한다.
 * 오라클은 이런 트랜젝션을 기반으로 데이터의 일관성을 보장한다.
 * SQL 기본명령어 중에서DDL, DCL은 하나의 명령어가 하나의 트랜젝션을 이루고 DML은 하나 이상의 명령으로 트랜젝션을 구성한다.
 * 오라클의 트랜젝션은 데이터의 일관성을 위해서 ALL-or Nothing 방식으로 처리한다.
 * 여러개의 명령어중 하나만 잘못되어도 모든 명령을 취소시켜서 데이터의 일관성을 유지한다.
 * 트랜젝션 관리를 위해 제공하는 명령어는 두가지인데, 
COMMIT 와 ROLLBACK 이다.

----------------------------------------------------------
### COMMIT
 * COMMIT 명령은 모든 작업을 정상 처리 완료하고 처리의 모든 과정을 확정하는 명령이다.
 * 모든 트랜젝션의 처리과정을 데이터베이스에 반영하고 변경된 모든 내용을 영구저장을 한다.
 * COMMIT 명령어를 수행하면 하나의 트랜젝션 과정을 종료한다.

----------------------------------------------------------
### ROLLBACK
 * ROLLBACK 명령은 작업중 문제가 발생해서 트랜젝션 처리 과정에 발생한 변경 내용을 취소하는 명령이다.
 * ROLLBACK은 트랜젝션으로 인한 하나의 묶음 처리가 시작되기 이전 상태로 되돌린다.
 * COMMIT을 하면 하나의 트랜잭션 과정이 종료된 것이기 때문에 그 전 과정을 ROLLBACK 할 수 없다.