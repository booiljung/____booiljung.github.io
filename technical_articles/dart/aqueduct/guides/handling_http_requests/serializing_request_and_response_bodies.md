https://aqueduct.io/docs/http/request_and_response/

# Serializing Request and Response Bodies

Aqueduct에서 HTTP 요청과 응답은 `Request`와 `Response`의 인스턴스입니다. 응용 프로그램이 수신하는 각 HTTP 요청에 대해 `Request` 인스턴스가 생성됩니다. 각 `Request`에 대해 `Response`을 만들어야 합니다. 응답은 [controller objects](https://aqueduct.io/docs/http/controller/)에 의해 생성됩니다. 이 가이드에서는 요청 및 응답 객체의 동작에 대해 설명합니다.

## The Request Object

애플리케이션에 대한 각 HTTP 요청마다 `Request`가 생성됩니다. `Request`는 HTTP 요청에 관한 모든 것을 저장하고, 더 쉽게 읽을 수 있는 몇 가지 추가 동작을합니다. [controllers](https://aqueduct.io/docs/http/controller/) 또는 클로저에 코드를 작성하여 요청을 처리합니다.

요청의 모든 속성은`raw` 속성 (dart 표준 라이브러리 `HttpRequest`)에서 사용할 수 있습니다. `Request`는 링크 된 컨트롤러가 사용할 수 있도록 컨트롤러에 데이터를 첨부 할 수있는`attachments`을 가지고 있습니다 :

```dart
router.route("/path").linkFunction((req) {
  req.attachments["key"] = "value";
}).linkFunction((req) {
  return Response.ok({"key": req.attachments["value"]});
});
```

`Request`는 또한 첨부 파일 `authorization`과 `path`를 가지고 있습니다. `authorization`은 `Authoriser`로부터 허가 정보를 포함하고 `path`는 `Router`로부터 요청 경로 정보를 가지고 있습니다.

## The Response Object

`Response`는 상태 코드, 헤더와 본문을 가지고 있습니다. 기본 생성자는 상태 코드, 헤더 맵 및 본문 객체를 사용합니다. 공통적 인 응답 유형에 대해 많은 명명 된 생성자가 있습니다.

```dart
Response(200, {"x-header": "value"}, body: [1, 2, 3]);
Response.ok({"key": "value"});
Response.created();
Response.badRequest(body: {"error": "reason"});
```

Headers are encoded according to [dart:io.HttpHeaders.add](https://api.dartlang.org/stable/2.0.0/dart-io/HttpHeaders/add.html). For body encoding behavior, see the following sections.

## Encoding and Decoding the HTTP Body

`Request`와 `Response` 객체는 HTTP 몸체를 다루기 위한 행동을 합니다. `Request` 본문의 내용을 코드에서 사용되는 Dart 객체로 디코딩합니다. Dart 객체를 `Response`에 제공하면 응답의 content-type에 따라 자동으로 인코딩됩니다.

### Decoding Request Bodies

모든 `Request`에는 `body` 속성이 있습니다. 이 객체는 요청 본문의 바이트를 Dart 객체로 디코딩합니다. 디코딩의 동작은 요청의 content-type 헤더에 의해 결정됩니다 (이 가이드의 뒷부분에 나오는 `CodecRegistry`섹션 참조). 본문을 디코딩 할 때 예상되는 Dart 객체 유형을 지정할 수 있습니다. 디코딩 된 본문 개체가 예상되는 형식이 아닌 경우 400 개의 잘못된 요청 오류를 보내는 예외가 throw됩니다.

```dart
// Ensures that the decoded body is a Map<String, dynamic>
final map = await request.body.decode<Map<String, dynamic>>();

// Takes whatever object the body is decoded into
final anyObject = await request.body.decode();
```

요청의 본문이 디코드되면 동기식 `as` 메소드를 통해 액세스 할 수 있습니다. 이 메서드는 또한 디코드 된 본문 개체의 형식을 적용하기 위해 형식 인수를 사용합니다.

```dart
final map = request.body.as<Map<String, dynamic>>();
```

Inferred Types

유형을 추론 할 수 있는 경우 `as`또는 `decode`에 유형 인수를 제공 할 필요가 없습니다. 예를 들어, `object.read (await request.body.decode())`는 타입 매개 변수를 제공 할 필요 없이 디코딩 된 바디의 타입을 `Map<String, dynamic>`으로 유추합니다.

내용 유형에 따라 본문을 디코딩 할 수없는 경우 (데이터 형식이 잘못됨) 적절한 오류 응답을 클라이언트에 보내는 오류가 발생합니다.

request body 동작에 대한 더 자세한 정보는 RequestBody에 대한 API 레퍼런스, [section on body binding for ResourceControllers](https://aqueduct.io/docs/http/resource_controller/)과 이 가이드 뒷부분의`Serializable `.

request body 동작에 대한 자세한 내용은 `ResourceController`에 대한 본문 바인딩 섹션 및 `Serializable`에 대한 이 가이드의 뒷부분에 있는 `RequestBody`에 대한 API 참조를 참조하십시오.

Max Body Size

요청 본문의 크기는 기본적으로 10MB로 제한되며 응용 프로그램 초기화 중 `RequestBody.maxSize` 값을 설정하여 변경할 수 있습니다.

### Encoding Response Body Objects

HTTP 응답은 종종 *body*를 포함합니다. 예를 들어, `GET /users/1`에 응답하는 본문은 사용자를 나타내는 JSON 객체 일 수 있습니다. 본문이 JSON 객체임을 클라이언트가 이해하도록 하기 위해 헤더 `Content-Type: application/json; charset=utf-8`를 포함합니다.

본문이 있는 `Response`를 만들 때 *body object*와 `contentType`을 제공합니다. 예 :

```dart
var map = {"key": "value"};

// ContentType.json is the default, setting it may be omitted.
// ContentType.json == `application/json; charset=utf-8'
final response = Response.ok(map)
  ..contentType = ContentType.json;
```

Body 객체는 content-type에 따라 인코딩 됩니다. 위의 경우, `map`은 먼저 JSON 문자열로 인코딩 된 다음 UTF8 바이트의 목록으로 인코딩 됩니다.

![Map Encoding](https://aqueduct.io/docs/img/object_body_flow.png)

`ContentType`은 세 가지 구성 요소로 구성됩니다 : 기본 유형, 부속 유형 및 선택적 문자 세트.

![Content Type Components](https://aqueduct.io/docs/img/content_type_components.png)

기본 및 하위 유형에 따라 첫 번째 변환 단계가 결정되고 charset이 다음 단계를 결정합니다. 각 단계는 `dart:convert`의 `Codec` 인스턴스에 의해 수행됩니다. 예를 들어, 콘텐츠 유형 `application/json`은 `JsonCodec`을 선택하고, charse t`utf-8`은 `Utf8Codec`을 선택합니다. 이 두 코덱은 `Map`을 바이트 목록으로 변환하기 위해 연속적으로 실행됩니다. 코덱은 응용 프로그램의 `CodecRegistry`에 의해 선택됩니다. 이에 대해서는 나중에 설명합니다.

선택한 코덱에 대해 본문 개체가 유효해야 합니다. 위의 예제에서 `Map<String, dynamic>`은 `JsonCodec`에 의해 인코딩 될 수 있습니다. 그러나 본문 개체를 인코딩 할 수 없는 경우 500 Server Error 응답이 전송됩니다. 하나의 '코덱'에 대한 유효한 입력은 다른 '코덱'에 대해 유효하지 않을 수 있습니다. body 객체가 응답의 `contentType`에 대해 유효하다는 것을 확인하는 것은 당신에게 달려 있습니다.

모든 콘텐츠 유형이 두 가지 변환 단계를 필요로하는 것은 아닙니다. 예를 들어, HTML 파일을 제공 할 때 body 객체는 이미 HTML `String`입니다. 그것은 charset 인코더에 의해서만 변환 될 것입니다 :

```dart
var html = "<html></html>";
var response = Response.ok(html)
  ..contentType = ContentType.html;
```

이미지 바디 객체는 이미 바이트의 목록이므로 전혀 변환 할 필요가 없습니다. content-type에 대해 등록 된 코덱이 없으면 본문 객체는 바이트 배열이어야합니다 (각 값은 0-255 사이에있는 `List<int>`).

```dart
final imageFile = File("image.jpg");
final imageBytes = await imageFile.readAsBytes();
final response = Response.ok(imageBytes)
  ..contentType = ContentType("image", "jpeg");
```

body 객체가 바이트 배열 인 한 본문의 자동 인코딩을 비활성화 할 수 있습니다.

```dart
final jsonBytes = utf8.encode(json.encode({"key": "value"}));
final response = Response.ok(jsonBytes)..encodeBody = false;
```

코덱 매핑에 대한 내용 유형에 대한 자세한 내용은 이후 섹션을 참조하십시오. 또한 내장 코덱과 코덱 추가에 대한 자세한 내용은 `CodecRegistry` 설명서를 참조하십시오.

### Streaming Response Bodies

body 객체는 `Stream<T>`일 수도 있습니다. `Stream<T>` body 객체는 파일을 제공 할 때 가장 자주 사용됩니다. 이렇게 하면 전체 파일을 먼저 메모리에 로드 할 필요 없이 파일 내용을 디스크에서 HTTP 클라이언트로 스트리밍 할 수 있습니다. (`FileController`도 참조하십시오.)

```dart
final imageFile = File("image.jpg");
final imageByteStream = imageFile.openRead();
final response = new Response.ok(imageByteStream)
  ..contentType = new ContentType("image", "jpeg");
```

바디 객체가`Stream<T> `일 때, 응답은 스트림이 닫힐 때까지 보내지지 않을 것입니다. 열린 파일에서와 마찬가지로 유한 스트림의 경우 전체 파일을 읽는 즉시 발생합니다. 자신을 구성하는 스트림의 경우 응답이 반환 된 후 언젠가 스트림을 닫아야 합니다.

## Codecs and Content Types

위 섹션에서는 응답 본문을 준비 할 때 코덱을 선택하는 방법을 살펴 보았습니다. UTF8 인코딩 된 JSON으로 보내지는 `ManagedObject<T>`바디 객체의 일반적인 경우는 '잘 작동'하며 대부분의 애플리케이션에 적합합니다. 웹 애플리케이션의 애셋 또는 XML과 같은 다른 데이터 형식을 제공 할 때 Aqueduct의 코덱 레지스트리 작동 방식을 이해하는 것이 중요합니다.

`CodecRegistry`는 내용 유형에서 `Codec`까지의 매핑을 포함합니다. 이 코덱은 응답 본문을 인코딩하고 요청 본문을 디코딩합니다. `application/json`, `application/x-www-form-urlencoded`, `text/*`를 위한 3 가지 내장 코덱이 있습니다. 응답이 보내질 때, 저장소는 `Response.contentType`의 프라이머리와 서브 타입과 정확히 일치하는 엔트리를 검색합니다. 엔트리가 존재하면 관련된 `코덱`이 변환을 시작합니다. 예를 들어, 내용 유형이 `application/json; charset=utf-8`, 내장 된 `application/json` 코덱은 body 객체를 인코딩합니다.

정확히 일치하는 항목이 없지만 와일드 카드 (`*`) 하위 유형이있는 기본 유형에 대한 항목이 있는 경우 해당 코덱이 사용됩니다. 예를 들어 `text/*`에 대한 내장 코덱은 `text/plain`과 `text/html` 둘 다에 대해 선택 될 것입니다. `text/html`을 위해해야 만하는 특별한 것이 있다면, 그 유형에 대해보다 구체적인 코덱을 추가 할 수 있습니다 :

```dart
class MyChannel extends ApplicationChannel {
  @override
  Future prepare() async {
    CodecRegistry.defaultInstance.add(ContentType("application", "html"), HTMLCodec());
  }
}
```

코덱은 `ApplicationChannel.prepare` 메소드에 추가 되어야 합니다. 코덱은 `dart:convert`에서 `Codec`을 구현해야 합니다. 위의 예에서 응답의 컨텐트 유형이 `text/html` 일 때, `HTMLCodec`은 body 객체를 인 코드 할 것입니다. 이 코덱은 보다 구체적이기 때문에 'text/ *'보다 우선합니다.

응답 본문에 대한 코덱을 선택하면 `ContentType.charset`이 선택된 코덱에 영향을 주지 않습니다. 응답의 content-type에 charset이 있으면 'UTF8'과 같은 charset 인코더가 마지막 인코딩 단계로 적용됩니다. 예를 들어, content-type `application/json; charset=utf-8`은 본문 개체를 JSON 문자열로 인코딩 한 다음 UTF8 바이트 목록으로 인코딩합니다. 응답 바디의 최종 인코딩 형식은 바이트 목록이므로 문자열을 생성하는 코덱에는 charset이 있어야 합니다.

저장소에 `Response`의 콘텐츠 유형에 대한 코덱이 없으면 본문 개체는 `List<int>`또는 `Stream<List<int>>`이어야 합니다. 본문 개체로 설정하기 전에 데이터를 변환하는 경우 자신의 코덱을 `CodecRegistry`에 추가하는 것이 좋습니다.

요청 본문은 항상 바이트 목록으로 시작하고 Dart 객체로 디코딩 됩니다. JSON 요청 본문을 디코딩하려면 먼저 UTF8 바이트 목록에서 문자열로 디코딩되어야 합니다. 클라이언트가 content-type 헤더에서 charset을 생략 할 수 있습니다. `CodecRegistry`에 추가 된 코덱은 charset-less 내용 유형을 해석하는 기본 문자 세트를 지정할 수 있습니다. 코덱이 리포지터리 (repository)에 추가 되었을 때, 컨텐츠 타입의 캐릭터 세트가 null가 아닌 경우는 디폴트가 됩니다. 예를 들어 JSON 코덱은 다음과 같이 추가됩니다.

```dart
CodecRegistry.defaultInstance.add(
  ContentType("application", "json", charset: "utf-8"),
  const JsonCodec(),
  allowCompression: true);
```

코덱을 등록 할 때 charset이 지정되지 않은 경우 요청 본문에 문자 집합 디코딩이 존재하지 않으면 발생하지 않습니다. 리포지토리가 항상 본문을 먼저 문자열로 디코딩하려고 시도하기 때문에 `String`에서 디코딩 된 내용 유형은 기본 charset을 사용하지 않아야 합니다.

### Compression with gzip

body 객체는 HTTP 클라이언트가 허용하는 경우 `gzip`으로 압축 될 수 있으며 응답의 내용 유형을 압축하도록 `CodecRegistry`가 구성되었습니다. `application/json`, `application/x-www-form-urlencoded` 및 `text/*`와 같은 세 가지 내장 코덱은 모두 압축을 허용하도록 구성됩니다. 압축은 변환의 마지막 단계로서 HTTP 클라이언트가`Accept-Encoding: gzip` 헤더를 보내는 경우에만 발생합니다.

코덱 저장소에 없는 콘텐츠 유형은 HTTP 클라이언트가 `Accept-Encoding`헤더로 압축을 허용하더라도 압축을 트리거하지 않습니다. 이는 이미지와 같은 바이너리 콘텐츠가 콘텐츠 특정 알고리즘에 의해 이미 압축되었을 가능성이 있으므로 '압축'되지 않도록 방지하기위한 것입니다. Aqueduct가 내장 유형 이외의 컨텐츠 유형을 압축하려면 `allowCompression` 플래그를 사용하여 저장소에 코덱을 추가하십시오. (기본값은 `true`입니다.)

```dart
CodecRegistry.add(
  ContentType("application", "x-special"),
   MyCodec(),
  allowCompression: true);
```

또한 변환 단계를 수행하지 않아도 코덱을 지정하지 않고도 컨텐트 유형이 압축을 사용하는지 여부를 설정할 수 있습니다.

```
CodecRegistry.setAllowsCompression(new ContentType("application", "x-special"), true);
```

## Serializable Objects

대부분의 요청 및 응답 본문은 JSON 객체 및 객체 목록입니다. Dart에서 JSON 객체는 maps입니다. `Serializable` 객체는 맵에서 읽어 들여 다시 맵로 변환 할 수 있습니다. `Serializable`을 서브 클래스 화하여 맵으로부터 키를 당신의 서브 클래스의 프로퍼티에 할당하고 프로퍼티를 맵에 다시 쓰게합니다. 이렇게 하면 응용 프로그램에서 선언 한 정적 유형이 예상 요청 및 응답 본문을 나타낼 수 있습니다. Aqueduct의 ORM 타입 인 `ManagedObject`는 `Serializable`입니다.

### Sending Serializable Objects as Response Bodies

응답의 본문 객체는 `Serializable`일 수 있습니다. 응답이 전송되기 전에 body 객체가 JSON (또는 다른 전송 형식)으로 인코딩되기 전에 `asMap()`이 호출됩니다.

예를 들어, 200 OK 응답으로 반환 된 하나의 직렬화 가능 객체 :

```dart
final query = Query<Person>(context)..where((p) => p.id).equalTo(1);
final person = await query.fetchOne();
final response = Response.ok(person);
```

응답 본문 개체는 `Serializable` 개체의 목록 일 수도 있습니다.

```dart
final query = Query<Person>(context);
final people = await query.fetch();
final response = Response.ok(people);
```

body 객체의 흐름이 다음 다이어그램에 표시됩니다. 각 주황색 항목은 허용 된 본문 개체 유형이며 HTTP 응답 본문으로 인코딩 될 때 진행되는 단계를 보여줍니다. 예를 들어, `Serializable`은 세 단계를 거치며 `List <int>`는 0 단계를 거치며 HTTP 응답에있는 그대로 추가됩니다.

![Response Body Object Flow](https://aqueduct.io/docs/img/response_flow.png)

### Reading Serializable Objects from Request Bodies

직렬화 가능 객체는 요청 본문에서 읽을 수 있습니다.

```dart
final person = Person()..read(await request.body.decode());
```

직렬화 가능 오브젝트의 리스트

```dart
List<Map<String, dynamic>> objects = await request.body.decode();
final people = objects.map((o) => Person()..read(o)).toList();
```

직렬화 가능 및 직렬화 가능리스트는 모두 [bound to a operation method parameter in a ResourceController](https://aqueduct.io/docs/http/resource_controller/) 일 수 있습니다.

```dart
@Operation.post()
Future<Response> addPerson(@Bind.body() Person person) async {
  final insertedPerson = await context.insertObject(person);
  return Response.ok(insertedPerson);
}
```

#### Key Filtering

`read`와 `Bind.body` (`Serializable` 바인딩시)는 키 필터링을 지원합니다. 키 필터는 본문의 키를 삭제하거나 본문에 키가 필요하거나 본문에 키가 있는 경우 오류를 throw하는 키 목록입니다. 예:

```dart
final person = Person()
  ..read(await request.body.decode(),
         ignore: ["id"],
         reject: ["password"],
         require: ["name", "height", "weight"]);
```

위의 경우: 본문에 'id'가 포함되어 있으면 값이 즉시 삭제됩니다. 본문에 'password'가 포함되어 있으면 400 상태 코드 예외가 throw됩니다. 본문에 이름, 높이 및 가중치가 모두 포함되어 있지 않으면 400 개의 상태 코드 예외가 발생합니다.

직렬화 가능리스트를 바인딩 할 때 필터는리스트의 각 요소에 적용됩니다.

```dart
@Operation.post()
Future<Response> addPerson(@Bind.body(reject: ["privateInfo"]) List<Person> people) async {
  // if any Person in the body contains the privateInfo key, a 400 Bad Request is sent and this method
  // is not called
}
```

### Subclassing Serializable

`Serializable` 객체는 `readFromMap()`과 `asMap()`을 구현해야 합니다.

`Serializable`을 확장 한 객체는 응답 본문 객체로 직접 사용될 수 있습니다 :

```dart
class Person extends Serializable {
  String name;
  String email;

  Map<String, dynamic> asMap() {
    return {
      "name": name,
      "email": email
    };
  }

  void readFromMap(Map<String, dynamic> inputMap) {
    name = inputMap['name'];
    email = inputMap['email'];
  }
}

final person = Person();
final response = Response.ok(person);
```

`readFromMap`은 모든 필터가 적용된 후에 `read`에 의해 호출 됩니다.

### Serializable and OpenAPI Generation

`Serializable` 타입이 OpenAPI 문서 생성 [here](https://aqueduct.io/docs/openapi/components/)과 어떻게 작동하는 지에 대한 섹션을 보십시오.

### Example: multipart/form-data

종속물로 `pubspec.yaml`에 `package:mime`을 추가하십시오.

```dart
dependencies:
  mime: any # prefer a better constraint than this
```

그리고 나서 그 패키지의 객체들로 바디를 디코딩 하십시오 :

```dart
import 'package:aqueduct/aqueduct.dart';
import 'package:mime/mime.dart';

class MyController extends ResourceController {
  MyController() {
    acceptedContentTypes = [ContentType("multipart", "form-data")];
  }

  @Operation.post()
  Future<Response> postForm() async {}
    final transformer = MimeMultipartTransformer(request.raw.headers.contentType.parameters["boundary"]);
    final bodyStream = Stream.fromIterable([await request.body.decode<List<int>>()]);
    final parts = await transformer.bind(bodyStream).toList();

    for (var part in parts) {
      final headers = part.headers;
      final content = await part.toList();
      ...
    }    
  }
}
```

