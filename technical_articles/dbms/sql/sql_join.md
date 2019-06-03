[Up](./index.md)

## `JOIN` 절

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

