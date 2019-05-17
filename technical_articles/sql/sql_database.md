[Up](./index.md)

## 데이터베이스

### 데이터베이스 생성 `CREATE DATABASE`

데이터베이스를 생성합니다.

```sql
CREATE DATABASE databasename;
```

### 데이터베이스 제거 `DROP DATABASE`

데이터베이스를 제거 합니다.
```sql
DROP DATABASE databasename;
```

사용자가 지우고자 하는 데이터베이스를 사용중인 동안은 해당 데이터베이스를 삭제할 수 없으므로 삭제하기 이전에 `USE database`로 다른 데이터베이스를 사용해야 합니다.

### 데이터베이스 사용 `USE DATABASE`

데이터베이스를 사용하도록 선택합니다.

###### MS-SQL

```plsql
USE databasename;
```

### 데이터베이스가 있는지 확인하여 데이터베이스 제거

데이터베이스가 있는지 확인하여 데이터베이스가 있으면 데이터베이스를 제거합니다.

###### MS-SQL

```mssql
IF EXIST(SELECT * from sys.databases WHERE name='databasename')
BEGIN
    DROP databasename;
END
```

* 이 섹션은 정확성 확인 필요.