[Up](index.md)

원문: https://aqueduct.io/docs/tut/oauth2/

# Dart: Aqueduct: 5. Adding Authentication and Authorization with OAuth 2.0

Our `heroes` application lets anyone create or view the same set of heroes. We will continue to build on the last chapter's project, `heroes`, requiring a user to log in before viewing or creating heroes.

We're Done With the Browser App

We're at the point now where using the browser application to test  our Aqueduct app gets a bit cumbersome. From here on out, we'll use `curl`, `aqueduct document client` and tests.

## The Basics of OAuth 2.0

[OAuth 2.0](https://tools.ietf.org/html/rfc6749)은 인증 지침을 포함하는 인증 프레임 워크입니다. 인증은 사용자 이름과 암호를 통해 특정 사용자임을 증명하는 프로세스입니다. 권한 부여는 사용자가 특정 리소스 또는 리소스 컬렉션에 액세스 할 수 있는지 확인하는 프로세스입니다. 우리의 응용 프로그램에서 사용자는 영웅을 보거나 만들 권한이 부여되기 전에 인증을 받아야 합니다.

간단한 인증 및 권한 부여 체계에서 각 HTTP 요청에는 사용자의 사용자 이름과 암호 (자격 증명)가 `Authorization` 헤더에 포함됩니다. 이 작업에는 여러 가지 보안 위험이 따르므로 OAuth 2.0은 또 다른 접근 방식을 취합니다. 즉, 자격 증명을 한 번 보내면 '액세스 토큰'이 반환됩니다. 그런 다음 각 요청에서 이 액세스 토큰을 보냅니다. 서버가 토큰을 부여하기 때문에 이미 자격 증명을 입력 했으므로 (* 인증 된 *) 토큰이 누구인지 기억합니다. 토큰에 시간 제한이 있고 상황이 잘못 될 경우 자격 증명을 취소 할 수 있다는 점을 제외하면 매번 자격 증명을 보내는 것과 사실 동일합니다.

Aqueduct에는 ORM을 활용하는 OAuth 2.0 구현이 내장 되어 있습니다. 이 구현은 `aqueduct` 패키지의 일부이지만`aqueduct/managed_auth`라고 하는 별도의 라이브러리입니다. OAuth 2.0에 익숙하지 않은 경우 이해하기 어려울 수있는 몇 가지 단계가 필요하지만 잘 테스트 되고 안전한 인증 구현을 얻을 수 있습니다.

대체 구현

대부분의 경우 `package:aqueduct/managed_auth`를 사용하는 것이 바람직합니다. 어떤 경우에는 다른 데이터베이스 시스템에 권한 정보를 저장하거나 [JWT](https://jwt.io)와 같은 토큰 형식을 사용할 수 있습니다. 이것은 중요한 테스트 노력이 필요한 복잡한 주제이며 이 자습서의 범위를 벗어납니다.

## Setting up OAuth 2.0: Creating a User Type

우리의 응용 프로그램에는 영웅을 관리하기 위해 응용 프로그램에 로그인하는 '사용자'라는 개념이 필요합니다. 이 사용자는 사용자 이름과 비밀번호를 갖습니다. 나중에 연습 할 때 사용자는 자신에게 속한 영웅의 목록을 갖게됩니다. `model/user.dart` 파일을 새로 만들고 다음 코드를 입력하십시오 :

```dart
import 'package:aqueduct/managed_auth.dart';
import 'package:heroes/heroes.dart';
import 'package:heroes/model/hero.dart';

class User extends ManagedObject<_User> implements _User, ManagedAuthResourceOwner<_User> {}

class _User extends ResourceOwnerTableDefinition {}
```

가져온 라이브러리 `package:aqueduct/managed_auth.dart`는 사용자, 토큰 및 기타 OAuth 2.0 관련 데이터를 저장하기 위해 ORM을 사용하는 유형을 포함합니다. 이러한 유형 중 하나는 사용자의 테이블 정의의 상위 클래스 인`ResourceOwnerTableDefinition`입니다. 이 유형은 Aqueduct가 인증을 구현하는 데 필요한 모든 필수 필드를 포함합니다.

자원 소유자

*리소스 소유자*는 OAuth 2.0 사양에서 나온 '사용자'에 대한 일반적인 용어입니다. 프레임 워크에서는 유형과 변수가 *resource owner*의 변형을 사용하는 것을 볼 수 있지만, 모든 의도와 목적을 위해 '사용자'로 간주 할 수 있습니다.

호기심이 있다면, `ResourceOwnerTableDefinition`은 다음과 같습니다 :

```dart
class ResourceOwnerTableDefinition {
  @primaryKey
  int id;

  @Column(unique: true, indexed: true)
  String username;

  @Column(omitByDefault: true)
  String hashedPassword;

  @Column(omitByDefault: true)
  String salt;

  ManagedSet<ManagedAuthToken> tokens;
}
```

Because these fields are in `User`'s table definition, our `User` table has all of these database columns.

`ManagedAuthResourceOwner`

Note that `User` implements `ManagedAuthResourceOwner<_User>` - this is a requirement of any OAuth 2.0 resource owner type when using `package:aqueduct/managed_auth`.

## Setting up OAuth 2.0: AuthServer and its Delegate

이제 사용자가 생겨서 새로운 사용자를 만들고 인증하는 방법이 필요합니다. 인증은 상당히 까다롭습니다. 특히 OAuth 2.0에서는 인증 작업을 수행하는 서비스 객체가 있습니다. 이 유형은 사용자를 인증하고 권한을 부여하는 데 필요한 모든 논리를 가지고 있습니다. 예를 들어 `AuthServer`는 유효한 사용자 자격 증명이 주어지면 새로운 토큰을 생성 할 수 있습니다.

`channel.dart`에서, 다음 import를 파일의 맨 위에 추가하십시오 :

```dart
import 'package:aqueduct/managed_auth.dart';
import 'package:heroes/model/user.dart';
```

그런 다음 채널에 새로운 `authServer` 속성을 선언하고 `prepare`에서 초기화하십시오 :

```dart
class HeroesChannel extends ApplicationChannel {
  ManagedContext context;

  // Add this field
  AuthServer authServer;

  Future prepare() async {
    logger.onRecord.listen((rec) => print("$rec ${rec.error ?? ""} ${rec.stackTrace ?? ""}"));

    final config = HeroConfig(options.configurationFilePath);
    final dataModel = ManagedDataModel.fromCurrentMirrorSystem();
    final persistentStore = PostgreSQLPersistentStore.fromConnectionInfo(
      config.database.username,
      config.database.password,
      config.database.host,
      config.database.port,
      config.database.databaseName);

    context = ManagedContext(dataModel, persistentStore);

    // Add these two lines:
    final authStorage = ManagedAuthDelegate<User>(context);
    authServer = AuthServer(authStorage);
  }
  ...
```

`AuthServer`는 인증과 권한 부여의 논리를 처리하지만, 그 작업에 사용하는 데이터를 저장하거나 가져 오는 방법을 알지 못합니다. 대신 데이터베이스에서 데이터를 저장하고 가져 오는 것을 처리하는 델리게이트 객체를 사용합니다. 우리 애플리케이션에서는 `package:aqueduct/managed_auth`의 `ManagedAuthDelegate<T>`를 델리게이트로 사용합니다. 이 유형은 이러한 작업에 ORM을 사용합니다. 타입 인수는 응용 프로그램의 사용자 객체 여야 합니다.

Delegation

델리게이션은 객체에 인터페이스로 그룹화 된 여러 콜백이 있는 디자인 패턴입니다. 각 콜백에 대한 클로저를 정의하는 대신 형식은 위임하는 개체가 호출하는 메서드를 구현합니다. 그것은 많은 양의 관련된 콜백을 깔끔한 클래스로 구성하는 방법입니다.

`aqueduct/managed_auth`를 임포트함으로써 우리는 토큰과 다른 인증 데이터를 저장하기 위해 응용 프로그램에 관리 객체를 몇 개 더 추가했습니다. 우리는 또한 새로운 `User` 관리 객체를 가지고 있습니다. 데이터베이스 마이그레이션을 실행하는 것이 좋습니다. 프로젝트 디렉토리에서 다음 명령을 실행하십시오.

```
aqueduct db generate
aqueduct db upgrade --connect postgres://heroes_user:password@localhost:5432/heroes
```

## Setting up OAuth 2.0: Registering Users

이제는 사용자 개념이 생겼으므로 데이터베이스와 응용 프로그램이 인증을 처리하도록 설정되었으므로 새로운 사용자를 만들 수 있습니다. 사용자 등록을 위한 새로운 컨트롤러를 만듭니다. 이 컨트롤러는 본문에 사용자 이름과 암호가 포함 된 `POST`요청을 수락합니다. 새 사용자를 데이터베이스에 삽입하고 사용자의 암호를 안전하게 해시합니다.

이 컨트롤러를 만들기 전에 고려해야 할 사항이 있습니다. 등록 끝점에는 사용자의 암호가 필요하지만 사용자의 암호를 암호화 해시로 저장합니다. 이렇게하면 데이터베이스에 액세스 할 수있는 사용자가 사용자의 암호를 알 수 없게됩니다. 요청의 본문을 `User` 객체에 바인드하기 위해서는 패스워드 필드가 필요하지만, 먼저 해싱하지 않고 데이터베이스에 패스워드를 저장하고 싶지는 않습니다.

우리는 *transient properties*로 이를 수행 할 수 있습니다. 임시 속성은 데이터베이스에 저장되지 않은 관리 대상 객체의 속성입니다. 테이블 정의 대신 관리 오브젝트 서브 클래스에서 선언됩니다. 기본적으로 일시적인 속성은 요청 본문에서 읽히지 않거나 응답 본문으로 인코딩되지 않습니다. `Serialize` 주석을 추가하지 않는 한. 이 속성을 `User` 유형에 추가하십시오 :

```dart
class User extends ManagedObject<_User> implements _User, ManagedAuthResourceOwner<_User> {
  @Serialize(input: true, output: false)
  String password;
}
```

이것은 `User`가 (요청 본문에서) 입력시 읽을 수 있지만 출력시 (응답 본문으로) 전송되지 않는 일시적인 속성인 `password`를 가짐을 선언합니다. 일시적인 속성이 데이터베이스에 저장되어 있지 않기 때문에 데이터베이스 마이그레이션을 실행할 필요가 없습니다.

이제 `controller/register_controller.dart` 파일을 만들고 다음 코드를 입력하십시오 :

```dart
import 'dart:async';

import 'package:aqueduct/aqueduct.dart';
import 'package:heroes/model/user.dart';

class RegisterController extends ResourceController {
  RegisterController(this.context, this.authServer);

  final ManagedContext context;
  final AuthServer authServer;

  @Operation.post()
  Future<Response> createUser(@Bind.body() User user) async {
    // Check for required parameters before we spend time hashing
    if (user.username == null || user.password == null) {
      return Response.badRequest(
        body: {"error": "username and password required."});
    }

    user
      ..salt = AuthUtility.generateRandomSalt()
      ..hashedPassword = authServer.hashPassword(user.password, user.salt);

    return Response.ok(await Query(context, values: user).insert());
  }
}
```

이 컨트롤러는 사용자가 포함 된 POST 요청을 받습니다. 사용자는 많은 필드 (username, password, hashedPassword, salt)를 가지고 있지만, 후자의 두 개를 계산하고 요청에 첫 두 개만 포함하도록 요구합니다. 컨트롤러는 암호를 데이터베이스에 저장하기 전에 썰트와 해시를 생성합니다. `channel.dart`에서 이 컨트롤러를 링크합시다 - 가져 오기를 잊지 마세요!

```dart
import 'package:heroes/controller/register_controller.dart';

...

  @override
  Controller get entryPoint {
    final router = Router();

    router
      .route('/heroes/[:id]')
      .link(() => HeroesController(context));

      router
      .route('/register')
      .link(() => RegisterController(context, authServer));

    return router;
  }
}    
```

커맨드 라인에서 curl을 사용하여 애플리케이션을 실행하고 새로운 사용자를 생성 해 봅시다. (우리는 하나의 isolate를 사용하고 시작을 가속화하도록 지정하기 위해 `-n1`을 지정할 것입니다.)

```
aqueduct serve -n1
```

그런 다음 서버에 요청을 보냅니다.

```
curl -X POST http://localhost:8888/register -H 'Content-Type: application/json' -d '{"username":"bob", "password":"password"}'
```

새 사용자 개체와 해당 사용자 이름을 다시 가져옵니다.

```
{"id":1,"username":"bob"}
```

## Setting up OAuth 2.0: Authenticating Users

이제 암호가 있는 사용자가 생겼으므로 사용자 자격 증명을 가져와 액세스 토큰을 반환하는 끝점을 만들 수 있습니다. 좋은 소식은 이 컨트롤러가 이미 Aqueduct에 존재한다는 것입니다. 단지 경로에 연결해야합니다. `channel.dart`에서`entryPoint`를 업데이트하여 `/auth/token` 경로에 `AuthController`를 추가하십시오:

```dart
@override
Controller get entryPoint {
  final router = Router();

  // add this route
  router
    .route('/auth/token')
    .link(() => AuthController(authServer));

  router
    .route('/heroes/[:id]')
    .link(() => HeroesController(context));

  router
    .route('/register')
    .link(() => RegisterController(context, authServer));

  return router;
}
```

`AuthController`는 유효한 사용자 인증 정보가 주어질 때 액세스 토큰을 부여하기 위한 OAuth 2.0 스펙을 따릅니다. 이 엔드 포인트에 대한 요청을 구조화해야 하는 방법을 이해하려면 OAuth 2.0 *클라이언트*에 대해 논의해야합니다. OAuth 2.0에서 클라이언트는 사용자를 대신하여 서버에 액세스 할 수 있는 애플리케이션입니다. 클라이언트는 브라우저 응용 프로그램, 모바일 응용 프로그램, 다른 서버, 음성 도우미 등이 될 수 있습니다. 클라이언트에는 항상 'com.stablekernel.account_app.mobile'과 같은 식별자 문자열이 있습니다.

인증 할 때 사용자는 항상 클라이언트를 통해 인증됩니다. 이 클라이언트 정보는 모든 인증 요청에 첨부되어야하며 서버는 클라이언트가 이전에 등록되었는지 검증해야합니다. 따라서 우리는 우리의 응용 프로그램을 위해 새로운 클라이언트를 등록해야 합니다. 클라이언트는 `aqueduct auth add-client` CLI를 사용하여 우리 애플리케이션의 데이터베이스에 저장됩니다. 프로젝트 디렉토리에서 다음 명령을 실행하십시오.

```sh
aqueduct auth add-client --id com.heroes.tutorial --connect postgres://heroes_user:password@localhost:5432/heroes
```

OAuth 2.0 Clients

클라이언트는 식별자가 있어야 하지만, secret, redirect URI 및 허용 범위 목록을 가질 수도 있습니다. 이러한 옵션이 인증에 미치는 영향에 대해서는 [OAuth 2.0 가이드](https://aqueduct.io/docs/auth/)를 참조하십시오. 특히 클라이언트 식별자에는 *refresh 토큰*을 발행하는 secret가 있어야 합니다. 클라이언트는 응용 프로그램의 데이터베이스에 저장됩니다.

이렇게하면 지난 번 데이터베이스 이전에서 생성 된 OAuth 2.0 클라이언트 테이블에 새로운 행이 삽입되어 인증 요청을 할 수 있습니다. 인증 요청은 다음 기준을 모두 충족해야합니다.

- 클라이언트 식별자 (및 존재하는 경우에는 비밀)는 기본 `Authorizatio` 헤더로 포함됩니다.
- 사용자 이름과 비밀번호가 요청 본문에 포함됩니다.
- 키-값 `grant_type=password`는 요청 본문에 포함됩니다
- 요청 본문 내용 유형은 `application/x-www-form-urlencoded`입니다. 이는 요청 본문이 실제로 쿼리 문자열이라는 것을 의미합니다 (예 :`username=bob&password=pw&grant_type=password`).

다트 코드에서는 다음과 같이 됩니다.

```dart
import 'package:http/http.dart' as http; // Must include http package in your pubspec.yaml

final clientID = "com.heroes.tutorial";
final body = "username=bob&password=password&grant_type=password";

// Note the trailing colon (:) after the clientID.
// A client identifier secret would follow this, but there is no secret, so it is the empty string.
final clientCredentials = Base64Encoder().convert("$clientID:".codeUnits);

final response = await http.post(
  "https://stablekernel.com/auth/token",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded",
    "Authorization": "Basic $clientCredentials"
  },
  body: body);
```

이 코드를 실행하거나 다음과 같은`curl`을 사용할 수 있습니다 :

```sh
curl -X POST http://localhost:8888/auth/token -H 'Authorization: Basic Y29tLmhlcm9lcy50dXRvcmlhbDo=' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=bob&password=password&grant_type=password'
```

성공하면 액세스 토큰을 포함하는 다음과 같은 응답을 받게됩니다.

```json
{"access_token":"687PWKFHRTQ9MveQ2dKvP95D4cWie1gh","token_type":"bearer","expires_in":86399}
```

Hang on to this access token, we'll use it in a moment.

## Setting up OAuth 2.0: Securing Routes

사용자를 생성하고 인증 할 수 있게 되었으므로 영웅 요청에 대한 액세스 토큰을 요구함으로써 익명 사용자로부터 영웅을 보호 할 수 있습니다. `channel.dart`에서 `/heroes` 채널의 중간에 `Authorizer`를 연결하십시오:

```dart
router
  .route('/heroes/[:id]')
  .link(() => Authorizer.bearer(authServer))
  .link(() => HeroesController(context));
```

An `Authorizer` protects a channel from unauthorized requests by validating the `Authorization` header of a request. When created with `Authorizer.bearer`, it ensures that the authorization header contains a valid access token. Restart your application and try and access the `/heroes` endpoint without including any authorization:

`Authoriser`는 요청의 `Authorization` 헤더를 검증함으로써 허가 되지 않은 요청으로부터 채널을 보호합니다. `Authorizer.bearer`로 생성 될 때, 권한 부여 헤더가 유효한 접근 토큰을 포함하고 있음을 보장 합니다. 애플리케이션을 다시 시작하고 권한을 부여하지 않고 `/heroes` 엔드 포인트에 액세스하십시오 :

```sh
curl -X GET --verbose http://localhost:8080/heroes
```

401 Unauthorized 응답을 받게됩니다. 이제 액세스 토큰을 무기명 인증 헤더에 포함 시키십시오 (토큰이 달라짐).

```sh
curl -X GET http://localhost:8080/heroes -H 'Authorization: Bearer 687PWKFHRTQ9MveQ2dKvP95D4cWie1gh'
```

You'll get back your list of heroes!

Other Uses of Authorizer

`Authoriser`는 액세스 토큰 범위와 기본 인증 자격 증명의 유효성을 검사 할 수 있습니다. 나중에 예제에서 이러한 예제를 볼 수 있습니다.

