#  Oracle 개발용

## 접속

DBMS에 접속

```sh
sqlplus '/as sysdba'
```

## 시스템 유저

### SYS:

DBA role을 가지고 있으며, data dictionary table의 소유자.

### SYSTEM:

DBA role을 가지고 있으며, data dictionary view의 소유자.

## 유저 조회

현재 사용자 보기

```sql
SHOW USER
```

유저이름 목록

```sql
SELECT * FROM DBA_USERS;
SELECT * FROM ALL_USERS;
```

username의 system privilege 확인

```sql
SELECT * FROM DBA_SYS_PRIVX WHERE GRANTEE='<username>';
```

username의 role 확인

```sql
SELECT * FROM DBA_ROLE_PRIVS WHERE GRANTEE='<username>';
```

role이 부여된 username 확인

```sql
SELECT * FROM DBA_SYS_PRIVS WHERE GRANTEE='<role>'
```

소유 기준으로 username에 부여된 객체 privilege 확인

```sql
SELECT * FROM DBA_TAB_PRIVS WHERE OWNER='<username>';
```

부여 기준으로 username에 부여된 객체 privilege 확인

```sql
SELECT * FROM DBA_TAB_PRIVS WHERE GRANTEE='<username>';
```

사용자가 소유한 테이블 목록

```sql
SELECT <table_name> FROM USER_TABLES;
```

## 테이블 스페이스

데이터 파일 목록 조회

```sql
SELECT * FROM DBA_DATA_FILES;
SELECT FILE_NAME, BYTES, STATUS FROM DBA_DATA_FILES;
```

테이블 스페이스 조회

```sql
SELECT * FROM DBA_TABLESPACES;
SELECT TABLESPACE_NAME, STATUS, CONTENTS FROM DBA_TABLESPACES;
```

테이블스페이스 생성

```sql
CREATE TABLESPACE <tablespace_name>
DATAFILE <dbf_data_file_path>
SIZE 200m;
```

자동으로 확장하도록 지정.

```sql
CREATE <tablespace_name>
DATAFILE <dbf_data_file_path>
SIZE <dbf_data_file_size>
AUTOEXTEND ON;
```

자동으로 확장 + 확장 크기

```sql
CREATE TABLESPACE <tablespace_name>
DATAFILE <dbf_data_file_path>
SIZE <default_size> AUTOEXTEND ON NEXT <extend_size>;
```

임시 테이블 스페이스 생성 + 자동 확장 + 확장 크기

```
CREATE TEMPORARY TABLESPACE TEMP테이블스페이스명
TEMPFILE <dbf_data_file_path>
SIZE <default_size> AUTOEXTEND ON NEXT <extend_size>;
```

디테일하게 파일 확장 지정

```sql
CREATE TABLESPACE <tablespace_name>
DATAFILE <dbf_data_file_path>
SIZE <dbf_data_file_size>
DEFAULT STORAGE(
    INITIAL		80k
    NEXT		80k
    MINEXTENTS	1
    MAXEXTENTS	121
    PCTINCREASE	80
) ONLINE;
```

## 사용자

### 12c에서 공용사용자 이름 `C##` 강제 규칙

12c에서 공용사용자 이름은 `C##`로 시작하도록 강제되어 있다.

12c에서 공용사용자 이름을 `C##`으로 시작하는 것을 끄려면

```sql
alter session set "_ORACLE_SCRIPT"=true;
```

12c에서 공용사용자 이름을 `C##`으로 시작하는 것을 다시 켜려면

```sql
alter session set "_ORACLE_SCRIPT"=false;
```

sath89 도커 이미지는 `C##`이 꺼져 있습니다. 그런데

```
alter session set "_ORACLE_SCRIPT"=true
```

하니까 C##으로 시작하는 유저 이름을 만들 수 있습니다. 이유는 모릅니다.

### 사용자 생성

```sql
CREATE USER <username>
IDENTIFIED BY <password>;
```

사용자 생성하고 테이블스페이스 권한 부여

```sql
CREATE USER <username>
IDENTIFIED BY <password>
DEFAULT TABLESPACE <tablespace_name>;
```

사용자 생성하고 테이블스페이스 부여하고 임시 테이블 스페이스 부여 (***)

```sql
CREATE USER <username>
IDENTIFIED BY <password>
DEFAULT TABLESPACE <tablespace_name>
TEMPORARY TABLESPACE <temp_tablespace_name>
PROFILE DEFAULT
ACCOUNT UNLOCK;
```

생성한 사용자에게 권한 부여하고 연결 

```sql
GRANT RESOURCE TO <username>;
GRANT CONNECT TO <username>;
GRANT CREATE VIEW TO <username>;
GRANT CREATE PROCEDURE TO <username>;
GRANT CREATE SEQUENCE TO <username>;
GRANT CREATE SESSION TO <username>;
GRANT CREATE TABLE TO <username>;
GRANT DBA TO <username>;
```

username의 잠금 해제

```sql
ALTER USER <username> ACCOUNT UNLOCK;
```

username의 비밀번호를 new_password로 변경

```sql
ALTER USER <username> IDENTIFIED BY '<new_password>';
```

username 삭제

```sql
DROP USER <username> CASCADE;
```

### username에 priviege 부여 및 회수

```sql
GRANT CREATE <privilege> TO '<username>';
REVOKE <privilege> ON <table_name> FROM '<username>';
```

- SYSDBA: 최고 관리자.
- SYSOPER : 최고 운영자.
- CREATE USER: 사용자 생성.
- CREATE TABLE: 테이블 생성.
- CREATE SEQUENCE: 시퀀스 생성.
- CREATE ANY TABLE: 모든 사용자의 테이블 생성.
- CREATE SESSION: 접속.
- CREATE VIEW: 뷰 생성.
- CREATE MATERIALIZED VIEW:
- CREATE PROCEDURE: 프로시저 생성.
- CREATE PUBLIC SYNONYM:
- CREATE TRIGGER:
- CREATE TYPE:
- SELECT ANY TABLE: 모든 사용자의 테이블 조회.
- DROP ANY TABLE:

username에게 모든 권한 주기

```sql
GRANT CONNECT, DBA, RESOURCE TO '<username>';
```

## 테이블스페이스

테이블스페이스 온라인 오프라인 변경

```
ALTER TABLESPACE <tablespace_name> OFFLINE;
ALTER TABLESPACE <tablespace_name> ONLINE;
```

생성된 테이블스페이스의 추가 및  크기 지정

```sql
ALTER TABLESPACE <tablespace_name>
ADD DATAFILE <dbf_data_file_path>
SIZE 100m;
```

생성된 테이블스페이스 크기 변경

```sql
ALTER DATABASE DATAFILE <dbf_data_file_path>
RESIZE 200M;
```

기존 테이블스페이스에 자동확장 변경하기

```sql
ALTER DATABASE DATAFILE <dbf_data_file_path>
AUTOEXTEND ON NEXT 10m
MAXSIZE 100m;
```

테이블스페이스 삭제

```sql
DROP TABLESPACE <tablespace_name>
INCLUDING CONTENTS
CASCADE CONSTRAINTS;
```

데이타 파일까지 삭제

```sql
DROP TABLESPACE <tablespace_name>
INCLUDING CONTENTS AND DATAFILES
CASCADE CONSTRAINTS;
```

## 유저에게 테이블 권한 부여 및 회수

유저에게 테이블 권한 부여.

```sql
GRANT SELECT ON <table_name> TO <username>;
GRANT INSERT ON <table_name> TO <username>;
GRANT DELETE ON <table_name> TO <username>;
GRANT UPDATE ON <table_name> TO <username>;
```

 또는

```sql
GRANT SELECT, INSERT, DELETE, UPDATE ON <table_name> TO <username>;
```

유저에게 테이블 권한 회수.

```sql
REVOKE SELECT ON <table_name> TO <username>;
REVOKE INSERT ON <table_name> TO <username>;
REVOKE DELETE ON <table_name> TO <username>;
REVOKE UPDATE ON <table_name> TO <username>;
```

 또는

```sql
REVOKE SELECT, INSERT, DELETE, UPDATE ON <table_name> TO <username>;
```

## 유저에게 시퀀스 권한 부여 및 회수

유저에게 시퀀스 권한 부여

```sql
GRANT SELECT, ALTER ON <username>.<sequence_name> TO <username>
CREATE SYNONYM <sequence_name> for <username>.<sequence_name>
```

