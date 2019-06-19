https://aqueduct.io/docs/auth/

# Overview

## Tasks

Aqueduct에는 [OAuth 2.0 사양](https://tools.ietf.org/html/rfc6749)에 따라 인증 및 승인을 관리하는 유형이 있습니다.

인증 및 권한 부여 로직을 관리하는 애플리케이션을 위한 `AuthServer` 서비스 객체를 생성합니다.  `AuthServer`는 설정과 필요한 데이터 저장을 처리하기 위해 `AuthServerDelegate`를 구현하는 헬퍼 객체를 필요로합니다. 대부분이 객체는 Aqueduct ORM을 사용하여 이 저장소를 관리하는 `ManagedAuthDelegate<T>`입니다.

`AuthServer` 서비스 객체는 컨트롤러 채널에 대한 접근을 보호하는`Authorizer` 컨트롤러에 주입됩니다. `AuthServer`는 `AuthCodeController`와 `AuthController`에도 주입되어 인증을 위한 HTTP API를 제공합니다.

`aqueduct auth` 명령 행 도구는 실시간 애플리케이션을 위한 클라이언트 식별자 관리와 같은 설정을 관리합니다.

![Authorization Objects](https://aqueduct.io/docs/img/authobjects.png)

- [What is OAuth 2.0?](what_is_oauth2.md)
- [Creating and Using AuthServers](creating_authservers_to_authenticate_and_authorize.md)
- [Securing Routes with Authorizer](securing_routes_with_authorizer.md)
- [Adding Authentication Endpoints](issue_access_tokens_with_autocontroller.md)
- [Using Scopes to Control Access](granular_authorization_with_oauth2_scopes.md)
- [Managing OAuth 2.0 Clients](manage_oauth2_client.md)