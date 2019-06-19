https://aqueduct.io/docs/db/json_columns/

# JSON Document Storage

구조화 되지 않은 바이너리 JSON 데이터를 `ManagedObject<TO>`속성에 저장하는 방법을 익히십시오.

## JSON Columns in Relational Databases

PostgreSQL은 정수, 문자열, 부울 및 날짜와 같은 많은 열 데이터 유형을 지원합니다. 열은 JSON 데이터 일 수도 있습니다. 이를 통해 구조화되지 않은 데이터와 간단한 객체를 테이블 열에 저장할 수 있습니다. JSON 열의 데이터를 한꺼번에 가져 오거나 조각 단위로 가져올 수 있습니다. JSON 데이터의 요소를 사용하여 쿼리 결과를 필터링 할 수 있습니다.

## The Document Data Type

JSON 문서 열은 `ManagedObject<T>`의 테이블 정의에서 `Document` 속성을 선언하여 데이터베이스 테이블에 추가됩니다. PostgreSQL에서는, `Document` 열의 데이터 형은 `jsonb`입니다. 문서 열은 JSON 인코딩 가능 데이터 만 포함 할 수 있습니다. 이 데이터는 일반적으로 JSON 인코딩 가능 데이터 만 포함하는 `Map` 또는 `List`입니다. 다음의 `ManagedObject<T>`선언은`jsonb` 타입의`contents` 컬럼을 가질 것입니다.

```dart
class Event extends ManagedObject<_Event> implements _Event {}
class _Event {
  @primaryKey
  int id;

  @Column(indexed: true)
  DateTime timestamp;

  Document contents;
}
```

`Document` 객체는 JSON 인코딩 가능 데이터를 담는 `data` 속성을 가지고 있습니다. `Document` 인스턴스를 생성 할 때 생성자의 선택적 매개 변수에 값이 제공되지 않는 한이 속성의 기본값은 null입니다.

```dart
final doc = new Document();
assert(doc.data == null);

final doc = new Document({"key": "value"});
assert(doc.data is Map);

final doc = new Document([0]);
assert(doc.data is List);
```

문서의 데이터는 `data` 속성을 통해 또는 아래 첨자 연산자를 통해 액세스 할 수 있습니다. `Document`의 subscript 연산자는 `data` 속성으로 호출을 전달합니다.

```dart
final doc = new Document({"key": "value"});

assert(doc["key"] == doc.data["key"]);
```

첨자 연산자에 대한 인수는 문자열 (`data`가 맵인 경우) 또는 정수 (`data`가리스트 인 경우) 일 수 있습니다.

## Basic Operations on Document Properties

`Document` 열은 다른 유형의 열과 같으므로 삽입 또는 업데이트 중에 설정되고 가져 오는 동안 읽힐 수 있습니다.

### Inserting Rows with Document Properties

`Document` 프로퍼티는 `Query<T>`로 삽입 할 때 먼저 설정됩니다. 쿼리의 `values` 속성은 JSON 인코딩 가능 값으로 초기화 된 `Document` 객체로 설정됩니다.

```
final query = Query<Event>(context)
  ..values.timestamp = DateTime.now()
  ..values.contents = Document({
    "type": "push",
    "user": "bob",
    "tags": ["v1"]
  });
final event = await query.insert();  
```

위의 `Document`에 대한 인수는 JSON으로 인코딩되어 `contents`열에 대한 thdatabase에 저장됩니다. 객체를 JSON으로 인코딩 할 수없는 경우 예외가 발생합니다.

### Fetching Rows with Document Properties

`Query<T>`로 `Document` 속성을 가진 객체를 가져올 때, 문서의 `data` 속성을 통해 열 값에 접근합니다.

```dart
final query = Query<Event>(context)
  ..where((e) => e.id).equalTo(1);
final event1 = await query.fetchOne();
event1.contents.data == {
  "type": "push",
  "user": "bob",
  "tags": ["v1"]
};
```

`Document` 속성을 가져 오면 JSON 데이터가 적절한 유형으로 디코딩됩니다. 이것은 아마도`Map` 또는 `List` 일 가능성이 있지만, JSON- 인코딩 가능한 객체가 될 수 있습니다. `Document` 속성에 저장된 데이터는 구조화되어 있지 않기 때문에 `data`의 타입은 `dynamic`입니다. 일관된 데이터 구조를 한 열에 저장하는 것이 좋습니다. 즉 항상 `Map`을 저장하거나 항상 `List`를 저장합니다.

### Updating Rows with Document Properties

`Document` 속성으로 행을 업데이트하는 것은 행을 삽입하는 것과 동일합니다.

```dart
final query = Query<Event>(context)
  ..where((e) => e.id).equalTo(1)
  ..values.contents = Document({
    "type": "push",
    "user": "bob",
    "tags": ["v1", "new"]
  });
final event = await query.updateOne();  
```

이 방법으로 업데이트하면 열에 저장된 문서가 완전히 대체됩니다.

### Accessing Document Values

`Document.data`의 타입은 `dynamic`입니다 - 그것은 유효한 JSON 타입이 될 수 있으며 사용될 때 예상되는 타입으로 형변환 될 수 있습니다. 이 데이터는 중첩 될 수 있습니다 (예 :`Maps`의`List`). 객체 키나 리스트 인덱스에 접근 할 때 `Document`에 직접 첨자 연산자를 사용할 수 있습니다.

```dart
// Object Access by key
final doc = Document({"key": "value"});
final value = doc["key"] == "value";

// List Access by index
final doc = Document(["v1", "v2"]);
final value = doc[0] == "v1";
```

동일한 구문으로 중첩 된 요소에 액세스 할 수 있습니다.

```dart
final doc = Document([
  {"id": 1},
  {"id": 2}
]);

final obj1 = doc[0]["id"]; // == 1
final obj2 = doc[1]["id"]; // == 2
```

`Document`에 아래 첨자 연산자를 사용하면 `data` 속성에서 그것을 호출합니다. 그러므로 모든 첨자 값은 Dart `List`와 `Map` 타입에 유효해야 합니다.

## Fetching Sub-documents

`Document` 속성을 가져올 때 기본 동작은 데이터베이스 열에 저장된 JSON 문서 전체를 반환하는 것입니다. `Query.returningProperties`와 첨자 연산자를 사용하여 필요한 문서의 일부를 가져올 수 있습니다.

```dart
final query = Query<Event>(context)
  ..returningProperties((e) => [e.id, e.contents["tags"]]);
final eventsWithTags = query.fetch();
```

반환 된 `Document` 속성에 아래 첨자 연산자를 사용하면 해당 키의 값만 반환됩니다. 예를 들어 위 쿼리가 실행되고 저장된 열의 값이 다음과 같은 경우:

```json
{
  "type": "push",  
  "user": "bob",
  "tags": ["v1"]  
}
```

`Event.contents`의 값에는 키 "tags"에 대한 배열 만 포함됩니다 :

```json
["v1"]
```

동일한 첨자 연산자를 사용하여 JSON 열의 배열을 색인 할 수도 있으며, 첨자 연산자도 중첩 될 수 있습니다. 예를 들어 다음 쿼리는 "tags"배열을 가져온 다음 인덱스 0에서 문자열을 가져옵니다.

```dart
final query = Query<Event>(context)
  ..returningProperties((e) => [e.id, e.contents["tags"][0]]);
final eventsWithFirstTag = await query.fetchOne();
eventsWithFirstTag.contents.data == "v1";
```

키 또는 색인이 JSON 문서에 존재하지 않으면, 리턴 된 등록 정보의 값은 널입니다. 이런 이유로`Document.data`에 접근 할 때 널 인식 연산자를 사용해야 합니다 :

```dart
final query = Query<Event>(context)
  ..returningProperties((e) => [e.id, e.contents["tags"][7]]); // 7 is out of bounds
final eventsWithFirstTag = await query.fetchOne();
if (eventsWithFirstTag.contents?.data == "v1") {
  ...
}
```

JSON 배열에서 요소를 가져올 때 음수 인덱스를 사용하여 배열의 끝에서 인덱스를 지정할 수 있습니다.

```dart
final query = Query<Event>(context)
  ..returningProperties((e) => [e.id, e.contents["tags"][-1]]);
final eventsWithLastTag = await query.fetchOne();
```

쿼리 당 `Document` 열에서 하나의 하위 구조 만 가져올 수 있습니다. 즉, 다음을 할 수 없습니다.

```dart
// Invalid
final query = Query<Event>(context)
  ..returningProperties((e) => [e.id, e.contents["type"], e.contents["user"]]);
```

`Query<T>`가 지원하지 않는 연산의 경우 SQL을 직접 사용할 수 있습니다 :

```dart
final eventTagCounts = await context.persistentStore.execute("SELECT jsonb_array_leng
```

