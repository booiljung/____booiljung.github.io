[Up](./index.md)

## `UNION` 연산자

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

