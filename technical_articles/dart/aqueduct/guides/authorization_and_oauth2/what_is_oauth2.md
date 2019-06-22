https://aqueduct.io/docs/auth/what_is_oauth/

# What is OAuth 2.0?

대부분의 응용 프로그램에는 사용자 개념이 있습니다. 누구든지 이 사용자임을 알리기 위해 사용자는 암호를 가지고 있습니다. 사용자가 응용 프로그램을 사용하고자 할 때 사용자 이름과 암호를 서버에 보내 사용자가 자신이 말하는 사람인지 확인합니다.

이렇게하는 간단한 방법은 모든 요청에 대해 Authorization 헤더에 사용자 이름과 암호를 보내는 것입니다. 이것이 나쁜 이유는 두 가지입니다. 첫째, 사용자가 무언가를 하고 싶어 할 때마다 비밀번호가 요청할 때마다 전송되기 때문에 안전하지 않을 수 있습니다. 둘째로, 사용자를 로그인 상태로 유지하려는 모든 응용 프로그램은 해당 암호를 저장해야하며 안전하지 않습니다.

OAuth 2.0에서는 사용자 이름과 비밀번호를 클라이언트 애플리케이션에 한 번만 제공합니다. 응용 프로그램은 해당 자격 증명을 서버로 보내고 서버는 응용 프로그램에 액세스 토큰을 다시 제공합니다. 액세스 토큰은 아무도 추측 할 수 없는 길고 임의의 문자열입니다. 사용자가 더 많은 작업을 원할 때 응용 프로그램은 사용자의 암호 대신 모든 요청과 함께 토큰을 보냅니다. 서버는 토큰을 확인하고 만료되지 않았는지 확인한 다음 응용 프로그램의 요청을 통과시킵니다. 응용 프로그램은 암호를 저장할 필요가 없으며 사용자에게 암호를 다시 묻지 않아도됩니다.

이 토큰 신임 정보 교환(credential-for-token exchange)은 POST 요청을 일부 엔드 포인트에 보내 사용자 이름과 비밀번호가 요청 본문에 전송되도록 합니다. 일반적으로 Aqueduct 응용 프로그램의 경로는 `/auth/token`이며 [AuthController](https://aqueduct.io/docs/auth/controllers/)의 인스턴스에 의해 처리됩니다.

OAuth 2.0은 미묘한 구분을 합니다. 사용자와 사용하는 응용 프로그램은 같은 것이 아닙니다. 사용자를 "서버에 요청"한다고 생각하는 것은 직관적이지만 실제로 사용자가 응용 프로그램에 요청을 하고 응용 프로그램이 서버에 요청을 보냅니다. 서버는 사용자를 대신하여 응용 프로그램 액세스 권한을 부여합니다. 다시 말해서 사용자가 응용 프로그램에 자격 증명을 입력하면 응용 프로그램이 서버로 이동하여 "이 사용자가 내가 할 수 있다고 말했습니다. 이봐,이 암호는 비밀 암호입니다!"

OAuth 2.0 서버는 사용자를 확인하는 것이 아니라 애플리케이션을 확인하기 때문에 중요한 차이입니다. 응용 프로그램에는 서버에 등록 된 식별자가 있습니다. 따라서 웹 클라이언트, Android 및 iOS 앱이 있는 생태계의 경우 세 가지 식별자 (각각 하나씩)가 있을 수 있습니다. 클라이언트 응용 프로그램은 대개 해당 식별자를 데이터베이스에 저장합니다. 이 식별자를 *클라이언트 식별자*라고 합니다. 클라이언트 식별자는 `aqueduct auth` 도구로 Aqueduct 응용 프로그램에 추가됩니다 ([Aqueduct Auth CLI](https://aqueduct.io/docs/auth/cli/) 참조).

사용자가 응용 프로그램을 통해 로그인하면 사용자 이름과 암호를 제출합니다. 사용자는 클라이언트 식별자를 제공하지 않습니다. 사실, 사용자는 클라이언트 식별자를 알지 못합니다. 응용 프로그램은 요청 본문에 사용자의 사용자 이름과 암호가 포함 된 요청을 보내고 Authorization 헤더에 클라이언트 식별자를 보냅니다. 세 명 모두 응용 프로그램에 토큰을 돌려주기 위해 서버를 체크 아웃해야합니다. 의사 코드의 전체 요청은 다음과 유사합니다.

```javascript
var request = HTTPRequest("/auth/token");
request.method = "POST";
request.contentType = "application/x-www-form-urlencoded";
request.authorization = Base64.encode("$clientID:");
request.body = {
  "username" : "bob@stablekernel.com",
  "password" : "supersecretstuff",
  "grant_type" : "password"
};
```

액세스 토큰이 만료 될 수 있습니다. 만료되는 데 걸리는 시간은 서버에 달려 있습니다. Aqueduct의 기본값은 24 시간입니다. 언뜻보기에 이것은 응용 프로그램이 사용자에게 암호를 다시 요구해야한다는 것을 의미합니다. 그러나 토큰을 새로 고칠 수도 있습니다. 토큰 새로 고침은 새로운 액세스 토큰을 허용하지만 암호를 묻지 않아도 됩니다. 이는 액세스 토큰에 refresh 토큰이 오기 때문에 가능합니다. 새로 고침 토큰은 길고 임의의 다른 문자열입니다. 따라서 토큰을 부여 할 때 서버가 보내는 JSON은 다음과 같습니다.

```
{
  "access_token" : "Abca09zzzza2o2kelmzlli3ijlka",
  "token_type" : "bearer",
  "refresh_token" : "lkmLIAmooa898nm20jannnnnxaww",
  "expire_in" : 3600
}
```

응용 프로그램이 액세스 토큰과 새로 고침 토큰에 모두 멈춥니 다. 토큰이 만료되면 새로 고침 토큰을 다시 서버로 보내어 대체 액세스 토큰을 가져옵니다. 이것은 액세스 토큰이 나온 경로와 동일한 경로 (`/auth/token`)를 통해 이루어집니다 - 매개 변수가 약간 다릅니다 :

```
var request = HTTPRequest("/auth/token");
request.method = "POST";
request.contentType = "application/x-www-form-urlencoded";
request.authorization = Base64.encode("$clientID:");
request.body = {
  "refresh_token" : "lkmLIAmooa898nm20jannnnnxaww",
  "grant_type" : "refresh_token"
};
```

새로 고침 토큰을 교환하면 몇 가지 값이 변경된다는 점을 제외하고 사용자 이름과 비밀번호의 초기 교환과 동일한 응답을 보입니다.

인증 및 인증 정보의 확인 및 저장은 [AuthServer](https://aqueduct.io/docs/auth/server/)에서 관리합니다.

### Other Methods for Obtaining Authorization

위의 토큰을 얻는 방법 - 사용자 이름과 패스워드를 `/auth/token`에 보내는 방법은 OAuth 2.0이 사용자를 인증하는데 사용할 수 있는 4 가지 방법 중 하나 일뿐 입니다. 이 특정한 것을 자원 소유자 암호 자격 증명 부여라고합니다. 자원 소유자는 '사용자'를 위한 멋진 단어입니다. 우리는 '암호 흐름'으로 줄일 수 있습니다. 아마도 가장 일반적인 흐름 모바일 응용 프로그램이고 프런트 엔드 웹 응용 프로그램은 이 흐름을 자주 사용합니다. 자격 증명을 입력하면 클라이언트 응용 프로그램에서 자격 증명을 직접 서버로 보냅니다.

일반적으로 사용되는 다른 흐름은 클라이언트 응용 프로그램이 사용자의 자격 증명을 볼 수 없도록합니다. 예를 들어 Google 계정으로 Pivotal Tracker에 로그인 할 수 있습니다. Pivotal Tracker의 계정에 비밀번호가 없습니다. 대신 Google 계정과 연결되어 있습니다. Pivotal Tracker에는 Google 비밀번호가 표시되지 않습니다. 이런 방식으로 Pivotal Tracker에 로그인하면 Google이 소유하고 운영하는 Google 인증 페이지로 이동합니다. 성공적으로 로그인하면 Google에서 Pivotal Tracker에 토큰을 제공합니다. Pivotal Tracker는 이제 사용자를 대신하여 작업을 수행 할 수있는 응용 프로그램입니다.

이를 *인증 코드 부여* 또는 '인증 코드 흐름'이라고 합니다. `AuthCodeController`의 인스턴스는 인증 코드 부여를 처리합니다. 코드가 수신되면 `AuthController`를 통해 토큰을 교환 할 수 있습니다.