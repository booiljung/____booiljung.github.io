[Up](./index.md)

## `GROUP BY` 문

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

