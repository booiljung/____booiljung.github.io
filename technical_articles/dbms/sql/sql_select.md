[Up](./index.md)

## 문과 구문 `SELECT`

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

