https://aqueduct.io/docs/db/db_tools/

# Database Migration and Tooling

The `aqueduct db` command line tool creates and executes *migration files*. A migration file contains Dart code that executes SQL commands to create and modify database tables. 

PostgreSQL 9.6 and Greater

The minimum version of PostgreSQL needed to work with Aqueduct is 9.6.

## Migration Files

애플리케이션의 데이터 모델은 `ManagedObject<T>`서브 클래스와 그 [table defintion](https://aqueduct.io/docs/db/modeling_data/)에 의해 기술 됩니다. 마이그레이션 파일은 응용 프로그램의 데이터 모델과 일치하도록 데이터베이스 스키마를 만들거나 수정하는 일련의 데이터베이스 명령을 설명합니다. 새로운 `ManagedObject<T>` 서브 클래스를 추가하거나 속성에 데이터베이스 인덱스를 추가하는 것과 같이, 애플리케이션이 처음 배포 될 때와 데이터 모델의 변경이 발생할 때 마이그레이션 파일이 데이터베이스에서 실행됩니다.

마이그레이션 파일은 코드에서 자동으로 생성됩니다. 각 마이그레이션 파일에는 최종 마이그레이션 파일이 생성 된 이후 변경된 사항만 포함됩니다. 예를 들어, `Author` 유형으로 애플리케이션을 시작하고 마이그레이션 파일을 생성 한 경우, 마이그레이션 파일은 작성자 테이블을 작성합니다. 그런 다음 `Book`유형을 추가하여 새 마이그레이션 파일을 생성하면 새 파일은 book 테이블 만 생성합니다. 마이그레이션을 사용하여 데이터베이스 스키마를 업그레이드하면 아직 실행되지 않은 모든 마이그레이션 파일이 실행됩니다.

여러 환경에서 여러 데이터베이스를 관리 할 수 있도록 마이그레이션 파일을 버전 제어에 저장해야합니다.

## Generating Migration Files

`aqueduct db generate` 명령은 새로운 마이그레이션 파일을 생성합니다. 이 툴은 애플리케이션의 모든 `ManagedObject<T>` 하위 클래스 (데이터 모델)를 찾아서 툴을 마지막으로 실행했을 때의 데이터 모델과 비교합니다. 데이터 모델 간의 차이점은 생성 된 마이그레이션 파일의 명령으로 표시됩니다. 새 마이그레이션 파일을 사용하여 데이터베이스를 업그레이드 하는 경우 데이터베이스는 응용 프로그램의 현재 데이터 모델과 일치합니다.

Finding ManagedObjects

관리 대상 객체 하위 클래스는 응용 프로그램 채널 파일로 직접 또는 과도 적으로 가져와야합니다. 프로젝트 디렉터리에서 가져 오지 않은 파일을 찾을 수 없습니다. 일반적으로 관리 대상 객체 하위 클래스 파일을 직접 가져올 필요가 없습니다. 즉, 채널에서 응용 프로그램이 초기화되고 모든 컨트롤러와 서비스가 가져와지고, 사용중인 관리 객체 하위 클래스를 가져옵니다. 응용 프로그램에서 관리 대상 객체 선언을 사용하는 동안 해당 객체가 발견 됩니다.

마이그레이션 파일은 프로젝트의 `migrations/` 디렉토리에 저장됩니다. 마이그레이션 파일의 접두어에는 버전 번호, "0"으로 채워진 8 자리 숫자, 접미사에는 .migration.dart가 붙습니다. 예를 들어, `00000001_initial.migration.dart`는 이전 파일 이름입니다. 파일 이름의 버전 번호 부분은 `.migration.dart` 접미사와 마찬가지로 필요합니다. 밑줄과 나머지 파일 이름은 선택 사항이며 효과가 없습니다. 파일 이름을 지정하는 방법 일뿐입니다. 다음은 두 가지 마이그레이션 파일 이름의 예입니다.

```
00000001_initial.migration.dart
00000002_add_user_nickname.migration.dart
```

마이그레이션 파일의 버전 번호는 적용되는 순서를 나타냅니다. 선행 0은 버전 번호를 비교하기 전에 파일 이름에서 제거됩니다. 버전 번호는 반드시 연속적 일 필요는 없지만 달리 수행하는 것은 바람직하지 않습니다. 마이그레이션 파일은 생성 된 후에 변경 될 수 있습니다 ([seeding data](https://aqueduct.io/docs/db/db_tools/#seeding-data) 참조).

## Validating Migration Files

`aqueduct db validate` 도구는 모든 마이그레이션 파일을 실행 한 후 데이터베이스 스키마가 응용 프로그램의 데이터 모델과 일치하는지 확인합니다. 유효성 검사 도구는 코드의 스키마와 마이그레이션 파일로 만든 스키마간에 발견 된 차이점을 표시합니다.

## Listing Migration Files

`aqueduct db list`를 사용하여 모든 데이터베이스 이전 파일과 해결 된 버전 번호를 나열하십시오.

## Getting a Database's Version

`aqueduct db get-version`을 사용하여 데이터베이스의 현재 버전 번호를 가져올 수 있습니다. 이 명령은 데이터베이스에 대한 연결 정보를 얻기 위해 다음 절에서 설명하는 것처럼 `--connect` 또는 `database.yaml` 파일을 사용합니다.

## Upgrading a Database Schema by Executing Migration Files

도구 `aqueduct db upgrade`는 실행 파일의 명령을 실행중인 데이터베이스에 적용합니다. 이 도구는 응용 프로그램의 디렉토리에서 실행되며 데이터베이스 연결 정보는 `--connect` 옵션과 함께 제공됩니다. 예를 들어, 다음 명령은 PostgreSQL 데이터베이스에서 현재 프로젝트 디렉토리의 이전 파일을 실행합니다.

```dart
aqueduct db upgrade --connect postgres://username:password@localhost:5432/my_application
```

대상 데이터베이스에서 아직 실행되지 않은 모든 마이그레이션 파일이 실행됩니다. 이 도구는 업그레이드 할 각 데이터베이스의 버전 테이블을 관리하여 실행해야하는 마이그레이션 파일을 판별 할 수 있도록 합니다.

연결 정보는 대안적으로 응용 프로그램 디렉토리의 `database.yaml`이라는 데이터베이스 구성 파일에 저장 될 수 있습니다. 이 파일이 다음과 같은 포맷으로 존재하면, `--connect`을 생략 할 수 있고 이 파일에서 연결 정보를 읽을 수 있습니다 :

```
username: "user"
password: "password"
host: "host"
port: port
databaseName: "database"
```

### When to Execute Migration Files

개발 중에는 변경 사항마다 마이그레이션 파일을 만들 필요가 없습니다. 새 버전의 응용 프로그램을 배포하기 전에 마이그레이션 파일을 실행하십시오.

프로덕션 데이터베이스에서 실행하지 않은 경우 마이그레이션 파일을 삭제할 수 있습니다. `aqueduct db generate`가 다시 실행될 때, 새로운 이주 파일에 추가 할 명령을 판별하기 전에 기존 이주 파일 만 재생합니다. 예를 들어, 시간이 지남에 따라 10 개의 마이그레이션 파일이 있고이를 모두 삭제하면 다음에 생성 된 마이그레이션 파일에 전체 데이터베이스 스키마를 다시 만드는 명령이 포함됩니다.

### Seeding Data

`seed` 메소드를 대체하여 데이터베이스 마이그레이션 중에 행을 삽입, 삭제 또는 편집 할 수 있습니다. 데이터를 파싱 할 때 `Query<T>`를 사용하는 대신 SQL 쿼리를 실행해야 합니다. 모든 마이그레이션이 확장하는 `Migration` 기본 클래스에는 마이그레이션이 수행되는 데이터베이스에 연결된 `PersistentStore`에 대한 속성이 있습니다.

```dart
class Migration2 extends Migration {
  @override
  Future upgrade() async {
    ...
  }

  @override
  Future downgrade() async {
    ...
  }

  @override
  Future seed() async {
    await store.executeQuery("INSERT IN _mytable (a) VALUES (1)");
  }
}
```

마이그레이션은 `upgrade` 메소드가 완료된 후에 실행됩니다. seeding 데이터는 또한`upgrade`와 같은 트랜잭션에서 발생합니다.

### Handling Non-nullable Additions

일부 데이터베이스 업그레이드는 현재 데이터베이스에있는 데이터에 따라 실패 할 수 있습니다. 일반적인 시나리오는 nullable이 아닌 기존 관리 객체에 속성을 추가하는 것입니다. 데이터베이스 테이블에 이미 행이 있는 경우 해당 행에는 새 열에 대한 값이 없으며 마이그레이션 실패합니다. 데이터베이스에 행이 없으면 이주가 올바르게 수행됩니다. 속성에 기본값 속성이 있거나 자동 증가 일 경우 마이그레이션은 항상 성공하며 기존 행은 새 열의 기본값을가집니다.

마이그레이션이 성공할 수 있도록 기존 행에 대한 값만 제공 할 수도 있습니다. 이것은 null-notability를 위반하는 schema-altering 명령의 인수로 추가됩니다.

```dart
@override
Future upgrade() async {
  database.addColumn("_mytable", SchemaColumn(...), unencodedInitialValue: "'text'")
}
```

인코딩되지 않은 초기 값은 SQL 명령에 직접 삽입됩니다. 이 값은 표에 표시된대로 SQL 값 리터럴이어야합니다.

| Type     | Unencoded Initial Value      | Value    |
| -------- | ---------------------------- | -------- |
| int      | "1"                          | 1        |
| String   | "'string'"                   | 'string' |
| double   | "2.0"                        | 2.0      |
| DateTime | "'1900-01-02T00:00:00.000Z'" | 1/2/1900 |