https://aqueduct.io/docs/auth/authorizer/

# Securing Routes with Authorizer

`Authorizer`의 인스턴스는 요청을 전달하기 전에 HTTP 요청의 권한 부여 정보를 확인하기 위해 애플리케이션 채널에 추가됩니다. 채널 액세스를 보호하며 일반적으로 `route` 바로 다음에 옵니다. 다음은 그 예입니다.

```dart
@override
Controller get entryPoint {
  final router = Router();

  router
    .route("/protected")
    .link(() => Authorizer.bearer(authServer))
    .link(() => ProtectedController());

  router
    .route("/other")
    .link(() => Authorizer.basic(authServer))
    .link(() => OtherProtectedController());

  return router;
}
```

`Authorizer`는 HTTP 요청의 `Authorization` 헤더를 파싱 합니다. `Authorizer`의 명명 된 생성자는 `Authorization` 헤더의 필수 형식을 나타냅니다. `Authorization.bearer()` 생성자는 헤더에 다음과 같은 형식의 OAuth 2.0 bearer 토큰이 필요합니다 :

```
Authorization: Bearer 768iuzjkx82jkasjkd9z9
```

`Authorizer.basic`은 사용자 이름과 암호가 콜론 문자 (`:`)로 결합되고 Base 64로 인코딩 된 HTTP 기본 인증을 기대합니다 :

```
// 'dXNlcjpwYXNzd29yZA==' is 'user:password'
Authorization: Basic dXNlcjpwYXNzd29yZA==
```

헤더를 파싱 할 수 없거나 존재하지 않거나 잘못된 형식 인 경우, `Authorizer`는 요청에 401 상태 코드로 응답하고 다음 컨트롤러가 요청을받지 못하게 합니다.

파싱되면, `Authoriser`는 확인을 위해 `AuthServer`에 정보 (베어러 토큰 또는 사용자 이름과 패스워드)를 보냅니다. `AuthServer`가 인증 정보를 거부하면, `Authoriser`는 요청에 401 상태 코드로 응답하고 다음 컨트롤러가 요청을 받지 못하게합니다. 그렇지 않으면 요청은 다음 컨트롤러로 계속됩니다.

`Authorizer.bearer`의 경우 요청 헤더의 값은 유효 기간이 만료되지 않은 액세스 토큰이어야 합니다. 이러한 유형의 권한 부여 프로그램은 엔드 포인트가 로그인 된 사용자를 요구할 때 사용됩니다.

`Authorizer.basic` authorizers 경우 OAuth 2.0 클라이언트 식별자를 찾고 클라이언트 비밀번호가 일치하는지 확인하여 인증 정보를 확인합니다. 이 유형의 권한 부여기가 있는 라우트를 * 클라이언트 인증 * 라우트라고 합니다. 이러한 유형의 권한 부여 프로그램은 엔드 포인트가 유효한 클라이언트 어플리케이션을 필요로하지만 로그인 한 사용자는 필요하지 않을 때 사용됩니다.

### Authorizer and OAuth 2.0 Scope

`Authoriser`는 요청의 베어러 토큰의 범위를 기반으로 컨트롤러에 대한 액세스를 제한 할 수 있습니다. 기본적으로, `Authorizer.bearer`는 유효한 베어러 토큰을 통과시킵니다. 원할 경우, `Authorizer`는 필요한 범위 목록으로 초기화됩니다. `Authorizer`에 나열된 *모든* 범위에 대한 액세스 권한이 있는 경우에만 요청이 `Authorizer`를 전달할 수 있습니다. 예를 들어, 다음은 적어도 `user:posts`와 `location` scope가 필요합니다:

```dart
router
  .route("/checkin")
  .link(() => Authorizer.bearer(authServer, scopes: ["user:posts", "location"]))
  .link(() => CheckInController());
```

범위를 기반으로 액세스를 제한하는 데 `Authorizer`를 사용하지 않아도 됩니다. 컨트롤러는 요청이 `Authorizer`를 통과 한 후에 범위 정보에 액세스 할 수 있으므로 범위를 사용하여보다 세부적인 권한 결정을 할 수 있습니다.

### Authorization Objects

무기명 토큰은 승인 된 권한 부여를 나타냅니다. 과거의 어느 시점에서 사용자가 자격 증명을 제공했으며 토큰이 이를 증명합니다. 베어러 토큰이 HTTP 요청의 권한 헤더에서 전송되면 응용 프로그램은 토큰이 있는 사용자와 그것이 발급 된 클라이언트 응용 프로그램을 조회 할 수 있습니다. 이 정보는 토큰이 검증되고 `Request.authorization`에 할당 된 후에 `Authorization`의 인스턴스에 저장됩니다.

`	Authorizer`에 의해 보호받는 컨트롤러는 이 정보에 액세스하여 행동을 더 결정할 수 있습니다. 예를 들어, 소셜 네트워킹 어플리케이션은 `Authorizer`에 의해 보호되는 `/news_feed` 엔드 포인트를 가질 수 있습니다. 인증 된 사용자가 `/news_feed`에 대한 요청을 하면 컨트롤러는 해당 사용자의 뉴스 피드를 반환합니다. `Authorization`을 사용하여 이것을 결정할 수 있습니다 :

```dart
class NewsFeedController extends ResourceController {
  NewsFeedController(this.context);

  ManagedContext context;

  @Operation.get()
  Future<Response> getNewsFeed() async {
    var forUserID = request.authorization.ownerID;

    var query = Query<Post>(context)
      ..where((p) => p.author).identifiedBy(forUserID);

    return Response.ok(await query.fetch());
  }
}
```

위의 컨트롤러에서 사용자가 다른 사용자의 게시물에 액세스하는 것은 불가능합니다.

`Authorization` 객체는 또한 액세스 토큰의 범위를 유지하므로 컨트롤러는 끝점의 정보/동작에 대해 보다 세밀한 결정을 내릴 수 있습니다. `Authorization`이 특정 범위에 접근 할 수 있는지 검사하는 것은 `scope`의 목록을 보거나 `authorizedForScope`를 사용하여 수행됩니다:

```dart
class NewsFeedController extends ResourceController {
  NewsFeedController(this.context);

  ManagedContext context;

  @Operation.get()
  Future<Response> getNewsFeed() async {
    if (!request.authorization.authorizedForScope("user:feed")) {
      return Response.unauthorized();
    }

    var forUserID = request.authorization.ownerID;

    var query = Query<Post>(context)
      ..where((p) => p.author).identifiedBy(forUserID);

    return Response.ok(await query.fetch());
  }
}
```

### Using Authorizers Without AuthServer

이 가이드 전체에서 `Authorizer`의 인스턴스에 대한 인수는 `AuthServer`라고 합니다. 이것은 사실이지만 - `AuthServer`가 `AuthValidator`를 구현하기 때문에 `AuthValidator`는 베어러 토큰과 사용자이름/암호 자격 증명을 검증하기위한 인터페이스입니다.

`AuthServer`를 사용하지 않고 `Authoriser`를 사용할 수 있습니다. 예를 들어, OAuth 2.0을 사용하지 않는 응용 프로그램은 모든 요청의 사용자 이름과 암호를 확인하기 위해 자체 AuthValidator 인터페이스를 제공 할 수 있습니다.

```dart
class BasicValidator implements AuthValidator {
  @override
  FutureOr<Authorization> validate<T>(AuthorizationParser<T> parser, T authorizationData, {List<AuthScope> requiredScope}) {}
    var user = await userForName(usernameAndPassword.username);
    if (user.password == hash(usernameAndPassword.password, user.salt)) {
      return Authorization(...);
    }

    // Will end up creating a 401 Not Authorized Response
    return null;
  }
}
```

`validate` 메쏘드는 신임장이 유효하면 `Authorization`을, 그렇지 않으면 null을 리턴해야 합니다. `파서(parser)`는 validator가 권한 부여 헤더 (예 :`Basic` 또는 `Bearer`)의 형식을 알 수 있게하고, `authorizationData`는 해당 헤더의 의미 있는 정보입니다. `AuthorizationParser<T>`의 두 가지 구체적 유형, `AuthorizationBasicParser`와 `AuthorizationBearerParser`가 있습니다. 기본 파서에 대한 인증 데이터는 사용자 이름과 비밀번호를 포함하는 `AuthBasicCredentials`의 인스턴스이며, 베어러 파서의 인증 데이터는 베어러 토큰 문자열입니다.