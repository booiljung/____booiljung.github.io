[Up](./index.md)

## 복사

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

