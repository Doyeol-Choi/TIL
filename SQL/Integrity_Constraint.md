# 데이터 무결성
* 무결성은 데이터의 정확성, 일관성, 유효성이 유지되는 것을 말하며 데이터베이스 관리시스템의 중요한 기능이다.
* 개체무결성: 모든 테이블은 기본키로 선택된 필드를 가져야 한다.
* 참조무결성: 참조 관계에 해당하는 두 테이블의 데이터를 항상 일관된 값을 가져야 한다.

----------------------------------------------------------
# 제약조건
* 데이터 무결성 제약조건이란 테이블에 적절하지 않는 값이 입력되는 것을 방지하기 위해 테이블을 생성할 때 각 컬럼에 정의하는 여러가지 규칙을 말한다.

* 종류
  - NOT NULL : 칼럼에 NULL 값을 포함하지 못하도록 지정한다.
  - UNIQUE : 테이블의 모든 로우에 대해서 유일한 값을 갖도록 한다.
  - PRIMARY KEY : 테이블의 각 레코드를 식별하기 위해 사용되는 키로 NULL과 중복된 값을 모두 허용하지 않는다(NOT NULL + UNIQUE)
  - FOREIGN KEY : 참조되는 테이블에 컬럼의 값이 항상 존재해야 한다.
  - CHECK : 저장 가능한 데이터 값의 범위나 조건을 지정하여 설정한 값만을 허용한다.
* 제약조건의 이름부여
  - CONSTRAINT 로 부여한다.
  - CREATE TABLE 테이블명(컬럼명 타입() CONSTRAINT 제약조건이름 제약조건(UNIQUE등));
  - 제약조건이름은 테이블명_컬럼명_조건 으로 만드는게 관례

----------------------------------------------------------
### NOT NULL
* NOT NULL 제약조건을 사용하면 해당 칼럼은 NULL값을 가질 수 없게 된다.
* 반드시 값을 입력해야 하는 칼럼에 지정한다.

----------------------------------------------------------
### UNIQUE
* 제약조건을 사용하면 해당 칼럼은 고유한 값을 가지게 할 수 있다 – 중복데이터 입력시 오류 발생
* 단 NULL값을 허용한다.
* 이름을 명시하지 않으면 자동으로 제약조건의 이름을 부여한다.

----------------------------------------------------------
### PRIMARY KEY (기본키)
* 테이블에 기본키를 생성합니다.
* 테이블에는 반드시 기본키가 존재해야 하며 기본키는 두 가지 특성을 가진다. NULL값을 가질 수 없고, 고유해야 한다.  
(NOT NULL + UNIQUE)
* 기본키는 특정 레코드를 고유하게 구분(식별)할 수 있어야 한다. - 레코드에 영향을 주지 않는 일련번호와 같은 것에 지정  
(개체무결성)

----------------------------------------------------------
### FOREIGN KEY (외래키) - 두 개의 테이블 필요
* 참조 무결성을 위한 외래키 제약조건을 생성한다.
* 외래키 제약조건을 생성하면 두 테이블 간의 관계가 형성된다.
* 외래키가 생성되는 테이블을 참조테이블 또는 자식테이블이라고 부른다.
* 외래키가 가르키는 대상이 되는 테이블을 기준테이블 또는 부모테이블이라고 부른다.
* 참조의 대상이 되는 키는 반드시 기본키나 유니크 제약조건이 있어야 한다. 즉 중복이 없어야 한다.
* 조건
  - 자식테이블의 외래키는 부모테이블의 기본키를 참조하고 있다.
  - 외래키 제약조건에 걸린 해당 컬럼에 데이터를 입력하기 위해서는 부모의 해당 데이터에 존재 해야한다.
  - 부모에 존재하지 않는 데이터 입력시 오류를 발생 시킨다.
* 외래키는 만들때 오류가 나올 가능성이 높아 일반적으로는 테스트까지 끝난 후 마지막에 추가 하는 경우가 많다.

----------------------------------------------------------
### CHECK
* CHECK 제약조건은 컬럼에 저장가능한 데이터의 범위나 조건을 정의한다. 해당 범위나 조건에 벗어나는 값이 들어오면 오류를 발생 시킨다.  
(데이터의 범위 설정)
* 사용할 땐 '컬럼명 CHECK(조건)' 과 같은 방식으로 사용한다.
* CHECK는 자바스크립트, html, 자바에서도 범위를 지정해 줄 수 있기 때문에 중요도는 떨어질 수 있다.

----------------------------------------------------------
### DEFAULT
* 아무런 값을 입력하지 않았을 때 디폴트 값으로 입력되게 만든다.
* 주로 날짜를 입력하는 컬럼에 자주 사용한다. (sysdate)

----------------------------------------------------------
### 제약조건 변경
* 실제로는 테이블을 만들때 처음부터 제약조건을 걸어두기보다는 사용하면서 제약조건을 추가하거나 변경하는 경우가 많다.
* 종류
  - 제약조건 추가 : add
  - 제약조건 변경 : modify
  - 제약조건 제거 : drop

* add 의 예시
  - ALTER TABLE reg_copy ADD CONSTRAINT reg_copy_stuno_fk FOREIGN KEY(stuno) REFERENCES student(stuno);
  - ALTER TABLE stu_copy ADD CONSTRAINT stu_copy_stuno_pk PRIMARY KEY(stuno);
* modify 의 예시
  - ALTER TABLE stu_copy MODIFY major CONSTRAINT stu_copy_major_nn NOT NULL;
* drop 의 예시
  - ALTER TABLE stu_copy DROP PRIMARY KEY;
  - ALTER TABLE stu_copy DROP CONSTRAINT stu_copy_major_nn;

----------------------------------------------------------
### 제약조건 비활성화
* 테스트 등을 할 때 외래키에서 오류가 발생할 경우 제약조건을 비활성화 시킬 수 있다.
* 비활성화 DISABLE
* 활성화  ENABLE
* 이 때문에 외래키를 테스트까지 다 끝나고 적용하는 경우도 있다.