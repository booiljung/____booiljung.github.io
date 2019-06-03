# SQL 구문 룩업 테이블

---

## 사용자 및 사용자 권한

#### 프로시저 사용 권한 부여 `GRANT EXECUTE ON`

###### MS-SQL

```mssql
GRANT EXECUTE ON procedurename TO username;
```

#### 프로시저 사용 권한 제거 `REVOKE EXECUTE ON`

###### MS-SQL

```mssql
REVOKE EXECUTE ON proceduername FROM username;
```

#### 데이터베이스 사용자 제거 `DROP USER`

###### MS-SQL

```mssql
DROP USER username;
```

#### 서버 액세스 권한 제거 `DROP LOGIN`

###### MS-SQL

```mssql
DROP LOGIN [<computername>\username];
```

---

## 데이터베이스

### 데이터베이스 생성 `CREATE DATABASE`

데이터베이스를 생성합니다.

```sql
CREATE DATABASE databasename;
```

### 데이터베이스 제거 `DROP DATABASE`

데이터베이스를 제거 합니다. 사용자가 지우고자 하는 데이터베이스를 사용중인 동안은 해당 데이터베이스를 삭제할 수 없으므로 삭제하기 이전에 `USE database`로 다른 데이터베이스를 사용해야 합니다.
```sql
DROP DATABASE databasename;
```

### 데이터베이스 사용 `USE DATABASE`

데이터베이스를 사용하도록 선택합니다.

###### MS-SQL

```plsql
USE databasename;
```

### 데이터베이스가 있는지 확인하여 데이터베이스 제거

데이터베이스가 있는지 확인하여 데이터베이스가 있으면 데이터베이스를 제거합니다.

###### MS-SQL

```mssql
IF EXIST(SELECT * from sys.databases WHERE name='databasename')
BEGIN
    DROP databasename;
END
```

---

## 테이블

### 테이블 생성 `CREATE TABLE`

테이블을 생성합니다.

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
   ....
);
```

### 테이블 제거 `DROP TABLE`

테이블을 제거합니다.

```sql
DROP TABLE table_name;
```

### 테이블 컬럼 추가, 삭제, 변경 `ALTER TABLE`

기존 테이블의 컬럼을 추가, 삭제, 변경 합니다.

##### `ADD COLUMN`

컬럼을 추가 합니다.

```sql
ALTER TABLE table_name
ADD column_name datatype; 
```

##### `DROP COLUMN`

컬럼을 제거합니다.

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

##### `ALTER` / `MODIFY` `COLUMN`

컬럼을 변경합니다.

###### SQL Server / MS Access

```sql
ALTER TABLE table_name
ALTER COLUMN column_name datatype;
```

###### My SQL / Oracle (10G 전)

```sql
ALTER TABLE table_name
MODIFY COLUMN column_name datatype; 
```

###### My SQL / Oracle (10G 이후)

```sql
ALTER TABLE table_name
MODIFY column_name datatype;
```

### 테이블의 모든 행을 제거 `DELETE FROM`

###### MS-SQL

```mssql
DELETE FROM tablename;
```

---

## 필드에 대한 제약

제약은 테이블을 생성하거나 테이블을 변경할때 지정할 수 있습니다.

```sql
CREATE TABLE table_name (
    column1 datatype constraint,
    column2 datatype constraint,
    column3 datatype constraint,
    ....
);
```

제약은 7가지가 있습니다.

1. `NOT NULL`: `NULL`일 수 없습니다. 반드시 값이 있어야 합니다.
2. `UNIQUE`: 컬럼내에서 유일한 값이어야 합니다.
3. `PRIMARY KEY`: `NOT NULL`이며 `UNIQUE` 입니다. 각 열을 유니크하게 식별 할 수 있게 합니다.
4. `FOREGIN KEY`: 다른 테이블의 열이나 레코드를 유니크하게 식별합니다.
5. `CHECK`: 값이 지정한 조건에 맞아야 합니다.
6. `DEFAULT`: 컬럽 삽입시 지정하지 않을때 기본값을 지정합니다.
7. 데이터 베이스를 성능을 올립니다. (반드시 그러하지는 않습니다)

### Null을 허용하지 않는 제약 `NOT NULL`

`NULL` 가질 수 없습니다. 값이 반드시 지정되어야 합니다.

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    ...
);
```

### 컬럼 내에서 유일한 값을 가져야 한다는 제약 `UNIQUE`

컬럼 내에서 유일한 값이어야 합니다.

##### `CREATE TABLE`에서의 `UNIQUE` 제약

테이블 생성시 컬럼에 `UNIQIE` 제약을 정의합니다.

###### SQL Server / Oracle / MS Access

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL UNIQUE,
    ...
);
```

###### MySQL

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    UNIQUE(column1),
    ...
);
```

###### MySQL / SQL Server / Oracle / MS Access:

`UNIQUE`제약에 이름을 주어 다수의 컬럼에 `UNIQUE`제약을 정의 합니다.

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    column2 datatype NOT NULL,
    ...
    CONSTRAINT constraint_name UNIQUE(column1, column2, ...)
    ...
);
```

##### `ALTER TABLE`에서의 `UNIQUE` 제약

테이블 변경시 `UNIQUE` 제약을 설정합니다.

###### MySQL / SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
ADD UNIQUE (column1));
```

###### MySQL / SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
ADD CONSTRAINT constrain_name UNIQUE (column1, column2);
```

##### `DROP UNIQUE` 제약

테이블에서 `UNIQUE`제약을 제거합니다.

###### MySQL:

```sql
ALTER TABLE table_name
DROP UNIQUE constraint_name;
```

###### SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
DROP CONSTRAINT constraint_name;
```

### `PRIMARY KEY` 제약

데이터베이스의 테이블에서 각 레코드의 유일성을 식별하는 제약입니다. `PRIMARY KEY`는 `NOT NULL` 이고 `UNIQUE` 입니다. 테이블은 하나의 `PRIMARY KEY`를 가질 수 있습니다.

##### `CREATE TABLE`에서의 `PRIMARY KEY` 제약

###### MySQL:

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    PRIMARY KEY (column1)
    ...
); 
```

###### SQL Server / Oracle / MS Access:

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL PRIMARY KEY,
    ...
); 
```

###### MySQL / SQL Server / Oracle / MS Access:

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    CONSTRAINT constraint_name PRIMARY KEY (column1, ...)
    ...
); 
```

##### `ALTER TABLE`에서의 `PRIMARY KEY` 제약

`ALTER TABLE`에서 `PRIMARY KEY`를 변경합니다.

###### MySQL / SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
ADD PRIMARY KEY (column1);
...
```

###### MySQL / SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
ADD CONSTRAINT constraint_name PRIMARY KEY (column1, ...)
```

##### `DROP PRIMARY KEY` 제약

테이블에서 `PRIMARY KEY`를 제거합니다.

###### MySQL:

```sql
ALTER TABLE table_name
DROP PRIMARY KEY;
```

###### SQL Server / Oracle / MS Access:

```sql
ALTER TABLE Persons
DROP CONSTRAINT constraint_name;
```

### `FOREIGN KEY` 제약

외래키는 연결된 다른 테이블의 행이나 레코드를 식별하며, 다른 테이블의 `PRIMARY KEY`를 참조합니다.

##### `CREATE TABLE`에서 `FOREIGN KEY` 제약

###### MySQL:

```sql
CREATE TABLE table_name1 (
    column1 datatype NOT NULL,
    column2 datatype NOT NULL,
    PRIMARY KEY (column1),
    FOREIGN KEY (column2) REFERENCES table_name2(pk_column_of_table_name2)
    ...
);
```

외래키 생성시 별도의 옵션을 지정하지 않으면 참조되고 있는 값을 삭제 또는 변경하려고 하면, 오류가 발생합니다. 기본 옵션은 아래와 같습니다.
- `ON DELETE RESTRICT`: 삭제시 제악
- `ON UPDATE RESTRICT`: 갱신시 제약.
옵션이 `RESTRICT`일때 참조 당하는 레코드에 대한 삭제를 시도하면 다음과 같은 오류가 발생합니다.

```shell
ERROR 1451 (23000): Cannot delete or update a parent raw: a foregin key constraint fail (database_name/tablename, column) REFEREnCES table_name (column))
```

삭제하거나 변경할때 `CASCADE`를 적용할 수 있습니다.

```sql
CREATE TABLE table_name1 (
    column1 datatype NOT NULL,
    column2 datatype NOT NULL,
    PRIMARY KEY (column1),
    FOREIGN KEY (column2) REFERENCES table_name2(pk_column_of_table_name2) ON DELETE CASCADE ON UPDATE CASCADE,
    ...
);
```

`ON DELETE CASCADE`의 적용은 매우 주의해야 한다. 참조하고 있는 다른 테이블의 레코드까지 삭제합니다. `FOREIGN KEY`의 옵션은 다음과 같습니다.

- `CASCASDE`: 종속 레코드까지 함께 영향을 받습니다. (삭제 되거나 변경 됩니다.)
- `RESTRICT`: 종속 레코드의 삭제나 변경을 금지합니다.
- `NO ACTION`: 해당 레코드만 삭제하거나 변경됩니다.
- `SET NULL`: 종속 레코드의 컬럼의 값이 `NULL`로 됩니다.
- `SET DEFAULT`: 종속 레코드의 컬럼의 값이 기본값으로 됩니다.

###### SQL Server / Oracle / MS Access:

```sql
CREATE TABLE table_name1 (
    column1 datatype NOT NULL PRIMARY KEY,
    column2 datatype FOREIGN KEY REFERENCES table_name2(pk_column_of_table_name2)
    ...
); 
```

##### `ALTER TABLE`에서의 `FOREIGN KEY` 제약

###### MySQL / SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name1
ADD FOREIGN KEY (column2) REFERENCES table_name2(column_of_table_name2);
```

###### MySQL / SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name1
ADD CONSTRAINT constraint_name
FOREIGN KEY (column_name2) REFERENCES table_name2(column_of_table_name2);
```

##### `DROP FOREIGN KEY` 제약

###### MySQL:

```sql
ALTER TABLE table_name1
DROP FOREIGN KEY foreign_key_of_column2;
```

###### SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name1
DROP CONSTRAINT foreign_key_of_column2;
```

### `CHECK` 제약

컬럼의 값이 특정 조건을 충족해야 합니다.

##### `CREATE TABLE`에서 `CHECK` 제약

###### MySQL:

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    column2 datatype NOT NULL,
    CHECK (column2 condition),
    ...
);
```

###### SQL Server / Oracle / MS Access:

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    column2 datatype NOT NULL CHECK(column2 condition),
    ...
); 
```

###### MySQL / SQL Server / Oracle / MS Access:

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL,
    column2 datatype NOT NULL,
    column3 datatype NOT NULL,
    CONSTRAINT constraint_name CHECK (column2 condition AND column3 condition)
); 
```

##### `ALTER TABLE`에서 `CHECK` 제약

###### MySQL / SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
ADD CHECK (column2 condition);
```

###### MySQL / SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
ADD CONSTRAINT constraint_name CHECK (column2 condition AND column3 condition);
```

##### `DROP CHECK` 제약

###### SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
DROP CONSTRAINT constraint_name;
```

###### MySQL:

```sql
ALTER TABLE table_name
DROP CHECK constraint_name;
```

### `DEFAULT` 제약

특별히 지정하지 않을때 기본 값을 지정합니다.

##### `CREATE TABLE`에서 `DEFAULT` 제약

###### My SQL / SQL Server / Oracle / MS Access:

```sql
CREATE TABLE Persons (
    column1 dataype NOT NULL,
    column2 dataype DEFAULT value2,
    column3 dataype DEFAULT GETDATE(),
); 
```

##### `ALTER TABLE`에서 `DEFAULT` 제약

###### MySQL:

```sql
ALTER TABLE table_name
ALTER column1 SET DEFAULT value;
```

###### SQL Server / MS Access:

```sql
ALTER TABLE table_name
ALTER COLUMN column1 SET DEFAULT value;
```

###### Oracle:

```sql
ALTER TABLE table_name
MODIFY column1 DEFAULT value;
```

##### `DROP DEFAULT` 제약

###### MySQL:

```sql
ALTER TABLE table_name
ALTER column1 DROP DEFAULT;
```

###### SQL Server / Oracle / MS Access:

```sql
ALTER TABLE table_name
ALTER COLUMN column1 DROP DEFAULT;
```

### `INDEX` 문

검색 성능을 올리기 위해 색인을 생성합니다.

##### `CREATE INDEX` 구문

테이블에 색인을 생성합니다. 중복값을 허용합니다.
```sql
CREATE INDEX index_name
ON table_name (column1, column2, ...);
```

##### `CREATE UNIQUE INDEX` 구문

테이블에 유일 색인을 생성합니다. 중복값을 허용하지 않습니다.
```sql
CREATE UNIQUE INDEX index_name
ON table_name (column1, column2, ...);
```

##### `DROP INDEX` 문

색인을 제거합니다.

###### MS Access:

```sql
DROP INDEX index_name ON table_name;
```

###### SQL Server:

```sql
DROP INDEX table_name.index_name;
```

###### DB2/Oracle:

```sql
DROP INDEX index_name;
```

###### MySQL:

```sql
ALTER TABLE table_name
DROP INDEX index_name;
```

### `AUTO INCERMENT` 필드

유니크 값이 설정되어 있는 경우 새 레코드가 테이블에 삽입될때 자동 증가를 지정합니다.

###### MySQL:

기본적으로 1에서 시작하여 1씩 증가합니다.

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL AUTO_INCREMENT,
    ...
)
```

또는, 시작값을 지정할 수도 있습니다.

```sql
CREATE TABLE table_name (
    column1 datatype NOT NULL AUTO_INCREMENT=start_value,
    ...
)
```

###### SQL Server:

`IDENTITY` 키워드를 사용합니다. 

```sql
CREATE TABLE table_name (
    column1 datatype IDENTITY(start_value1, increamental_value1) PRIMARY KEY,
    ...
)
```

###### Access:

```sql
CREATE TABLE table_name (
    column1 datatype PRIMARY KEY AUTOINCREMENT,
    ...
)
```

###### Oracle

별도의 시퀀스를 생성하여 테이블 삽입시 직접 값을 지정합니다.

```sql
    CREATE SEQUENCE sequence_name
    MINVALUE min_value
    START WITH start_value
    INCREMENT BY increamental_value
    CACHE cache_count;

    INSERT INTO table_name (column1, column2, column3, ...)
    VALUES (sequence_name.nextval, value2, value3, ...);    
```

### 날짜 타입

###### MySQL:

- DATE: YYYY-MM-DD
- DATETIME: YYYY-MM-DD HH:MI:SS
- TIMESTAMP: YYYY-MM-DD HH:MI:SS
- YEAR: YYYY 또는 YY

###### SQL Server:

- DATE: YYYY-MM-DD
- DATETIME: YYYY-MM-DD HH:MI:SS
- SMALLDATETIME: YYYY-MM-DD HH:MI:SS
- TIMESTAMP: 유니크한 숫자.

---

## `VIEW`

뷰는 `SELECT`를 통해 테이블처럼 보이게 합니다.

#### `CREATE VIEW` 문

뷰를 생성합니다.

```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

#### `CREATE OR REPLACE VIEW` 구문

뷰를 변경합니다.

```sql
CREATE OR REPLACE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

#### `DROP VIEW` 구문

데이터베이스에서 뷰를 제거합니다.

###### MySQL, Oracle, MS-SQL

```sql
DROP VIEW view_name;
```

---

## 문과 구문

### `SELECT` 문

지정한 테이블에서 지정한 컬럼을 선택하여 반환 합니다.

```sql
SELECT column1, column2, ...
FROM table_name;
```

또는 모든 컬럼을 반환하려면,

```sql
SELECT * FROM table_name; 
```

### `SELECT DISTINCT` 문

결과에 대해 중복을 제거 합니다. `MAX`, `SUM`, `HAVING` 절 등을 사용하려면 `GROUP BY`를 사용합니다.

```sql
SELECT DISTINCT column1, column2, ...
FROM table_name;
```

### `WHERE` 절

레코드들에 대해 조건으로 필터를 적용합니다. 

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition; 
```

### `AND`, `OR`, `NOT` 연산자

`WHERE`에서 조건들을 결합합니다.
`NOT`, `AND`, `OR` 순서로 우선도가 높습니다.

#### `AND` 구문

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 AND condition3 ...;
```

#### `OR` 구문

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition1 OR condition2 OR condition3 ...; 
```

#### `NOT` 구문

```sql
SELECT column1, column2, ...
FROM table_name
WHERE NOT condition; 
```

### `ORDER BY` 키워드

결과를 정렬합니다. `ASC`은 오름차순으로, `DESC`는 내림차순으로 정렬합니다.

#### `ORDER BY` 구문

```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC; 
```

### `INSERT INTO` 문

지정한 테이블에 새 레코드를 삽입합니다.

#### `INSERT INTO` 구문

테이블에 새 레코드를 삽입하는 방법은 두가지가 있습니다. 컬럼 이름과 값을 매치하여 지정하는 방법이 있고,

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

또는 컬럼 순서에 따라 값만 지정하는 방법이 있습니다.

```sql
INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```

### `NULL` 값

컬럼에 `NULL`을 허용하면 필드는 널 값을 가질 수 있습니다. `NULL`은 값이 없다는 것을 나타냅니다.

#### `IS NULL` 구문

지정한 컬럼이 `NULL`인지 검사합니다.

```sql
SELECT column_names
FROM table_name
WHERE column_name IS NULL;
```

#### `IS NOT NULL` 구문

지정한 컬럼이 `NULL`이 아닌지 검사합니다.

```sql
SELECT column_names
FROM table_name
WHERE column_name IS NOT NULL; 
```

### `UPDATE` 문

지정한 테이블에서 조건에 맞는 기존의 레코드의 값을 갱신합니다.

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition; 
```

### `DELETE` 문

지정한 테이블에서 조건에 맞는 레코드를 삭제합니다.

```sql
DELETE FROM table_name
WHERE condition;
```

### `TOP`, `LIMIT` or `ROWNUM` 절

레코드의 수량을 지정합니다.

###### SQL Server / MS Access 구문:

첫 3개의 레코드를 지정합니다.

```sql
SELECT TOP number|percent column_name(s)
FROM table_name
WHERE condition;
```

###### MySQL 구문:

최대 수량을 지정합니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
LIMIT number;
```

###### Oracle 구문:

행의 갯수를 지정합니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE ROWNUM <= number;
```

## 함수

##### `MIN()` 함수 구문

선택된 컬럼에서 가장 작은 값을 반환 합니다.

```sql
SELECT MIN(column_name)
FROM table_name
WHERE condition;
```

##### `MAX()` 구문

선택된 컬럼에서 가장 큰 값을 반환 합니다.

```sql
SELECT MAX(column_name)
FROM table_name
WHERE condition;
```

### `COUNT()`, `AVG()`, `SUM()` 함수

##### `COUNT()` 구문

지정한 컬럼의 레코드 수량을 반환합니다.

```sql
SELECT COUNT(column_name)
FROM table_name
WHERE condition;
```

##### `AVG()` 구문

지정한 숫자 컬럼의 평균을 반환합니다.

```sql
SELECT AVG(column_name)
FROM table_name
WHERE condition;
```

### `SUM()` 구문

지정한 숫자 컬럼의 합을 반환합니다.

```sql
SELECT SUM(column_name)
FROM table_name
WHERE condition;
```

### `LIKE` 연산자

pattern이 포함되었는지 비교 합니다. pattern은 와일드카드 문자를 포함 할 수 있습니다.

```sql
SELECT column1, column2, ...
FROM table_name
WHERE string_column LIKE pattern;
```

#### 와일드카드 문자:

- %: 0개 이상의 문자 대치.
- _: 1개의 문자 대치.

###### MS Access나 SQL Server:

문자 범위를 지정할 수 있습니다.

- [charlist]: 문자 범위.
- [^charlist]: 포함되지 않아야 하는 문자 범위.
- [!charlist]: 포함되지 않아야 하는 문자 범위.

---

### `IN` 연산자

`WHERE`절에서 다중 값을 지정할 수 있습니다.

#### `IN` 구문

`IN`다음에 직접 값을 지정합니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...); 
```

또는 서브쿼리로 값을 지정합니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT STATEMENT);
```

### `BETWEEN` 연산자

#### `BETWEEN` 구문

값의 범위를 지정합니다. 값은 숫자, 텍스트, 날짜일 수 있습니다. 두개의 값 사이에는 `AND`를 둡니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2; 
```

---

### 별칭

테이블이나 컬럼에 대해 별칭을 부여하며, 가독성을 위해 사용 되기도 하며, 쿼리 중에만 존재합니다.
복잡한 연산이 적용된 테이블이나 컬럼을 다룰때 유용합니다.

##### 별칭 컬럼 구문

선택된 컬럼에 대해 별칭을 부여 합니다.

```sql
SELECT column_name AS alias_name
FROM table_name;
```

#### 별칭 테이블 구문

선택된 테이블에 대해 별칭을 부여 합니다.

```sql
SELECT column_name(s)
FROM table_name AS alias_name;
```

### `JOIN` 절

두개 이상의 테이블에서 레코드를 결합하기 위해 사용되며 5가지가 있습니다.

- `(INNER) JOIN`
- `LEFT (OUTER) JOIN`
- `RIGHT (OUTER) JOIN`
- `FULL OUTER JOIN`
- `Self JOIN`

##### `(INNER) JOIN` 키워드

두개의 테이블에서 값이 일치하는 레코드를 선택합니다.

```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2 ON table1.column_name = table2.column_name;
```

##### `LEFT (OUTER) JOIN` 키워드

좌측 테이블의 모든 레코드를 선택하고, 우측 테이블에서 값이 일치하는 레코드를 선택합니다.

```sql
SELECT column_name(s)
FROM table1
LEFT JOIN table2 ON table1.column_name = table2.column_name;
```

##### `RIGHT (OUTER) JOIN` 키워드

우측 테이블의 모든 레코드를 선택하고, 좌측 테이블에서 값이 일치하는 레코드를 선택합니다.

```sql
SELECT column_name(s)
FROM table1
RIGHT JOIN table2 ON table1.column_name = table2.column_name;
```

##### `FULL OUTER JOIN` 키워드

두개의 테이블에서 값이 일치하는 모든 레코드를 반환합니다.

```sql
SELECT column_name(s)
FROM table1
FULL OUTER JOIN table2 ON table1.column_name = table2.column_name; 
```

##### Self `JOIN`

특별히 `JOIN` 절을 명시하지 않으며, 묵시적으로 별칭을 지정하여, 조건절에서 사용할 수 있습니다.

```sql
SELECT column_name(s)
FROM table1 T1, table1 T2
WHERE condition;
```

여기서 `T1`, `T2`는 별칭이며 쿼리 내에서 유효합니다.

### `UNION` 연산자

##### `UNION` 구문

두 개 이상의 `SELECT` 문의 결과를 결합합니다. 각 `SELECT` 문의 컬럼 갯수는 동일해야 하며, 컬럼의 데이터 타입은 유사해야 합니다, 각 `SELECT`  문 안의 컬럼은 순서가 같아야 합니다. `UNION` 구문은 기본적으로 중복을 허용하지 않습니다. 중복을 허용하려면 `UNION ALL`을 사용해야 합니다.

```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

##### `UNION ALL` 구문

중복을 허용하는 `UNION` 입니다.

```sql
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

### `GROUP BY` 문

데이터를 원하는 그룹으로 나눌 수 있습니다. `DISTINCT`와 다르게 집계함수(`COUNT`, `MAX`, `MIN`, `SUM`, `AVG`)를 적용할 수 있습니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
ORDER BY column_name(s); 
```

### `DISTINT` 와 `GROUP BY` 문

###### `DISTINCT` 로만 가능한 경우

```sql
SELECT COUNT(DISTINCT column1)
FROM table_name;
```

###### `GROUP BY`로만 가능한 경우

```sql
SELECT column1, MIN(column2), MAX(column2)
FROM table_name
GROUP BY column1;
```

### `HAVING` 절

`WHERE`절에서는 집계함수를 사용할 수 없습니다. `HAVING`절에서 집계함수로 조건을 비교할 수 있으며, 일반적으로 `HAVING` 절은 `GROUP BY` 절과 함께 사용됩니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING condition
ORDER BY column_name(s);
```

### `EXISTS` 연산자

`WHERE`절에서 서브쿼리의 레코드가 존재하는지 확인합니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE EXISTS
(SELECT column_name FROM table_name WHERE condition);
```

### `ANY` 및 `ALL` 연산자

`ANY`와 `ALL` 연산자는 `WHERE`절이나 `HAVING`절의 서브쿼리에서 사용됩니다.

##### `ANY` 구문

서브쿼리 값이 조건에 하나라도 일치하면 `TRUE`를 반환합니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name operator ANY
(SELECT column_name FROM table_name WHERE condition);
```

여기서 operator는 표준 비교 연산자 (=, <>, !=, >, >=, <, <=)입니다.

##### `ALL` 구문

서브쿼리 값이 모두 조건에 일치하면 `TRUE`를 반환합니다.

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name operator ALL
(SELECT column_name FROM table_name WHERE condition);
```

여기서 operator는 표준 비교 연산자 (`=`, `<>`, `!=`, `>`, `>=`, `<`, `<=`)입니다.

### `SELECT INTO` 문

어느 테이블에서 새 테이블로 복사 합니다.

##### `SELECT INTO` 구문

테이블의 전체 컬럼을 새 테이블로 복사합니다.

```sql
SELECT *
INTO newtable [IN externaldb]
FROM oldtable
WHERE condition; 
```

특정 컬러만을 지정하여 새 테이블로 복사 할 수 있습니다.

```sql
SELECT column1, column2, column3, ...
INTO newtable [IN externaldb]
FROM oldtable
WHERE condition;
```

### `INSERT INTO SELECT` 문

소스과 타겟 컬럼의 데이터 타입이 일치해야 합니다. 타겟 테이블에 레코드가 이미 있으면 영향을 받지 않습니다.

##### `INSERT INTO SELECT` 구문

```sql
INSERT INTO table2
SELECT * FROM table1
WHERE condition;
```

특정 컬럼을 다른 테이블로 복사

```sql
INSERT INTO table2 (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM table1
WHERE condition;
```

---

## 프로시저

#### 프로시저 생성 `CREATE PROCEDURE`

프로시저를 생성합니다.

###### MS-SQL

```mssql
CREATE PROCEDURE procedurename @parametername parametertype
	AS
	BEGIN
	-- 프로시저 코드
	END
```

#### 프로시저 실행 `EXCUTE`

프로시저를 실행합니다.

###### MS-SQL

```mssql
EXECUTE procedurename argument;
```

#### 프로세저 제거 `DROP PROC`

프로시저를 제거합니다.

###### MS-SQL

```mssql
DROP PROC procedurename;
```

---

## 트랜젝션

###### MS-SQL

```mssql
BEGIN TRAN;
    -- SQL statements
COMMIT;
---

## 데이터 타입

### MySQL 데이터 타입:

#### MySQL 문자열 데이터 타입:

- `CHAR(size)`: 고정길이 문자열, 최대 255 문자.
- `VARCHAR(size)`: 가변길이 문자열, 최대 255 문자. 255 문자를 초과하면 `TEXT` 타입이 됩니다.
- `TINYTEXT`: 최대 255 문자의 문자열.
- `TEXT`: 최대 65,535 문자의 문자열.
- `BLOB`: 최대 65,535 바이트의 데이터.
- `MEDIUMTEXT`: 최대 16,777,215 문자의 문자열.
- `MEDIUMBLOB`: 최대 16,777,215 바이트의 데이터.
- `LONGTEXT`: 최대 4,294,967,295 문자의 문자열.
- `LONGBLOB`: 최대 4,294,967,295 바이트의 데이터.
- `ENUM(x,y,z,etc.)`: 최대 65,535 개의 값 리스트.
- `SET`: 문자열 목록으로 64개의 서로 다른 멤버를 가질 수 있으며, 각각은 `,`으로 구분됩니다.

#### MySQL 숫자 데이터 타입:
- `TINYINT(size)`: -128 ~ 127.
- `UNSIGNED TINYINT(size)`: 0 ~ 255.
- `SMALLINT(size)`: -32,768 ~ 32,767.
- `UNSIGNED SMALLINT(size)`: 0 ~ 65,535.
- `MEDIUMINT(size)`: -8,388,608 ~ 8,388,607.
- `UNSIGNED MEDIUMINT(size)`: 0 ~ 16777215.
- `INT(size)`: -2,147,483,648 ~ 2,147,483,647.
- `UNSIGNED INT(size)`: 0 ~ 4,294,967,295.
- `BIGINT(size)`: -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807.
- `UNSIGNED BIGINT(size)`: 0 ~  18,446,744,073,709,551,615.
- `FLOAT(size,d)`: 단정도형.
- `DOUBLE(size,d)`: 배정도형.
- `DECIMAL(size,d)`: 문자열로 저장된 `DECIMAL`.

#### MySQL 날짜 데이터 타입:

- `DATE()`: YYYY-MM-DD.
- `DATETIME()`: YYYY-MM-DD HH:MI:SS.
- `TIMESTAMP()`: 초단위로 저장. YYYY-MM-DD HH:MI:SS (UTC 1970-01-01 00:00:01 부터 2038-01-09 03:14:07 까지)
- `TIME()`: HH:MI:SS.
- `YEAR()`: YYYY 또는 YY.

#### SQL Server 데이터 타입.

##### SQL Server 문자열 데이터 타입.

- `char(n)`: 고정길이 문자열, 최대 8,000 문자.
- `varchar(n)`: 가변길이 문자열, 최대 8,000 문자.
- `varchar(max)`: 가변길이 문자열, 최대 1,073,741,824 문자.
- `text`: 가변 길이 문자열, 최대 2G 텍스트 데이터.
- `nchar`: 고정길이 유니코드 문자열, 최대 4,000 문자.
- `nvarchar`: 가변길이 유니코드 문자열, 최대 4,000 문자.
- `nvarchar(max)`: 가변길이 유니코드 문자열, 536,870,912 문자.
- `ntext`: 가변 길이 유니코드 문자열. 최대 2GB 텍스트 데이터.
- `binary(n)`: 고정길이 바이너리 문자열. 8,000 바이트.
- `varbinary`: 가변길이 바이너리 문자열. 8,000 바이트.
- `varbinary(max)`: 가변길이 바이너리 문자열. 최대 2GB.
- `image`: 가변 길이 바이너리 문자열. 최대 2GB.

##### SQL Server 숫자 데이터 타입.

- `bit`: 0, 1 또는 `NULL`을 갖는 정수. 1 byte.
- `tinyint`: 0 ~ 255 범위의 숫자. 1 byte.
- `smallint`: -32,768 ~ 32,767 범위의 숫자. 2 bytes.
- `int`: -2,147,483,648 ~ 2,147,483,647. 4 bytes.
- `bigint`: -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807. 8 bytes.
- `decimal(p, s)`: 고정소수점. 실수의 값을 정확하기 표현하기 위해 사용. 소수부의 자릿수를 미리 정해두고 고정된 자릿수로만 소수 부분을 표현. 범위는 $-10^{38}$ ~ $10^{37}$. `p`는 최대 자리수이며 1~38이며 기본은 18. `s`는 소수점 아래 자리수를 지정하며 0부터 `p`까지이며 기본은 0.
- `smallmoney`:  -214,748.3648 ~ 214,748.3647 의 금전. 4 바이트.
- `money`: -922,337,203,685,477.5808 to 922,337,203,685,477.5807의 금전. 8 바이트.
- `float(n)`: $-1.79^{308}$ ~ $1.79^{308}$. n이 24이면 4 바이트, n이 25~53이면 8 바이트.
- `real`: $-3.40^{38}$ ~ $3.40^{38}$. 4바이트.

#### SQL Server 날짜 데이터 타입.

- `datetime`: 1월 1일부터 12월 31일까지, 9999. 3.33 밀리초 정밀도. 8바이트.
- `datetime2`: 1월 1일부터 12월 31일까지, 9999. 100 나노초 정밀도. 8바이트.
- `smalldatetime`: 1900년 1월 1일부터 2079년 유월 6일까지. 1분 정밀도. 4바이트.
- `date`: 날짜만 저장. 0001년 1월 1일부터 9999년 12월 31일까지.
- `time`: 시간만 저장. 100 나노초 정밀도.
- `datetimeoffset`: `datetime2`와 동일하며 타임존 옵셋이 추가됨. 8~10바이트.
- `timestamp`: 행이 추가되거나 변경될때 유일한 숫자를 저장.

#### SQL Server 기타 데이터 타입.

- `sql_variant`: 최대 8,000 바이트의 various data types, except text, `ntext` 및 `timestamp`를 저장.
- `uniqueidentifier`: GUID를 저장.
- `xml`: XML 형식의 데이터를 저장. 최대 2GB.
- `cursor`: 데이터베이서 조작시 커서 참조를 저장.
- `table`: 나중의 처리 결과 집합을 저장.

#### Access 데이터 타입.

- `Text`: 텍스트 또는 텍스트와 숫자의 결합을 저장. 최대 255 문자.
- `Memo`: 65,536 길이의 문자를 저장. 정렬 불가능.
- `Byte`: 0 ~ 255 범위의 숫자.
- `Integer`: -32,768 ~ 32,767 범위의 숫자.
- `Long`: -2,147,483,648 ~ 2,147,483,647 범위의 숫자.
- `Singl`e: 단정도 부동소수점.
- `Double`: 배정도 부동소수점.
- `Currency`: 최대 15자리의 금전에 4자리의 자리수. 8바이트. 각 국가별 화폐를 저장할때 유용.
- `AutoNumber`: 1부터 시작하는 고유한 숫자를 자동으로 주어진다.
- `Date` / `Time`:
- `Yes` / `No`:
- `Ole Object`:
- `Hyperlink`:
- `Lookup Wizard`:

---

## SQL 내장 함수

### MySQL 내장함수

#### MySQL 문자열 관련 내장 함수

- `ASCII`: 지정한 문자의 ASCII를 반환.
- `CHAR_LENGTH`: 지정한 문자열의 길이를 반환.
- `CHARACER_LENGTH`: 지정한 문자열의 길이를 반환.
- `CONCAT`: 두개 이상의 식을 연결.
- `CONCAT_WS`: 두개 이상의 식을 연결하고 그것을 추가.
- `FIELD`: 목록의 값들에서 해당 값의 위치를 반환.
- `FIND_IN_SET`: `SET`에서 주어진 문자열의 위치를 반환.
- `FORMAT`: `#,###.##`으로 포맷.
- `INSERT`: 문자열에 서브문자열을 삽입.
- `INSTR`: 문자열에서 다른 문자열이 처음 나타나는 위치를 반환.
- `LCASE`: 소문자로 변환.
- `LEFT`: 문자열을 좌측을 기준으로 주어진 길이만큼 추출.
- `LENGTH`: 주어진 문자열의 길이를 바이트 단위로 반환.
- `LOCATE`: 문자열에서 다른 문자열이 처음 나타나는 위치를 반환.
- `LOWER`: 문자열을 소문자로 변환.
- `LPAD`: 문자열의 좌측에 주어진 문자 및 주어진 길이로 패딩.
- `LTRIM`: 문자열의 좌측의 공백 문자를 제거.
- `MID`: 문자열에서 하위 문자열을 추출.
- `POSITION`: 문자열에서 부분 문자열의 위치를 반환.
- `PREEAT`: 주어진 문자열을 주어진 횟수만큼 반복 연결.
- `REPLACE`: 문자열에서 주어진 다른 문자열을 대치.
- `REVERSE`: 문자열의 순서를 뒤집음.
- `RIGHT`: 문자열의 우측을 기준으로 주어진 길이 만큼 추출.
- `RPAD`: 문자열의 우측에 주어진 문자 및 주어진 길이로 패딩.
- `RTRIM`: 문자열의 우측의 공백 문자를 제거.
- `SPACE`: 지정한 길이의 공백 문자열을 반환.
- `STRCMP`: 두개의 문자열을 비교.
- `SUBSTR`: 문자열의 어느 위치라도 지정하여 부분 문자열을 추출.
- `SUBSTRING`: 문자열의 어느 위치라도 지정하여 부분 문자열을 추출.
- `SUBSTRING_INDEX`: 구분 기호 이전의 문자열의 부분 문자열을 반환.
- `TRIM`: 문자열의 시작과 끝에서 공백 문자를 제거.
- `UCASE`: 문자열을 대문자로 변환.
- `UPPER`: 문자열을 대문자로 변환.

#### MySQL 숫자 관련 내장 함수

- `ABS`:
- `ACOS`:
- `ASIN`:
- `ATAN`:
- `ATNA2`:
- `AVG`:
- `CEIL`:
- `CEILING`:
- `COS`:
- `COT`:
- `COUNT`:
- `DEGREES`:
- `DIV`:
- `EXP`:
- `FLOOR`:
- `GREATEST`:
- `LEAST`:
- `LN`:
- `LOG`:
- `LOG10`:
- `LOG2`:
- `MAX`:
- `MIN`:
- `MOD`:
- `PI`:
- `POW`:
- `POWER`:
- `RADIANS`:
- `RAND`:
- `ROUND`:
- `SIGN`:
- `SIN`:
- `SQRT`:
- `SUM`:
- `TAN`:
- `TRUNCATE`:

#### MySQL 날짜 관련 함수

- `ADDDATE`:
- `ADDTIME`:
- `CURDATE`:
- `CURRENT_DATE`:
- `CURRENT_TIME`:
- `CURRENT_TIMESTAMP`:
- `CURTIME`:
- `DATE`:
- `DATEDIFF`:
- `DATE_ADD`:
- `DATE_FORMAT`:
- `DATE_SUB`:
- `DAY`:
- `DAYNAME`:
- `DAYOFMONTH`:
- `DAYOFWEEK`:
- `DAYOFYEAR`:
- `EXTRACT`:
- `FROM_DAYS`:
- `HOUR`:
- `LAS_DAY`:
- `LOCALTIME`:
- `LOCALTIMESTAMP`:
- `MAKEDATE`:
- `MAKETIME`:
- `MICROSECONDS`:
- `MINUTE`:
- `MONTH`:
- `MONTHNAME`:
- `NOW`:
- `PERIOD_ADD`:
- `PERIOD_DIFF`:
- `QUARTER`:
- `SECOND`:
- `SEC_TO_TIME`:
- `STR_TO_DATE`:
- `SUBDATE`:
- `SUBTIME`:
- `TIME`:
- `TIME_FORMAT`:
- `TIME_TO_SEC`:
- `TIMEDIFF`:
- `TIMESTAMP`:
- `TO_DAYS`:
- `WEEK`:
- `WEEKDAY`:
- `WEEKOFYEAR`:
- `YEAR`:
- `YEARWEEK`:

#### MySQL 고급 함수

- `BIN`:
- `BINARY`:
- `CASE`:
- `CAST`:
- `COALESCE`:
- `CONNECTION_ID`:
- `CONV`:
- `CONVERT`:
- `CURRENT_USER`:
- `DATABASE`:
- `IF`:
- `IFNULL`:
- `ISNULL`:
- `LAST_INSERT_ID`:
- `NULLIF`:
- `SESSION_USER`:
- `SYSTEM_USER`:
- `USER`:
- `VERSION`:

### SQL Server 함수

#### SQL Server 문자열 관련 함수.

- `ASCII`:
- `CHAR`:
- `CHARINDEX`:
- `CONCAT`:
- `concat with +`:
- `DATELENGTH`:
- `LEFT`:
- `LEN`:
- `LOWER`:
- `LTRIM`:
- `NCHAR`:
- `PAINDEX`:
- `REPLACE`:
- `RIGHT`:
- `RTRIM`:
- `SPACE`:
- `STR`:
- `STUFF`:
- `SUBSTRING`:
- `UPPER`:

#### SQL Server 숫자 관련 함수.

- `ABS`:
- `AVG`:
- `CEILNG`:
- `COUNT`:
- `FLOOR`:
- `MAX`:
- `MIN`:
- `RAND`:
- `ROUND`:
- `SIGN`:
- `SUM`:

#### SQL Server 날짜 관련 함수.

- `CURRENT_TIMESTAMP`:
- `DATEADD`:
- `DATEDIFF`:
- `DATENAME`:
- `DATEPART`:
- `DAY`:
- `GETDATE`:
- `GETUTCDATE`:
- `MONTH`:
- `YEAR`:

#### SQL Server 변환 함수.

- `CAST`:
- `CONVERT`:

#### SQL Server 고급 함수. 

- `COALESCE`:
- `CURRENT_USER`:
- `ISDATe`:
- `ISNULL`:
- `ISNUMERIC`:
- `NULLIF`:
- `SESSION_USER`:
- `SESSIONPROPERTY`:
- `SYSTEM_USER`:
- `USER_NAME`:

### Oracle 함수

#### Oracle 문자열 함수.

- `ASCII`:
- `ASCIISTR`:
- `CHR`:
- `COMPOSE`:
- `CONCAT`:
- `||`:
- `CONVERT`:
- `DECOMPOSE`:
- `DUMP`:
- `INITCAP`:
- `INSTR`:
- `INSTR2`:
- `INSTR4`:
- `INSTRB`:
- `INSTRC`:
- `LENGTH`:
- `LENGTH2`:
- `LENGTH4`:
- `LENGTHB`:
- `LENGTHC`:
- `LOWER`:
- `LPAD`:
- `LTRIM`:
- `NCHR`:
- `REGEXP_INSTR`:
- `REGEXP_REPLACE`:
- `REGEXP_SUBSTR`:
- `REPLACE`:
- `RPAD`:
- `RTRIM`:
- `SOUNDEX`:
- `SUBSTR`:
- `TRANSLATE`:
- `TRIM`:
- `UPPER`:
- `VSIZE`:
- `ABS`:
- `ACOS`:
- `ASIN`:
- `ATAN`:
- `ATAN2`:
- `AVG`:
- `BITAND`:
- `CEIL`:
- `COS`:
- `COSH`:
- `COUNT`:
- `EXP`:
- `FLOOR`:
- `GREATEST`:
- `LEAST`:
- `LN`:
- `LOG`:
- `MAX`:
- `MEDIAN`:
- `MIN`:
- `MOD`:
- `POWER`:
- `REGEXP_COUNT`:
- `REMAINDER`:
- `ROUND`:
- `ROWNUM`:
- `SIGN`:
- `SIN`:
- `SQRT`:
- `SUM`:
- `TAN`:
- `TANH`:
- `TRUNC`:

#### Oracle 날짜 함수.

- `ADD_MONTHS`:
- `CURRENT_DATE`:
- `CURRENT_TIMESTAMP`:
- `DBTIMEZONE`:
- `EXTRACT`:
- `LAST_DAY`:
- `LOCALTIMESTAMP`:
- `MONTH_BETWEEN`:
- `NEW_TIME`:
- `NEXT_DAY`:
- `ROUND`:
- `SESSIONTIMEZONE`:
- `SYSDATE`:
- `SYSTIMESTAMP`:
- `TRUNC`:
- `TZ_OFFSET`:

