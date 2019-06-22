https://aqueduct.io/docs/auth/cli/

# Manage OAuth 2.0 Clients

`aqueduct auth` 명령 행 도구는 OAuth 2.0 클라이언트 애플리케이션 식별자를 생성하여 애플리케이션의 데이터베이스에 삽입합니다. 이 도구를 사용하려면 `ManagedAuthDelegate<T>`를 사용해야 하며, 데이터베이스는 그것을 지원할 테이블을 포함하고 있어야합니다 (자세한 내용은 [this guide](https://aqueduct.io/docs/auth/server/)를보십시오). ).

인증 토큰에 대한 사용자 이름과 암호를 교환하려면 등록 된 클라이언트 식별자가 필요합니다. 토큰은 인증 사용자와 클라이언트 응용 프로그램 모두에 속합니다. 클라이언트는 `aqueduct/managed_auth`에서 `ManagedAuthClient`의 인스턴스로 표현됩니다. 인증 클라이언트는 액세스 토큰을 요청할 때 권한 부여 헤더에 클라이언트 ID (및 해당되는 경우 클라이언트 암호)를 제공해야 합니다.

OAuth 2.0 클라이언트에는 클라이언트를 고유하게 식별하는 문자열 식별자가 있어야 합니다. 예를 들어, `com.food_app.mobile`은 'Food App'에 대한 모바일 애플리케이션의 클라이언트 식별자 일 수 있습니다.

간단한 OAuth 2.0 클라이언트를 만들려면 다음 명령 줄 유틸리티를 실행할 수 있습니다.

```sh
aqueduct auth add-client \
  --id com.food_app.mobile \
  --connect postgres://user:password@dbhost:5432/food_app
```

`connect` 옵션은이 도구가 연결하고 레코드를 `ManagedAuthClient` 데이터베이스 테이블에 삽입 할 애플리케이션의 데이터베이스를 식별합니다. 식별자는`id` 옵션을 통해 제공됩니다.

이런 방식으로 생성 된 OAuth 2.0 클라이언트는 *public* 클라이언트입니다. 고객의 비밀은 없습니다. 리소스 소유자 부여 플로를 사용하지만 클라이언트 보안을 보호 할 수 없는 OAuth 2.0 클라이언트는 이 유형의 클라이언트를 사용해야 합니다. 자바 스크립트 애플리케이션처럼 소스 코드를 볼 수 있으면 애플리케이션은 클라이언트 보안을 확보 할 수 없습니다. 네이티브 모바일 응용 프로그램은 클라이언트 코드를 잠재적으로 해독하여 소스 코드를 해독 할 수 있기 때문에 공개 클라이언트도 사용하는 것이 좋지만 반드시 필요한 것은 아닙니다.

클라이언트 인증 된 끝점 (`Authorizer.basic`으로 보호 된 끝점)에 요청을 하면 클라이언트 헤더가 인증 헤더에서 생략됩니다. 문자열을 base64로 인코딩하는 것은 `clientID:`이며 콜론 (`:`)이 필요합니다. 예를 들어 Dart에서 공용 클라이언트에 대한 인증 헤더를 생성하려면 다음과 같이하십시오.

```dart
var clientID = "com.foobar.xyz";
var clientCredentials = Base64Encoder().convert("$clientID:".codeUnits);
var header = "Basic $clientCredentials";
```

## Confidential Clients

An OAuth 2.0 client is *confidential* if it has a client secret. Client secrets can be provided with the `auth` tool:

```sh
aqueduct auth add-client \
  --id com.food_app.mobile \
  --secret myspecialsecret \
  --connect postgres://user:password@dbhost:5432/food_app
```

클라이언트 secret은 저장되기 전에 무작위로 생성 된 salt으로 해싱 (여러 번)됩니다. 따라서 실제 값은 다른 곳에 안전하게 저장되어야 합니다. 예를 들어 LastPass를 사용합니다.

## Redirect URIs

인증 코드 흐름 (`AuthCodeController`에 의해 제공됨)을 허용하기 위해서, 클라이언트는 반드시 리다이렉트 URI를 가져야 합니다. 이것은 사용자 이름과 암호를 입력 한 후 인증 사용자의 브라우저가 리디렉션되는 URI입니다. 클라이언트는 리디렉션 URI가 있는 기밀 클라이언트 여야 합니다.

```sh
aqueduct auth add-client \
  --id com.food_app.mobile \
  --secret myspecialsecret \
  --redirect-uri https://someapp.com/callback \
  --connect postgres://user:password@dbhost:5432/food_app
```

## Scopes

애플리케이션이 OAuth 2.0 범위를 사용하는 경우 클라이언트는 토큰이 액세스 할 수있는 범위를 가질 수 있습니다. 이렇게 하면 인증 할 클라이언트가 범위를 제한 할 수 있습니다.

```sh
aqueduct auth add-client \
  --id com.food_app.mobile \
  --secret myspecialsecret \
  --allowed-scopes 'scopeA scopeB scopeC.readonly' \
  --connect postgres://user:password@dbhost:5432/food_app
```

범위는 공백으로 구분되며 셸에서 전체 문자열을 하나의 값으로 처리 할 수 있도록 따옴표로 묶어야합니다.

Scope은 클라이언트가 `aqueduct auth set-scope`로 이미 생성 된 후에 설정 될 수 있습니다 :

```dart
aqueduct auth set-scope \
  --id com.food_app.mobile \
  --scopes 'scopeA scopeC' \
  --connect postgres://user:password@dbhost:5432/food_app
```

## Other Info

데이터베이스에 명령을 보내는 모든 `aqueduct` 명령과 마찬가지로, `connect` 옵션은 다음과 같은 형식의 프로젝트 디렉토리에 있는 `database.yaml` 파일로 대체 될 수 있습니다:

```yaml
username: "user"
password: "password"
host: "host"
port: 5432
databaseName: "my_app"
```