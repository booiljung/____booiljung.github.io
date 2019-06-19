원문: https://aqueduct.io/docs/http/resource_controller/

# ResourceController

A `ResourceController` is a [controller](https://aqueduct.io/docs/http/controller/) that provide conveniences for implementing endpoint controllers. A `ResourceController` must be subclassed, and in that subclass, you write a method for each operation on that type of resource. For example, a `UserController` might handle the following operations:

- creating a new user (`POST /users`)
- getting all users (`GET /users`)
- getting an individual user (`GET /users/:id`)
- updating an individual user (`PUT /users/:id`)
- deleting an individual user (`DELETE /users/:id`)

These methods that are invoked for an operation are called *operation methods*.

## Operation Methods

An operation method is an instance method of a `ResourceController` subclass that has an `@Operation` annotation. It must return an instance of `Future<Response>`. Here's an example:

```dart
class CityController extends ResourceController {
  @Operation.get()
  Future<Response> getAllCities() async {
    return new Response.ok(["Atlanta", "Madison", "Mountain View"]);
  }
}
```

위 동작 메쏘드는`CityController`가 경로 변수없이 `GET` 요청을 처리 할 때 호출됩니다. 경로 변수로 연산 메소드를 처리하기 위해 경로 변수의 이름이 `@Operation` 주석에 추가됩니다 :

```dart
class CityController extends ResourceController {
  @Operation.get()
  Future<Response> getAllCities() async {
    return new Response.ok(["Atlanta", "Madison", "Mountain View"]);
  }

  @Operation.get('name')
  Future<Response> getCityByName() async {
    final id = request.path.variables['name'];
    return new Response.ok(fetchCityWithName(name));
  }
}
```

Path Variables

이 컨트롤러는 라우트 스펙 `/cities/[:name]`에 연결되어 이 두 작업을 모두 처리 할 수 있습니다. [Routing](https://aqueduct.io/docs/http/routing/)에서 경로 변수에 대해 자세히 읽어보십시오.

Operation의 네임드 생성자는 어떤 HTTP 메소드가 오퍼레이션 메소드를 처리하는지 알려줍니다. 다음과 같은 명명 된 생성자가 있습니다.

- `Operation.post()`
- `Operation.get()`
- `Operation.put()`
- `Operation.delete()`

표준 `Operation()`생성자는 비표준 연산에 대한 첫 번째 인수로 HTTP 메소드를 사용합니다. 예 :

```dart
@Operation('PATCH', 'id')
Future<Response> patchObjectWithID() async => ...;
```

모든 `Operation` 생성자는 경로 변수의 변수 목록을 취합니다. 오퍼레이션에 대한 다중 경로 변수가 있을 수 있습니다. 오퍼레이션 메소드는 모든 경로 변수가 요청 경로에 있는 경우에만 호출됩니다. 주어진 HTTP 메소드에 대해 여러 가지 오퍼레이션 메소드가 있을 수 있습니다. 각 메소드는 다른 경로 변수 세트를 예상합니다.

다음은 두 개의 경로 변수가 필요한 작업의 예입니다.

```dart
@Operation.get('userID', 'itemID')
Future<Response> getUserItem() async {
  final userID = request.path.variables['userID'];
  final itemID = request.path.variables['itemID'];
  return new Response.ok(...);
}
```

요청에 대한 오퍼레이션 메소드가없는 경우 405 메소드가 허용되지 않음 응답이 자동으로 송신되고 오퍼레이션 메소드가 호출되지 않습니다.

## Routing to a ResourceController

`ResourceController` 서브 클래스는 응용 프로그램 채널에서 `Router`가 선행 되어야합 니다. `Router`는 경로 변수를 파싱하여 컨트롤러가 어떤 동작 방법을 호출해야 하는지 결정할 수 있도록 경로 변수를 파싱합니다. `ResourceController`에 대한 일반적인 경로는 선택적 식별 경로 변수를 포함합니다 :

```dart
router
  .route("/cities/[:name]")
  .link(() => new CityController());
```

이 라우트는 `CityController`가 경로 변수와 'name'경로 변수가 없는 모든 HTTP 메소드에 대한 오퍼레이션 메소드를 구현할 수 있게 합니다.

하위 리소스를 자체 컨트롤러로 분해하는 것이 좋습니다. 예를 들어, 다음이 선호됩니다.

```dart
router
  .route("/cities/[:name]")
  .link(() => new CityController());

router
  .route("/cities/:name/attractions/[:id]")
  .link(() => new CityAttractionController());
```

대조적으로`/cities/[:name/[attractions/[:id]]]` 경로는 유효하지만 컨트롤러 로직을 훨씬 다루기 힘들게 만듭니다.

## Request Bindings

오퍼레이션 메소드는 HTTP 요청의 특성을 해당 매개 변수에 바인드 할 수 있습니다. 오퍼레이션 메소드가 호출 될 때, 해당 특성의 값은 오퍼레이션 메소드의 인수로 전달됩니다. 예를 들어, 다음은 'X-API-Key'라는 헤더를 `apiKey` 인수에 바인드 합니다.

```dart
@Operation.get('name')
Future<Response> getCityByName(@Bind.header('x-api-key') String apiKey) async {
  if (!isValid(apiKey)) {
    return new Response.unauthorized();
  }
  return new Response.ok(...);
}
```

다음 표에서는 가능한 바인딩 유형을 보여줍니다.

| Property            | Binding                           |
| ------------------- | --------------------------------- |
| Path Variable       | `@Bind.path(pathVariableName)`    |
| URL Query Parameter | `@Bind.query(queryParameterName)` |
| Header              | `@Bind.header(headerName)`        |
| Request Body        | `@Bind.body()`                    |

원하는 수의 HTTP 요청 속성을 단일 작업 메서드에 바인딩 할 수 있습니다.

### Optional Bindings

바인딩은 선택적으로 만들 수 있습니다. 바인딩이 옵션 인 경우 바인딩 된 속성이 요청에 없는 경우에도 작업 메서드가 여전히 호출됩니다. 바인딩을 선택적으로 만들려면 작업 메서드의 선택적 매개 변수로 이동합니다.

```dart
@Operation.get()
Future<Response> getAllCities({@Bind.header('x-api-key') String apiKey}) async {
  if (apiKey == null) {
    // No X-API-Key in request
    ...
  }
  ...
}
```

바인드 된 파라미터는, 요청에 없는 경우는 null가 됩니다. 다른 Dart 선택적 매개 변수와 마찬가지로 기본값을 제공 할 수 있습니다.

```dart
@Operation.get()
Future<Response> getAllCities({@Bind.header("x-api-key") String apiKey: "public"}) async {
  ...
}
```

### Automatically Parsing Bindings

쿼리, 헤더 및 경로 바인딩은 `int` 나 `DateTime`과 같은 다른 타입으로 자동적으로 파싱 될 수 있습니다. 바운드 매개 변수의 유형을 원하는 유형으로 선언하기 만하면 됩니다.

```dart
Future<Response> getCityByID(@Bind.query('id') int cityID)
```

바운드 매개 변수의 타입은 `String`이거나`parse`(예를 들어`int`, `DateTime`)를 구현하는 타입 일 수 있습니다. 쿼리 매개 변수는 `bool` 매개 변수에 바인딩 될 수도 있습니다. 부울 쿼리 매개 변수는 쿼리 매개 변수에 값이 없는 경우 true입니다 (예: `/ path?boolean`).

어떤 이유로 파싱에 실패하면 오류 응답이 전송되고 오퍼레이션 메소드가 호출되지 않습니다. 예를 들어, 위 예제는 `int cityID`를 바인딩 합니다. - 경로 변수 `id`가 `int`로 파싱 될 수 없다면 `404 Not Found` 응답이 보내집니다. 쿼리 매개 변수 또는 헤더 값을 파싱 할 수없는 경우 `400 Bad Request` 응답이 전송됩니다.

또한 `List<T>` 매개 변수를 헤더와 쿼리 매개 변수에 바인딩 할 수 있습니다. 여기서 `T`는 위와 동일한 기준을 충족해야 합니다. 쿼리 매개 변수와 헤더는 요청에서 두 번 이상 나타날 수 있습니다. 예를 들어, 요청 URL이 `/path?id=1&id=2`로 끝나고 오퍼레이션 메소드가 다음과 같으면 `ids`의 값은 `[1, 2]`입니다:

```dart
Future<Response> getCitiesByIDs(@Bind.query('id') List<int> ids)
```

Note that if a parameter is *not* bound to a list and there  are multiple occurrences of that property in the request, a 400 Bad  Request response will be sent. If you want to allow multiple values, you  must bind to a `List<T>`.

매개 변수가 목록에 바인딩되지 않고 요청에 해당 속성이 여러 번 나오는 경우 `400 Bad Request` 응답이 전송됩니다. 여러 값을 허용하려면 `List <T>`에 바인드 해야 합니다.

### Header Bindings

다음 연산 메쏘드는 `X-API-Key`라는 이름의 헤더를 `apiKey` 매개 변수에 바인드합니다:

```dart
class CityController extends ResourceController {
  @Operation.get()
  Future<Response> getAllCities(@Bind.header('x-api-key') String apiKey) async {
    if (!isValid(apiKey)) {
      return new Response.unauthorized();
    }
    return new Response.ok(['Atlanta', 'Madison', 'Mountain View']);
  }
}
```

`X-API-Key` 헤더가 요청에 존재하면 그 값은 `apiKey`에서 사용 가능합니다. 그렇지 않으면 `getAllCities(apiKey)`가 호출되지 않고 `400 Bad Request` 응답이 보내집니다. `apiKey`가 옵션 인 경우, 메소드는 정상적으로 호출되고`apiKey`는 null 또는 기본값입니다.

헤더 이름은 HTTP 사양에 따라 대/소문자를 구분합니다. 따라서 헤더 이름은 `X-API-KEY`, `X-Api-Key` `x-api-key`등이 될 수 있으며 모든 경우에 `apiKey`가 바인딩 됩니다.

### Query Parameter Bindings

다음 작업 메서드는 `name`이라는 쿼리 매개 변수를 매개 변수 `cityName` 에 바인딩 합니다.

```dart
class CityController extends ResourceController {
  @Operation.get()
  Future<Response> getAllCities(@Bind.query('name') String cityName) async {
    return new Response.ok(cities.where((c) => c.name == cityName).toList());
  }
}
```

쿼리 매개 변수는 필수 또는 선택적 일 수 있습니다. 필요한 경우 쿼리 매개 변수가 요청 URL에없는 경우 `400 Bad Request` 응답이 전송되고 아무런 작업 메서드도 호출되지 않습니다. 선택 사항 인 경우 바운드 변수는 null 또는 기본값입니다.

쿼리 매개 변수는 대소문자를 구분합니다. 이 바인딩은 `name`쿼리 매개 변수와 일치하지만 `Name`또는 `NAME`은 일치하지 않습니다.

content-type이 `application/x-www-form-urlencoded`인 경우 쿼리 매개 변수가 요청 본문의 쿼리 문자열에 바인딩 될 수도 있습니다.

### Path Variable Bindings

다음 오퍼레이션 메소드는 경로 변수 `id`를 매개 변수 `cityID`에 바인드 합니다.

```dart
class CityController extends ResourceController {
  @Operation.get('id')
  Future<Response> getCityByID(@Bind.query('id') String cityID) async {
    return new Response.ok(cities.where((c) => c.id == cityID).toList());
  }
}
```

경로 변수는 [routes](https://aqueduct.io/docs/http/routing/)를 만들 때 사용할 수 있습니다. `Router`는 경로 변수를 포함하는 경로를 가져야 하고 그 경로 변수는 `Operation` 주석에 나열되어야 합니다. 경로 변수는 대소문자를 구분하며 선택 사항이 아닐 수도 있습니다.

`Operation`에 존재하지 않는 경로 변수를 바인드 하려고 하면 시작할 때 런타임 예외가 발생합니다. 호출 할 조작 메소드에 경로 변수를 바인드 할 필요가 없습니다.

### HTTP Request Body Bindings

The body of an HTTP request can also be bound to a parameter:

```dart
class CityController extends ResourceController {
  CityController(this.context);

  final ManagedContext context;

  @Operation.post()
  Future<Response> addCity(@Bind.body() City city) async {
    final insertedCity = await context.insertObject(city);

    return new Response.ok(insertedCity);
  }
}
```

`Bind.body()`는 하나의 요청 본문만 있기 때문에 식별 인수를 취하지 않습니다 (단, 무시, 키 요구 또는 거부에 대한 선택적 인수를 취하는데, 이는 `Serializable.read`의 동작과 일치합니다. 바운드 형식이 `Serializable` 또는 목록 일 때 작동합니다.

바운드 매개 변수 유형 (이 예제에서`City`)은 `Serializable`을 구현해야 합니다. Aqueduct는 content-type에서 요청 본문을 자동으로 디코딩하고, 바인딩 된 매개 변수 유형의 새 인스턴스를 만들고, `read` 메소드를 호출합니다. 위의 예에서 유효한 요청 본문은 다음 JSON입니다.

```json
{
  "id": 1,
  "name": "Atlanta"
}
```

HTTP Body Decoding

요청 본문은 비 직렬화되기 전에 해당 내용 유형에 따라 디코딩 됩니다. JSON 이외의 콘텐츠 유형을 디코딩 하는 것을 포함하여 요청 본문 디코딩에 대한 자세한 내용은 [이 가이드](https://aqueduct.io/docs/http/request_and_response/)를 참조하십시오.

파싱에 실패하거나 `read`가 예외를 던지면 400 Bad Request 응답이 보내지고 연산 메소드는 호출되지 않습니다.

`List<Serializable>`매개 변수를 요청 본문에 바인딩 할 수도 있습니다. 도시 목록이 포함 된 다음 JSON을 고려하십시오.

```dart
[
  {"id": 1, "name": "Atlanta"},
  {"id": 2, "name": "Madison"}
]
```

이 본문은 바운드 매개 변수를 원하는 형식의`List`로 선언하여 바인딩 할 수 있습니다.

```dart
Future<Response> addCity(@Bind.body() List<City> cities)
```

List vs Object

엔드 포인트는 단일 오브젝트 또는 오브젝트 목록을 가져야 하지만 둘다는 가져 가지 않아야 합니다. 요청 본문이 JSON 목록이고 바인딩 된 변수가 목록이 아닌 경우 `400 Bad Request` 응답이 전송됩니다 (반대의 경우도 마찬가지 임). 적절한 유형의 본문 바인딩을 선언하면 응용 프로그램에 대한 OpenAPI 사양을 자동 생성하는 데 필요한 값을 확인하고 도움을 줍니다.

요청의 `Content-Type`이 `x-www-form-urlencoded`인 경우 그 요청은 `Bind.body`이 아니라 `Bind.query`로 묶여 있어야 합니다.

Key Filters in Bind.body()

Filters can be applied to keys of the object being read. Filters can  ignore keys, require keys or throw an error if a key is found. See more [here](https://aqueduct.io/docs/http/request_and_response/).

### Property Binding

`ResourceController`s의 속성은 또한 `Bind.query`와 `Bind.header` 메타 데이터를 가질 수 있습니다. 이것은 요청의 값을`ResourceController` 인스턴스 자체에 바인드하여 모든 조작 메소드에서 액세스 할 수 있도록합니다.

```dart
class CityController extends ResourceController {
  @requiredBinding
  @Bind.header("x-timestamp")
  DateTime timestamp;

  @Bind.query("limit")
  int limit;

  @Operation.get()
  Future<Response> getCities() async {
      // can use both limit and timestamp
  }
}
```

위의 예제에서`timestamp`와 `limit`은 `getCities`가 호출되기 전에 바인딩 되어 있습니다. 기본적으로 바운드 프로퍼티는 선택 사항입니다. `requiredBinding` 주석을 추가하면 속성이 필수로 변경됩니다. 필요한 경우 필수 등록 정보가 없는 요청이 `400 Bad Request` 상태 코드로 실패하고 오퍼레이션 메소드가 호출되지 않습니다.

## Other ResourceController Behavior

바인딩 외에,`ResourceController`는 이해하는 데 중요한 다른 행동을 합니다.

### Request and Response Bodies

`ResourceController`는 받아 들일 수 있는 HTTP 요청 본문의 내용 유형을 제한 할 수 있습니다. 기본적으로`ResourceController`는 `POST/PUT` 메소드에 대해서만 `application/json` 요청 바디를 받습니다. 이것은 constructor의 `acceptedContentTypes` 속성을 설정하여 수정할 수 있습니다.

```dart
class UserController extends ResourceController {
  UserController() {
    acceptedContentTypes = [ContentType.JSON, ContentType.XML];
  }
}
```

허용 된 컨텐트 유형 이외의 컨텐트 유형에 대한 요청이 있을 경우 컨트롤러는 자동으로 `415 Unsupported Media TYpe` 응답으로 응답합니다.

HTTP 요청의 본문은 콘텐츠 형식이 승인되고 요청을 처리 할 작업 메서드가 있는 경우 디코딩됩니다. 요청에 대해 일치하는 작업 방법이 없으면 본문이 디코딩되지 않습니다. body는 당신의 오퍼레이션 메소드가 호출되기 전에`ResourceController`에 의해 디코드됩니다. 따라서 항상 동기 메서드 인 `RequestBody.as`를 사용하여 조작 메소드 내에서 본문에 액세스 할 수 있습니다.

```dart
@Operation.post()
Future<Response> createThing() async {
  // do this:
  Map<String, dynamic> bodyMap = request.body.as();

  // no need to do this:
  Map<String, dynamic> bodyMap = await request.body.decode();

  return ...;
}
```

`ResourceController`는 응답을 위한 기본 컨텐트 타입을 가질 수 있습니다. 기본적으로 이것은 `application/json`입니다. 이 기본값은 생성자에서 `responseContentType`을 변경하여 변경할 수 있습니다 :

```dart
class UserController extends ResourceController {
  UserController() {
    responseContentType = ContentType.XML;
  }
}
```

`responseContentType`은 * default * 응답 내용 유형입니다. 개별 `Response`는 자신의 `contentType`을 설정할 수 있으며, 이는 `responseContentType`보다 우선합니다. 예를 들어, 다음 컨트롤러는 기본적으로 JSON을 반환하지만 요청에서 특별히 XML을 요청하면 그것이 반환 됩니다.

```dart
class UserController extends ResourceController {
  UserController() {
    responseContentType = ContentType.JSON;
  }

  @Operation.get('id')
  Future<Response> getUserByID(@Bind.path('id') int id) async {
    var response = new Response.ok(...);

    if (request.headers.value(Bind.headers.ACCEPT).startsWith("application/xml")) {
      response.contentType = ContentType.XML;
    }

    return response;
  }
}
```

### More Specialized ResourceControllers

많은 `ResourceController` 서브 클래스는 [queries](https://aqueduct.io/docs/db/executing_queries/)를 실행할 것입니다. 상용구 코드를 줄이기 위한 유용한`ResourceController` 하위 클래스가 있습니다.

`QueryController<T>`는 들어오는 요청을 기반으로 `Query <T>`를 생성합니다. 요청에 본문이 있으면 이 `Query<T>`의 `values` 속성을 해당 본문에서 읽습니다. 요청에 경로 변수가 있으면 `Query<T>`는 표현식을 기본 키 값에 할당합니다. 예를 들어 PUT 요청에 응답하는 정상적인 `ResourceController`에서 다음과 같이 작성할 수 있습니다 :

```dart
@Operation.put('id')
Future<Response> updateUser(@Bind.path('id') int id, @Bind.body() User user) async {
  var query = new Query<User>(context)
    ..where((u) => u.id).equalTo(id)
    ..values = user;

  return new Response.ok(await query.updateOne());
}
```

`QueryController<T>`는 오퍼레이션 메소드가 호출되기 전에 이 질의를 생성하여 상속받은 `query` 속성에 저장합니다. `ManagedObject<T>` 서브 클래스는 `QueryController<T>`에 대한 타입 인자입니다.

```dart
class UserController extends QueryController<User> {
  UserController(ManagedContext context) : super(context);

  @Operation.put('id')
  Future<Response> updateUser(@Bind.path('id') int id) async {
    // query already exists and is identical to the snippet above
    var result = await query.updateOne();
    return new Response.ok(result);
  }
}
```

`ManagedObjectController<T>`는 훨씬 강력합니다; 당신은 그것을 하위 클래스화 할 필요조차 없습니다. CRUD 엔드 포인트가 코드없이 수행하는 모든 작업을 수행합니다. 다음은 예제 사용법입니다.

```dart
router
  .route("/users/[:id]")
  .link(() => new ManagedObjectController<User>(context));
```

이 컨트롤러의 동작은 다음과 같습니다.

| Request           | Action                                                       |
| ----------------- | ------------------------------------------------------------ |
| POST /users       | Inserts a user into the database with values from the request body |
| GET /users        | Fetches all users in the database                            |
| GET /users/:id    | Fetches a single user by id                                  |
| DELETE /users/:id | Deletes a single user by id                                  |
| PUT /users/:id    | Updated a single user by id, using values from the request body |

컬렉션 가져 오기에서 반환 된 개체 (예 :`GET /users`)는 쿼리 매개 변수로 수정할 수 있습니다. 예를 들어 다음 요청은 오름차순으로 이름별로 정렬 된 사용자를 반환합니다.

```
GET /users?sortBy=name,asc
```

결과는 질의 매개 변수`offset`, `count`, `pageBy`, `pageAfter`와 ``pagePrior`를 사용하여 페이징 될 수 있습니다 ([고급 쿼리의 페이징](https://aqueduct.io/docs/db/advanced_queries/) 참조) .

`ManagedObjectController<T>`도 하위 클래스화 할 수 있습니다. 서브 클래스를 사용하면 실행 전에 쿼리를 조정하거나 응답을 보내기 전에 결과를 조정할 수 있도록 콜백을 재정의 할 수 있습니다. 가져 오기, 업데이트, 삭제 등의 각 작업에는 이 작업을 수행하는 한 쌍의 메소드가 있습니다. 예를 들어, 다음 하위 클래스는 'PUT'을 통해 업데이트 하기 전에 쿼리와 결과를 변경합니다.

```dart
class UserController extends ManagedObjectController<User> {
  UserController(ManagedContext context) : super(context);

  Future<Query<User>> willUpdateObjectWithQuery(
      Query<User> query) async {
    query.values.lastUpdatedAt = new DateTime.now().toUtc();
    return query;
  }

  Future<Response> didUpdateObject(User object) async {
    object.removePropertyFromBackingMap("private");
    return new Response.ok(object);
  }
}
```

`ManagedObjectController<T>`와 결합 할 때 강력한 [검증](https://aqueduct.io/docs/db/validations/) 장을 보시오.