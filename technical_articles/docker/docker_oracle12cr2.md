[Up](./index.md)

# Ubuntu에 Oracle 12c 설치

## [sath89/oracle-12c](https://hub.docker.com/r/sath89/oracle-12c/)

- oracle 12c release 1 도커 이미지입니다.
- `-v` 옵션으로 호스트 디렉토리를 지정하면 퍼미션 오류가 발생하는데 해결 방법은 모릅니다.
- 그래서 개발용 테스용으로 사용하시기를 바랍니다.

### 설치

먼저 도커 이미지를 끌어옵니다. 파일이 비교적 큽니다.

```sh
sudo docker pull sath89/oracle-12c
```

다음은, 도커 이미지를 실행합니다. 컨테이너 이름은 `oracle_database`라고 주었습니다.

```sh
sudo docker run -d -p 8080:8080 -p 1521:1521 -e DBCA_TOTAL_MEMORY=1024 --name oracle_database sath89/oracle-12c
```

볼륨마운트는 `-v <host_directory>:/u01/app/oracle`이지만 퍼미션 오류가 발생합니다. 더 테스트가 필요하지만, 개발용 임시 DB를 구성하는것이 목적이므로 여기까지만 합니다.

다음은, 컨테이너가 올라오는 과정을 추적합니다.

```
sudo docker logs oracle_database -f
```

```sh
Database not initialized. Initializing database.
Starting tnslsnr
Copying database files
1% complete
3% complete
11% complete
18% complete
26% complete
37% complete
Creating and starting Oracle instance
40% complete
45% complete
50% complete
55% complete
56% complete
60% complete
62% complete
Completing Database Creation
66% complete
70% complete
73% complete
85% complete
96% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/xe/xe.log" for further details.
Configuring Apex console
Database initialized. Please visit http://#containeer:8080/em http://#containeer:8080/apex for extra configuration if needed
Starting web management console

PL/SQL procedure successfully completed.

Starting import from '/docker-entrypoint-initdb.d':
ls: cannot access /docker-entrypoint-initdb.d/*: No such file or directory
Import finished

Database ready to use. Enjoy! ;)
```

### 접속

접속시 다음 설정으로 접속 합니다.

| key          | value     |
| ------------ | --------- |
| hostname     | localhost |
| poart        | 1521      |
| sid          | xe        |
| service name | xe        |
| username     | system    |
| password     | oracle    |

sqlplus로 접속시 다음과 같습니다.

```sh
sqlplus system/oracle@/localhost:1521/xe
```

`sys` 와 `system`계정의 비밀번호는 `oracle`입니다.

다음 이슈를 참조 합니다.

https://github.com/MaksymBilenko/docker-oracle-12c/issues/16)

### 어드민

컨테이너 내부의 `/bin/bash`를 실행하려면 다음과 같습니다.

```sh
sudo docker exec -it oracle_database /bin/bash
```

컨테이너 내부의 `oracle` 유저로 전환합니다.

```sh
su oracle
```

컨테이너 내부의 `ORACLE_HOME` 폴더로 이동합니다.

```sh
cd $ORACLE_HOME
```

컨테이너 내부의 sqlplus를 실행하여 `sysdba`로 로그인 합니다.

```sh
bin/sqlplus '/as sysdba'
```

```

SQL*Plus: Release 12.1.0.2.0 Production on Tue May 17 12:11:19 2016

Copyright (c) 1982, 2014, Oracle. All right reserved.

Connected to:
Oracle Database 12c Standard Edition Release 12.1.0.2.0 - 64bit Production

```

sqlplus에서 오라클 인스턴스의 상태를 확인합니다.

```sql
SQL> select database_status from v$instance;
```

```sql
DATABASE_STATUS
-----------------
ACTIVE

SQL>
```

버전을 조회합니다.

```sql
select * from v$version;
```

전체 테이블스페이스를 조회합니다.

```sql
select * from dba_data_files
또는
select * from dba_tablespaces
```

### 컨테이너 재실행

컨테이너를 재실행하려면

```sh
sudo docker start oracle_database
```

### APEX

오라클 Application Express web management console로 접속시 다음과 같이 설정 합니다.

| key        | value                         |
| ---------- | ----------------------------- |
|            | http://localhost:8080/apex    |
| workspalce | INTERNAL                      |
| user       | ADMIN                         |
| password   | `0Racle$` #첫글자 zero에 주의 |

Apex를 v5.*으로 업그레이드 하려면

```sh
sudo docker run -it --rm --volumes-from ${DB_CONTAINER_NAME} --link ${DB_CONTAINER_NAME}:oracle-database -e PASS=YourSYSPASS sath89/apex install
```

### 오라클 Enterprise Management console.

| key               | value                    |
| ----------------- | ------------------------ |
|                   | http://localhost:8080/em |
| user              | sys                      |
| password          | oracle                   |
| connect as sysdba | true                     |

EM을 끄려면

```sh
sudo docker run -d -e WEB_CONSOLE=false -p 1521:1521 -v /my/oracle/data:/u01/app/oracle sath89/oracle-12c
```

additional init 스크립트로 시작하거나 덤프를 하려면

```sh
docker run -d -p 1521:1521 -v /my/oracle/data:/u01/app/oracle -v /my/oracle/init/SCRIPTSorSQL:/docker-entrypoint-initdb.d sath89/oracle-12c
```

By default Import from `docker-entrypoint-initdb.d` is enabled only if you are initializing database (1st run).

To customize dump import use `IMPDP_OPTIONS` env variable like `-e IMPDP_OPTIONS="REMAP_TABLESPACE=FOO:BAR"`
To run import at any case add `-e IMPORT_FROM_VOLUME=true`

**In case of using DMP imports dump file should be named like ${IMPORT_SCHEME_NAME}.dmp**

**User credentials for imports are  ${IMPORT_SCHEME_NAME}/${IMPORT_SCHEME_NAME}**

If you have an issue with database init like DBCA operation failed, please reffer to this [issue](



### [Oracle Database 12c now available on Docker](https://sqlmaria.com/2017/04/27/oracle-database-12c-now-available-on-docker/)

---

# 오라클 데이터베이스 서버 도커 이미지

 제대로 설치되지 않음.

## 주의사항

도커 이미지는 다음 제약을 가집니다.

1. 싱글 인스턴스 데이터베이스만을 지원합니다.
2. Dataguard는 지원되지 않습니다.
3. 데이터베이스 옵션과 패치는 지원되지 않습니다.

## 오라클 데이터베이스 서버 12.2.0.1 엔터프라이즈 슬림판

엔터프라이즈 에디션 슬림 변형판 (`12.2.0.1-slim` tag)은 디스크 용량을 4GB로 줄이고 더 빠르게 컨테이너를 시작 합니다. 이 이미지는 다음 특징을 제공하지 않습니다.  Analytics, Oracle R, Oracle Label Security, Oracle Text, Oracle Application Express and  Oracle DataVault.

## 이미지 사용법

### 도커 스토어에 로그인

```sh
sudo docker login
```

username과  password를 물으면 입력하고 로긍인 합니다.

### 오라클 데이터베이스 서버 인스턴스 시작

다음처럼 간단하게 오라클 데이터베이스 서버를 실행할 수 있습니다.

```sh
sudo docker container run -d -it --name <container_name> store/oracle/database-enterprise:12.2.0.1
```

 슬림판을 사용하려면

```
sudo docker run -d -it --name -it <container_name> store/oracle/database-enterprise:12.2.0.1-slim
```

합니다.

오라클 데이터베이스 이미지가 없으면 다운로드 받고 실행 합니다.

```
Unable to find image 'store/oracle/database-enterprise:12.2.0.1' locally
12.2.0.1: Pulling from store/oracle/database-enterprise
4ce27fe12c04: Pull complete 
9d3556e8e792: Pull complete 
fc60a1a28025: Pull complete 
0c32e4ed872e: Pull complete 
b465d9b6e399: Pull complete 
Digest: sha256:40760ac70dba2c4c70d0c542e42e082e8b04d9040d91688d63f728af764a2f5d
Status: Downloaded newer image for store/oracle/database-enterprise:12.2.0.1
37d15368ac374869a6b71f4f4774838ac5a1b25053fad8fb09d204031b7392d4
```

실행 상태는 `ps`로 확인 할 수 있습니다.

```
sudo docker container ps
```

```
CONTAINER ID        IMAGE                                       COMMAND                  CREATED             STATUS                             PORTS                NAMES
ff353b329a2a        store/oracle/database-enterprise:12.2.0.1   "/bin/sh -c '/bin/ba…"   46 seconds ago      Up 44 seconds (health: starting)   1521/tcp, 5500/tcp   oracle_data
```

데이터베이스 컨테이너 상태는 `(health)`로 표시되었습니다.

### bash에 접속

```sh
sudo docker exec -it <container_name> bash
```

루트 디렉토리는 다음과 같습니다.

```
ORCL  boot  etc   lib    media  opt   root  sbin  sys  u01  u03  usr
bin   dev   home  lib64  mnt    proc  run   srv   tmp  u02  u04  var
```

### 데이터베이스 서버로 접속

`sys`유저의 기본 패쓰워드는 `Oracle_db1`입니다.

#### 컨테이너 안에서 접속

다음은 컨테이너의 SQLPlus로 데이터베이스 서버에 접속 합니다.

```
sudo docker exec -it <container_name> bash -c "source /home/oracle/.bashrc; sqlplus /nolog"
```

#### 컨테이너 밖에서 접속

데이터베이스 서버는 SQLNet 프로토콜을 통한 오라클 클라이언트의 접속을 위해 1512포트와 오라클 XML DB를 위해 5500 포트를 노출(expose)합니다. SQLPlus 또는 어느 JDBC 클라이언트라도 외부에서 접속 할 수 있습니다.

컨테이너 외부에서 SQLPlus를 사용하여 접속하려면 

```sh
sqlplus sys/Oradoc_db1@ORCLCDB as sysdba
```

합니다.

외부에서 접속하려면 다음처럼 `-p` 또는 `-p` 옵션을 지정하여야 합니다.

```
sudo docker run -d -it --name <container_name> -P store/oracle/database-enterprise:12.2.0.1
```

슬림판은

```sh
sudo docker run -d -it --name <container_name> -P store/oracle/database-enterprise:12.2.0.1-slim
```

 `-P`은 도커에 의해 할당된 포트를 지정합니다. 매핑된 포트는 다음에 의해 지정됩니다.

```
sudo docker port <container_name> 1521/tcp -> 0.0.0.0:<mapped_host_port>
```

`<mapped_host_port>` 와 `<ip_address_of_host>` create `tnsnames.ora` in the  directory pointed to by environment variable `TNS_ADMIN`.

```
ORCLCDB=(
	DESCRIPTION=(
		ADDRESS=(PROTOCOL=TCP)(HOST=<ip_address_of_host>)(PORT=<mapped_host_port>)
	)(
		CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=ORCLCDB.localdomain)
	)
)
ORCLPDB1=(
	DESCRIPTION=(
		ADDRESS=(PROTOCOL=TCP)(HOST=<ip_address_of_host)(PORT=<mapped_host_port>)
	)(
		CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=ORCLPDB1.localdomain)
	)
)
```

### 커스텀 구성

오라클 데이터베이스 서버 컨테이너는 컨테이너 시작시 커스텀 구성 파라미터를 제공합니다. 커스텀 구성 파라미터는 옵션입니다. 다음은 커스텀 구성 파라미터 목록이며 ENV 파일에서 제공됩니다.  (`ora.conf`).

#### `DB_SID`

이 파라미터는 데이터베이스의 `ORACLE_SID` 를 변경합니다. 기본 값은 `ORCLCDB` 입니다.

#### `DB_PDB`

이 파라미터는 PDB의 이름을 변경 합니다. 기본값은 `ORCLPDB1` 입니다.

#### `DB_MEMORY`

이 파라미터는 오라클 서버가 요구하는 메모리를 지정합니다. 이 값은 SGA와 PGA용 메모리로 결정됩니다. 기본값은 2GB입니다.

#### `DB_DOMAIN`

이 파라미터는 데이터베이스 서버가 사용하는 도메인을 지정합니다. 기본값은  `localdomain` 입니다.

커스텀 구성 파라미터로 오라클 데이터베이스 서버를 시작하려면

```sh
sudo docker run -d -it --name <container_name> -P --env-file ora.conf store/oracle/database-enterprise:12.2.0.1
```

슬림판은

```sh
sudo docker run -d -it --name <container_name> -P --env-file ora.conf store/oracle/database-enterprise:12.2.0.1-slim
```

`DB_SID`와 `DB_PDB` and `DB_DOMAIN` 의 커스텀 값은  tnsnames.ora를 업데이트 합니다.

## SYS 유저의 기본 패스워드 변경

오라클 데이터베이스 서버의 기본 패스워드는 `Oradoc_db1`으로 시작됩니다. 이 컨테이너 생성시 비밀번호는 보호되지 않으며 변경되어야 합니다. SQLPlus를 실행하여 다음처럼 패스워드를 변경 합니다.

```
alter user sys identified by <new_password>;
```

## 자원 요구사항

컨테이너에 실행에는 최소한의 디스크 용량은 8GB이며, 메모리는 2GB가 필요합니다.

## 데이터 베이스 로그

데이터베이스 로그는 다음처럼 하여 볼 수 있습니다.

```sh
sudo docker logs <container_name>
```

## 기존 데이터베이스 재사용

오라클 데이터베이스 서버 이미지는 도커 데이터 볼륨을 사용하여 데이터 파일, redo 로그, 감사 로그, 경보 로그, 트레이스 파일을 저장합니다. 데이터 볼륨은 컨테이너 안의 `/ORCL` 에 마운트 됩니다. 데이터 볼륨을 사용하여 데이터베이스를 시작하려면 `docker run`을 사용할때 다음을 지정합니다.

```sh
sudo docker run -d -it --name <container_name> -v <host_data_volume>:/ORCL store/oracle/database-enterprise:12.2.0.1
```

슬림판은

``` sh
sudo docker run -d -it --name <container_name> -v <host_data_volume>:/ORCL store/oracle/database-enterprise:12.2.0.1
```

`<host_data_volume>` 은 도커에 의해 생성되고 컨테이너 내부에 마운트된 데이터 볼륨 입니다. `<host_data_volume>`에 의해 영구인 데이터 파일은 다른 컨테이너에 의해 재사용될 수 있습니다.

## 데이터 볼륨에 호스트 시스템의 디렉토리 사용

호스트 시스템의 디렉토리를 데이터 볼륨에 사용하려면,

```
sudo docker run -d -it --name <container_name> -v <host_data_volume>:/ORCL store/oracle/database-enterprise:12.2.0.1
```

슬림판은

```sh
sudo docker run -d -it --name <container_name> -v <host_data_volume>:/ORCL store/oracle/database-enterprise:12.2.0.1-slim
```

으로 지정합니다. 여기서 `<host_data_volume>`은 호스트의 디렉토리입니다.

## 참조

- [Oracle Database Server Docker Image Documentation](https://store.docker.com/profiles/booiljung/content/sub-bb7a7749-c931-497a-9114-cc867728f53f)

- [sath89/oracle-12c](https://hub.docker.com/r/sath89/oracle-12c/)
- [oracle-12c를 docker를 이용하여 구동하기 ](http://hellogohn.com/post_one261)

- [Docker를 기반으로 다양한 데이터베이스 환경 통합하기](https://medium.com/chequer/docker%EB%A5%BC-%EA%B8%B0%EB%B0%98%EC%9C%BC%EB%A1%9C-%EB%8B%A4%EC%96%91%ED%95%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%ED%99%98%EA%B2%BD-%ED%86%B5%ED%95%A9%ED%95%98%EA%B8%B0-96aa68363775)

- [Oracle Database 12c now available on Docker](https://sqlmaria.com/2017/04/27/oracle-database-12c-now-available-on-docker/)

