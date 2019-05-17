# 오라클 데이터베이스 서버 도커 이미지

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
$ docker login
```

username과  password를 물으면 입력하고 로긍인 합니다.

### 오라클 데이터베이스 서버 인스턴스 시작

다음처럼 간단하게 오라클 데이터베이스 서버를 실행할 수 있습니다.

```sh
sudo docker container run -d -it --name <container_name> store/oracle/database-enterprise:12.2.0.1
```

 슬림 변형판을 사용하려면

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

### 데이터베이스 서버로 접속

`sys`유저의 기본 패스워드는 `Oracle_db1`입니다.

#### 컨테이너 안에서 접속

다음은 컨테이너의 SQLPlus로 데이터베이스 서버에 접속 합니다.

```
sudo docker exec -it <container_name> bash -c "source /home/oracle/.bashrc; sqlplus /nolog"
```

#### 컨테이너 밖에서 접속

데이터베이스 서버는 SQLNet 프로토콜을 통한 오라클 클라이언트의 접속을 위해 1512포트와 오라클 XML DB를 위해 5500 포트를 노출(expose)합니다. SQLPlus 또는 어느 JDBC 클라이언트라도 외부에서 접속 할 수 있습니다.

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

Using this `<mapped_host_port>` and `<ip_address_of_host>` create `tnsnames.ora` in the  directory pointed to by environment variable `TNS_ADMIN`.

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

컨테이너 외부에서 SQLPlus를 사용하여 접속하려면 

```sh
sqlplus sys/Oradoc_db1@ORCLCDB as sysdba
```

합니다.

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

---

##### 참조

- [Oracle Database Server Docker Image Documentation](https://store.docker.com/profiles/booiljung/content/sub-bb7a7749-c931-497a-9114-cc867728f53f)