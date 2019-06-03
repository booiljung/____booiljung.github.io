[Up](./index.md)

## 존재 여부 확인

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

