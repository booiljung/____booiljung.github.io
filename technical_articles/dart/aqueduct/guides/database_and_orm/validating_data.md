https://aqueduct.io/docs/db/validations/

# Validating Data

데이터는 `update`와 `insert` 쿼리를 통해 데이터베이스에 추가됩니다. 이 두 작업의 일부로`ManagedObject<T>`는 해당 속성이 유효한 값을 가지도록 보장합니다. 예를 들어, `Person` 객체는 그 이름이 대문자로 시작하고 그 전화 번호가 숫자 값만을 가지고 있음을 보장 할 수 있습니다. 하나 이상의 유효성 검증이 실패하면 갱신 또는 삽입 조작이 실패하고 데이터가 데이터베이스로 송신되지 않습니다. 유효성 검증 실패는 클라이언트가 요청을 수정하는 것을 돕기 위해 오류 메시지와 함께 HTTP 응답을 리턴하는 `QueryException`을 던집니다.

검증을 설정하는 좋은 방법은 테이블 정의의 속성에 `Validate` 메타 데이터를 추가하는 것입니다. 다음은 트윗이 140 자 미만인지 확인하는 유효성 검증의 예입니다.

```dart
class Tweet extends ManagedObject<_Tweet> implements _Tweet {}
class _Tweet {
  @primaryKey
  int id;

  @Validate.length(lessThan: 140)
  String message;
}
```

### Built-in Validators

일반적인 작업에는 몇 가지 기본 유효성 검사가 있습니다. 예를 들어 정규 표현식을 값에 적용하여 올바르게 형식을 지정하거나 가능한 값을 사용 가능한 옵션 목록으로 제한하는 것이 일반적입니다. 공통의 validator는, `Validate` 클래스의 이름 첨부 생성자로서 사용할 수 있습니다. 다음은 그 예입니다.

```dart
class _Story {
  @primaryKey
  int id;

  @Validate.oneOf(const ["started", "accepted", "rejected", "delivered"])
  String state;
}
```

기본 제공 유효성 검사기는 HTTP 응답에서 반환되는 오류 메시지를 자동으로 생성하므로 유용합니다. 예를 들어, 이전 코드 스니펫은 `state` 속성이 네 개의 나열된 문자열 중 하나 여야 함을 나타냅니다. 이 네 개의 문자열 중 하나가 아닌 다른 값이 사용되면 HTTP 클라이언트에 반환되는 오류 메시지는 다음과 같습니다.

```
"The value `invalidValue` is not valid for `state`. Valid values are: 'started', 'accepted', 'rejected', 'delivered'.".
```

가능한 옵션에 대해서는`Validate`에 대한 API 참조와 명명 된 생성자를 참조하십시오.

관리 대상 객체 서브 클래스 (일시적인 속성)에서 선언 된 속성에 대한 `Validate`주석은 아무 효과가 없습니다.

### Validating Relationships

유효성 검증은 테이블 정의에 선언 된 특성에만 유효합니다. 관계 속성에 적용된 유효성 검사기는 관련 개체의 기본 키 (즉, 외래 키 값)에 적용됩니다. 유효성 검사 논리는 유효성이 검사되는 관리 대상 개체의 속성에서만 실행됩니다. 관련 개체의 속성에 대한 유효성 검사는 실행되지 않습니다. 관리 대상 객체의 그래프를 검증 할 때는 관련 객체에 대한 유효성 검사를 수동으로 시작해야합니다.

Validating Related Objects

유효성 검증의 작동은 오브젝트가 관계로서 유효성이 검증 될 때와 다릅니다. 다시 말해, 객체의 기본 키에 적용된 유효성 검사는 해당 객체에 대한 외부 키 참조에 적용될 때 다른 동작을 요구할 가능성이 높습니다.

### Custom Validators

내장 된 유효성 검사기가 응용 프로그램의 사용 사례에 충분하지 않은 경우가 있습니다. `Validate`의 서브 클래스를 생성하여 커스텀 검증 동작을 제공 할 수 있습니다. 예를 들어, `ValidatePhoneNumber` 클래스가 있다면 :

```dart
class _Person {
  @primaryKey
  int id;

  @ValidatePhoneNumber()
  String phoneNumber;
}
```

`Validate`의 서브 클래스는 `Validate.validate()`를 오버라이드 (override) 해, 인스턴스 생성시에 슈퍼 클래스의 1 차 생성자를 호출 할 필요가 있습니다. 다음은 전화 번호 유효성 검사기의 예입니다.

```dart
class ValidatePhoneNumber extends Validate {
  ValidatePhoneNumber({bool onUpdate: true, bool onInsert: true}) :
    super(onUpdate: onUpdate, onInsert: onInsert);

  @override
  void validate(ValidationContext context, dynamic value) {  
    if (value.length != 15) {
      context.addError("must be 15 digits");      
    }

    if (containsNonNumericValues(value)) {
      context.addError("must contain characters 0-9 only.");      
    }
  }
}
```

`value`가 검증 기준에 부합하지 않는 경우,이 메소드는 전달 된 `ValidationContext`에 에러 문자열을 추가합니다. 오류 메시지는 간략해야 하며 실패한 성공적인 기준을 나타내야 합니다. 유효성을 검사하는 속성에 대한 정보는 자동으로 오류 메시지에 추가되므로 해당 정보를 포함 할 필요가 없습니다. 유효성 검사가 끝날 때 컨텍스트에 오류가 없는 경우 유효성 검사가 성공합니다. 그렇지 않으면 실패합니다.

`ValidationContext`는 또한 유효성이 검사되는 속성에 대한 정보와, 삽입되는 객체 또는 갱신 될 객체에 대한 검증이 실행 중인지 여부에 대한 정보를 가지고 있습니다.

### Validation Behavior

속성은 둘 이상의 `Validate` 메타 데이터를 가질 수 있습니다. 이 경우 속성에 대한 모든 유효성 검사가 통과해야 합니다. 여러 유효성 검사가 수행되는 순서는 정의되지 않았으며 변경 될 수 있습니다. 다음은 속성의 값이 10 자이고 10 자의 알파벳 대문자 만 포함하도록하는 유효성 검사의 예입니다.

```dart
@Validate.length(equalTo: 10)
@Validate.matches(r"$[A-Z]+^")
String tenCapitalLetters;
```

디폴트로, 검증은 `Query<T>`의 `insert` 또는 `update` 메소드가 호출 될 때 실행됩니다. 유효성 검사기는 선택적 생성자 인수 `onUpdate`와 `onInsert`에 값을 전달함으로써 `insert` 또는`update`에서만 실행되도록 제한 될 수 있습니다:

```dart
@Validate.matches(r"^[A-Z]+$", onInsert: true, onUpdate: false)
String validateOnInsertOnly;
```

속성의 값이 삽입 또는 업데이트 쿼리에 지정되지 않은 경우 유효성 검사가 작동하는 방식을 이해하는 것이 중요합니다. 예를 들어 `name`과 `email` 속성을 가진 `Person`을 생각해보고 `email`가 설정되지 않은 쿼리에 삽입하면 :

```dart
var query = new Query<Person>(context)
  ..values.name = "Bob";

await query.insert();
```

`email.`이 `Query.values`에 설정되지 않았기 때문에, 그 속성에 대한 검증은 실행되지 않습니다.

속성을 설정해야하거나 속성을 설정하지 않아도 되도록 요구할 수있는 특수한 유효성 검사기가 두 개 있습니다. `Validate.present()`는 관련된 프로퍼티가 값을 가져야한다는 것을 요구합니다. 개체가 삽입되거나 업데이트 될 때마다 이 유효성 검사기가 있는 속성을 제공해야합니다. 예를 들어, 다음 선언문에서는 삽입시 `email`가 설정 되어야 하지만 업데이트는 필요하지 않습니다.

```dart
@Validate.present(onUpdate: false, onInsert: true)
String email;
```

`Validate.present()`의 역은 `Validate.absent()`입니다. 이 유효성 검증은 특성이 설정되지 않도록 합니다. 이 값은 삽입 중에 값을 포함해야하지만 업데이트 할 수 없는 경우에 유용합니다. 다음은 그 예입니다.

```dart
@Validate.absent(onUpdate: true, onInsert: false)
String canOnlyBeSetOnce;
```

위의 선언에서 유효성 검사기는 업데이트 작업에서만 실행되며 `canOnlyBeSetOnce` 속성에 값이 없는지 확인합니다. 이 유효성 검사기는 삽입 작업에서 실행되지 않으므로 개체를 처음 삽입 할 때 제약이 없습니다.

값이 null 일 때 유효성 검사기가 실행되지 않습니다. 예를 들어 다음 삽입은 `email` 속성에`null`을 명시적으로 삽입합니다.

```dart
var query = new Query<Person>(context)
  ..values.email = null
  ..values.name = "Bob";

await query.insert();
```

Nullability는 `Column.isNullable` 속성에 의해 강제됩니다. 다음 선언을 고려하십시오.

```dart
@Column(nullable: false)
@Validate.length(greaterThan: 10)
String name;
```

여기서, name 속성은 null이 아니어야하며 10 문자보다 길어야 합니다. 이 속성을 삽입하거나 업데이트하는 동작은 다음 표에 나와 있습니다.

| Input Value for Name                    | Validation Runs? | Outcome                                                      |
| --------------------------------------- | ---------------- | ------------------------------------------------------------ |
| Insert value longer than 10 characters  | Yes              | Successful database insert                                   |
| Insert value shorter than 10 characters | Yes              | Database insert not executed, exception thrown               |
| Insert value not specified              | No               | Database insert fails with non-null violation, exception thrown |
| Insert value is null                    | No               | Database insert fails with non-null violation, exception thrown |
| Update value longer than 10 characters  | Yes              | Successful database update                                   |
| Update value shorter than 10 characters | Yes              | Database update not executed, exception thrown               |
| Update value not specified              | No               | Successful database update                                   |
| Update value is explicit null           | No               | Successful database update                                   |

이 동작은 `ManagedObject<T>`인스턴스를 부분적으로 업데이트 할 수있게 해주며 부분 PUT도 허용합니다. `Validate.present()` 메타 데이터를 모든 속성에 추가하여 부분 PUT을 방지 할 수 있습니다.

이것은 또한 모든 사용자 정의 유효성 검사기가 `Validate.validate()`에 전달 된 값이 null이 아니라고 주장 할 수 있음을 의미합니다.

### Other Validator Behavior

`Validate`를 서브 클래스 화하여 만들 수 없는 validator에 대해서는 `ManagedObject<T> .validate()`를 오버라이드 할 수 있습니다. 이 메소드는 유효성 검증에 둘 이상의 특성이 포함될 때 유용합니다. 다음은 그 예입니다.

```dart
class Person extends ManagedObject<_Person> implements _Person {
  @override
  ValidationContext validate({Validating forEvent: Validating.insert} {
    final ctx = super.validate(forEvent: forEvent);
    if (a + b > 10) {
      ctx.addError("a + b must be greater than 10");
    }
    return ctx;
  }
}
```

이 메소드를 오버라이드 (override) 할 때, 주석에 의해 관리되는 검증을 실행하려면, `super` 구현을 호출 할 필요가 있습니다. 슈퍼 클래스의 구현에 의해 생성 된 `ValidationContext`를 반환해야합니다.

### Skipping Validations

검증은 `Query<T>.values`를 통해 값이 설정 될 때만 실행됩니다. `Query<T>.valueMap`를 통해 설정된 값은 검증되지 않고 유효성 검사없이 데이터를 삽입하는 데 유용합니다. 다음은 유효성 검사를 건너 뛰는 예제입니다.

```dart
var query = new Query<Person>(context)
  ..valueMap = {
    "name" : "xyz",
    "email" : "whatever"
  };
```

### Update and Insert Callbacks

`ManagedObject<T>`서브 클래스는 `willUpdate`와 `willInsert`를 오버라이드 (override) 해, 갱신 또는 삽입 전에 변경을 실시 할 수가 있습니다. 예를 들어, 관리 대상 객체는 업데이트되거나 생성 될 때 날짜가 삽입되거나 업데이트 될 때 설정 될 수 있습니다.

```dart
class Person extends ManagedObject<_Person> implements _Person {
  @override
  void willUpdate() {
    updatedAt = new DateTime.now().toUtc();
  }

  @override
  void willInsert() {
    createdAt = new DateTime.now().toUtc();
  }
}
class _Person {
  @primaryKey
  int id;

  String name;
  DateTime createdAt;
  DateTime updatedAt;
}
```

`willUpdate`와 `willInsert`는 검증이 일어나기 전에 실행됩니다. 유효성 검사와 마찬가지로 `Query.valueMap`을 사용할 때 `willUpdate`와 `willInsert`는 건너 뜁니다.