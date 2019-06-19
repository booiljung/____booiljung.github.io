[Up](./index.md)

원문: https://aqueduct.io/docs/testing/mixins/

# Testing Applications That Use ORM and OAuth 2.0

이 문서에서는 ORM 및 OAuth 2.0을 사용하는 응용 프로그램을 테스트하기 위해 테스트 코드를 설정하는 방법에 대해 설명합니다. 이러한 유형의 애플리케이션에는 추가 초기화 단계가 필요합니다. 테스트 데이터베이스를 설정하십시오.

## ORM을 사용하는 응용 프로그램 테스트

Aqueduct의 ORM은 PostgreSQL을 데이터베이스로 사용합니다. 테스트를 실행하기 전에 Aqueduct는 로컬 PostgreSQL 데이터베이스에 애플리케이션의 데이터베이스 테이블을 생성합니다. 테스트가 완료되면 해당 테이블이 삭제됩니다. 이렇게 하면 각 테스트 스위트에 대해 빈 데이터베이스로 시작하고 테스트 중에 데이터베이스에 있는 레코드를 정확하게 제어 할 수 있지만 데이터베이스 스키마를 관리하거나 모의 구현 (예 : SQLite)을 사용하지 않아도 됩니다.

로컬로 PostgreSQL을 설치해야 합니다.

macOS에서 [Postgres.app](https://postgresapp.com)은 간단한 응용 프로그램으로 실행할 수 있는 자체 포함 PostgreSQL 인스턴스입니다. ([다른 플랫폼을위한 PostgreSQL 설치](https://www.postgresql.org/download/)를 참조하십시오.)

### Local Database for Tests

동일한 데이터베이스가 모든 응용 프로그램을 테스트 하기 위해 재사용됩니다. 이 데이터베이스는 개발 컴퓨터 당 한 번만 만들거나 TravisCI와 같은 CI 도구에서 실행해야 합니다. PostgreSQL의 프롬프트에서 다음을 실행하십시오.

```sql
CREATE DATABASE dart_test;
CREATE USER dart WITH createdb;
ALTER USER dart WITH password 'dart';
GRANT all ON DATABASE dart_test TO dart;
```

응용 프로그램의 `config.yaml.src`에 있는 데이터베이스 설정은 다음과 일치해야 합니다 :

```yaml
username: dart
password: dart
host: localhost
port: 5432
databaseName: dart_test
```

여러분의 응용 프로그램은 `TestHarness<T>`의 하위 클래스와 함께 실행될 때, 로컬 테스트 데이타베이스에 연결하기 위해 데이타베이스 연결을 구성 할 것입니다. `TestHarnessORMMixin`을 테스트 하니스와 혼합 하고 `onSetUp`을 오버라이드하여 `resetData`를 호출해야합니다. `seed`를 오버라이드 (override)하여 테스트 데이터를 데이터베이스에 삽입 할 수도 있습니다.

```dart
class Harness extends TestHarness<AppChannel> with TestHarnessORMMixin {
  @override
  ManagedContext get context => channel.context;

  @override
  Future onSetUp() async {
    await resetData();
  }

  @override
  Future seed() async {
    /* insert some rows here */
  }
}
```

시드 데이터

당신은`seed` 메쏘드에서만 정적 인 데이터를 저장해야 합니다; 런타임 중에 변경할 수 없는 범주나 국가 코드 같은 것을 포함 할 수 있습니다. 특정 테스트 케이스를 위해 조작 된 데이터는 테스트 `setUp` 콜백이나 테스트 자체에서 호출되어야한다.

### 응용 프로그램 실행을 위한 로컬 데이터베이스

응용 프로그램을 로컬에서 실행하기 위해서는 테스트 데이터베이스와 별도의 데이터베이스를 사용해야 합니다. `psql`을 실행하여 PostgreSQL 터미널을 열고 다음 명령을 실행하여 데이터베이스를 로컬로 생성 할 수 있습니다 :

```sql
CREATE DATABASE my_app_name;
CREATE USER my_app_user WITH PASSWORD 'mypassword';
GRANT ALL ON DATABASE my_app_name TO my_app_user;
```

마이그레이션 스크립트를 생성하고 실행하여 로컬 데이터베이스에 스키마를 추가하십시오.

```sh
aqueduct db generate
aqueduct db upgrade --connect postgres://my_app_user:mypassword@localhost:5432/my_app_name
```

## OAuth 2.0을 사용하는 응용 프로그램 테스트

OAuth 2.0을 사용하는 응용 프로그램은 `TestHarnessAuthMixin`을 혼합해야 합니다. 이 mixin은 클라이언트 식별자를 등록하고 사용자를 인증하는 메소드를 추가합니다. 두 메소드 모두 클라이언트 식별자나 사용자에 대한 권한 부여 정보가 있는 기본 헤더가 있는 `agent`를 반환합니다.

대부분 ORM으로 구동되는 OAuth2 델리게이트는 `package:aqueduct/managed_auth`를 사용합니다. 이 mixin을 사용할 때 `TestHarnessORMMixin`을 mixin해야 합니다.

```dart
class Harness extends TestHarness<AppChannel> with TestHarnessAuthMixin<AppChannel>, TestHarnessORMMixin {
  @override
  ManagedContext get context => channel.context;

  @override
  AuthServer get authServer => channel.authServer;

  Agent publicAgent;

  @override
  Future onSetUp() async {    
    await resetData();
    publicAgent = await addClient("com.aqueduct.public");
  }

  Future<Agent> registerUser(User user, {Agent withClient}) async {
    withClient ??= publicAgent;

    final req = withClient.request("/register")
      ..body = {"username": user.username, "password": user.password};
    await req.post();

    return loginUser(withClient, user.username, user.password);
  }
}
```

