https://aqueduct.io/docs/db/serialization/

# ManagedObject Serialization and Deserialization

이 가이드에서는 `ManagedObject<T>`가 HTTP 요청 본문에서 읽히고 HTTP 응답 본문에 쓰여지는 방법을 배웁니다.

## Basic Conversion

`ManagedObject<T>`는 `Map<String, dynamic>`객체와의 변환이 가능합니다. 각 키는 객체의 속성 이름입니다. `ManagedObject`를 `Map`으로 디코딩하려면 `read` 메소드를 호출하십시오 :

```dart
final object = MyManagedObject();
object.read({
  "key": "value"
});

// object.key == "value"
```

입력 데이터가 유효하지 않은 경우 유효성 검사 예외 (상태 코드 : 400)가 발생합니다. 키에 해당 속성이 없거나 값의 유형이 예상되는 유형과 일치하지 않거나 관리 대상 개체의 일부 제약 조건을 위반했습니다.

필터는 읽을 객체의 키에 적용 할 수 있습니다. 필터는 키를 무시하거나 키가 필요하거나 키가 있는 경우 예외를 throw 할 수 있습니다. 다음은 `id`가 필수이지만 제공되지 않으므로 읽기가 예외를 throw하는 예제입니다.

```dart
object.read({
  "key": "value"
}, require: ["id"]);
```

ManagedObjects inherit Serializable

`read` 메쏘드와 그 필터는 `Serializable`에서 상속받으며 [여기](https://aqueduct.io/docs/http/request_and_response/)에서 좀 더 자세하게 논의됩니다. 직렬 객체와 같은 관리 객체는 조작 메소드 매개 변수에 바인딩 될 수 있습니다.

관리 대상 개체에는 기본 필터 집합으로 사용할 수있는 기본 키 목록이 있습니다.

```dart
object.read({}, require: object.entity.defaultProperties);
```

관리 객체를 Map으로 직렬화하려면 인스턴스 메소드 `asMap`을 사용하십시오.

```dart
final object = MyManagedObject();
Map<String, dynamic> map = object.asMap();
```

속성이 객체에 설정되어 있지 않으면 Map에 기록되지 않습니다.

`Map`에 해당하는 관리 대상 객체의 값은 항상 JSON으로 인코딩되거나 격리 객체를 통해 전송 될 수있는 프리미티브 값입니다. 다음은 직렬화 형식 테이블을 보여줍니다.

| Dart Type                          | Serialized Type                                         |
| ---------------------------------- | ------------------------------------------------------- |
| `int`                              | number (`int`)                                          |
| `double`                           | number (`double`)                                       |
| `String`                           | string (`String`)                                       |
| `DateTime`                         | ISO 8601 Timestamp (`String`)                           |
| `bool`                             | boolean (`bool`)                                        |
| `Document`                         | map or list (`Map<String, dynamic>` or `List<dynamic>`) |
| Any `enum`                         | string (`String`)                                       |
| Belongs-To or Has-One Relationship | map (`Map<String, dynamic>`)                            |
| Has-Many Relationship              | list of maps (`List<Map<String, dynamic>>`)             |

## Behavior of Null Values

관리 객체의 속성은 null이 될 수 있습니다. 그 이유는 값이 실제로 null이거나 값을 사용할 수 없기 때문입니다. 예를 들어, 관리 대상 객체의 새 인스턴스를 만들 때 해당 값이 사용 가능하지 않습니다 (객체가 비어 있음). 객체를 Map로 인코딩 할 때 사용 가능한 값만 포함되고 사용할 수 없는 속성의 키는 생략됩니다.

```dart
final myObject = MyManagedObject(); // empty object
myObject.asMap() == {}; // true

myObject.id = 1;
myObject.asMap() == {
  "id": 1
}; // true
```

관리 대상 객체의 `asMap` 값은 속성 값이 참으로 null 인 경우에만 null입니다.

```dart
myObject.id = null;
myObject.asMap() == {
  "id": null
}; // true
```

속성 값은 접근자를 통해, `read`를 사용할 때, 또는 질의에서 객체를 반환 할 때 사용할 수 있습니다.

## Behavior of Transient Properties

기본적으로 테이블 정의가 아닌 관리 객체 하위 클래스에서 선언 된 임시 속성은 객체의 `asMap()`에 포함되지 않습니다. `Serialize` 주석은 일시적인 속성이 이 맵에 포함 되도록 합니다.

```dart
class Employee extends ManagedObject<_Employee> implements _Employee {
  int a; // NOT included in asMap, NOT read in read

  @Serialize()
  int b; // included in asMap, read in read

  @Serialize(input: true, output: false)
  int c; // NOT included in asMap, read in read

  @Serialize(input: false, output: true)
  int d; // included in asMap, NOT read in read
}

class _Employee {
  @primaryKey
  int id;
}
```

별도의 getter와 setter가 속성 대신 존재할 수 있습니다. 이 주석으로 getters가 `asMap`에 추가되고 setter가 `read`에 대해 입력됩니다.

```dart
class User extends ManagedObject<_User> implements _User {
  @Serialize()
  set transientValue(String s) {
    ...
  }

  @Serialize()
  String get transientValue => ...;
}
```

일시적인 속성의 키는 값이 null 인 경우 `asMap()`에 존재하지 않습니다.

## Behavior of Relationship Properties

관리 대상 객체가 인코딩 되면 관계 속성은 맵 (소유 관계 또는 관계가 있는 관계) 또는 맵 목록 (많은 관계가 있는 경우)으로 표시됩니다. 속성 가용성에 대한 동일한 규칙이 관계 속성에도 적용됩니다. 다음은 적절하게 명명 된 관계 속성을 사용하여 관리 대상 객체를 미러링하는 예제 맵을 보여줍니다.

```json
{
  "id": 1,
  "belongsTo": {
    "id": 1
  },
  "hasOne": {
    "id": 2,
    "name": "Fred"
  },
  "hasMany": [
    {"id": 3, "name": "Bob"},
    {"id": 4, "name": "Joe"},
  ]
}
```

속한 관계는 * 항상 * Map입니다. 이것은 종종 객체의 속한 관계를 생성하거나 업데이트하는 클라이언트 응용 프로그램에서 중요합니다. 예를 들어, `id == 1`을 가진 부모와 함께 Timmy라는 이름의 자식을 만들고자하는 클라이언트는 다음 JSON을 보낼 것입니다 :

```json
{
  "name": "Timmy",
  "parent": {
    "id": 1
  }
}
```

이는 다음과 같이 이 구조를 전개하는 일부 프레임 워크와 다릅니다.

```dart
{
  "name": "Timmy",
  "parent_id": 1
}
```

