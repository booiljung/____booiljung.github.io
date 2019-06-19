https://aqueduct.io/docs/tut/storing-data/

# 3. Storing Data in a Database

이전 실습에서 우리는 응용 프로그램에서 읽는 데이터베이스에 heroes을 로드 했습니다. 이제 응용 프로그램이 영웅을 데이터베이스에 저장, 삭제 및 수정할 수 있습니다. 여행의 이 부분을 시작하기 전에 HTTP API가 어떻게 작동하는지 이해하는 것이 중요합니다.

## HTTP Resources and Methods

[HTTP specification](https://tools.ietf.org/html/rfc7231)은  *resource*의 개념을 정의합니다. heros, 은행 계좌, 집 안의 전등 스위치, 남극 대륙의 온도 센서 등 어떤 것도 리소스가 될 수 있습니다. 이 중 일부는 물리적 인 물체 (조명 스위치)이고 일부는 디지털이며 모드 리소스 입니다. HTTP 서버 응용 프로그램은 이러한 자원에 대한 인터페이스입니다. 클라이언트는 자원을 사용하여 무언가를 수행 할 것을 요청하고 서버는 이를 완료하는 방법을 찾습니다.

리소스는 URI로 식별됩니다. URI는 리소스를 보편적으로 식별합니다: 연결할 서버의 주소와 해당 서버의 리소스를 식별하는 경로가 있습니다. Aqueduct 응용 프로그램을 작성할 때 우리는 URL의 서버 부분을 별로 신경 쓰지 않습니다. 인터넷은 그 부분을 파악합니다. 우리가 신경 쓰는 것은 `/heroes`처럼 URL 경로입니다.

응용 프로그램은 URL 경로를 사용하여 요청에서 작업 할 리소스를 결정합니다. 바로 지금, 우리의 응용 프로그램은 heroes 리소스와 함께 작동합니다. `/heroes/1` 경로를 사용하는 요청은 개별 heroes (숫자 '1'로 식별 됨)으로 무엇인가를 하기를 원합니다. `/heroes` 경로를 가진 요청은 영웅들의 전체 컬렉션에 작용할 것입니다.

이러한 작업은 주로 GET, POST 또는 DELETE와 같은 요청 방법으로 설명됩니다. 이러한 각 메소드는 자원에 적용 할 수 있는 조치를 설명하는 일반적인 의미를 가집니다. 예를 들어, `GET /heroes`는 "모든 영웅 리소스를 얻습니다"를 의미합니다. 이러한 각 메소드의 의미는 다음과 같습니다.

- GET: 일부 리소스 또는 개별 리소스의 컬렉션을 반환합니다.
- POST: 어떤 자원의 콜렉션에 자원을 삽입하거나 추가합니다. 자원의 표현은 요청 본문에 있습니다.
- PUT: 요청 본문의 내용으로 자원을 대체합니다 (경우에 따라 일부 자원의 전체 모음을 바꿉니다)
- DELETE: 자원을 삭제합니다 (경우에 따라 일부 자원의 전체 모음을 삭제함).

우리는 이 방법과 요청 경로를 결합하여 엄청난 동작을 만들 수 있습니다. 더 중요한 것은 이러한 사양을 따르면 클라이언트 응용 프로그램은 매우 적은 노력으로 일반 라이브러리를 사용하여 HTTP API에 액세스 할 수 있다는 것입니다. 이를 통해 브라우저, 휴대 전화 또는 기타 인터넷 연결 장치에서 쉽게 사용할 수있는 복잡한 시스템을 만들 수 있습니다.

## Inserting Data

새로운 영웅을 데이터베이스에 삽입 할 수 있는 행동을 추가하는 것으로 시작하겠습니다. 앞에서 논의한 바와 같이 HTTP 요청은 `POST /heroes`형식을 취해야 하며 영웅 컬렉션에 새로운 영웅을 추가합니다. 이 요청에는 본문에 영웅의 JSON 표현이 포함됩니다. 예 :

```json
{
  "name": "Master of Aqueducts"
}
```

우리의 `HeroesController`가 이 작업을 처리 할 것입니다. 일반적으로 단일 엔드 포인트컨트롤러는 자원 콜렉션과 개별 자원에 대한 모든 조작을 처리해야 합니다. `heroes_controller.dart`에 다음 조작 메소드를 추가하십시오 :

```dart
@Operation.post()
Future<Response> createHero() async {
  final Map<String, dynamic> body = await request.body.decode();
  final query = Query<Hero>(context)
    ..values.name = body['name'] as String;

  final insertedHero = await query.insert();

  return Response.ok(insertedHero);
}
```

여기서 중요한 세 가지가 있습니다: 이 메소드는 요청 본문에서 JSON 객체를 디코딩하고, JSON 객체에 이름을 가진 새로운 영웅을 삽입하는 쿼리를 구성한 다음 새로 삽입 된 영웅을 응답에 반환합니다.

디코딩 된 body가 할당 된 변수 또는 매개 변수의 유형과 일치하지 않으면 상태 코드 400 예외가 발생합니다. 즉, 이 방법으로 body를 디코딩하면 본문이 예상 된 형식인지 확인하고 그렇지 않은 경우 사용자를 대신하여 오류를 클라이언트에 반환합니다. 예를 들어 누군가가 JSON 객체의 목록을 게시하면 이 메소드에서 단일 JSON 객체를 예상하기 때문에 400 개의 잘못된 요청 응답을 받게 됩니다.

삽입 쿼리는 `values` 객체의 속성을 설정합니다. `values` 객체는 삽입 될 타입의 인스턴스입니다. 쿼리에 `insert`를 호출하면 값이 있는 행이 삽입됩니다. 데이터베이스에 의해 설정된 기본 키를 가진 새로운 영웅이 반환되어 응답 본문으로 반환됩니다. 위의 경우 생성 된 SQL은 다음과 같습니다.

```
INSERT INTO _Hero (name) VALUES ('Hero Name');
```

Column Attributes

영웅의 `id`는 `@primaryKey` 주석 때문에 자동 생성됩니다. 이 주석은 id를 기본 키와 '자동 증가'로 구성하는 `Column`입니다. 자동 증가 된 열 값은 자동으로 생성됩니다 (1, 2, 3 ...). 열 옵션에 대해서는 [Column API 참조](https://www.dartdocs.org/documentation/aqueduct/latest/aqueduct/Column-class.html)를 참조하십시오.

응용 프로그램을 다시 실행하십시오. 브라우저 응용 프로그램에서 페이지 상단의 `Heroes`을 클릭하십시오. 그런 다음 'Hero name:'입력란에 이름을 입력하고 `Add`를 클릭하십시오. 새로운 영웅이 나타납니다. 응용 프로그램을 다시 실행할 수 있으며 영웅은 컴퓨터의 데이터베이스에 저장되었으므로 계속 사용할 수 있습니다.

![Insert Hero](https://aqueduct.io/docs/img/run3.png)

요청 body에서 쿼리 단위로 값을 하나씩 할당하는 것은 번거로울 수 있습니다. 요청 본문을 자동으로 관리 객체에 자동으로 가져 와서 쿼리의 'values'에 할당 할 수도 있습니다.

```dart
@Operation.post()
Future<Response> createHero() async {
  final hero = Hero()
    ..read(await request.body.decode(), ignore: ["id"]);
  final query = Query<Hero>(context)..values = hero;

  final insertedHero = await query.insert();

  return Response.ok(insertedHero);
}
```

`read` 메소드는 `Map<String,dynamic>`을 managed object로 읽습니다. 각 키의 값은 동일한 이름의 특성에 지정됩니다. `ignore:` 선택적 매개 변수는 그것을 읽기 전에 맵에서 키의 값을 제거합니다. 이 방법으로 키를 거부하거나 요구할 수도 있습니다. 요청 본문에 managed object 의 속성으로 선언되지 않은 키가 포함되어 있으면 400 상태 코드 예외가 발생합니다.

Sub-resources

단일 컨트롤러가 리소스 컬렉션과 개별 리소스에 대한 모든 작업을 처리해야 한다고 언급 했습니다. 일부 자원은 서브 자원을 가질 수 있을 정도로 복잡 합니다. 예를 들어, X-Men이나 Fantastic Four와 같은 영웅 조직에는 영웅이 포함 되어 있지만 조직 소유의 건물과 장비도 포함될 수 있습니다. 영웅, 건물 및 장비는 조직의 하위 리소스입니다. 각 하위 리소스는 모든 것을 단일 경로와 컨트롤러로 밀어 넣는 대신 자체 경로와 컨트롤러를 가져야 합니다. 예를 보려면 다음 코드 스니펫을 참조하십시오.

```dart
@override
Controller get entryPoint {
  return Router()
    ..route("/organizations/[:orgName]")
      .link(() => OrganizationController());
    ..route("/organizations/:orgName/heroes/[:heroID]")
      .link(() => OrgHeroesController());
    ..route("/organizations/:orgName/buildings/[:buildingID]")
      .link(() => OrgBuildingController());
}
```

## Request and Response Bodies

지금까지 우리는 요청 및 응답 body이 어떻게 처리되는지에 대해 대부분 글로 나타내었으며, 이제는 이 주제를 파고 들기에 좋은 시기입니다.

### Response Body Encoding

응답을 만들 때 상태 코드와 선택적으로 헤더와 본문을 지정합니다. 예를 들어, 다음은 비어있는 목록 본문과 함께 상태 코드 200 OK를 사용하여 응답을 생성합니다.

```
Response.ok([])
```

`Response.ok`의 첫번째 인수는 * body object *입니다. body object는 응답의 `contentType`에 따라 자동으로 인코딩됩니다. 기본적으로 응답의 콘텐츠 유형은 `application/json`입니다. 따라서 기본적으로 모든 응답 본문 개체는 응답 본문에 JSON 인코딩 됩니다.

Other Response Constructors

`Response`의 기본 생성자는 상태 코드, 헤더 맵 및 본문 객체를 취합니다: `Response(200,{},"body")`.  `Response.ok` 나 `Response.notFound`와 같이 `Response`을 위한 많은 생성자가 있습니다. 이러한 생성자는 상태 코드를 설정하고 해당 유형의 응답을 위한 매개 변수를 표시합니다. 예를 들어 200 OK 응답에는 본문이 있어야하므로 `Response.ok`에는 필수 본문 개체 인수가 있습니다. 가능한 생성자 및 응답 속성에 대해서는 [API 참조 (Response for API)](https://www.dartdocs.org/documentation/aqueduct/latest/aqueduct/Response-class.html)를 참조하십시오.

본문 개체가 인코딩 된 형식을 변경하려면 응답의 `contentType`을 설정합니다. 예를 들어,

```dart
Response.ok([])
  ..contentType = new ContentType("application", "xml");
```

지원되는 기본 컨텐츠 유형은 JSON, `application/x-www-form-urlencoded` 및 모든 `text / *`타입 입니다. 다른 콘텐츠 유형을 인코딩하려면 `Codec`을 `CodecRegistry`로 등록해야합니다. 본문 개체는 응답의 content-type에 의해 선택된 코덱이 인코딩 할 수있는 경우에만 유효합니다. 그렇지 않으면 오류가 발생하고 대신 500 Server Error 응답이 전송됩니다.

`Serializable`을 구현하는 타입은 또한 바디 객체 일 수 있습니다. 이 타입을 구현하는 객체는 `asMap()`메서드를 제공하여 해당 속성을 인코더로 전달되기 전에 `Map`으로 변환합니다. 이 `Map`은 응답의 콘텐츠 형식 코덱에 대해 인코딩 가능해야합니다. 또한 각 객체의 `asMap()`의 목록이 인코더로 전달되는 `Serializable`의 `List`를 제공 할 수도 있습니다.

`ManagedObject`는 `Serializable` 인터페이스를 구현하므로 모든 관리 객체 (및 관리 객체 목록)는 본문 객체가 될 수 있습니다.

### Request Body Decoding

`Request`는 `RequestBody` 타입의 `body` 속성을 가지고 있습니다. `RequestBody`는 요청 본문의 내용을 응용 프로그램에서 사용하는 Dart 객체로 디코딩합니다. 이 디코딩은 요청의 content-type과 관련된 `Codec `에 의해 수행 됩니다. 디코딩 된 객체는 데이터 형식에 따라 결정됩니다. 예를 들어 JSON 배열은 `List`로 디코딩하고, JSON 객체는 `Map`으로 디코딩합니다.

요청 본문을 디코딩 할 코드를 작성할 때 요청 본문이 예상 된 형식 인지도 확인합니다. 예를 들어,`HeroesController`는 다음과 같이`decode`를 호출합니다 :

```dart
Map<String, dynamic> body = await request.body.decode();
```

`decode` 메쏘드는 `Map <String, dynamic>`으로 추측되는 타입 인자를 가지고 있습니다. 디코드 된 본문이 `Map`이 아닌 경우 클라이언트에 적절한 오류 응답을 보내는 예외가 throw됩니다.

요청의 본문을 operation 메소드 매개 변수에 바인딩 할 수도 있습니다. 'HeroesController'에서 'Hero` 인스턴스를 요청 본문에 바인딩합시다. 해당 파일의 코드를 다음과 같이 업데이트하십시오.

```dart
@Operation.post()
Future<Response> createHero(@Bind.body(ignore: ["id"]) Hero inputHero) async {
  final query = Query<Hero>(context)
    ..values = inputHero;

  final insertedHero = await query.insert();

  return Response.ok(insertedHero);
}
```

요청 본문 개체의 값은 `Hero` 개체로 디코딩 됩니다. 요청 본문의 각 키는 `Hero`속성에 매핑 됩니다. 예를 들어, `name`키의 값은 `inputHero.name`에 저장됩니다. 어떤 이유로 든 요청 본문을 Hero 인스턴스로 디코딩하지 못하면 400 개의 잘못된 요청 응답이 전송되고 작업 메서드가 호출되지 않습니다.

Binding Serializables

본문은 모든 유형에 바인딩 될 수 있습니다. 디코딩 된 본문이 예상되는 유형과 일치하는 경우에만 요청이 성공합니다. `Serializable` 서브 클래스 (또는 `List<Serializable>`)는 본문에 바인딩 될 때 본문을 `Map<String, dynamic>`(또는 `List<Map<String, dynamic>>`). 모든 `ManagedObject`는 `Serializable`을 구현합니다. 따라서 여러분은 body 바인딩을 사용하여 관리 된 객체들 (그리고 그것들의리스트)을 바인딩 할 수 있습니다.

`영웅` 응용 프로그램을 다시 실행하십시오. <http://aqueduct-tutorial.stablekernel.io>에서 화면 상단의 `Heroes` 버튼을 클릭하십시오. 텍스트 필드에 새로운 영웅 이름을 입력하고 `추가`를 클릭하십시오. 새로운 영웅이 목록에 추가 된 것을 볼 수 있습니다! 응용 프로그램을 종료하고 다시 실행할 수 있으며 새로운 영웅을 가져올 수 있습니다.

![Aqueduct Tutorial Third Run](https://aqueduct.io/docs/img/run3.png)

Query Construction

`values` 및 `where`와 같은 속성은 분석기에서 유형 및 이름 확인 열을 기준으로 오류를 방지합니다. IDE가 속성 이름을 자동 완성하므로 코드 작성 속도가 빨라집니다. [specific behavior](https://aqueduct.io/docs/db/advanced_queries/)은 쿼리가 생성하는 SQL에서이 두 속성의 값을 포함해야 하는지 여부를 결정하는 데 사용됩니다.