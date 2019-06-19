https://aqueduct.io/docs/testing/mock/

# Mocking External Services

Aqueduct 응용 프로그램은 종종 다른 서버와 통신합니다. 예를 들어, 응용 프로그램은 GitHub API에 팀 작업 흐름에 대한 분석을 수집하도록 요청할 수 있습니다. 자동화 된 테스트를 실행하면 실제 GitHub API를 사용하는 것이 실현 가능하지 않습니다. GitHub이 속도를 제한 할 것이고 반환되는 데이터가 끊임없이 변하기 때문입니다.

이 문제를 해결하기 위해 테스트 중에 서비스의 "mocks"를 만들 수 있습니다. Aqueduct는`aqueduct/test` 라이브러리에 `MockServer`와 `MockHTTPServer`라는 두 가지 테스트 유틸리티를 가지고 있습니다.

## Using a MockHTTPServer

Aqueduct 응용 프로그램은 종종 다른 서버와 통신합니다. 예를 들어, 응용 프로그램은 GitHub API에 팀 작업 흐름에 대한 분석을 수집하도록 요청할 수 있습니다. 자동화 된 테스트를 실행하면 실제 GitHub API를 사용하는 것이 실현 가능하지 않습니다. GitHub이 속도를 제한 할 것이고 반환되는 데이터가 끊임없이 변하기 때문입니다. 이 문제를 해결하기 위해 테스트 중에 서비스의 "mocks"를 만들 수 있습니다. Aqueduct는 `aqueduct/test` 라이브러리에 `MockServer`와`MockHTTPServer`라는 두 가지 테스트 유틸리티를 가지고 있습니다.

```dart
test("Will get correct user from GitHub", () async {
  var response =
    await app.client.authenticatedRequest("/github_profile/fred").get();

  var requestSentByYourApplicationToGitHub = await githubMock.next();
  expect(requestSentByYourApplicationToGitHub.method, "GET");
  expect(requestSentByYourApplicationToGitHub.path, "/users/search?name=fred");
});
```

위의 코드에서 우리는 `GET /github_profile/fred`라는 요청이 애플리케이션에 전송 될 때마다 GitHub의 API에서 사용자를 검색하고 검색 할 때마다이를 기대하고 있습니다. 이 테스트는 GitHub API에 대한 요청에 대한 요청을 올바르게 변환했는지 확인합니다. 요청이 없다면 (프로그래머 오류로 인해) `githubMock.next()`에서 반환 된 `Future`가 결코 완료되지 않기 때문에 이 테스트는 실패합니다. 아무도 배달되지 않았으므로 다음 요청이 없습니다!

디폴트로, `MockHTTPServer`에 보내진 요청은 아무 것도 없는 200 OK 응답입니다. 모의 서버에서 응답을 대기열에 넣어 이 동작을 변경할 수 있습니다.

```dart
test("Will get correct user from GitHub", () async {
  githubMock.queueResponse(new Response.ok({"id": 1, "name": "fred"}));

  var response =
    await app.client.authenticatedRequest("/github_profile/fred").get();
  expect(response, hasResponse(200, partial({
    "id": 1,
    "name": "fred"
  })))
});
```

위의 코드에서, `queueResponse`는 특정한 몸체를 가진 모의 서버 큐에 200 OK Response를 추가합니다. 모의 서버는 수신 한 다음 요청에 대해 응답을 보냅니다. `/ github_profile/fred`의 구현에서 여러분의 응용 프로그램은 GitHub API가 여러분의 모의 서버라는 점을 제외하고 GETHub API에 `GET /users/search?name=fred`를 보냅니다. 대신 대기중인 응답을 반환합니다. 따라서 대기중인 응답은 GitHub API의 예상 응답입니다.

요청이 완료된 후 응답은 대기열에서 제거되고 후속 응답은 기본값으로 되돌아 갑니다. 원하는 만큼 응답을 대기열에 넣을 수 있습니다. 실패한 요청 (응답을 얻지 못하는 요청)을 시뮬레이션 할 수도 있습니다.

```dart
mockServer.queueResponse(MockHTTPServer.mockConnectionFailureResponse);
```

`MockHTTPServer`를 서브 클래스 화하고 `open` 메소드를 재정의하여 응답을 결정하는 로직을 추가 할 수도 있습니다. 자세한 내용은 `MockHTTPServer.open` 구현체를 참조하십시오.

## Configuring a MockHTTPServer

테스트를 설정할 때 `MockHTTPServer`가 생성됩니다. 테스트를 해체 할 때는 반드시 닫아야 합니다. 동일한 모의 서버를 모든 테스트에서 사용한다면 (예 :`setUpAll`에서 열어서), 각 테스트 후에 반드시 모의 서버를 지우십시오:

```dart
import 'package:aqueduct/test.dart';

void main() {
  var mockServer = new MockHTTPServer(4000);

  setUpAll(() async {
    await mockServer.open();
  });

  tearDownAll(() async {
    await mockServer.close();
  });

  tearDown(() async {
    mockServer.clear();
  });
}
```

`MockHTTPServer` 인스턴스는 특정 포트의 localhost를 수신합니다. 테스트 가능한 외부 서비스 요청을하는 응용 프로그램은 구성 파일에 해당 서비스의 기본 URI를 제공해야 합니다. [테스트 중 사용 된 구성 파일](https://aqueduct.io/docs/application/configure/)에 있는 해당 서비스의 URI는 localhost와 특정 포트를 가리켜 야합니다. 예를 들어, 배치 된 `config.yaml` 파일에 다음 키-값이있는 경우 :

```
github:
  baseURL: https://api.github.com/  
```

그러면 `config.src.yaml`은 다음을 갖습니다 :

```
github:
  baseURL: http://localhost:4000/
```

응용 프로그램은 이 구성 파일을 읽고 요청을 실행할 서비스에 기본 URL을 삽입합니다.

```dart
class AppConfiguration extends Configuration {
  AppConfiguration(String fileName) : super.fromFile(fileName);

  APIConfiguration github;
}

class AppApplicationChannel extends ApplicationChannel {
  @override
  Future prepare() async {
    var config = new AppConfiguration(options.configurationFilePath);

    githubService = new GitHubService(baseURL: config.github.baseURL);
  }
}
```

`APIConfiguration`은 기존의 타입이며이 목적을 위한 것입니다.

또한 데이터베이스 연결을 위한 테스트 전략은 모조 (mock)를 사용하는 것이 아니라 테스트 중에 설정되고 찢어지는 임시 로컬 데이터베이스를 사용하는 것이 아닙니다. 이는 데이터 모델 생성 코드를 소유하고 있기 때문에 가능하지만 외부 서비스의 개발 API에 액세스 할 수는 없습니다.