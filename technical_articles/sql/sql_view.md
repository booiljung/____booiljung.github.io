[Up](./index.md)

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
