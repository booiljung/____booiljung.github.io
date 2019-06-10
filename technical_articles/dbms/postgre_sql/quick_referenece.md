# PostgreSQL 11 Quick Reference

### Notation

대문자: 키워드

소문자: 사용자 정의 단어

`[]`: 선택 가능한 옵션

`<>`: 필수 옵션

### 설치

#### 설치 여부 확인

```
$ aptitude show postgresql | grep State
State: not installed
```

#### 설치

```
$ sudo apt-get install postgresql
```

##### 설치 확인

```
$ dpkg -l | grep postgres
```

```
ii  postgresql                                 10+190                                       all          object-relational SQL database (supported version)
ii  postgresql-10                              10.8-0ubuntu0.18.04.1                        amd64        object-relational SQL database, version 10 server
ii  postgresql-client-10                       10.8-0ubuntu0.18.04.1                        amd64        front-end programs for PostgreSQL 10
ii  postgresql-client-common                   190                                          all          manager for multiple PostgreSQL client versions
ii  postgresql-common                          190                                          all          PostgreSQL database-cluster manager
```

##### 계정 확인

```
$ cat /etc/passwd | grep postgres
```

```
postgres:x:127:135:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
```

##### 상태 확인

```
$ /etc/init.d/postgresql status
```

```
● postgresql.service - PostgreSQL RDBMS
   Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
   Active: active (exited) since Thu 2019-06-06 20:42:21 KST; 1min 38s ago
 Main PID: 9441 (code=exited, status=0/SUCCESS)
    Tasks: 0 (limit: 4915)
   CGroup: /system.slice/postgresql.service

 6월 06 20:42:21 booil1804 systemd[1]: Starting PostgreSQL RDBMS...
 6월 06 20:42:21 booil1804 systemd[1]: Started PostgreSQL RDBMS.
```

##### 네트워크 확인

```
$ sudo netstat -tnlp | grep postgres
```

```
tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      11354/postgres
```

#### psql 접속

psql로  `postgre`계정에 접속: 

```
$ sudo -u postgres psql
```

#### psql 로그 아웃

```
\q
```

#### 패스워드 설정

DB관리자 패스워드 변경:

```
alter user postgres with password '새패스워드'
```

#### 접속 IP 설정

```
$ ps -ef | grep postgresql.conf | grep -v grep
```

```
$ postgres 11354     1  0 20:42 ?        00:00:00 /usr/lib/postgresql/10/bin/postgres -D /var/lib/postgresql/10/main -c config_file=/etc/postgresql/10/main/postgresql.conf
```

설정파일 변경

```
$ sudo vi /etc/postgresql/10/main/postgresql.conf
```

```
listen_addresses = 포트번호 지정
```

재시작

```
$ sudo /etc/init.d/postgresql restart
```

#### 테스트

원격지에서 접속 여부 확인

```
$ psql -h 아이피주소 -U postgres template1
```

### DBMS

##### 시작

```
$ psql database_name
```

##### 버전 확인

```
SELECT VERSION()
```

##### 현재 날짜 확인

```
SELECT CURRENT_DATE
```

##### 간단한 계산

```
SELECT expression
```

##### psql 종료

```
\q
```

## 유저

#### 유저 조회

```sql
SELECT * FROM PG_SHADOW;
```

또는

```
\du
```

또는 추가 정보를 보려면

```
\du+
```



#### 유저 ROLE

| roles         | 설명                   |
| ------------- | ---------------------- |
| `SUPERUSER`   | 유저 생성 및 권한 부여 |
| `CREATE ROLE` | role을 생성            |
| `CREATE DB`   | DB 생성                |
| `REPLICATION` | DB 복사                |

#### 유저 생성

```sql
CREATE USER 유저이름 [[WITH] 옵션들 ...]
```

| 옵션                                           | 설명                                   |
| ---------------------------------------------- | -------------------------------------- |
| `[SUPERUSER | NOSUPERUSER(기본값)]`            | superuser role 부여                    |
| `[CREATEDB | NOCREATEDB(기본값)]`              | DB 생성 role 부여                      |
| `[CREATEUSER | NOCREATEUSER(기본값)]`          | 유저 생성 role 부여                    |
| `[INHERIT (기본값) | NOINHERIT]`               | role이 다른 유저에게 상속되는 역할     |
| `[LOGIN | NOLOGIN]`                            | 로그인 role 부여                       |
| `[CONNECTION LIMIT collection_limit]`          | 동시연결세션수, 기본값은 무제한으로 -1 |
| `[ENCRYPED | UNENCRYPED] PASSWORD '패스워드']` | 패스워드 지정, 생략 가능               |

#### 유저 변경

```
ALTER USER 유저이름 [[WITH] 옵션 [...]]
```

##### 유저 이름 변경

```
ALTER USER 유저이름 RENAME TO 새유저이름
```

#### 유저 삭제

```
DROP USER 유저이름
```

### 데이터베이스

#### 데이터베이스 생성

셸에서

```
$ sudo -u postgres createdb 데이터베이스이름
```

또는 psql에서

```
CREATE DATABASE 데이터베이스이름 [옵션들 ...]
```

| options                                   | 설명                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| `[[WITH] [OWNER [=] 새오너이름]`          | 데이터베이스를 소유할 유저이름 지정                          |
| `[TEMPLATE [=] template]`                 | 템플리트 지정                                                |
| `[LC_COLLATE [=] lc_collate`              | 컬럼과 인덱스, `ORDER BY`절의 문자열에 적용되는 정렬 순서 설정 |
| `[LC_CTYPE [=] lc_ctype]`                 | 문자 분류로 대소문자 숫자 분류를 설정                        |
| `[TABLESPACE [=] tablespace]`             | 지정한 TABLESPACE가 데이터베이스에서 만든 개체에 사용되는 기본 테이블이 됨. |
| `[CONNECTION LIMIT [=] collection_limit]` | 데이터베이스에 대한 동시 접속 수 제한. 기본값은 -1로 무제한. |

#### 데이터베이스 변경

psql에서:

```
ALTER DATABASE 데이터베이스이름
```

#### 데이터베이스 삭제

psql에서:

```
DROP DATABASE 데이터베이스이름
```

#### 데이터베이스 목록

psql에서:

```
\l
```

또는

```
\list
```

```
                                  List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
-----------+----------+----------+-------------+-------------+-----------------------
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
           |          |          |             |             | postgres=CTc/postgres
```

또는

```
SELECT * FROM pg_database;
```

```
  datname  | datdba | encoding | datcollate  |  datctype   | datistemplate | datallowconn | datconnlimit | datlastsysoid | datfrozenxid | datminmxid | dattablespace |               datacl                
-----------+--------+----------+-------------+-------------+---------------+--------------+--------------+---------------+--------------+------------+---------------+-------------------------------------
 postgres  |     10 |        6 | en_US.UTF-8 | en_US.UTF-8 | f             | t            |           -1 |         13054 |          548 |          1 |          1663 | 
 template1 |     10 |        6 | en_US.UTF-8 | en_US.UTF-8 | t             | t            |           -1 |         13054 |          548 |          1 |          1663 | {=c/postgres,postgres=CTc/postgres}
 template0 |     10 |        6 | en_US.UTF-8 | en_US.UTF-8 | t             | f            |           -1 |         13054 |          548 |          1 |          1663 | {=c/postgres,postgres=CTc/postgres}
(3 rows)

```

#### 데이터베이스 오너 변경

psql에서:

```
ALTER DATABASE OWNER TO 새오너이름
```



### 스키마

#### 스키마 생성

```
CREATE SCHEMA 스키마이름 [AUTHORIZATION 유저이름]
```

```
CREATE SCHEMA AUTHORIZATION 유저이름 [3. 스키마원소 [ ... ] ]
```

```
CREATE SCHEMA IF NOT EXISTS 스키마이름 [ AUTHORIZATION 유저이름 ]
```

```
CREATE SCHEMA IF NOT EXISTS AUTHORIZATION 유저이름
```

#### 스키마 변경

```
ALTER SCHEMA 스키마이름 RENAME TO 새스키마이름
```

```
ALTER SCHEMA 오너유저이름 OWNER TO 새유저이름
```

#### 스키마 삭제

```
DROP SCHEMA 스키마이름
```

#### 스키마 경로 검색

```
SHOW SEARCH_PATH
```

### 테이블스페이스

#### 테이블스페이스 생성

```
CREATE TABLESPACE 테이블스페이스이름 옵션들...
```

| 옵션                | 설명           |
| ------------------- | -------------- |
| OWNER 소유유저이름  | 소유 유저 이름 |
| LOCATION '디렉토리' | 디렉토리       |

#### 테이블스페이스 변경

```
ALTER TABLESPACE 테이블스페이스이름
```

| options                          | 설명           |
| -------------------------------- | -------------- |
| `RENAME TO 새테이블스페이스이름` | 이름 변경      |
| `OWNER TO 새오너유저이름`        | 소유 유저 변경 |

#### 테이블스페이스 삭제

```
DROP TABLESPACE 테이블스페이스이름
```

## 데이터 타입 (type)

| 타입 이름                           | 별칭             | 설명                                          |
| ----------------------------------- | ---------------- | --------------------------------------------- |
| `bigint`                            | `int8`           | 부호 있는 8바이트 정수                        |
| `bigserial`                         | `serial8`        | 자동 증가하는 8바이트 정수                    |
| `bit[(n)]`                          |                  | 고정길이 비트열                               |
| `bit varying [(n)]`                 | `varbit[(n)]`    | 가변길이 비트열                               |
| `boolean`                           | `bool`           | 부울                                          |
| `box`                               |                  | 평면상의 직사각형                             |
| `bytea`                             |                  | 바이너리 데이터 ("byte array")                |
| `character[(n)]`                    | `char[(n)]`      | 고정길이 문자열                               |
| `character varying[(length)]`       | `varchar[(n)]`   | 가변길이 문자열                               |
| `cidr`                              |                  | IPv4 또는 IPv6 네트워크 주소                  |
| `circle`                            |                  | 평면상의 원                                   |
| `date`                              |                  | 날짜                                          |
| `double precision`                  | `float8`         | 배정도 부동소수점 수                          |
| `inet`                              |                  | IPv4 또는 IPv6 호스트 주소                    |
| `integer`                           | `int`, `int4`    | 부호 있는 4바이트 정수                        |
| `interval[fields][(p)]`             |                  | 시간 간격                                     |
| `json`                              |                  | JSON                                          |
| `jsonb`                             |                  | 압축되지 않은 이진 JSON, BSON                 |
| `line`                              |                  | 평면상의 직선                                 |
| `lseg`                              |                  | 평면상의 선분                                 |
| `macaddr`                           |                  | MAC (Media access control) 주소               |
| `macaddr8`                          |                  | MAC (Media Access Control) 주소 (EUI-64 포맷) |
| `money`                             |                  | 통화액                                        |
| `numeric[(p,s)]`                    | `decimal[(p,s)]` | 정밀도를 선택 가능한 수                       |
| `path`                              |                  | 평면상의 기하하적 경로                        |
| `pg_lsn`                            |                  | PostgresSQL 로그 시퀀스 번호                  |
| `point`                             |                  | 평면상의 기하학적 위치                        |
| `polygon`                           |                  | 평면상의 기하학적 다각형                      |
| `real`                              | `float4`         | 단정도 부동소수점 수                          |
| `smallint`                          | `int2`           | 부호 있는 2바이트 정수                        |
| `smallserial`                       | `serial2`        | 자동증가 2바이트 정수 (부호는?)               |
| `serial`                            | `serial4`        | 자동증가 4바이트 정수 (부호는?)               |
| `text`                              |                  | 가변길이 문자열                               |
| `time[(p)][without time zone]`      |                  | Time of day without time zone                 |
| `time[(p)][with time zone]`         | `timetz`         | Time of day with time zone                    |
| `timestamp[(p)][without time zone]` |                  | 날짜와 시간 without time zone                 |
| `timestamp[(p)][with time zone]`    | timestamptz      | 날짜와 시간 with time zone                    |
| `tsquery`                           |                  | 텍스트 검색 쿼리                              |
| `tsvector`                          |                  | 텍스트 검색 문서                              |
| `txid_snapshot`                     |                  | 유저 레벨 트랜잭션 ID 스냅샷                  |
| `uuid`                              |                  | universally unique identifier                 |
| `xml`                               |                  | XML                                           |

### 테이블

#### 테이블 생성

```
CREATE TABLE 테이블이름 (컬럼들, ...)
```

`컬럼`들은:

```
컬럼이름		자료형
```

#### 테이블 변경

```
ALTER TABLE 테이블이름 [options]
```

##### 테이블 컬럼 추가

```
ALTER TABLE ADD COLUMN 컬럼옵션
```

##### 테이블 컬럼 삭제#################

```
ALTER TABLE
```

##### 테이블 컬럼 변경################

```
ALTER TABLE
```

##### 테이블 컬럼 이름 변경

```
ALTER TABLE 테이블이름 RENAME COLUMN 컬럼이름 TO 새컬럼이름
```

##### 테이블 컬럼을 다른 테이블스페이스로 이동

```
ALTER TABLE 테이블이름 SET TABLESPACE 다른테이블스페이스이름
```

#### 테이블 삭제

소유자나 수퍼유저만 삭제 가능.

```
DROP TABLE 테이블이름
```

테이블이 존재하면 삭제

```
DROP TABLE IF EXIST 테이블이름
```

### 제약 조건

| 이름                | 설명                          |
| ------------------- | ----------------------------- |
| `CHECK(논리적조건)` | 비교                          |
| `NOT NULL`          | 유효해야 함                   |
| `UNIQUE`            | 열에서 모든 행에 유일해야 함. |
| `PRIMARY KEY`       | `NOT NULL` AND `UNIQUE`       |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |
|                     |                               |

#### 외래키

다른 테이블의 primary key를 말함.

```
...
	FOREIGN KEY (컬럼, ...) REFERENCES 테이블이름 (컬럼, ...)
```

테이블 조작

#### 테이블 열 조회

```
SELECT [ ALL | DISTINCT [ ON (expression, ...) ] ]
	[ * | expression [ [ AS ] output_name ], ...]
	[ FROM from_item, ... ]
	[ WHERE condition, ... ]
	[ GROUP BY expression, ...]
	[ HAVING condition, ...]
	[ WINDOW window_name AS window_definition, ...]
	[ < UNION | INTERSECT | EXCEPT > [ALL | DISTNCT] select]
    [ ORDER BY expression [ ASC | DSC | USING operators ] [ NULLS < FIRST | LAST > ], ...]
    [ LIMIT < count | ALL > ]
    [ OFFSET start [ ROW | ROWS ]]
```

| Options                                 | 설명                                         |
| --------------------------------------- | -------------------------------------------- |
| `SELECT ALL`                            | 중복되는 열까지 포함                         |
| `SELECTION DISTINCT ON expression, ...` | 중복되는 열 제외                             |
| `AS output_name`                        | 별칭 지정                                    |
| `FROM from_item, ...`                   | 가져올 테이블들을 지정                       |
| `WHERE condition, ...`                  | `FROM`절의 테이블 내에서 가져올 조건 지정    |
| `GROUP BY expression, ...`              | 그룹 조건 지정                               |
| `HAVING condition, ...`                 | `GROUP BY` 절과 함께 쓰이며 그룹의 조건 지정 |
| `UNION`                                 | 열의 개수가 동일 & 타입이 동일 합집합        |
| `INTERSECT`                             | 열의 개수가 동일 & 타입이 동일 교집합        |
| `EXCEPT`                                | 열의 개수가 동일 & 타입이 동일 차집합        |
| `ORDER BY expression`                   | 오른차순 또는 내림 차순 정렬                 |
| `LIMIT`                                 | 쿼리 열 갯수 제한                            |
| `OFFSET`                                | 쿼리 열 시작 갯수 제한                       |

테이블의 모든 열의 행 조회

```
SELECT *
	FROM 테이블이름
```

테이블의 모든 열의 지정한 행 조회

```
SELECT column_names, ...
	FROM 테이블이름
```

테이블의 지정한 조건의 모든 열의 행 조회

```
SELECT *
	FROM 테이블이름
	WHERE conditions
```

테이블의 지정한 조건의 모든 열이 행을 기준 행을 지정하고 정렬하여 조회

```
SELECT *
	FROM 테이블이름
	ORDER BY column_name
```

지정한 컬럼의 중복을 제거하여 조회

```
SELECT DISTINCT column_names
	FROM 테이블이름
```

지정한 컬럼의 중복을 제거하고 정렬하여 조회

```
SELECT DISTINCT column_names
	FROM 테이블이름
	ORDER BY column_names
```

#### 테이블에 열 삽입 (INSERT)

열의 좌측 컬럼부터 순차적으로 값 지정.

```
INSERT INTO 테이블이름 VALUES (value, ...)
```

지정한 컬럼만 값을 지정.

```
INSERT INTO 테이블이름 (column_names,...) VALUES (values, ...)
```

텍스트 파일에서 가져 오기

```
COPY 테이블이름 FROM `file_path`
```

#### 테이블의 열 변경

열 변경

```
UPDATE 테이블이름
	SET column_name = value, ...
	WHERE conditions
```

#### 테이블의 열 삭제

```
DELETE FROM 테이블이름 WHERE condition
```

#### 조인

| join_type            | 설명 |
| -------------------- | ---- |
| `[INNER] JOIN`       |      |
| `LEFT [OUTER] JOIN`  |      |
| `RIGHT [OUTER] JOIN` |      |
| `FULL [OUTER] JOIN`  |      |
| `CROSS JOIN`         |      |

---

## 참조

- [PostgreSQL](https://www.postgresql.org)