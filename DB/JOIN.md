# JOIN

## INNER JOIN

<br>

### 1. 동등조인 (EQUI-JOIN)

<br>

조인 방법중에서 가장 기본이면서 일반적인 방법이 바로 동등조인 이다.
동등조인은 WHERE 절에서 등호연산자를 사용하여 두개 이상의 테이블이나 뷰를 연결한 조인이다. 이때 WHERE절에 기술한 조건을 조인조건이라고 한다.

조인조건은 컬럼단위로 기술되며, 두 테이블에 공통으로 존재하는 컬럼을 등호연산자로 연결하여 해당 조건에 부합하는 레코드만을 추출하게 된다.

<br>

입력
```
SELECT a.employee_id, a.emp_name, a.department_id, b.department_name
FROM employees a, departments b
WHERE a.department_id = b.department_id;
```

결과
|EMPLOYEE_ID|EMP_NAME|DEPARTMENT_ID|DEPARTMENT_NAME|
|-----------|--------|-------------|---------------|
|198|Donald OConnell|50|배송부|
|199|Douglas Grant|50|배송부|
|200|Jennifer Whalen|10|총무기획부|
|201|Michael Hartstein|20|마케팅|

<br>

이때 조인조건으로 사용된 컬럼인 department_id는 employees 테이블에서는 일반컬럼, departments 테이블에서는 기본키이므로 employees 테이블의 department_id 컬럼에 값이 없어 null값 이 있을 수 있다. 이럴경우 null값이 아닌 레코드만 추출된다.

<br>

### 2. 세미조인 (SEMI-JOIN)

<br>

세미조인은 서브쿼리를 사용하여 서브쿼리에 존재하는 데이터만 메인 쿼리에서 추출하는 조인 방법으로 IN과 EXISTS 연산자를 사용한다.

<br>

#### 2-1. EXISTS 사용

```
SELECT department_id, department_name
FROM departments a
WHERE EXISTS(
    SELECT *
    FROM employees b
    WHERE a.department_id = b.department_id
        AND b.salary > 3000
    )
ORDER BY a.department_name;
```

EXISTS 연산자는 저간에 만족하는 데이터가 한건이라도 있으면 결과를 즉시 반환한다.

<br>

#### 2-2. IN 사용

```
SELECT department_id, department_name
FROM departments a
WHERE a.department_id IN(
    SELECT b.department_id
    FROM employees b
    WHERE b.salary > 3000
    )
ORDER BY a.department_name;
```

IN 연산자는 OR 조건으로 변환할 수 있다.

<br>

세미조인과 동등조인의 차이점은 세미조인은 중복값에 대해 한개의 레코드만 출력되게 되는데, 동등조인을 사용하게 되면 중복값이 모두 출력된다는 점이다.

<br>

### 3. 안티조인(ANTI-JOIN)

<br>

안티조인은 세미조인의 반대개념으로서, 서브쿼리의 B 테이블에 없는 메인쿼리 A테이블의 데이터만 추출하는 조인 방법이다. 즉 두개의 테이블 중 메인쿼리에만 존재하는 데이터를 추출하는 방법으로 NOT IN, NOT EXISTS 연산자를 사용한다. 

<br>

#### 3-1. NOT IN 사용

<br>


```
SELECT a.department_id, a.emp_name, a.department_id, b.department_name
FROM employees a, departments b 
WHERE a.department_id = b.department_id
    AND NOT IN(
        SELECT department_id
        FROM departments
        WHERE manager_id IS NULL
    );
```

서브쿼리에서 departments 테이블 속성중 manager_id 속성값이 null인 레코드중 department_id 속성값을 조회하고 메인 쿼리에서 NOT IN 연산자를 통해 employees 테이블중 서브쿼리의 결과로 조회된 department_id 값을 제외한 레코드를 조회하여 출력하게 된다.

즉 메인쿼리의 결과는 manager_id 값이 null이 아닌 데이터만을 조회하여 출력하게 된다.

<br>

#### 3-2. NOT EXISTS 사용

<br>


```
SELECT count(*)
FROM employees a
WHERE NOT EXISTS( 
        SELECT 1
        FROM departments c
        WHERE a.department_id = c.department_id
            AND manager_id IS NULL
    );
```

위와같이 쿼리문을 작성하여 조회된 결과물을 살펴보면 employees 테이블의 
department_id 값이 null인 값도 출력되게 되는데 그 이유는 서브쿼리에서 조인조건을 명시하는데, department_id가 null로 동일하고, 해당 레코드의 manager_id가 null이 아니기 때문에 서브쿼리에 department_id 가 null인 레코드도 조회되기 때문이다.

<br>


### 4. 셀프조인(SELF-JOIN)

<br>


셀프조인은 하나의 테이블을 사용해 조인하는 방법을 말한다. 주로 하나의 테이블에서 서로 연관이 있는 컬럼을 가지고 있을경우 사용한다.

<br>

입력
```
SELECT a.employee_id, a.emp_name, b.employee_id, b.emp_name, a.department_id
FROM employees a, employees b
WHERE a.employee_id < b.employee_id
    AND a.department_id = b.department_id
    AND a.department_id = 20;
```

결과
|EMPLOYEE_ID|EMP_NAME|EMPLOYEE_ID|EMP_NAME|DEPARTMENT_ID|
|-----------|----------|---------|--------|-------------|
|201|Micharl Hartstein|202|Pat Fay| 20|

<br>

employees 테이블을 a와 b로 나누어 조인을 하게 되는데 해당 쿼리는 같은 부서번호를 가진 사원 중 A사원번호가 B사원번호보다 작은 건을 조회하는 쿼리이다.

employees 테이블에 부서번호가 20 인 건은 2건뿐이며 조건에 의해 결과는 한건만 추출된다.

<br>

 - - -

<br>

## OUTER JOIN

<br>

외부조인은 일반 조인을 확장한 개념으로, 조인조건에 만족하는 데이터 뿐만 아니라 한쪽 테이블에 조인 조건에 명시된 컬럼에 값이 없거나 해당 레코드가 없어도 데이터를 추출한다.

<br>


### 1. 일반 조인

<br>

입력
```
SELECT a.department_id, a.department_name, b.job_id, b.department_id
FORM departments a, job_history b
WHERE a.department_id = b.department_id;
```

결과
|dapertment_id|department_name|job_id|department_id|
|-------------|--------------|-------|-------------|
|20|마케팅|MK_REP|20|
|50|배송부|ST_CLERK|50|
|50|배송부|ST_CLERK|50|
|60|IT|IT_PROG|60|
...

예시에서는 department_id 컬럼을 조인조건으로 두었기 때문에 
department 테이블에 부서정보가 존재한다 하더라도 job_history 테이블에 없다면 조회되지 않는다.

이때 job_history에 없더라도 department 테이블의 모든 데이터를 출력하게 하기 위해서는 외부조인을 사용한다.

<br>

### 2. 외부조인

<br>

입력
```
SELECT a.department_id, a.department_name, b.job_id, b.department_id
FORM departments a, job_history b
WHERE a.department_id = b.department_id(+);
```
결과
|dapertment_id|department_name|job_id|department_id|
|-------------|--------------|-------|-------------|
|10|총무기획부| | |
|20|마케팅|MK_REP|20|
|30|구매/생산부| | |
|40|인사부| | |
|50|배송부|ST_CLERK|50|
...

<br>

조인 조건에서 데이터가 없는 테이블의 컬럼에 (+)기호를 붙여 데이터가 없더라도 조회되도록 했으며, 데이터가 없는경우 컬럼값은
NULL로 출력된다.

<br>

#### 외부조인 정리

1. 조인 대상 테이블 중 데이터가 없는 테이블 조인 조건에 (+)를 붙인다.
2. 외부조인이 여러개일 때에는 해당테이블의 모든 조건에 (+)를 붙인다.
3. 한번에 한 테이블에만 외부 조인을 할 수 있다.
4. (+) 연산자가 붙은 조건과 OR을 같이 사용할 수 없다.

<br>

- - -

## 카타시안 조인

카타시안 조인은 쿼리의 FROM 절에 두 테이블을 명시했지만 두 테이블간 조인 조건이 없는 조인을 말한다. 이와같은 경우에는 결과값은 두 테이블의 레코드 수의 곱이다. 즉 A 테이블에 10개의 레코드가 있고, B 테이블에 3개의 레코드가 있다면 결과값은 30개의 레코드가 조회된다.

<br>


- - -



## ANSI 조인

<br>

ANSI 조인은 ANSI SQL 문법을 사용한 조인을 말하며 앞서 다루었던 모든 조인을 ANSI JOIN으로 표현하여 사용할 수 있다.

기존문법과 ANSI 조인의 차이점은 조인 조건의 위치에 있는데, 조인 조건이 WHERE 절에 위치한 기존 문법과 달리 ANSI 조인은 조인 조건이 FROM 절에 위치한다.

<br>

### ANSI 내부조인

<br>

기존문법
```
SELECT A.컬럼1, A.컬럼2, B.컬럼1, B.컬럼2
FROM 테이블 A, 테이블 B
WHERE A.컬럼1 = B.컬럼1
```

ANSI 문법
```
SELECT A.컬럼1, A.컬럼2, B.컬럼1, B.컬럼2
FROM 테이블 A INNER JOIN 테이블 B
    ON( A.컬럼1 = B.컬럼1 )
WHERE ...
```

<br>

### ANSI 내부조인

<br>

기존문법
```
SELECT A.컬럼1, A.컬럼2, B.컬럼1, B.컬럼2
FROM 테이블 A, 테이블 B
WHERE A.컬럼1 = B.컬럼1 (+)
```

ANSI 문법
```
SELECT A.컬럼1, A.컬럼2, B.컬럼1, B.컬럼2
FROM 테이블 A LEFT JOIN 테이블 B
    ON( A.컬럼1 = B.컬럼1 )
WHERE ...
```

<br>

기존 문법에서는 기준 테이블과 대상 테이블에서 대상 테이블쪽 조인 조건에 (+)를 붙였지만 ANSI 문법에서는 FROM 절에 명시된 테이블 순서에 입각해 먼저 명시된 테이블 기준으로 LEFT 또는 RIGHT를 붙인다.

### CROSS 조인

기본 문법에서 조인조건을 작성하지 않는 카타시안 조인같은 경우 ANSI 문법에서는 CROSS 조인이라고 부른다.

<br>

기본문법
```
SELECT a.employee_id, a.emp_name, b.department_id, b.department_name
FROM employees a, departments b;
```

ANSI 문법
```
SELECT a.employee_id, a.emp_name, b.department_id, b.department_name
FROM employees a CROSS JOIN departments b;
```
<br>

### FULL OUTER 조인

<br>

FULL OUTER 조인 같은 경우에는 ANSI 조인에서만 제공하는 기술로써 기본 문법을 사용해서는 구현할 수 없다.

다음 두 테이블안에 있는 데이터는 다음과 같다.

|TABLE_A|
|-------|
|10|
|20|
|40|

|TABLE_B|
|-------|
|10|
|20|
|30|

40은 A에만 존재하고, 30은 B에만 존재할 때, 두 테이블을 조인하여 다음과 같은 결과를 조회한다.

|EX_A|EX_B|
|----|----|
|10|10|
|20|20|
||30|
|40||

<br>

기본 문법의 외부조인을 사용하여 (+) 를 붙여 표현할 수 있을 것 같지만 기본문법에서 (+)는 한 쪽에만 작성가능하기 때문에 A 테이블과 B 테이블모두 (+)를 붙이게 되면 SQL 오류가 발생하게 된다.

<br>

이때 FULL OUTER JOIN 를 사용하면 문제없이 출력할 수 있다.

```
SELECT a.EX_A, b.EX_B
FROM  TABLE_A a FULL OUTER JOIN TABLE_B b
    ON ( a.emp_id = b.emp_id );
```

<br>

# 참조
[도서] [오라클 SQL과 PL/SQL을 다루는 기술](https://search.shopping.naver.com/book/catalog/32466949488?cat_id=50010586&frm=PBOKPRO&query=%EC%98%A4%EB%9D%BC%ED%81%B4+SQL%EA%B3%BC+PL%2FSQL%EC%9D%84+%EB%8B%A4%EB%A3%A8%EB%8A%94+%EA%B8%B0%EC%88%A0&NaPm=ct%3Dlgt19ff4%7Cci%3D5fc3a30e9cdaedf414cf5f2bd774974597e04077%7Ctr%3Dboknx%7Csn%3D95694%7Chk%3Dc0d983479bb149afc86fd1e8637798ddaac939f6)
