[Up](index.md)

원문: https://aqueduct.io/docs/testing/tests/

# Testing in Aqueduct

Aqueduct는 개발 시점부터 테스트 할 수 있도록 제작되었습니다. 실제로 이것은 두 가지를 의미합니다.

- 배포 된 Aqueduct 응용 프로그램은 테스트중인 Aqueduct 응용 프로그램과 코드 차이가 없습니다.
- Aqueduct에서 테스트를 작성하는 데 유용한 유틸리티가 있습니다.

## How Tests are Written

Aqueduct 테스트 스위트는 응용 프로그램의 테스트 인스턴스 용으로 특별히 빌드 된 구성 파일을 사용하여 응용 프로그램을 시작합니다. 이 응용 프로그램에 전송 된 요청의 응답을 확인하는 테스트 사례를 작성합니다. 때로는 의도한 부작용이 발생했는지 확인하기 위해 응용 프로그램의 서비스에 접근 할 수 있습니다. 예를 들어, 요청이 실행 된 후 행이 데이터베이스 테이블에 추가되었는지 확인할 수 있습니다.

`TestHarness<T>`는 테스트 중인 응용 프로그램의 초기화를 처리하는 `package:aqueduct_test`의 타입입니다. 테스트 사용자가 데이터베이스에 시드를 하거나 OAuth 2.0 클라이언트를 추가하는 것과 같이 애플리케이션 별 시작 작업을 추가하기 위해 종종 하위 클래스로 분류됩니다. 테스트 하니스는 테스트의 `main` 함수의 시작 부분에 설치됩니다.

```dart
void main() {
  final harness = new TestHarness<MyApplicationChannel>()..install();

  test("GET /endpoint returns 200 and a simple object", () async {
    final response = await harness.agent.get("/endpoint");
    expectResponse(response, 200, body: {"key": "value"});
  });
}}
```

`TestHarness.install`이 호출되면, 테스트가 시작될 때 '테스트 모드'에서 응용 프로그램을 시작할 두 콜백을 설치하고, 테스트가 완료된 후에 중지합니다. '테스트 모드'에서 실행되는 응용 프로그램은 로컬 HTTP 서버를 생성하고 테스트가 실행되는 것과 동일한 격리에서 `ApplicationChannel`을 인스턴스화합니다. 이를 통해 ApplicationChannel의 서비스에 접근하여 서비스가 관리하는 상태에 대한 테스트 기대치를 추가 할 수 있습니다.

이 방법으로 응용 프로그램을 시작하면 옵션에 몇 가지 기본값이 있습니다.

- 응용 프로그램이 임의의 포트에서 수신 대기합니다.
- `configurationFilePath`는 `config.src.yaml`입니다.

`config.src.yaml` 파일은 배포 구성과 동일한 구조를 가져야 만하지만 값은 테스트 제어 값으로 대체됩니다. 예를 들어 데이터베이스 연결 구성은 프로덕션 데이터베이스 대신 로컬 테스트 데이터베이스를 가리 킵니다. 응용 프로그램 구성에 대한 자세한 내용은 [이 안내서](https://aqueduct.io/docs/application/configure/)를 참조하십시오.

Harness install

`install` 메소드는 `package:test`에서 `setUpAll`과 `tearDownAll`을 호출하여 응용 프로그램을 시작하고 멈춥니 다. `TestHarness.start`와 `TestHarness.stop`를 호출하여 응용 프로그램을 수동으로 시작하고 중지 할 수 있습니다. 그러나, `onSetUp`과`onTearDown`이 각 테스트마다 호출되지 않기 때문에 이것은 권장되지 않습니다.

테스트 할 때 잡히지 않는 예외

테스트 하네스는 테스트에서 실패를 유발할 수 있도록 캡처되지 않은 예외를 이스케이프 처리하도록 응용 프로그램을 구성합니다. 이는 모든 예외가 포착되어 HTTP 클라이언트에 오류 응답을 보내는 응용 프로그램을 정상적으로 실행하는 경우와 다릅니다.

### TestHarness 서브 클래스 사용하기

대부분의 응용 프로그램은 응용 프로그램 사용자 정의를 제공하기 위해 `TestHarness<T>`를 서브 클래스 화해야합니다. (CLI를 통해 생성 된 응용 프로그램은 `test/harness/app.dart`에 서브클래스가 있습니다.) 응용 프로그램이 시작될 때와 각 테스트의 전후에 테스트 중에 발생하는 이벤트에 대한 콜백 메소드를 대체합니다.

```dart
class Harness extends TestHarness<WildfireChannel> {
  @override
  Future onSetUp() async {
    // called before each test
  }
}
```

이 콜백을 호출하기 위해서는 테스트 슈트의 시작 부분에서 테스트 하니스에 `install`을 호출해야 합니다.

ORM 또는 OAuth 2.0을 사용하는 응용 프로그램을 테스트하기 위해 하네스에 믹스인 할 수 있는 클래스에 대해서는 [harness mixins](https://aqueduct.io/docs/testing/mixins/)를 참조하십시오.

## 에이전트를 사용하여 요청 실행

`TestHarness<T>`는 테스트중인 응용 프로그램에 대한 요청을 실행하기 위해 사용되는 `agent` 속성을 가지고 있습니다.  `Agent`는 요청을 실행하고 유효성을 검사 할 수있는 응답 객체를 반환하는 `get` 및 `post`와 같은 메소드를 가지고 있습니다. 사용법은 다음과 같습니다.

```dart
test("After POST to /thing, GET /thing/:id returns created thing", () async {
  final postResponse = await harness.agent.post("/thing", body: {"key": "value"});
  expectResponse(postResponse, 200);

  final thingId = postResponse.body.as<Map>()["id"];
  final getResponse = await harness.agent.get("/thing/$thingId");
  expectResponse(getResponse, 200, body: {
    "id": thingId,
    "key": "value"
  });
});
```

대부분의 요청은 `TestHarness.get` 및 `TestHarness.post`와 같은 메소드에서 설정되고 실행될 수 있습니다. 추가 설정 옵션을 위해 `TestHarness.request`를 사용하여 그 속성에 의해 더 커스터마이징 될 수 있는 요청 객체를 생성하십시오:

```dart
final request = harness.agent.request("/endpoint")
  ..headers["X-Header"] = "Value";
```

요청에 본문이 포함되면 본문은 요청의 콘텐츠 유형에 따라 인코딩됩니다 (기본값은 JSON). 인코딩 동작은 응용 프로그램 논리의 인코딩 및 디코딩을 관리하는 것과 동일한 유형인 `CodecRegistry`에 의해 제공됩니다. 테스트 요청에 본문을 추가 할 때 인코딩되지 않은 값 (예 : Dart`Map` )을 제공하고 올바른 값 (예 : JSON 객체)으로 인코딩됩니다. 반대 측면에서 응답 본문의 유효성을 검사 할 때 테스트 코드가 응답을 받기 전에 본문이 이미 Dart 유형으로 디코딩됩니다.

Codecs and CodecRegistry

테스트는 응용 프로그램과 동일한 격리에서 실행됩니다. 응용 프로그램에서 코덱 저장소에 등록 된 코덱은 테스트 요청을 인코딩하고 디코딩하는 코드에서 자동으로 사용할 수 있습니다. 기본값 이외의 코덱을 옵트 인하는 데 특별한 조치를 취할 필요는 없습니다.

### 에이전트가 요청에 기본값 추가

`Agent`는 그것으로부터의 요청에 적용되는 디폴트 값을 가집니다. 이 값에는 헤더와 요청 본문 content-type이 포함됩니다. 예를 들어, 각 요청마다 헤더를 추가하는 코드를 작성하지 않고도 모든 요청에 추가 헤더 값을 지정할 수 있습니다.

하네스의 기본 에이전트는 `application/json` `contentType`을 가진 요청을 만듭니다. 다른 기본값 세트에 대해 추가 에이전트를 작성할 수 있습니다.

인증을 필요로 하는 엔드 포인트를 테스트 할 때 특히 유용합니다. 여기서 인증은 각 요청에 첨부되어야 합니다. 이 작업을 쉽게 해주는 [harness mixins](https://aqueduct.io/docs/testing/mixins/)이 있는 일반적인 작업입니다.

## Writing Test Expectations

에이전트가 요청을 실행하면 응답에 테스트 기대치를 기록합니다. 이러한 기대치에는 상태 코드 확인, 응답의 헤더 및 본문이 원하는 값입니다. 기대치는 응답 속성에 일치를 적용하여 설정됩니다. 예 :

```dart
test("GET /foo returns 200 OK", () async {
  final response = await harness.agent.get("/foo");

  expect(response.statusCode, 200);
  expect(response, hasHeaders({"x-timestamp": greaterThan(DateTime(2020))}));
  expect(response, hasBody(isNull));
});
```

응답 헤더 및 본문의 유효성 검사는 상태 코드 유효성 검사보다 더 복잡 할 수 있습니다. `hasBody` 및`hasHeaders` `matchers`는 응답 헤더와 body에 대한 기대를 쉽게 만듭니다.

`hasHeaders` 정규 표현 엔진은 헤더 명과 값의 맵을 취해, 응답의 헤더가 맵 내의 각 헤더에 일치하는 헤더를 포함 할 것을 기대합니다. 값은 `String` 또는 다른 `Matcher` 일 수 있습니다. 응답에 예상보다 많은 헤더가 있을 수 있습니다. 헤더는 무시됩니다. 모든 헤더를 정확히 지정하려면 `hasHeaders`를 전달하기 위한 옵션 플래그가 있습니다.

`hasBody` 매처는 응답의 디코드 된 본문과 비교되는 모든 객체 또는 matcher를 취합니다. body는 이 비교 전에 내용 유형에 따라 디코딩됩니다. 예를 들어, 응답이 JSON 객체 `{"key": "value"}`를 반환하면, 이 객체는 먼저 값 `{'key': 'value'}`로 Dart `Map`으로 디코딩 됩니다. 다음과 같은 결과는 모두 참입니다.

```dart
// exact match of Dart Map
expect(response, hasBody({'key': 'value'}));

// a map that contains a key whose value starts with 'v'
expect(response, hasBody({'key': startsWith('v')}));

// a map that contains the key 'key'
expect(response, hasBody(containsKey('key')));

// a map with one entry
expect(response, hasBody(hasLength(1)));
```

다른 테스트 커버리지가 있는 대형 응답 본문의 경우 몇 가지 값에 대한 기대치만 설정할 수 있습니다. 예를 들어, 50 개의 키가 있는 맵을 가질 수 있지만, `status = 'pending'`을 확인하는 것이 중요합니다. 이를 위해 `partial` 맵 매처가 있습니다. 그것은 당신이 제공하는 키만 검사한다는 점에서 `hasHeaders`와 비슷하게 동작합니다 - 다른 어떤 키도 무시됩니다. 예 :

```dart
// Just ensure the body contains an object with at least status=pending, version>1
expect(response, hasBody(partial({
  "status": "pending",
  "version": greaterThan(1)
})));
```

`partial`을 사용하는 경우 맵에 `isNotPresent` 매처가 있는 키가 없는지 확인할 수도 있습니다.

```dart
test("Get 200 that at least have these keys", () async {
  var response = await app.client.request("/endpoint").get();
  expect(response, hasResponse(200, partial({
    "key3": isNotPresent
  })));
});
```

이렇게 하면 `key3`이 맵에 없는 것을 확인할 수 있습니다. 이것은 `key3:null`을 확인하는 것과는 다르며, 이는 `key3`의 값이 실제로 `null` 값 이었을 때 참이 될 것입니다. 더 자세한 matcher에 대해서는 `aqueduct/test`에 대한 [API Reference](https://www.dartdocs.org/documentation/aqueduct/latest/aqueduct.test/aqueduct.test-library.html)를 참조하십시오.

### 부작용 확인

멱등하지 않은 요청 (어떤 식 으로든 데이터를 변경 함)의 경우 요청 후에 데이터의 상태가 올바르게 변경되었는지 확인해야합니다. 이는 업데이트 된 데이터를 얻기 위해 응용 프로그램에서 처리하는 다른 요청을 보내서 수행됩니다. 예를 들어, `POST/employees`를 사용하여 employee을 만든 후에는 `GET/employees/:id`가 방금 보낸 동일한 데이터를 가질 것으로 기대하여 employee가 올바르게 저장되었는지 확인합니다.

경우에 따라 API를 통해 예상 변경 사항에 액세스 할 수 없는 경우도 있습니다. 예를 들어 새 직원을 만들면 감사 데이터베이스에 레코드가 추가되지만 이 데이터베이스는 공용 API를 통해 액세스 할 수 없다고 가정 해 봅시다. 그러나 테스트 할 때 레코드가 데이터베이스에 추가되었는지 확인해야 합니다. `TestHarness.channel`을 통해 테스트에서 데이터베이스 연결과 같은 애플리케이션 서비스에 액세스 할 수 있습니다. 예를 들어, 응용 프로그램의 테스트 데이터베이스에 대해`Query <T> `를 실행할 수 있습니다 :

```dart
test("POST /employees adds an audit log record", () async {
  final response = await harness.agent.post("/employees", body: {
    "name": "Fred"
  });

  expect(response, hasStatus(202));

  final context = harness.channel.context;
  final query = new Query<AuditRecord>(context)
    ..where((record) => record.user.id).equalTo(response.body.as<Map>()['id']);
  final record = await query.fetchOne();
  expect(record, isNotNull);
});
```

`ApplicationChannel`에서 액세스 할 수 있는 모든 것, 그래서 역시 테스트 할 수 있습니다.