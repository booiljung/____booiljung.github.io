https://aqueduct.io/docs/deploy/deploy_local/

# Deploying an Aqueduct Application on a Local Machine

다른 배포 옵션은 [Aqueduct 응용 프로그램 배포](https://aqueduct.io/docs/deploy/)를 참조하십시오.

### Purpose

영구 저장소가있는 Aqueduct 응용 프로그램의 로컬 개발 버전을 실행하려면. 이것은 Aqueduct 어플리케이션으로 클라이언트 어플리케이션을 개발할 때 유용합니다. [Aqueduct Applications 테스트](https://aqueduct.io/docs/testing/)도 읽으십시오.

### Prerequisites

1. [Dart has been installed.](https://www.dartlang.org/install)
2. [PostgreSQL has been installed locally.](https://aqueduct.io/docs/getting_started/)
3. [Aqueduct has been activated globally.](https://aqueduct.io/docs/getting_started/)
4. [An application has been created with `aqueduct create`.](https://aqueduct.io/docs/getting_started/)

이 중 하나 이상이 사실이 아니라면 [시작하기](https://aqueduct.io/docs/getting_started/)를 참조하십시오.

### Overview

1. Create a local database.
2. Upload the application schema to the local database.
3. Add an OAuth 2.0 client.
4. Modify the configuration file.
5. Run the application.

Estimated Time: 5 minutes.

### Step 1: Create a Local Database

응용 프로그램과 동일한 이름의 데이터베이스와 해당 데이터베이스에 액세스 할 수있는 사용자를 만듭니다. 데이터베이스를 작성할 수 있는 권한이있는 사용자로 다음 SQL을 로컬로 실행하십시오.

```sql
CREATE DATABASE app_name;
CREATE USER app_name_user WITH CREATEDB;
ALTER USER app_name_user WITH PASSWORD 'yourpassword';
GRANT ALL ON DATABASE app_name TO app_name_user;
```

dart_test database

데이터베이스에 'dart_test'라는 이름을 사용하지 마십시오. 이 데이터베이스는 Aqueduct에서 기본적으로 테스트를 실행하는 데 사용됩니다.

### Step 2: Upload the Application Schema

프로젝트에 대한 데이터베이스 마이그레이션 파일을 아직 만들지 않은 경우 프로젝트 디렉토리에서 데이터베이스 스키마 생성 도구를 실행하십시오.

```sh
aqueduct db generate
```

이 명령은 `migrations/00000001_initial.migration.dart` 파일을 생성합니다. 이제 데이터베이스 마이그레이션 도구를 실행하여 로컬 데이터베이스에 대해 이주 파일을 실행하십시오. `--connect` 옵션의 값이 마지막 단계에서 생성 된 데이터베이스의 값과 일치하는지 확인하십시오.

```sh
aqueduct db upgrade --connect postgres://app_name_user:yourpassword@localhost:5432/app_name
```

(`--connect`를 사용하는 대신 `database.yaml`이라는 파일에 데이터베이스 자격 증명을 제공 할 수 있습니다. 자세한 내용은 `aqueduct db --help`를 참조하십시오.)

### Step 3: Add an OAuth 2.0 client.

`package:aqueduct/managed_auth`를 사용한다면, OAuth2 클라이언트 식별자를 생성하고 싶을 것입니다. 커맨드 라인에서 다음을 실행하여 옵션 `--connect`의 값이 최근에 생성된 데이터베이스와 일치하는지 확인하십시오.

```sh
aqueduct auth add-client --id com.app_name.standard --secret abcdefghi --connect postgres://app_name_user:yourpassword@localhost:5432/app_name
```

### Step 4: Modify the Configuration File

`config.yaml`가 존재하지 않으면, 설정 파일 템플리트 `config.yaml.src`를 복사하여 만듭니다.

`config.yaml`에서, 데이터베이스 인증 정보를 로컬 데이터베이스로 갱신하십시오.

```yaml
database:
 username: app_name_user
 password: yourpassword
 host: localhost
 port: 5432
 databaseName: app_name
```

### Step 5: Run the Application

프로젝트 디렉토리에서 다음을 실행하십시오.

```sh
aqueduct serve
```

이제 응용 프로그램이 실행 중입니다. 생성 된 시작 스크립트를 프로젝트의 `bin` 디렉토리에서 실행할 수도 있습니다 :

```sh
dart bin/main.dart
```

응용 프로그램을 다시 시작하면 데이터베이스의 데이터가 그대로 유지됩니다.

