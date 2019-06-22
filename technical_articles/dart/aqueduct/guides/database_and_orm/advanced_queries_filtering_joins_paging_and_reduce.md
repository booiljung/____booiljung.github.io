https://aqueduct.io/docs/db/advanced_queries/

# Advanced Queries: Filtering, Joins, Paging and Reduce

## Paging Fetched Result Sets

테이블의 행을 연속적 청크로 정렬하고 가져올 수 있습니다. 이 정렬은 대부분의 속성에서 발생할 수 있습니다. 예를 들어, 소셜 미디어 애플리케이션에서 사용자는 오랜 시간 동안 만든 콘텐츠를 수천 가지의 콘텐츠로 보유 할 수 있습니다. 콘텐츠를 가져올 가능성이 있는 유스 케이스는 가장 최근의 콘텐츠만 잡아서 필요에 따라 이전 콘텐츠만 가져 오는 것입니다. Aqueduct는 특정 범위 내에서 행의 하위 집합을 가져올 수 있는 쿼리를 작성하기 위해 `Query<T>`에서 두 가지 메커니즘을 제공합니다.

Naive 페이징은 `Query<T>`의 `fetchLimit`과 `offset` 속성을 사용하여 수행 할 수 있습니다. 예를 들어, 테이블에 100 개의 행이 있고 한 번에 10 개를 가져 오려면 각 쿼리의 값은`fetchLimit`에 대해 10입니다. 첫 번째 쿼리는 0, 10, 20 등의 오프셋을 갖습니다. 특히`sortBy`를 사용할 때, 이 유형의 페이징은 효과적 일 수 있습니다. 이 유형의 페이징에 대한 단점 중 하나는 페치간에 행을 추가하거나 삭제하는 경우 행을 건너 뛰거나 복제 할 수 있다는 것입니다.

![Paging Error](https://aqueduct.io/docs/img/paging.png)

예를 들어 시간순으로 정렬 된 위의 7 가지 객체를 생각해보십시오. 첫 번째 항목 (`offset=0`)에서 한 번에 두 개의 객체 (`fetchLimit=2`)로 페이지를 작성하면 첫 번째 결과 집합이 처음 두 객체가됩니다. 다음 페이지는 원래 오프셋과 같은 한도를 더한 것입니다. 다음 두 줄을 가져옵니다. 그러나 다음 페이지를 가져 오기 전에 새로운 객체가 삽입되고 이미 가져온 색인에 삽입됩니다. 다음 페이지는 `3:00pm`를 다시 반환 합니다. 이런 식으로 페이징 할 때 행이 삭제되면 비슷한 문제가 발생합니다.

클라이언트 응용 프로그램이 중복을 확인하고 병합해야하는 것은 정말 귀찮은 일입니다. 이 문제의 영향을 받지 않는 다른 페이징 기술은 클라이언트가 오프셋 대신 이전 페이지의 마지막 개체에서 값을 보내는 것에 의존합니다. 따라서 위의 예에서 두 번째 쿼리에서 오프셋 2를 요청하는 대신 `1:30pm`값을 보냅니다. 쿼리는 전송 된 값보다 적은 값으로 행을 필터링하고 나머지 행을 정렬 한 다음 맨 위에서 최신 값을 가져옵니다.

`Query.pageBy`는 이 기술을 사용합니다. 그것의 사용법은 `sortBy`와 비슷합니다 :

```dart
var firstQuery = Query<Post>(context)
  ..pageBy((p) => p.dateCreated, QuerySortOrder.descending)
  ..fetchLimit = 10;

var firstQueryResults = await firstQuery.fetch();

var oldestPostWeGot = firstQueryResults.last.dateCreated;
var nextQuery = Query<Post>(context)
  ..pageBy((p) => p.dateCreated, QuerySortOrder.descending, boundingValue: oldestPostWeGot)
  ..fetchLimit = 10;
```

이 쿼리는 최신 10 개의 게시물을 가져옵니다. 그런 다음 첫 번째 결과 집합에 있는 가장 오래된 게시물보다 새로운 모든 것을 건너 뛰고 다음 10을 가져옵니다.

페이징 할 때, 쿼리는 `fetchLimit`을 가져야 합니다. 그렇지 않으면 모든 행을 정렬하고 반환하는 것입니다. 속성 선택기를 사용하여 페이지 할 속성을 식별합니다. `pageBy`의 두 번째 인수는 행이 정렬되는 순서를 정의합니다.

페이징을 처음 시작할 때 아직 결과가 없으므로 마지막 결과 세트의 값을 지정할 수 없습니다. 이 경우, `pageBy`의 `boundingValue`는 null입니다 - 처음부터 시작한다는 것을 의미합니다. 첫 번째 세트가 페치되면, `boundingValue`는 리턴 된 마지막 오브젝트의 페이징 특성 값입니다.

이것은 경계 값을 취하는 쿼리 매개 변수를 끝점에 추가하여 종종 수행됩니다. (예를 들어`ManagedObjectController<T>`를 참조하십시오.)

`pageBy` 질의는 값이 더 이상 남아 있지 않을 때 객체의 빈리스트를 반환합니다. 마지막 페이지에 남아있는 객체의 수가 `fetchLimit`보다 작으면, 그 객체 만 반환됩니다. 예를 들어, 네 개의 객체가 남아 있고 `fetchLimit`이 10이면 반환되는 객체의 수는 4가 될 것입니다.

다음에 의해 페이지 될 등록 정보를 색인화해야 합니다.

```dart
@Column(indexed: true)
int pageableProperty;
```

## Filtering Results of a Fetch Operation

일반적으로 테이블의 모든 행을 가져 오는 것은 의미가 없습니다. 대신 특정 기준 또는 일부 기준과 일치하는 특정 객체 또는 객체를 원합니다.

`Query`의 `where` 메소드는 이 기준을 질의에 추가하는 안전하고 우아한 방법입니다. 이 메소드를 사용하여 조회중인 오브젝트의 특성에 부울 표현식을 지정할 수 있습니다. 각 표현식은 생성 된 SQL 조회의 WHERE 절에 추가됩니다. 다음은 `id`가 1 인`User`를 찾는 쿼리의 예입니다 :

```dart
var query = Query<User>(context)
  ..where((u) => u.id).equalTo(1);
```

(여기서 생성 된 SQL은 `SELECT _user.id, _user.name, ... FROM _user WHERE _user.id = 1`이됩니다.)

`equalTo`와 같은 많은 표현식이 있습니다 - 전체 목록은 `QueryExpression <T>`에 대한 문서를 보십시오.

`where`를 여러 번 호출하여 여러 기준을 쿼리에 추가 할 수 있습니다. 각 기준은 논리 '와'로 결합됩니다. 예를 들어, 다음 쿼리는 `name`이 "Bob"이고 `email`이 null이 아닌 모든 사용자를 찾을 것입니다 :

```dart
final query = Query<User>(context)
  ..where((u) => u.name).equalTo("Bob")
  ..where((u) => u.email).isNotNull();
```

관계 특성에도 기준을 적용 할 수 있습니다. nullable / not null 체크를 적용 할 수 있습니다.

```dart
var employedQuery = Query<Person>(context)
  ..where((c) => c.company).isNotNull();
```

`identifyBy` 표현식을 사용하여 특정 객체에 속한 객체를 찾는 경우가 더 자주 있습니다. 예를 들어 특정 회사의 모든 직원을 찾을 때 :

```dart
var preferredQuery = Query<Employee>(context)
  ..where((c) => c.company).identifiedBy(23);
```

위의 내용은 기본 키 값이 23 인 회사에서 근무하는 직원만 반환합니다. 이는 다음과 같으며 둘 다 허용됩니다.

```dart
var sameQuery = Query<Employee>(context)
  ..where((c) => c.company.id).equalTo(23);
```

위의 예에서 쿼리를 작성할 때 관계 속성을 선택할 수 있습니다. 직원이 속한 회사이기 때문에 직원 테이블에는 회사의 기본 키를 저장하는 열이 있습니다. 이를 외래 키 열이라고합니다. 속한 관계의 기본 키를 선택하는 쿼리를 작성할 때 Aqueduct는 이를 외부 키 열 값을 사용하도록 해석 할 수 있습니다.

쿼리되는 테이블의 외래 키 열에 의해 지원되지 않는 속성을 선택하려면 다음 절의 조인을 참조하십시오.

## Including Relationships in a Fetch (aka, Joins)

`Query<T>`는 관계 속성을 가져올 수도 있습니다. 따라서 쿼리를 통해 전체 모델 그래프를 가져올 수 있으며 데이터베이스 왕복 횟수를 줄일 수 있습니다.

기본적으로 관계 속성은 쿼리에서 가져 오지 않으므로 객체의 `asMap()`에 포함되지 않습니다. 예를 들어, `User`가 많은 `Task`s가 있는 다음과 같은 정의를 생각해보십시오 :

```dart
class User extends ManagedObject<_User> implements _User {}
class _User {
  @primaryKey
  int id;

  String name;
  ManagedSet<Task> tasks;  
}

class Task extends ManagedObject<_Task> implements _Task {}
class _Task {
  @primaryKey
  int id;

  @Column(#tasks)
  User user;

  String contents;
}
```

`Query<User>`는 각 `User`의 `name`과 `id`를 가져옵니다. `User`의 `tasks`는 반입되지 않기 때문에 반환 된 데이터는 다음과 같습니다:

```dart
var q = Query<User>(context);
var users = await q.fetch();

users.first.asMap() == {
  "id": 1,
  "name": "Bob"
}; // yup
```

`join()` 메쏘드는 쿼리가 관련 객체도 포함하도록 지시합니다. 다음은 사용자와 작업을 가져 오는 가져 오기를 보여줍니다.

```dart
var q = Query<User>(context)
  ..join(set: (u) => u.tasks);
var users = await q.fetch();

users.first.asMap() == {
  "id": 1,
  "name": "Bob",
  "tasks": [
      {"id": 1, "contents": "Take out trash", "user" : {"id": 1}},
      ...
  ]
}; // yup
```

has-many 관계에 join 할 때, `set:` 인자는 `ManagedSet`을 선택해야만 하는 속성 선택자를 취합니다. (has-one 또는 belongs-to 관계를 가져올 때는 `object:`인자를 사용하십시오.)

`join()` 메소드는 새로운 `Query<T>`를 반환하는데, `T`는 조인된 객체의 타입입니다. 즉 위의 코드는 다음과 같이 작성할 수도 있습니다.

```dart
var q = Query<User>(context);

// type annotation added for clarity
Query<Task> taskSubQuery = q.join(set: (u) => u.tasks);
```

### Configuring Join Queries

조인에 의해 작성된 조회는 실행하지 않지만 다른 조회와 같이 구성하십시오. 부모 쿼리는 조인 된 쿼리를 추적하고 부모 쿼리를 실행합니다. 예를 들어, 조인 된 개체에 대해 반환 된 속성을 수정할 수 있습니다.

```dart
var q = Query<User>(context);

q.join(set: (u) => u.tasks)  
  ..returningProperties((t) => [t.id, t.contents]);

final usersAndTasks = await q.fetch();  
```

조인 쿼리에 필터링 기준을 적용 할 수도 있습니다. `Children`이 많은 `Parent`를 생각해보십시오. 부모를 가져 와서 자식에 합류 할 때, join 질의에 대한 `where` 표현식은 어떤 자식이 반환되는지에 영향을 주지만 어떤 부모가 반환되는지에는 영향을 미치지 않습니다. 예를 들어 다음 쿼리는 모든 부모를 가져 오지만 1년 이상 된 하위 만 포함합니다.

```dart
final q = Query<Parent>(context);
q.join(set: (p) => p.children)
  ..where((c) => c.age).greaterThan(1);

final parentsAndTheirChildren = await q.fetch();
```

### Filtering Objects by Their Relationships

그러나 부모 쿼리에 비슷한 표현식을 적용하면 1년 이상 된 자녀가 있는 부모만 반환됩니다.

```dart
final q = Query<Parent>(context)
  ..where((c) => c.children.haveAtLeastOneWhere.age).greaterThan(1);
  ..join(set: (p) => p.children);

final parentsWithOlderChildren = await q.fetch();
```

차이점은 표현식이 적용되는 부분입니다. 자식 쿼리에 적용하면 기준을 충족하지 않는 하위 개체가 제거됩니다. 부모 쿼리에 적용하면 조건을 충족하지 않는 부모가 제거됩니다. `hasAtLeastOneWhere` 속성은 has-many 관계에 고유합니다. has-one 또는 belongs-to 관계의 속성을 선택할 때 속성에 직접 액세스합니다.

```dart
final q = Query<Child>(context)
  ..where((p) => p.parent.age).greaterThan(30)
  ..join(object: (p) => e.parent);

final childrenWithParentsOver30 = await q.fetch();
```

명시 적으로 속성에 참여하지 않고도 관계 속성을 사용할 수 있습니다. SQL JOIN은 계속 수행되지만 관련 오브젝트는 결과 세트에 포함되지 않습니다.

```dart
final q = Query<Child>(context)
  ..where((p) => p.parent.age).greaterThan(30);

final employeesWithManagersOver30YearsOld = await q.fetch();
```

### Multiple Joins

둘 이상의 조인을 쿼리에 적용 할 수 있으며 하위 쿼리를 중첩 할 수 있습니다. 따라서 이것은 관계 속성이 존재한다고 가정 할 때 모두 유효합니다.

```dart
var q = Query<User>(context)
  ..join(object: (u) => u.address);

q.join(set: (u) => u.tasks)
  ..join(object: (u) => u.location);
```

그러면 모든 사용자, 주소, 모든 작업 및 각 작업의 위치가 가져옵니다. 당신은 여기에 사물의 좋은 크기의 나무를 얻을 것입니다.

## Reduce Functions (aka, Aggregate Functions)

쿼리는 `count`, `sum`, `average`, `min` 및 `max`와 같은 함수를 수행하는 데에도 사용될 수 있습니다. 다음은 그 예입니다.

```dart
var query = Query<User>(context);
var numberOfUsers = await query.reduce.count();
```

일부 속성의 값을 사용하는 reduce 함수의 경우 속성 선택자는 해당 속성을 식별하는 데 사용됩니다.

```dar
var averageSalary = await query.reduce.sum((u) => u.salary);
```

`Query<T>`에 구성된 값은 `reduce` 함수에도 영향을 미칩니다. 예를 들어, `Query.where`를 적용한 다음 `sum` 함수를 실행하면 where 절의 기준을 만족하는 행만 합계 됩니다.

```dart
var query = Query<User>(context)
  ..where((u) => u.name.equalTo("Bob");
var averageSalaryOfPeopleNamedBob = await query.reduce.sum((u) => u.salary);
```

## Fallbacks

항상 임의의 SQL을 `PersistentStore.execute`로 실행할 수 있습니다. 리턴 된 객체는 각각의 열 목록에 대해 `List<List<dynamic>>`(행 목록)이 됩니다.

raw WHERE 절에 `Query.predicate`를 제공 할 수도 있습니다. `QueryPredicate`는 질의의 where 절로 설정된 `String`입니다. `QueryPredicate`는 두 개의 프로퍼티, 즉 포맷 문자열과 파라미터 값의 `Map<String, dynamic>`을 가지고 있습니다. `format` 문자열은 모든 입력 값을 매개 변수화 할 수 있습니다. 매개 변수는 `@`토큰을 사용하여 형식 문자열로 표시됩니다.

```dart
// Creates a predicate that would only include instances where some column "id" is less than 2
var predicate = QueryPredicate("id < @idVariable", {"idVariable" : 2});
```

`@`토큰 다음의 텍스트는 `[A-Za-z0-9_]`를 포함 할 수 있습니다. 결과로 나오는 where 절은 각 토큰을 매개 변수 맵에서 일치하는 키로 대체하여 형성됩니다. 값은 어떤 식 으로든 변환되지 않으므로 열에 적합한 유형이어야 합니다. `Map`에 키가 없으면 예외가 발생합니다. 추가 키는 무시됩니다.