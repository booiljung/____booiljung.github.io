https://aqueduct.io/docs/auth/controllers/

# Issue Access Tokens with AuthController

Aqueduct의 Auth 프레임워크를 사용하는 응용 프로그램은 액세스 토큰에 대한 자격 증명을 교환 할 끝 점이 있어야 합니다. 개발자는 이러한 엔드 포인트를 직접 구현하고 `AuthServer`와 직접 대화 할 수 있지만 OAuth 2.0 사양은 행복이 사라지는 곳입니다. 따라서 Aqueduct에는`AuthController`와 `AuthCodeController` 권한 부여 토큰 부여와 갱신을 처리하는 두 개의 `Controller`가 있습니다.

### Issue, Refresh and Exchange Tokens with AuthController

`AuthController`는 접근 토큰을 허용하고 그것들을 갱신 합니다. 또한 액세스 토큰에 대해 `AuthCodeController`에서 가져온 인증 코드를 교환합니다.

응용 프로그램에서 `AuthController`를 사용하는 것은 간단합니다. `Router`에 연결하고`AuthServer`를 전달하십시오.

```dart
@override
Controller get entryPoint {
  final router = Router();

  router
    .route("/auth/token")
    .link(() => AuthController(authServer));

  return router;
}
```

액세스 토큰을 부여하기 위해 클라이언트 응용 프로그램은 HTTP `POST` 를 컨트롤러에 보냅니다. 요청에는 다음이 있어야 합니다.

- 클라이언트 ID 및 클라이언트 비밀 키가 있는 인증 헤더 (존재하는 경우)
- 인증 사용자의 사용자 이름과 암호가 있는`x-www-form-urlencoded`본문.

본문에는 키-값 쌍 `grant_type=password`도 포함되어야 합니다. 예를 들어 다음 Dart 코드는 성공적인 인증을 시작합니다.

```dart
var clientID = "com.app.demo";
var clientSecret = "mySecret";
var body = "username=bob@stablekernel.com&password=foobar&grant_type=password";
var clientCredentials = Base64Encoder().convert("$clientID:$clientSecret".codeUnits);

var response = await http.post(
  "https://stablekernel.com/auth/token",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded",
    "Authorization": "Basic $clientCredentials"
  },
  body: body);
```

OAuth 2.0 클라이언트 ID가 공개 인 경우 (즉, 클라이언트 비밀번호가 없는 경우) 인증 헤더에서 비밀번호가 누락됩니다.

```dart
// Notice that the separating colon (:) is still present.
var clientCredentials = Base64Encoder().convert("$clientID:".codeUnits);
```

비밀번호 토큰 요청에 대한 응답은 OAuth 2.0 사양을 따르는 JSON 본문입니다.

```json
{
  "access_token": "...",
  "refresh_token": "...",
  "expires_in": 3600,
  "token_type": "bearer"
}
```

`expires_in` 필드는 발행 날짜와 만기일의 델타를 기반으로 한 계산 된 속성입니다. `issueded` 및 `expirationdate` 열의 값을 수동으로 편집하지 않아야 합니다. 토큰은 동일한 엔드 포인트를 통해 새로 고쳐 지지만 갱신 토큰 및 `grant_type=refresh_token`만을 포함하는 페이로드로 갱신됩니다.

```json
grant_type=refresh_token&refresh_token=kjasdiuz9u3namnsd
```

OAuth 2.0 클라이언트 식별자 및 비밀 정보 생성에 대한 자세한 내용은 [Aqueduct Auth CLI](https://aqueduct.io/docs/auth/cli/)를 참조하십시오.

Aqueduct 응용 프로그램이 범위를 사용하는 경우 추가 `scope` 매개 변수에 요청 된 권한 범위의 공백으로 구분 된 목록을 포함 할 수 있습니다. 허용 된 범위 만 반환되고 부여되며 범위가 허용되지 않으면 요청이 실패합니다. 범위가 제공되면 응답 범위에서 부여 된 범위를 사용할 수 있습니다.

`Authoriser` 가 `AuthController` 인스턴스를 보호해서는 안됩니다. `Authorization` 헤더는 `AuthController`에 의해 파싱되고 검증됩니다.

권한이 부여되면 액세스 토큰을 사용하여 응용 프로그램 채널에서 `Authorizer.bearer()`를 전달할 수 있습니다.

### Issue Authorization Codes with AuthCodeController

`AuthCodeController`는 OAuth 2.0 인증 코드 흐름을 관리합니다. 인증 코드 흐름은 Aqueduct 응용 프로그램이 타사 응용 프로그램이 권한이 부여 된 자원에 액세스 할 수 있게 할 때 사용됩니다.

사람들이 직접 메모를 저장할 수 있는 Aqueduct 응용 프로그램을 만들었다 고 가정 해 보겠습니다. 이제 친구가 할 일 목록 인 자신의 응용 프로그램으로 접근합니다. 자신의 메모 작성 기능을 구축하는 대신 친구는 자신의 응용 프로그램 사용자가 사용자가 응용 프로그램에 저장 한 메모에 액세스하길 원합니다. 신뢰할 수있는 반면, 친구가 가입자의 사용자 이름과 비밀번호에 액세스하는 것을 원하지는 않습니다.

친구가 서버에서 호스팅하는 HTML 페이지로 연결되는 응용 프로그램 링크를 추가합니다. 사용자는이 페이지에 자격 증명을 입력하면 서버에 'POST'요청을 보냅니다. 서버는 사용자의 브라우저를 친구의 응용 프로그램으로 리디렉션하여 응답합니다. *인증 코드*는 리디렉션 URL의 쿼리 문자열에 포함됩니다.

친구의 애플리케이션이 URL의 코드를 파싱하여 해당 서버로 전송합니다. 배후에서는 서버가 이 코드를 액세스 토큰 용 서버와 교환합니다.

`AuthCodeController`는 `GET`과 `POST` 요청 모두에 응답합니다. `GET`을 하면, 로그인 폼을 가진 HTML 페이지를 제공합니다. 이 로그인 양식의 제출 조치는 사용자의 사용자 이름과 암호를 사용하여 동일한 엔드 포인트에 `POST` 를 보냅니다. 성공시, `POST`의 응답은 인증 코드가 있는 302 리디렉션입니다.

`AuthCodeController`를 설정하는 것은 `AuthController`를 설정하는 것만큼이나 간단하지만, HTML 로그인 폼을 렌더링하는 함수가 필요합니다. 다음은 그 예입니다.

```dart
@override
Controller get entryPoint {
  final router = Router();

  router
    .route("/auth/code")
    .link(() => AuthCodeController(
      authServer, renderAuthorizationPageHTML: renderLogin));

  return router;
}

Future<String> renderLogin(
    AuthCodeController requestingController,
    URI requestURI,
    Map<String, String> queryParameters) {
  var html = HTMLRenderer.templateWithSubstitutions(
    "web/login.html", requestURI, queryParameters);

  return html;
}
```

HTML 렌더링 함수에 전달 된 모든 값은 폼의 쿼리 매개 변수로 보내야합니다. 이 매개 변수에는 필요한 보안 구성 요소와 범위 정보가 들어 있습니다.

친구의 응용 프로그램이 로그인 페이지 (`GET/auth/code`)에 연결되면`state`, `client_id`, `response_type`의 세 가지 질의 매개 변수를 포함해야 합니다. 선택적으로 `scope`를 포함 할 수 있습니다.

```
https://stablekernel.com/auth/code?client_id=friend.app&response_type=code&state=87uijn3rkja
```

`client_id`의 값은 당신의 친구의 어플리케이션을 위해 특별히 만들어 져서 데이터베이스에 저장되어야 합니다. ([Aqueduct Auth CLI](https://aqueduct.io/docs/auth/cli/)에서 `aqueduct auth`를 사용하여 클라이언트 식별자를 생성하는 방법에 대한 자세한 내용을 보십시오.) `response_type`은 항상 `code`이어야 합니다. `state`는 친구의 응용 프로그램이 만드는 값이어야합니다 - 이것은 종종 세션 쿠키와 같은 임의의 값입니다.

친구의 응용 프로그램 사용자가 이 과정을 거치면 다시 친구의 응용 프로그램으로 리디렉션됩니다. 생성 된 인증 코드와 `state` 값은 모두 URL의 쿼리 매개 변수가 됩니다. 리디렉션 URL은 다음과 같습니다.

```
https://friends.app/code_callback?code=abcd672kk&state=87uijn3rkja
```

`aqueduct auth`로 클라이언트 식별자를 생성 할 때 리디렉션 URL이 미리 결정됩니다.

친구의 응용 프로그램은 `state`가 `GET /auth/code`에서 보낸 `state`와 일치하는지 확인합니다. 그런 다음 코드를 서버에 보냅니다. 그런 다음 서버는 `application/x-www-form-urlencoded`  본문과 함께 `AuthController` - *NOT* `AuthCodeController`에 `POST `를 실행하여이 코드를 서버와 교환합니다:

```
grant_type=authorization_code&code=abcd672kk
```

###### 액세스 토큰은 친구에게 데이터베이스에 저장되는 서버로 반환됩니다. 사용자 중 한 명이 애플리케이션의 데이터에 액세스해야 하는 요청을 할 때마다 해당 액세스 토큰으로 요청을 실행합니다.