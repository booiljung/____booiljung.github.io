[Up](./index.md)

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

