https://aqueduct.io/docs/auth/server/

# Creating AuthServers to Authenticate and Authorize

`AuthServer`는 인증 토큰의 생성, 검증 및 새로 고침을 처리하는 서비스입니다. 여러분은 어플리케이션 채널에 `AuthServer`를 생성하고 인증을 다루는 타입으로 주입합니다. 이 유형에는 다음이 포함됩니다.

- `Authorizer`: 무단 액세스로부터 엔드 포인트 컨트롤러를 보호하는 미들웨어 컨트롤러
- `AuthController`: 액세스 토큰을 부여하는 엔드 포인트 컨트롤러
- `AuthCodeController`: 액세스 토큰을 교환 할 인증 코드를 부여하는 엔드 포인트 컨트롤러

`AuthServer`는 클라이언트 식별자와 액세스 토큰처럼 사용하고 생성하는 데이터를 유지해야합니다. 저장은 종종 데이터베이스에 의해 수행되지만 메모리, 캐시 또는 다른 매체에 있을 수 있습니다. `AuthServer`는 저장 매체가 많기 때문에 저장소 자체를 수행하지 않습니다 - 응용 프로그램에 특정한 `AuthServerDelegate`의 인스턴스에 의존합니다. 이를 통해 스토리지를 검증 로직과 독립적으로 유지할 수 있습니다.

## Creating Instances of AuthServer and AuthServerDelegate

`AuthServerDelegate`는 `AuthServer`가 클라이언트 식별자, 토큰 및 다른 권한 산출물의 저장을 처리하기 위해 사용하는 인터페이스입니다. `AuthServer`는 `AuthServerDelegate`의 구체적인 구현으로 생성 되어야 합니다. Aqueduct는 ORM을 사용하는 `AuthServerDelegate`의 구체적인 구현을 포함합니다. 철저한 테스트를 거쳐 만료 된 데이터를 올바르게 처리하므로 자체 스토리지를 구현하는 대신 이 구현을 사용하는 것이 좋습니다.

이 구현의 이름은 `ManagedAuthDelegate<T>`입니다. Aqueduct의 하위 패키지에 있으며 명시적으로 가져와야 합니다. 다음은 `AuthServer`와 `ManagedAuthDelegate<T>`를 만드는 예입니다:

```dart
import 'package:aqueduct/aqueduct.dart';
import 'package:aqueduct/managed_auth.dart';

class MyApplicationChannel extends ApplicationChannel {  
  AuthServer authServer;

  @override
  Future prepare() async {
    final context = ManagedContext(...);
    final delegate = ManagedAuthDelegate<User>(context);
    authServer = AuthServer(delegate);
  }
  ...
}
```

(`ManagedAuthDelegate`는 타입 인자를 가지고 있습니다 - 이것은 다음 섹션에서 다룰 것입니다.)

`AuthServer`는 권한 부여 작업을 처리하는 방법을 가지고 있지만, 거의 사용되지 않습니다. 대신 `AuthCodeController`와 `AuthController`는 애플리케이션의 HTTP API를 통해 인증 토큰을 부여하는 경로에 연결됩니다. `Authoriser`인스턴스는 채널의 보안 경로를 보호합니다. 이 모든 타입들은`AuthServer`에서 적절한 메소드를 호출합니다. 다음은 권한 부여를 설정하고 사용하는 `ApplicationChannel` 하위 클래스의 예입니다.

```dart
import 'package:aqueduct/aqueduct.dart';
import 'package:aqueduct/managed_auth.dart';

class MyApplicationChannel extends ApplicationChannel {
  AuthServer authServer;
  ManagedContext context;

  @override
  Future prepare() async {
    context = ManagedContext(...);
    final delegate = ManagedAuthDelegate<User>(context);
    authServer = AuthServer(delegate);
  }

  @override
  Controller get entryPoint {
    final router = Router();

    // Set up auth token route- this grants and refresh tokens
    router.route("/auth/token").link(() => AuthController(authServer));

    // Set up auth code route- this grants temporary access codes that can be exchanged for token
    router.route("/auth/code").link(() => AuthCodeController(authServer));

    // Set up protected route
    router
      .route("/protected")
      .link(() => Authorizer.bearer(authServer))
      .link(() => ProtectedController());

    return router;
  }
}
```

AuthController와 같은 인증 컨트롤러에 대한 자세한 내용은 [Authorization Controllers](https://aqueduct.io/docs/auth/controllers/)를 참조하십시오. 경로 보안에 대한 자세한 내용은 [Authorizers](https://aqueduct.io/docs/auth/authorizer/)를 참조하십시오.

## Using ManagedAuthDelegate

`ManagedAuthDelegate<T>`는 `AuthServerDelegate`를 구현 한 것으로, `AuthServer`를 위한 권한 토큰과 클라이언트를 저장합니다. 저장은 Aqueduct의 ORM에 의해 수행됩니다. `ManagedAuthDelegate<T>`는 기본적으로 표준 `aqueduct` 라이브러리의 일부가 아닙니다. 이 클래스를 사용하려면, 어플리케이션은 `package:aqueduct/managed_auth.dart`를 가져 와야합니다.

`ManagedAuthDelegate<T>`에 대한 타입 인자는 애플리케이션의 '사용자 '또는 '계정 ' 개념을 나타냅니다. - OAuth 2.0 용어는 이 유형을 *리소스 소유자*로 나타냅니다. 리소스 소유자는 애플리케이션에 특정한 `ManagedObject<T>`서브 클래스여야 합니다. 테이블 정의는 `ResourceOwnerTableDefinition`을 확장 해야 하고 인스턴스 유형은 `ManagedAuthResourceOwner<T>`를 구현 해야 합니다. 여기서 `T`는 테이블 정의입니다. 기본 정의는 다음과 같습니다.

```dart
class User extends ManagedObject<_User>
    implements _User, ManagedAuthResourceOwner<_User> {
}

class _User extends ResourceOwnerTableDefinition {
  @Column(unique: true)
  String email;
}
```

테이블 정의에서 `ResourceOwnerTableDefinition`을 확장함으로써 데이터베이스 테이블은 다음과 같은 네 개의 컬럼을 가집니다:

- an integer primary key named `id`
- a unique string `username`
- a password hash
- a salt used to generate the password hash

`ResourceOwnerTableDefinition`은 또한 그 대신에 부여 된 각 토큰에 대한 `tokens`의 `ManagedSet`을 가지고 있습니다.

`ManagedAuthResourceOwner<T>`인터페이스는 타입 인자가 `ManagedObject<T>`와`ResourceOwnerTableDefinition` 둘 다 보장하고, `ManagedAuthDelegate<T>`의 타입 파라미터를 제한하는 것 이외의 다른 목적을 제공하지 않습니다.

`managed_auth` 라이브러리는 또한 두개의 `ManagedObject<T>` 서브 클래스를 선언합니다. `ManagedAuthToken`은 인증 토큰과 코드의 인스턴스를 나타내고, `ManagedAuthClient`는 OAuth 2.0 클라이언트의 인스턴스를 나타냅니다. 즉, `ManagedAuthDelegate<T>`를 사용하는 Aqueduct 어플리케이션은 사용자, 토큰 및 클라이언트 중 최소한 세 개의 데이터베이스 테이블을 갖습니다.

`ManagedAuthDelegate<T>`는 더 이상 사용되지 않을 때 권한 부여 토큰과 코드를 삭제합니다. 이것은 자원 소유자가 갖는 토큰 수와 토큰 만기 날짜에 의해 결정됩니다. 리소스 소유자가 40 개 이상의 토큰/코드를 획득하면 가장 오래된 토큰/코드 (만료 날짜로 결정)가 삭제됩니다. 효과적으로 자원 소유자는 40 개의 토큰으로 제한됩니다. 이 숫자는`ManagedAuthDelegate<T>`를 인스턴스화 할 때 변경할 수 있습니다 :

```dart
final delegate = ManagedAuthDelegate(context, tokenLimit: 20);
```

## Configuring the Database

`ManagedAuthDelegate<T>`는 사용자, 토큰 및 클라이언트에 대한 데이터베이스 테이블을 필요로 합니다. 프로젝트에서 [데이터베이스 명령 행 도구](https://aqueduct.io/docs/db/db_tools/)를 사용하여 마이그레이션 스크립트를 생성하고 데이터베이스에 대해 실행하십시오. 이 도구는 여러분의 사용자 유형인 `ManagedAuthToken`과 `ManagedAuthClient`에 대한 선언을 보고 적절한 테이블을 생성합니다.