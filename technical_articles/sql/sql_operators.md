[Up](./index.md)

## 연산자

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

