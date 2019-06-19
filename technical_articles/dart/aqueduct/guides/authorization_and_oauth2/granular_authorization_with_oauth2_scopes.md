https://aqueduct.io/docs/auth/auth_scopes/

# Granular Authorization with OAuth 2.0 Scopes

많은 애플리케이션에서 운영에는 다양한 수준의 액세스 제어가 있습니다. 예를 들어 사용자는 '메모'를 만드는 데 특별한 권한이 필요할 수 있지만 모든 사용자는 메모를 읽을 수 있습니다. OAuth 2.0에서는 작업에 대한 권한이 액세스 토큰의 *scope*에 의해 결정됩니다. 특정 범위를 요구하도록 작업을 정의 할 수 있으며 해당 범위로 해당 액세스 토큰을 부여한 경우에만 해당 작업을 요청할 수 있습니다.

스코프는 `notes` 나 `notes.readonly`와 같은 문자열 식별자입니다. 클라이언트 응용 프로그램은 사용자를 대신하여 인증 할 때 이러한 범위 식별자 중 하나 이상을 액세스 토큰에 부여하도록 요청합니다. 유효한 범위는 액세스 토큰과 함께 저장되므로 이후의 액세스 토큰 사용으로 범위를 참조 할 수 있습니다.

# Scope Usage in Aqueduct

액세스 토큰의 범위는 사용자가 인증 할 때 결정됩니다. 인증하는 동안 클라이언트 응용 프로그램은 요청 된 범위를 나타내며 Aqueduct 응용 프로그램은 해당 범위가 클라이언트 응용 프로그램과 사용자에게 허용되는지 여부를 결정합니다. 이 범위 정보는 액세스 토큰에 첨부됩니다.

액세스 토큰으로 요청이 이루어지면 `Authorizer`는 토큰의 범위를 검색합니다. 요청이 확인 된 후, `Authorizer`는 범위 정보를 `Request.authorization`에 저장합니다. 연결된 컨트롤러는이 정보를 사용하여 요청 처리 방법을 결정할 수 있습니다. 일반적으로 컨트롤러는 액세스 토큰이 작업 범위가 충분하지 않은 경우 요청을 거부하고 403 Forbidden 응답을 보냅니다.

따라서 응용 프로그램에 범위를 추가하는 작업은 다음 세 단계로 구성됩니다.

1. 작업에 범위 제한을 추가합니다.
2. OAuth2 클라이언트 식별자 (및 선택적으로 사용자)에 대한 허용 범위를 추가합니다.
3. 인증 할 때 클라이언트 응용 프로그램을 업데이트하여 범위를 요청합니다.

## Adding Scope Restrictions to Operations

`Authorizer`는 요청을 처리 할 때 요청에 첨부 된 `Authorization` 객체를 생성합니다. `Authorization` 객체는 접근 토큰에 부여 된 모든 스코프를 포함하는 `scopes` 속성을 가지고 있습니다. 이 객체에는 특정 범위가 해당 범위 목록에 유효한지 확인하기위한 편리한 방법도 있습니다.

```dart
class NoteController extends Controller {
  @override
  Future<RequestOrResponse> handle(Request request) async {
    if (!request.authorization.isAuthorizedForScope("notes")) {
      return Response.forbidden();
    }

    return Response.ok(await getAllNotes());
  }
}
```

Use an Authorizer

`Request`의 `authorization` 속성은 요청이 `Authorizer`에 의해 처리 된 후에 만 유효합니다. 그렇지 않으면 `null`입니다.

`Authorizer`는 요청을 링크 된 컨트롤러로 전달하기 전에 요청의 유효성을 검사 할 수도 있습니다.

```dart
router
  .route('/notes')
  .link(() => Authorizer.bearer(authServer, scopes: ['notes']))
  .link(() => NoteController());
```

위의 경우, `NoteController`는 요청의 전달자 토큰에 'notes' 범위가 있는 경우에만 도달합니다. 범위가 충분하지 않으면 403 Forbidden 응답이 전송됩니다. 이것은 `NoteController`의 모든 연산에 적용됩니다.

동일한 리소스에 대해 서로 다른 작업에 대해 별도의 범위를 갖는 것이 일반적입니다. `Scope` 주석은 이 목적을 위해 `ResourceController` 연산 메소드에 추가 될 수 있습니다.

```dart
class NoteController extends ResourceController {
  @Scope(['notes.readonly'])
  @Operation.get()
  Future<Response> getNotes() async => ...;

  @Scope(['notes'])
  @Operation.post()
  Future<Response> createNote(@Bind.body() Note note) async => ...;
}
```

요청에 의도 된 작동 방법에 대한 충분한 범위가 없는 경우 403 Forbidden 응답이 전송됩니다. `Scope` 어노테이션을 사용할 때는 `Authoriser`를 `ResourceController` 전에 링크해야 하지만`Authoriser` 스코프를 지정할 필요는 없습니다.

`Scope` 주석이나 `Authorizer`에 여러 범위 항목이 포함되어 있으면 액세스 토큰에 각 항목에 대한 범위가 있어야 합니다. 예를 들어 주석 `@Scope(['notes', 'user'])`는 'notes'와 'user'범위를 모두 가질 수있는 액세스 토큰이 필요합니다.

## Defining Permissible Scope

클라이언트 응용 프로그램이 사용자 대신 인증되면 액세스 토큰에 대한 요청 범위 목록이 포함됩니다. Aqueduct 응용 프로그램은 인증 클라이언트 식별자와 인증 사용자 모두에 대해 범위가 허용되는 경우 요청 된 범위를 토큰에 부여합니다.

허용 범위를 인증 클라이언트에 추가하려면 `aqueduct auth` 명령 행 도구를 사용하십시오. 새로운 클라이언트 식별자를 생성 할 때 `--allowed-scope '옵션을 포함 시키십시오:

```sh
aqueduct auth add-client \
  --id com.app.mobile \
  --secret myspecialsecret \
  --allowed-scopes 'notes users' \
  --connect postgres://user:password@dbhost:5432/db_name
```

기존 클라이언트 식별자를 수정할 때 `aqueduct auth set-scope` 명령을 사용하십시오 :

```sh
aqueduct auth set-scope \
  --id com.app.mobile \
  --scopes 'notes users' \
  --connect postgres://user:password@dbhost:5432/db_name
```

각 범위는 공백으로 구분 된 문자열입니다. 위 예제는 `com.app.mobile` 클라이언트 ID로 인증하는 클라이언트가 'notes'와 'users'범위로 액세스 토큰을 부여 할 수 있게합니다. 클라이언트 응용 프로그램에서 해당 클라이언트 응용 프로그램에 사용할 수 없는 범위를 요청하면 부여 된 액세스 토큰에 해당 범위가 포함되지 않습니다. 클라이언트 식별자에 대해 사용할 수 있는 요청 범위가 없으면 액세스 토큰이 부여되지 않습니다. 응용 프로그램에 범위 제한을 추가 할 때는 해당 작업에 액세스 할 수 있는 모든 클라이언트 응용 프로그램이 해당 범위를 부여 할 수 있어야합니다.

범위는 응용 프로그램의 '사용자' 개념의 일부 속성에 의해 제한 될 수도 있습니다. 이 사용자 레벨 필터링은 `AuthServerDelegate`에서 `getAllowedScopes`를 오버 라이딩함으로써 이루어집니다. 기본적으로이 메소드는 `AuthScope.Any`를 반환합니다. 이는 제한이 없음을 의미합니다. 클라이언트 응용 프로그램이 범위를 허용하면 해당 응용 프로그램과 함께 로그인하는 모든 사용자가 해당 범위를 요청할 수 있습니다.

이 메소드는 인증하는 사용자에게 유효한 `AuthScope`리스트를 반환 할 수 있습니다. 다음 예제는 `@ManagementAuthDelegate<T>` 하위 클래스를 보여줍니다. 이 하위 클래스는`@standkernel.com` 사용자 이름에 대한 스코프와 `@hotmail.com` 주소에 대한 스코프가 없고 다른 모든 사람들에게는 제한된 범위를 허용합니다.

```dart
class DomainBasedAuthDelegate extends ManagedAuthDelegate<User> {
  DomainBasedAuthDelegate(ManagedContext context, {int tokenLimit: 40}) :
        super(context, tokenLimit: tokenLimit);

  @override
  List<AuthScope> getAllowedScopes(covariant User user) {
    if (user.username.endsWith("@stablekernel.com")) {
      return AuthScope.Any;
    } else if (user.username.endsWith("@hotmail.com")) {
      return [];
    } else {
      return [AuthScope("user")];
    }
  }      
}
```

`getAllowedScopes`에게 건네지는 `user`는 인증 되고 있는 사용자입니다. 이전에 `AuthServer`에 의해 꺼내 졌을 것입니다. `AuthServer`는 `AuthDelegate.getResourceOwner`를 호출하여이 객체를 가져옵니다. `ManagedAuthDelegate<T>`에 대한이 메소드의 기본 구현은 사용자의`id`, `username`, `salt`와 `hashedPassword`만을 가져옵니다.

허용 된 범위를 제한하기 위해 응용 프로그램 사용자 객체의 다른 속성을 사용하는 경우 이러한 속성을 가져 오려면 `getResourceOwner`도 재정의 해야 합니다. 예를 들어, 응용 프로그램의 사용자가`role` 속성을 가지고 있다면, 그것과 다른 네 가지 필수 속성을 가져와야 합니다. 다음은 구현 예입니다.

```dart
class RoleBasedAuthDelegate extends ManagedAuthDelegate<User> {
  RoleBasedAuthDelegate(ManagedContext context, {int tokenLimit: 40}) :
        super(context, tokenLimit: tokenLimit);

  @override
  Future<User> getResourceOwner(
      AuthServer server, String username) {
    final query = Query<User>(context)
      ..where((u) => u.username).equalTo(username)
      ..returningProperties((t) =>
        [t.id, t.username, t.hashedPassword, t.salt, t.role]);

    return query.fetchOne();
  }

  @override
  List<AuthScope> getAllowedScopes(covariant User user) {
    var scopeStrings = [];
    if (user.role == "admin") {
      scopeStrings = ["admin", "user"];
    } else if (user.role == "user") {
      scopeStrings = ["user:email"];
    }

    return scopeStrings.map((str) => AuthScope(str)).toList();
  }
}
```

## Client Application Integration

범위가 지정된 Aqueduct 응용 프로그램과 통합되는 클라이언트 응용 프로그램에는 인증을 수행 할 때 요청 된 범위 목록이 포함되어야 합니다. `AuthController`를 통해 인증 할 때 `scope` 매개 변수가 양식 데이터 본문에 추가 되어야 합니다. 이 매개 변수의 값은 요청 범위의 URL로 인코딩 된 공백으로 구분 된 URL이어야 합니다.

```
username=bob&password=foo&grant_type=password&scope=notes%20users
```

`AuthCodeController`를 통해 인증 할 때, 이 동일한 질의 매개 변수는 초기 'GET' 요청에 추가되어 로그인 양식을 렌더링합니다.

인증이 완료되면 JSON 응답 본문에서 허용 범위 목록을 공백으로 구분 된 문자열로 사용할 수 있습니다.

```json
{
  "access_token": "...",
  "refresh_token": "...",
  "token_type": "bearer",
  "expires_in": 3600,
  "scopes": "notes users"
}
```

# Scope Format and Hierarchy

영숫자 및 일부 기호로 제한되는 것 이외에 범위 문자열이 어떻게 표시 되는지에 대한 확실한 지침은 없습니다. 그러나 Aqueduct는 간단한 범위 지정 구조를 제공합니다. 두 개의 특수 기호 인 `:`과 `.`가 있습니다.

계층 구조는 `:`문자로 지정됩니다. 예를 들어, 다음은 사용자 및 하위 리소스와 관련된 범위의 계층 구조입니다.

- `user` (can read/write everything a user has)
- `user:email` (can read/write a user's email)
- `user:documents` (can read/write a user's documents)
- `user:documents:spreadsheets` (can read/write a user's spreadsheet documents)

이러한 범위가 계층을 구성하는 방법에 유의하십시오. 각 세그먼트는 범위를 더 제한적으로 만듭니다. 예를 들어 액세스 토큰에 'user : email' 범위가있는 경우 사용자의 전자 메일에만 액세스 할 수 있습니다. 그러나 액세스 토큰에 'user' 범위가있는 경우 전자 메일을 포함하여 사용자가 가지고있는 모든 항목에 액세스 할 수 있습니다.

또 다른 예로 `user:documents` 스코프를 가진 접근 토큰은 모든 사용자의 문서에 접근 할 수 있지만 `user:documents:spreadsheets`'라는 범위는 스프레드 시트 문서로만 제한됩니다.

범위는 종종 읽기 대 쓰기 액세스를 나타내는 데 사용됩니다. 언뜻보기에 계층 구조 연산자를 사용하는 것이 좋습니다. `user:email:read`과 `user:email:write`를 사용합니다. 그러나, `user:email:write` 를 가진 접근 토큰은 전자 메일을 읽을 수 있는 권한이 없으며 의도하지 않은 것일 수 있습니다.

이것은 scope modifiers 가 들어 오는 곳입니다. 범위 수정 자 (scope modifier)는 범위 문자열의 끝 부분에 `.` 다음에 추가됩니다. 예를 들어, `user:email.readonly`는 사용자의 이메일에 대한 읽기 전용 액세스 권한을 부여하는 반면 `user:email`는 읽기 및 쓰기 권한을 부여합니다.

한정자가 없는 액세스 토큰에는 권한 any 수정자가 있습니다. 따라서 `user`와 `user:email`는 모두 `user:email.readonly` 보호 된 자원과 행동에 접근 할 수 있지만 `user:email.readonly`는 `user:email`에 의해 보호 받는 자원에 접근 할 수 없습니다.

범위 한정자는 범위 문자열의 마지막 세그먼트에만 유효합니다. 즉,`user:documents.readonly:spreadsheets`는 유효하지 않지만 `user:documents:spreadsheets.readonly`는 유효합니다.