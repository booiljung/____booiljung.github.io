[Up](./index.md)

## 사용자 및 사용자 권한

#### 프로시저 사용 권한 부여 `GRANT EXECUTE ON`

###### MS-SQL

```mssql
GRANT EXECUTE ON procedurename TO username;
```

#### 프로시저 사용 권한 제거 `REVOKE EXECUTE ON`

###### MS-SQL

```mssql
REVOKE EXECUTE ON proceduername FROM username;
```

#### 데이터베이스 사용자 제거 `DROP USER`

###### MS-SQL

```mssql
DROP USER username;
```

#### 서버 액세스 권한 제거 `DROP LOGIN`

###### MS-SQL

```mssql
DROP LOGIN [<computername>\username];
```

