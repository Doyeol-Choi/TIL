# JOIN
 * 여러 테이블에 저장된 데이터를 한꺼번에 조회할 필요가 있을때 조인을 사용한다.

 * 예를들어 A테이블에 a,b 의 정보가 있고, B테이블에 b,c 정보가 있을때 A테이블의 a정보를 이용해 b를 알아내고 b를 가지고 B테이블의 c 정보를 조회하여 한번에 a,b,c 내용을 모두 조회할 수 있다.

 * 이렇듯 특정 정보를 가지고 어떤 정보를 조회하고자 할 때 여러 테이블을 조회해야 하는 경우가 있는데 이때 조인기술을 사용한다.


## 카테시안 곱
 * 조인은 기본적으로 두 테이블의 결합을 의미하는데 이때 어떤 조건없이 하는 평면 결합을 '카테시안 곱'이라고 한다.
   ```sql
   SELECT * FROM 테이블1, 테이블2;
   ```
 * 카테시안 곱으로 얻어지는 컬럼의 수는 테이블1의 컬럼수 + 테이블2의 컬럼수이다.

 * 레코드의 수는 테이블1의 레코드 수와 테이블2의 레코드 수를 곱한 값이다.

 * 결론은 매우 비효율적이다.

 ## 조인의 종류
 * 오라클에서 사용하는 조인
   - EQUI 조인, NON-EQUI 조인, OUTER 조인, SELF 조인
 * ANSI 표준 SQL 조인
   - INNER조인, CROSS조인, NATURAL조인, JOIN USING, OUTER 조인

 * INNER조인과 OUTER조인을 잘 알고 있자

--------------------------------------------------------------
### EQUI 조인 (INNER 조인)
 * 조인 대상의 두 테이블에서 공통의 칼럼을 '='(equal)비교를 통해서 같은 값을 가지는 행을 연결하여 결과를 생성하는 조인 방법

 * 예: 부서번호를기준으로사원(employee)테이블과부서(department)테이블을조인해본다
   ```sql
   SELECT * FROM department, employee WHERE department.dno = employee.dno;
   ```
 * 예2 : 7788 사원의 이름과 급여 그리고 부서명을 알고 싶다.

   ```sql
   SELECT employee.ename, employee.salary, department.dname FROM department, employee WHERE department.dno = employee.dno AND employee.eno=7788;
   ```
 * dno와 같이 겹치는 컬럼만 확실하게 테이블을 명시해주어도 된다. 테이블의 별칭을 써서 간략하게 사용할 수 있다.
   ```sql
   SELECT ename, salary, dname, e.dno FROM department d, employee e WHERE d.dno = e.dno AND e.eno=7788;
   ```
 * 테이블 별칭 조건
    - 테이블 별칭은 30자까지 가능하다.
    - FROM절에서 테이블명을 명시하고 공백 후 별칭 지정한다.
    - 하나의 SQL 명령문에서 테이블명과 별명을 혼용할 수 없다.
    - 테이블 별칭은 해당 SQL 내부에서만 사용 가능하다.

 * JOIN ON => INNER JOIN (표준)
    - INNER JOIN ~ ON 혹은 JOIN ~ ON 으로 사용 (권장)
   ```sql
   SELECT * FROM department d INNER JOIN employee e ON d.dno = e.dno WHERE eno=7788;
   ```
----------------------------------------------------------
### NON-EQUI 조인
 *  EQUI 조인은 조인의 대상이 되는 컬럼의 일치를 가지고 조인을 한다.
 * NON-EQUI 조인은 조인의 조건이 일치여부가 아닌 특정 범위 내에 있는지 조사하기 위해 사용한다.
 * WHERE 조건절에 <, > 나 BETWEEN a AND b 와 같은 ‘=‘ 이 아닌 연산자 사용한다.
   ```sql    
   SELECT ename, salary, grade FROM employee e, salgrade s WHERE e.salary BETWEEN s.losal AND s.hisal;
   ```
------------------------------------------------------------------
### SELF 조인
 * SELF 라는 말 그대로 자기 자신과 조인을 맺는 것을 말한다.
 * 조인 대상이 자기 자신이라는 것 외는 EQUI와 같다.
 * 특별한 경우가 아니면 사용할 일이 많이 없다.
 * 사원 이름과 담당 직장상사의 이름을 같이 출력해보기.
   ```sql
   SELECT emp.eno, emp.ename, manag.eno AS "상사사원번호", manag.ename AS "상사이름" FROM employee emp, employee manag WHERE emp.manager = manag.eno;
   ```
------------------------------------------------------------------
### OUTER 조인
 * EQUI 조인에서 조인 조건의 칼럼에서 한쪽의 데이터가 없는 경우 = 비교 결과는 거짓이라 조인되지 않는다.
 * 양측 칼럼의 값 중 하나가 NULL인 상태로 조인 결과를 출력할 필요가 있을때 OUTER JOIN을사용한다.
 * OUTER 조인을 할 때는 테이블의 기준점을 잡는 것이 중요하다.

 * LEFT OUTER JOIN (표준)
   - FROM 기준으로 왼쪽 테이블이 모두 합쳐져야 함을 의미
      ```sql
      SELECT * FROM department d LEFT OUTER JOIN employee e ON d.dno = e.dno;
      ```
 * RIGHT OUTER JOIN
    - FROM 기준으로 오른쪽 테이블이 모두 합쳐져야 함을 의미

 * FULL OUTER JOIN
    - LEFT OUTER 와 RIGHT OUTER 조인을 합친 형태

 * 오라클에서는 NULL이 출력 되어야 하는 쪽 조건에 (+)기호를 붙인다.
   ```sql
   SELECT * FROM department d, employee e WHERE d.dno = e.dno(+);
   ```
------------------------------------------------------------------
### INNER 조인
 * 조인 결합 조건이 일치 하는 것만 출력한다.
 * NULL 값이 출력되지 않는다.