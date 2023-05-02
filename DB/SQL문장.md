# SQL문장

## INSERT
<br>

### 1. 컬럼명 기술 생략

<br>

insert 를 사용하여 DB에 값을 저장할 때 테이블의 모든 컬럼에 값을 넣을경우 테이블명 뒤에 어떤컬럼에 값을 넣을지 결정하는 괄호를 생략할 수 있다.

<br>

```
INSERT INTO 테이블명 VALUES(값1, 값2, ...);
```

<br>

컬럼명을 생략했을때 VALUES 절에 들어갈 컬럼값의 순서는 " SELECT * FROM 테이블명 " 결과로 나오는 컬럼 순서이다. 

<br>

### 2. INSERT ~ SELECT 형태

<br>

INSERT ~ SELECT 형태의 INSERT문은 다른 테이블이나 뷰의 결과로 나온 데이터를 또다른 테이블에 넣는 문장이다.

만약 사원 테이블에서 월급이 5000이상인 사원의 사원번호와 사원명을 조회한 결과를 Test테이블에 넣는다면 SQL문은 다음과 같다.

<br>

```
INSERT INTO test(emp_id, emp_name)
SELECT emp_id, emp_name
FROM employees
WHERE salary > 5000;
```

<br>

위의 SQL문이 실행되면 SELECT 문의 결과로 나오는 데이터들이 test 테이블에 저장된다.

<br>

## MERGE

<br>

MERGE문은 조건을 비교해서 테이블에 해당 조건에 맞는 데이터가 없다면 INSERT, 있으면 UPDATE를 수행하는 문장이다.

<br>

```
MERGE INTO 테이블명
    USING (update 나 insert 가 될 데이터 원천)
        ON (update가 될 조건)
WHEN MATCHED THEN
    SET 컬럼1 = 값1, 컬럼2 = 값2, ...
WHEN update 조건
    DELETE WHERE update_delete 조건
WHEN NOT MATCHED THEN
    INSERT (컬럼1, 컬럼2, ...) VALUES(값1, 값2, ...)
    WHERE insert 조건;
```

<br>

예시를 통해 MERGE문을 사용하는 과정을 알아보면 다음과 같다.

<br>

### 1. test에 사용할 테이블 생성

<br>

 ```
 CREATE TABLE test(
    employ_id NUMBER,
    bonus_amt NUMBER DEFAULT 0;
 )
 ```

<br>

### 2. 조건에 맞는 데이터를 test 테이블에 저장.

<br>

 ```
 INSERT INTO test(employee_id)
 SELECT e.employee_id
 FROM employees e, sales s
 WHERE e.employee_id = s.employee_id
 AND s.SALES_MONTH DETWEEN '200010' AND '200012'
 GROUP BY e.employee.id;
 ```

<br>

위의 조건은 2000년 10월 부터 동년 12월 사이에 매출을 달성한 사원번호를 중복없이 나열했으며 결과는 다음과 같다.

<br>
test

|EMPLOYEE_ID|BONUS_AMT|
|-----------|---------|
|148|0|
|153|0|
|154|0|
|155|0|
|161|0|

<br>

### 3. UPDATE 조건 

<br>

```
SELECT employee_id, manager_id, salary, salary * 0.01
FROM employees
WHERE employee_id IN (
    SELECT employee_id
    FROM TEST
);
```

<br>

위의 SQL문은 test테이블에 작성된 사원번호들의 사원번호, 관리자번호, 급여, 급여의 1%값을 뽑아내는 결과를 갖는다.

<br>

결과
|EMPLOYEE_ID|MANAGER_ID|SALARY|SALARY*0.01|
|-----------|----------|------|-----------|
|153|145|8000|80|
|153|145|7000|70|
|148|100|11000|110|
|161|146|7000|70|
|154|145|7500|75|

### 4. INSERT 조건

<br>

```
SELECT employee_id, manager_id, salary, salary * 0.001
FROM employees
WHERE employee_id NOT IN (
    SELECT employee_id
    FROM TEST
)
AND manager_id=146;
```

<br>

위의 SQL문은 관리자 번호가 146이면서 사원번호가 test테이블에 없는 레코드만 뽑아 사원번호, 관리자번호, 급여, 급여의 0.1%값을 출력하는 결과를 갖는다.

<br>

결과
|EMPLOYEE_ID|MANAGER_ID|SALARY|SALARY*0.01|
|-----------|----------|------|-----------|
|159|146|8000|8|
|156|146|10000|10|
|157|146|9500|9.5|
|160|146|7500|7.5|
|158|146|9000|9|

<br>

### 5. MERGE문 SQL 작성

<br>

MERGE문을 사용하면 위의 3, 4 단계의 작업을 한번에 실행할 수 있다. MERGE문은 다음과 같다.

<br>

```
MERGE INTO test a
    USING (
        SELECT employee_id, salary, manager_id
        FROM employees
        WHERE manager_id = 146
    )b
    ON (a.employee_id = b.employee_id)

WHEN MATCHED THEN
    UPDATE SET a.bonus_amt = a.bonus_amt + b.salary * 0.01

WHEN NOT MATCHED THEN
    INSERT (a.employee_id, a.bonus_amt) 
    VALUES (b.employee_id, b.salary * 0.001)
    WHERE b.salary < 8000;
```

SQL문을 해석하면 MERGE 부분에 값을 저장할 테이블과 조건이 되는 select문을 기본키를 사용하여 연결한다.

그다음 조건에 맞는 컬럼일 경우 WHEN MATCHED THEN 안의 SQL문장이 실행되고, 조건에 맞지 않는경우 WHEN NOT MATCHED THEN 안의 SQL문장이 실행된다.

결과적으로 MERGE문을 사용하면 조건에 맞는경우 UPDATE를 실행하여 값을 최신화 할 수 있고, 조건에 맞지 않는경우 INSERT를 실행하여 값을 추가할 수 있다. 


<br>

# 참조
[도서] [오라클 SQL과 PL/SQL을 다루는 기술](https://search.shopping.naver.com/book/catalog/32466949488?cat_id=50010586&frm=PBOKPRO&query=%EC%98%A4%EB%9D%BC%ED%81%B4+SQL%EA%B3%BC+PL%2FSQL%EC%9D%84+%EB%8B%A4%EB%A3%A8%EB%8A%94+%EA%B8%B0%EC%88%A0&NaPm=ct%3Dlgt19ff4%7Cci%3D5fc3a30e9cdaedf414cf5f2bd774974597e04077%7Ctr%3Dboknx%7Csn%3D95694%7Chk%3Dc0d983479bb149afc86fd1e8637798ddaac939f6)
