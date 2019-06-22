https://aqueduct.io/docs/db/connecting/

# Connecting to a Database from Aqueduct

이 문서는 새로운 PostgreSQL 데이터베이스를 만들고 그것을 연결하는 Aqueduct 응용 프로그램을 설정하는 방법을 안내합니다.

## Creating a Database

Aqueduct ORM을 사용하려면 PostgreSQL 데이터베이스 서버가 있어야 하며 응용 프로그램을 위한 데이터베이스를 만들어야 합니다. 로컬에서 개발할 때는 [Postgres.app](https://postgresapp.com)을 사용하여 개발 데이터베이스 서버를 신속하게 설정하십시오. 이 애플리케이션을 실행 한 후 왼쪽 메뉴에서 새 데이터베이스 서버를 생성하거나 선택하고 데이터베이스 아이콘 중 하나를 두 번 클릭하여 `psql` 명령 행 도구를 엽니 다. (`Postgres.app`를 사용하지 않는다면 `$PATH`에 psql이 있는지 확인하고 명령 행에서 실행하십시오.)

`psql` 안에 다음 명령을 입력하여 응용 프로그램에 대한 데이터베이스 및 데이터베이스 사용자를 만듭니다.

```sql
CREATE DATABASE my_app_name;
CREATE USER my_app_name_user WITH PASSWORD 'password';
GRANT ALL ON DATABASE my_app_name TO my_app_name_user;
```

## Using ManagedContext to Connect to a Database

Aqueduct로부터의 데이터베이스에 대한 인터페이스는 다음 두 객체를 포함하는`ManagedContext`의 인스턴스입니다 :

- a `ManagedDataModel` that describes your application's data model
- a `PersistentStore` that manages a connection to a single database

`ManagedContext` 는 이 두 객체를 사용하여 응용 프로그램과 데이터베이스 간의 이동 데이터를 조정합니다. ``Query<T>`객체는 컨텍스트의 persistent store를 사용하여 명령을 보낼 데이터베이스를 결정하고 데이터 모델을 사용하여 데이터베이스 행을 객체로 또는 그 반대로 매핑합니다.

컨텍스트는 모든 서비스 객체와 마찬가지로 `ApplicationChannel.prepare`에서 생성됩니다.

```dart
class MyApplicationChannel extends ApplicationChannel {
  ManagedContext context;

  @override
  Future prepare() async {
    var dataModel = ManagedDataModel.fromCurrentMirrorSystem();
    var psc = PostgreSQLPersistentStore.fromConnectionInfo(
        "my_app_name_user", "password", "localhost", 5432, "my_app_name");

    context = ManagedContext(dataModel, psc);
  }
}
```

`ManagedDataModel.fromCurrentMirrorSystem`은 어플리케이션의 코드에서 모든 `ManagedObject<T>` 서브 클래스를 찾습니다. 선택적으로 정확한 목록을 지정할 수 있습니다.

```dart
var dataModel = ManagedDataModel([User, Post, Friendship]);
```

Finding ManagedObjects

managed  object 객체 하위 클래스는 application channel 파일로 직접 또는 과도적으로 가져와야합니다. 프로젝트 디렉터리에서 가져 오지 않은 파일을 찾을 수 없습니다. 일반적으로 managed  object 객체 하위 클래스 파일을 직접 가져올 필요가 없습니다. 즉, 채널에서 응용 프로그램이 초기화되고 모든 컨트롤러와 서비스가 가져와지고, 사용중인 managed  object 하위 클래스를 가져옵니다. 응용 프로그램에서 managed  object 객체 선언을 사용하는 동안 해당 객체가 발견됩니다.

데이터베이스 쿼리를 실행해야 하는 컨트롤러에는 컨텍스트에 대한 참조가 있어야 합니다. 이것은 일반적으로 컨텍스트를 컨트롤러의 생성자에 전달하여 수행됩니다.

```dart
class MyApplicationChannel extends ApplicationChannel {
  ManagedContext context;

  @override
  Future prepare() async {
    context = ManagedContext(...);
  }

  @override
  Controller get entryPoint {
    return Router()
      ..route("/users/[:id]").link(() => UserController(context));
  }
}
```

## Using a Configuration File

데이터베이스의 연결 정보는 구성 파일에서 가장 자주 읽습니다. 이를 통해 코드를 수정하지 않고도 다양한 환경 (생산, 개발 등)에 대한 구성을 작성할 수 있습니다. 빈 데이터베이스에 대해 자동화 된 테스트를 실행해야하므로 테스트하는 데 매우 중요합니다. ([설정 파일에 대해 자세히 보기](https://aqueduct.io/docs/application/configure/).)

```dart
class MyConfiguration extends Configuration {
  MyConfiguration(String configPath) : super.fromFile(configPath);

  DatabaseConfiguration database;
}

class MyApplicationChannel extends ApplicationChannel {
  ManagedContext context;

  @override
  Future prepare() async {
    final config = MyConfiguration(options.configurationFilePath);

    final dataModel = ManagedDataModel.fromCurrentMirrorSystem();
    final psc = PostgreSQLPersistentStore.fromConnectionInfo(
        config.database.username,
        config.database.password,
        config.database.host,
        config.database.port,
        config.database.databaseName);        

    context = ManagedContext(dataModel, psc);
  }
}
```

`MyConfiguration` 선언은 YAML 파일이 다음과 같은 구조를 가져야한다는 것을 요구합니다:

```
database:
  username: bob
  password: bobspassword
  host: localhost
  port: 5432
  databaseName: my_app
```

### Connection Behavior

persistent store는 하나의 데이터베이스 연결을 관리합니다. 이 연결은 자동으로 유지됩니다. 쿼리가 처음 실행될 때 연결이 열립니다. 연결이 끊어지면 다음 쿼리에서 연결을 다시 엽니 다. 연결이 열리지 않으면 쿼리를 실행하려고 할 때 예외가 발생합니다. 이 연결은 잡히지 않으면 503 응답을 리턴합니다.

