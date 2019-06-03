[Up](./index.md)

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
- `ON DELETE RESTRICT`: 삭제시 제약.
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

```mysql
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

