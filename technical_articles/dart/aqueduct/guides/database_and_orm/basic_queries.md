https://aqueduct.io/docs/db/executing_queries/

# Inserting, Updating, Deleting and Fetching Objects

객체를 가져 오거나, 삽입하거나, 삭제하거나 업데이트 하는 등의 명령을 데이터베이스에 보내려면 `Query<T>`의 인스턴스를 생성, 구성 및 실행해야 합니다. type 인자는 `ManagedObject`의 서브 클래스 여야 합니다. 이 서브 클래스는 질의가 수행 될 테이블을 결정합니다.

쿼리는 SQL 쿼리를 컴파일하고 실행하며 연결할 데이터베이스를 결정하기 위해 [ManagedContext](https://aqueduct.io/docs/db/connecting.db)가 필요합니다. 다음은 'User'의 모든 인스턴스를 가져 오는 `Query<T>` 예제 입니다.

```dart
final query = Query<User>(context);
final allUsers = await query.fetch();
```

`Query<T>`는 `fetch`, `update`, `insert`, `delete`의 네 가지 기본 실행 메소드를 가지고 있습니다.

- `fetch`는 데이타베이스로부터 데이타를 검색 할 것입니다 (그것은 SQL의 `SELECT` 연산과 같습니다).
- `update`는 데이터베이스의 기존 데이터를 수정합니다 (이는 SQL의 `UPDATE` 연산과 같습니다).
- `insert`는 새로운 데이터를 데이터베이스에 추가합니다 (이는 SQL의 `INSERT` 연산과 같습니다).
- `delete`는 데이타베이스에서 데이타를 제거합니다 (이는 SQL의 `DELETE`과 동일합니다).

`Query<T>`는 많은 설정 가능한 속성들을 가지고 있습니다. 이러한 속성은 가져 오는 객체, 데이터베이스로 전송되는 데이터, 데이터가 반환되는 순서 등을 결정합니다.

다음 절에서는 이렇게 선언 된 `User` 관리 객체 하위 클래스가 있다고 가정합니다.

```dart
class User extends ManagedObject<_User> implements _User {}
class _User {
  @primaryKey
  int id;

  @Column(indexed: true)
  String email;

  String name;
}
```

### Inserting Data with a Query

질의를 사용하여 데이터를 삽입하려면 새로운 `Query<T>`객체를 만들고, 그것의 `values` 속성을 설정 한 다음 `insert()`메소드를 호출하십시오.

```dart
final query = Query<User>(context)
  ..values.name = "Bob"
  ..values.email = "bob@stablekernel.com";  

final user = await query.insert();  
```

`Query<T>`의 `values`는 `T` (당신이 삽입하는 관리 객체 타입)의 인스턴스입니다. `values`의 개별 속성을 설정하거나, 다른 곳에서 만든 인스턴스에 `values`를 할당 할 수 있습니다:

```dart
final userValues = User()
  ..name = "Bob"
  ..email = "bob@stablekernel.com";
final query = Query<User>(context)..values = userValues;
final user = await query.insert();  
```

어느 쪽이든 이 쿼리는 다음 SQL로 변환됩니다.

```sql
INSERT INTO _user (name, email) VALUES ('Bob', 'bob@stablekernel.com') RETURNING id, name, email;
```

`values` 객체에 설정된 값만 SQL INSERT 쿼리에 포함됩니다. 이 예제에서는 `name`과 `email` 모두 `id`가 아니라 `name`과 `email`만이 쿼리에 포함되었습니다. 이 경우 기본 키가 자동으로 증가하므로 데이터베이스에서 생성합니다.

명시 적으로 `null`로 설정된 값은 `NULL`로 전송됩니다. 예를 들어 다음과 같은 `Query<T>`와 그 SQL을 생각해 봅시다.

```dart
var query = Query<User>(context)
  ..values.name = null
  ..email = "bob@stablekernel.com";

// INSERT INTO _user (name, email) VALUES (NULL, 'bob@stablekernel.com') RETURNING id, name, email;
await query.insert();
```

삽입 쿼리는 삽입 된 행을 나타내는 관리 객체를 반환합니다.

Prefer the Inserted Object

개체를 삽입 한 후에 쿼리를 채우는 데 사용한 값보다는 삽입 쿼리에서 반환 한 개체를 사용하는 것이 좋습니다. 쿼리를 반환하는 개체는 데이터베이스 행을 정확하게 나타내지만 쿼리를 작성하는 데 사용된 개체는 불완전하거나 다를 수 있습니다. 예를 들어 자동 증가 기본 키는 쿼리 작성 인스턴스에서 사용할 수 없지만 성공적인 쿼리에서 반환 된 개체에 사용됩니다.

인스턴스를 `values`에 할당하거나 `values`의 속성을 구성 할 때 선택할 때 주의 할 점이 하나 있습니다. 작성한 인스턴스에서 관계 속성에 액세스하기 전에 인스턴스화해야 합니다. `values`의 관계 속성에 접근 할 때, 관련된 객체의 빈 인스턴스가 접근 즉시 생성됩니다.

```dart
final employee = Employee()
  ..manager.id = 1; // this is a null pointer exception because manager is null

final query = Query<Employee>(context)
  ..values.manager.id = 1; // this is OK because of special behavior of Query
```

일단 객체를 `values`에 할당하면 `values`의 행위를 받아들이고 접근 할 때 관계를 인스턴스화합니다. 객체를 `values`에 할당 한 후에는 원래 객체의 변경 사항이 `values`에 반영되지 않습니다. 즉, 객체가 참조 대신 복사됩니다.

간단한 삽입과 두 개 이상의 객체 삽입을 위해 컨텍스트에서 메소드를 사용할 수 있습니다.

```dart
final context = ManagedContext(...);
final bob = User()..name = "Bob";
final jay = User()..name = "Jay";

final insertedObject = await context.insertObject(bob);
final insertedObjects = await context.insertObjects([bob, jay]);
```

### Updating Data with a Query

`Query<T>`를 사용하여 행을 업데이트하는 것은 데이터를 삽입하는 것과 유사합니다. 변경할 속성에 대해 `Query.values`를 설정합니다. `Query<T>`의 type 매개 변수는 쿼리가 실행될 때 어떤 데이터베이스 테이블이 업데이트 될 것인지를 나타냅니다.

업데이트 쿼리는 단일 행이나 행의 하위 집합으로 제한 될 수 있습니다. 이것은 SQL 명령의  where 절로 변환되는 `Query.where` 속성을 구성함으로써 이루어집니다. 다음은 그 예입니다.

```dart
// A Query that will change any user's whose name is 'Bob' to 'Fred'
var query = Query<User>(context)
  ..values.name = "Fred"
  ..where((u) => u.name).equalTo("Bob");

List<User> bobsThatAreNowFreds = await query.update();
```

`values`와 마찬가지로 `where`는 질의가 수행되는 것과 동일한 관리 객체 유형입니다. 위의 예제에서, `values`와 `where` 둘은  `User`의 인스턴스 입니다. 이 쿼리는 다음 SQL을 실행합니다.

```dart
UPDATE _user SET name='Fred' WHERE name='Bob';
```

`where` 속성은 매우 강력하고 융통성이 있기 때문에 이 가이드의 뒷부분에서 전체 섹션을 다룹니다. 지금은 `update()`와 관련된 몇 가지 사항을 고수 할 것입니다.

`insert()`처럼, `update ()`를 실행할 때 쿼리의 `values` 속성에 설정된 값만 업데이트됩니다. 생략 된 값은 포함되지 않습니다. `null`로 설정해야 하는 값은 명시적으로 쿼리에서 `null`로 설정되어야 합니다.

```dart
// A Query that will remove names from anyone currently named Bob.
var query = Query<User>(context)
  ..values.name = null
  ..where((u) => u.name).equalTo("Bob");
```

갱신 조회는 수정 된 모든 행을 결과로 리턴합니다. 업데이트 된 행이 없으면 반환 값은 빈 목록입니다.

`query<T> update`에는 `updateOne`이라는 변종이 있습니다. `updateOne` 메쏘드는 일반적인 `update`가하는 것과 같은 방법으로 SQL 질의를 만들고 실행할 것입니다. 그러나 목록 대신 업데이트 된 단일 인스턴스 만 리턴 할 것입니다. 이것은 호출자가 목록 대신 단일 인스턴스를 반환하는 편리한 방법입니다.

```dart
// Update user with id = 1 to have the name 'Fred'
var query = Query<User>(context)
  ..values.name = "Fred"
  ..where((u) => u.id).equalTo(1);

var updatedUser = await query.updateOne();
```

갱신 된 행이없는 경우, `updateOne` 메소드는`null`를 돌려줍니다. `updateOne`가 사용되고 하나 이상의 행이 갱신되면, `updateOne`는 예외를 던지고 데이터에 대한 변경은 되돌릴 수 없다는 것에 주의하는 것이 중요합니다. 이것은 실수 일 가능성이 있으므로 오류로 간주되므로 예외가 발생합니다. 특정 `updateOne` 쿼리가 여러 행에 영향을 주는지 여부를 인식하는 것은 프로그래머의 몫입니다.

업데이트 쿼리에는 실수로 모든 행을 업데이트하지 못하게 하는 안전 기능이 있습니다. `where`를 설정하지 않고 업데이트를 수행하기 위해 `Query<T>`를 실행하려고 하면 요청을 수행하기 전에 예외가 발생합니다. 실제로 테이블의 모든 행을 업데이트하려면 실행하기 전에`Query.canModifyAllInstances`를 `true`로 설정해야 합니다. (이 속성의 기본값은 false입니다.)

### Deleting Data with a Query

`Query<T>`는 `delete()`를 사용할 때 데이터베이스에서 행을 삭제합니다. `update` 질의와 마찬가지로 `Query<T>`의 `where` 속성을 사용하여 하나 또는 여러 개의 행을 지정해야 합니다. 삭제 연산의 결과는 삭제 된 행의 수가 `Future<int>`가 됩니다.

```dart
var query = Query<User>(context)
  ..where((u) => u.id).equalTo(1);

int usersDeleted = await query.delete();
```

또한 업데이트 쿼리와 마찬가지로 삭제 쿼리는 `canModifyAllInstances`를 사용하여 테이블의 모든 행을 실수로 삭제하지 못하게 하는 안전 기능을 가지고 있습니다.

쿼리의 `values`에 설정된 모든 속성은 삭제를 실행할 때 무시됩니다.

### Fetching Data with a Query

`Query<T>`의 네 가지 기본 연산 중 데이터를 가져 오는 것이 가장 구성 가능합니다. 어떤 엔티티의 모든 인스턴스를 가져 오는 간단한 `Query<T>`는 다음과 같습니다:

```dart
var query = Query<User>(context);

List<User> allUsers = await query.fetch();
```

페치 쿼리는 `fetchLimit` 속성을 가진 많은 인스턴스로 제한 될 수 있습니다. `Query<T> `의 `offset`을 설정하여 첫 번째 `offset`행 수를 건너 뛸 수도 있습니다. `fetchLimit`과`offset` 사이에 순진한 페이징을 구현할 수 있습니다. 그러나 이 유형의 페이징에는 여러 가지 문제가 있으므로 나중에 다른 페이징 메커니즘이 있습니다.

`Query<T>`를 가져 오면 `where` 속성을 사용하여 삭제 및 업데이트 쿼리와 마찬가지로 결과 집합을 필터링합니다. 객체를 가져올 때 쿼리의 `values`는 무시됩니다. `fetchOne`으로 단일 인스턴스를 가져올 수도 있습니다. 인스턴스가 발견되지 않으면, `null`이 리턴됩니다. 검색 기준이 고유한 것이 확실한 경우에만 이 방법을 사용하십시오.

```dart
var query = Query<User>(context)
  ..where((u) => u.id).equalTo(1);

User oneUser = await query.fetchOne();
```

기본 키를 사용하여 객체를 가져올 때 `ManagedContext.fetchObjectWithID` 메소드를 사용할 수 있습니다. 메서드는 객체의 유형을 추론 할 수 있어야 하며 그렇지 않으면 객체를 제공해야합니다.

```dart
final object = await context.fetchObjectWithID<User>(1);
```

### Sorting

가져 오기 결과는 `Query<T>`의 `sortBy` 메소드를 사용하여 정렬 될 수 있습니다. 다음은 그 예입니다.

```dart
var q = Query<User>(context)
  ..sortBy((u) => u.dateCreated, QuerySortOrder.ascending);
```

`sortBy`는 두 개의 인수를 취합니다. 즉, 정렬 할 속성과 정렬 순서를 반환하는 클로저입니다.

`Query<T>` 결과는 여러 속성으로 정렬 될 수 있습니다. `Query<T>`에서 여러 개의 sortBy가 호출 될 때, 나중에 `sortBy`가 이전 `sortBy`에서 연결을 끊는 데 사용됩니다. 예를 들어 다음 쿼리는 성을 기준으로 정렬 한 다음 이름순으로 정렬합니다.

```dart
var q = Query<User>(context)
  ..sortBy((u) => u.lastName, QuerySortOrder.ascending)
  ..sortBy((u) => u.firstName, QuerySortOrder.ascending);
```

따라서 다음과 같은 세 가지 이름이 'Sally Smith', 'John Wu', 'Sally Wu'와 같이 순서가 됩니다.

### Property Selectors

정렬에 대한 섹션에서 *속성 선택기*를 사용하여 정렬 할 사용자의 속성을 선택하는 것을 보았습니다. 이 구문은 필터링 및 조인과 같은 많은 다른 쿼리 조작에 사용됩니다. 속성 선택자는 쿼리중인 유형의 객체를 제공하고 해당 객체의 속성을 반환해야 하는 클로저입니다. 이전 섹션의 `(u) => u.lastName` 셀렉터는 사용자의 성을 선택하는 프로퍼티 셀렉터입니다.

Dart 분석기는 속성 선택기의 인수를 유추하며 항상 쿼리되는 개체와 동일한 형식입니다. 이렇게하면 IDE 자동 완성, 정적 오류 검사 및 프로젝트 전체 이름 변경과 같은 다른 도구를 사용할 수 있습니다.

Live Templates

쿼리 작성 속도를 높이려면 IntelliJ에서 'ps'를 입력 할 때 속성 선택기를 생성하는 라이브 템플릿을 만듭니다. 템플릿의 소스는 `(o) => o.$END$`입니다. 이 바로 가기가 포함 된 IntelliJ에 대한 다운로드 설정 구성 [여기](https://aqueduct.io/docs/intellij/)이 있습니다.

## Specifying Result Properties

관리 대상 객체를 반환하는 쿼리(즉, `insert()`, `update()` 및 `fetch()`)를 실행하면 각 객체의 기본 속성을 가져옵니다. 관리 대상 객체의 기본 속성은 테이블 정의에 선언 된 데이터베이스 열 (column)에 해당하는 속성입니다. 관리 객체의 기본 속성은 테이블 정의를 선언 할 때 수정할 수 있습니다.

```dart
class _User {
  @Column(omitByDefault: true)
  String hashedPassword;
}
```

Any property with `omitByDefault` set to true will not be fetched by default.

A property that is `omitByDefault` can still be fetched. Likewise, a property that is in the defaults can still be omitted. Each `Query<T>` has a `returningProperties` method to adjust which properties do get returned from the query. Its usage looks like this:

`omitByDefault`가 true로 설정된 모든 속성은 기본적으로 가져 오지 않습니다.

`omitByDefault` 인 속성은 여전히 가져올 수 있습니다. 마찬가지로 기본값에 있는 속성은 여전히 생략 할 수 있습니다. 각각의`Query<T>`는 어떤 속성이 질의에서 반환되는지를 조정하는`returningProperties` 메소드를 가지고 있습니다. 사용법은 다음과 같습니다.

```dart
var query = Query<User>(context)
  ..returningProperties((user) => [user.id, user.name]);
```

`returningProperties`는 다중 속성 선택 자입니다. 하나의 속성만 반환하는 대신 속성 목록을 반환합니다.

`returningProperties`에 '속한'관계를 포함시킬 수 있지만 'has-many'또는 'has-one'관계는 포함 할 수 없습니다. 시도하면 예외가 발생합니다. 이러한 관계의 속성을 포함 시키려면 [고급 쿼리에서 조인](https://aqueduct.io/docs/db/advanced_queries/)을 참조하십시오.

`returningProperties`에서 관리 객체의 기본 키를 생략하면 자동으로 추가됩니다. 기본 키는`ManagedObject<T>`서브 클래스의 인스턴스로 행을 변환하는 데 필요합니다.

### Exceptions and Errors

쿼리를 실행할 때 쿼리가 유효하지 않은 경우, 데이터베이스에 연결할 수 없는 경우, 제약 조건이 위반 된 경우 등 여러 가지 이유로 실패 할 수 있습니다. 대부분의 경우이 예외는 기본 데이터베이스 드라이버에서 발생합니다. 컨트롤러에서 throw 될 때 이러한 예외는 500 Server Error 응답을 트리거 합니다.

사용자 입력 (예 : 데이터베이스 제약 조건 위반, 유효하지 않은 데이터 유형)에 대한 응답으로 발생하는 예외는 QueryException 또는 ValidationException으로 다시 해석됩니다. 이 예외 타입들은 모두 디폴트 500 Server 에러 대신에 보내지는 연관된 `Response` 객체를 가지고 있습니다.

이러한 이유 때문에 컨트롤러에서 데이터베이스 쿼리 예외를 catch 할 필요가 없습니다. 적절한 응답이 사용자를 대신하여 전송됩니다.

### Statement Reuse

Aqueduct는 가능한 경우 쿼리를 매개 변수화하고 다시 사용합니다. 이를 통해 상당한 속도와 보안을 향상시킬 수 있습니다. 이 기능을 사용하기 위해 특별한 조치를 취할 필요는 없습니다. 그러나 현재 현재이 기능을 사용 중지 할 수 없습니다.