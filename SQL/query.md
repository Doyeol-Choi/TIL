# table 조회
```sql
DESC 테이블이름
```

# 특정요소 조회
```sql
SELECT eno, ename, salary, salary*12+NVL(commission, 0) AS "연 봉" FROM employee;
SELECT 컬럼명, 함수 as 별칭 FROM 테이블;
```

# 중복값 제거
```sql
SELECT DISTINCT DNO FROM employee;
SELECT DISTINCT 컬럼명 FROM 테이블이름;
```

# 조건문
```sql
SELECT ename, salary FROM employee WHERE ename = 'SMITH';
-- ' ' 작은따옴표 안에 작성  
SELECT * FROM employee WHERE salary <= 1500;
SELECT * FROM employee WHERE ename != 'SMITH';
SELECT * FROM employee WHERE hiredate <= '1981/05/31' AND salary >= 1200;
-- AND, OR 등을 이용해 조건 2개 이상
SELECT 컬럼명 FROM 테이블 WHERE 조건;
```

# 줄바꿈
 * ;(세미콜론)기준으로 문장이 끝나며, 문장이 길어지면 대문자 명령어 기준으로 줄을 나눈다.


# 명령어 순서
 * SELECT ~ FROM ~ WHERE ~ GROUP BY ~ HAVING ~ ORDER BY