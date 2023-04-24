# 제약조건

제약 조건은 컬럼에 대한 속성 형태로 정의하지만 사실은 객체이며 데이터 무결성을 보작하기 위해 사용된다. 제약조건에는 NOT NULL, UNIQUE, PRIMARY KEY, FOREIGN KEY, CHECK 등이 있다.

<br>

### 1. NOT NULL

<br>


NOT NULL 제약조건은 NULL을 허용하지 않는 제약조건이다. 즉 테이블의 속성중 NOT NULL이 정의된 속성은 데이터가 추가될 때 반드시 값이 들어있어야 테이블에 저장된다.

<br>


```
create table test(
    col_null varchar2(10),
    col_not_null varchar2(10) NOT NULL
);
```

<br>


제약조건은 객체이기 때문에 고유의 이름이 존재하는데 제약조건을 정의할때 별도로 명시하지 않으면 프로그램내에서 자동 생성되며 사용자가 생성한 제약조건은 USER_CONSTRAINTS 시스템 뷰에서 확인할 수 있다.

<br>

```
SELECT constraint_name, constraint_type, table_name,     
    search_condition
FROM user_constraints
WHERE table_name = '???';
```

<br>


### 2. UNIQUE

<br>


UNIQUE 제약조건은 중복값을 허용하지 않는 제약조건이다. 테이블에 데이터를 추가할 때, UNIQUE 제약조건이 명시된 속성값과 동일한 속성값이 이미 테이블에 존재할경우 해당 데이터는 테이블에 저장되지 않는다.

주의할점은 값이 없는경우, 즉 NULL은 제약조건 UNIQUE 비교대상에서 제외되어 영향을 받지 않고 저장된다.

<br>


```
create table test(
    col_not_unique varchar2(10),
    col_unique varchar2(10) UNIQUE
);
```

<br>


### 3. PRIMARY KEY

<br>


기본키는 NOT NULL 제약조건과 UNIQUE 제약조건의 속성을 동시에 가진 
제약조건으로 테이블간 단 한개의 기본키만 생성할 수 있다.

테이블에 들어있는 데이터는 무결성을 유지하고 있어야 한다. <span style="color:yellow"> 데이터 무결성이란 데이터의 정확성과 일관성을 유지한다는 뜻이다.</span> 마치 학생의 학번과 같이 고유한 값을 가지고 있어야만 학번을 통해 특정 학생을 식별할 수 있는 것과 같다. 테이블 내에서는 기본키를 가지고 특정 데이터를 식별할 수 있다.

<br>


```
create table test(
    col_primary varchar2(10) PRIMARY KEY
    col_not_primary varchar2(10),
);
```

<br>


여러 속성을 묶어 하나의 기본키로 만들 수 있다. 이럴경우에는 CONSTRAINT 키워드를 사용해서 기본키에 추가를 해주어야 한다.

<br>


```
create table test(
    col_primary varchar2(10)
    col_add_primary varchar2(10),
    col_not_primary varchar2(10),
    CONSTRAINT PK_TEST PRIMARY KEY (col_primary, col_add_primary)
);
```

<br>


두개 이상의 속성을 묶어 기본키로 지정했을 경우에는 기본키로 지정한 col_primary + col_add_primary 의 조합이 유일해야하며 NULL값은 포함될 수 없다. 

기본키는 최대 32개의 속성을 묶을 수 있으며, 특수한 경우를 제외하고 기본키는 반드시 생성하는것이 원칙이다.

<br>


### 4. FOREIGN KEY

<br>


외래키는 테이블 간의 참조 데이터 무결성을 위한 제약 조건이다. 

```
CONSTRAINT 외래키명 FOREIGN KEY(컬럼명, ...)
REFERENCES 참조 테이블(참조 테이블의 컬럼명, ...)
```

참조할 테이블에 있는 지정한 컬럼에 저장된 값만 테이블에 저장,삭제되도록 제약조건을 걸어 참조 무결성을 보장한다. 

ex) 학년마다 5반까지 있는 학교에서 7반에 학생을 추가, 삭제 하는경우 방지

외래키의 제약사항은 다음과 같다.

<br>


* 반드시 참조 테이블이 먼저 생성되야 하며, 참조키는 참조테이블의 기본키여야 한다.
* 외래키에 사용할 수 있는 속성수는 최대 32개이다.
* 참조하는 속성과 외래키 속성의 순서와 개수는 일치해야 한다.

<br>


### 5. CHECK

<br>


CHECK 제약조건은 입력되는 속성값에 조건을 저정하여 해당 조건에 부합하는 경우에만 테이블에 데이터가 저장되도록 하는 제약조건이다.

```
CREATE TABLE TEST(
    num1 NUMBER,
    gender VARCHAR2(10),
    CONSTRAINT check1 CHECK (num1 BETWEEN 1 AND 10),
    CONSTRAINT check2 CHECK (gender IN ('MALE','FEMALE'))
);
```

위와 같이 제약조건을 걸어둠으로써 num1속성의 값으로 1에서 10까지의 숫자만 속성값으로 들어올 수 있고, gender속성의 값으로는 'MALE','FEMALE' 만 속성값으로 들어올 수 있게 된다.
