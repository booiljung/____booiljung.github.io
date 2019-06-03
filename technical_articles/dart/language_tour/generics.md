[Up](./index.md)

## 제네릭 (Generics)

[List](https://api.dartlang.org/stable/dart-core/List-class.html)의 기본 배열 유형에 대한 API 문서를 보면 형식이 실제로 `List<E>`입니다. `<...>`표기법은 List를 제네릭(또는  매개변수화) 타입으로 표시합니다. 타입은 타입 매개 변수가 있는 타입입니다. [By Convention](https://dart.dev/guides/language/effective-dart/design#do-follow-existing-mnemonic-conventions-when-naming-type-parameters) 대부분의 타입 변수는 `E`, `T`, `S`, `K` 및 `V`와 같은 한 문자로 된 이름을 가지고 있습니다.

<p id="why-use-generics"/>

###  왜 제네릭을 사용해야 할까요? (Why use generics?)

제네릭은 타입 안전성을 위해 종종 필요하지만 코드 실행을 허용하는 것보다 많은 장점이 있습니다.

- 제네릭 형식을 올바르게 지정하면 코드를 더 잘 생성 할 수 있습니다.
- 제네릭을 사용하여 코드 중복을 줄일 수 있습니다.

리스트에만 문자열을 포함 시키려면 `List<String>`( "string of list"로 읽음)으로 선언 할 수 있습니다. 그렇게 하면 여러분, 동료 프로그래머, 그리고 여러분의 도구가 비문자열(non-string)을 리스트에 대입하는 것이 실수 일 수 있음을 감지 할 수 있습니다. 다음은 그 예입니다.

```dart
var names = List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
names.add(42); // Error
```

제네릭을 사용하는 또 다른 이유는 코드 중복을 줄이는 것입니다. 제네릭을 사용하면 정적 분석을 활용하면서 여러 타입간에 단일 인터페이스와 구현을 공유 할 수 있습니다. 예를 들어, 객체 캐싱을위한 인터페이스를 생성한다고 가정 해보십시오.

```dart
abstract class ObjectCache {
  Object getByKey(String key);
  void setByKey(String key, Object value);
}
```

이 인터페이스의 문자열 지정 버전을 원한다는 것을 눈치채게 되면 다른 인터페이스를 만들 수 있습니다:

```dart
abstract class StringCache {
  String getByKey(String key);
  void setByKey(String key, String value);
}
```

나중에 이 인터페이스의 숫자 지정 버전을 원한다고 결정했습니다. You get the idea.

제네릭 타입을 사용하면 이러한 모든 인터페이스를 작성하는 번거로움을 줄일 수 있습니다. 대신 타입 매개 변수를 사용하는 단일 인터페이스를 만들 수 있습니다.

```dart
abstract class Cache<T> {
  T getByKey(String key);
  void setByKey(String key, T value);
}
```

이 코드에서 `T`는 스탠드-인 타입니다. 개발자가 나중에 정의 할 수있는 타입이라고 생각할 수 있는 자리 표시자(placeholder) 입니다.

<p id="using-collection-literals"/>

###  콜렉션 리터럴 사용하기 (Using collection literals)

리스트, 세트 및 맵 리터럴을 매개 변수화 할 수 있습니다. 매개 변수화 된 리터럴은 여는 괄호 앞에`<타입>`(리스트와 세트에 대해) 또는 `<키타입, 값타입>`(맵에 대해)을 추가한다는 것을 제외하고는 이미 본 리터럴과 같습니다. 다음은 유형화 된 리터럴 사용의 예입니다:

```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var uniqueNames = <String>{'Seth', 'Kathy', 'Lars'};
var pages = <String, String>{
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};
```

<p id="using-parameterized-types-with-constuctors"/>

###  생성자에서 매개 변수화 된 타입 사용 (Using parameterized types with constructors)

생성자를 사용할 때 하나 이상의 타입을 지정하려면 클래스 이름 바로 뒤에 꺽쇠 괄호 (`<...> `)로 유형을 넣으세요. 예 :

```dart
var nameSet = Set<String>.from(names);
```

다음 코드는 정수형 키와 View 타입의 값을 갖는 맵을 생성합니다.

```dart
var views = Map<int, View>();
```

<p id="generic-collections-and-the-types-they-contain"/>

###  Generic collections and the types they contain

다트의 제네릭 형식은 reified 형식이므로 런타임에 형식 정보를 전달합니다. 예를 들어 컬렉션의 타입을 테스트 할 수 있습니다.

```dart
var names = List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```

**Note:** 반대로 자바의 제네릭은 erasure를 사용합니다. 즉, 일반 타입 매개 변수는 런타임에 제거됩니다. 자바에서는 객체가 `List`인지 여부를 테스트 할 수 있지만 `List<String>`인지 테스트 할 수는 없습니다.

<p id="restricting-the-parameterized-type"/>

###  매개 변수 타입의 제약하는 방법 (Restricting the parameterized type)

제네릭 타입을 구현할 때 해당 매개 변수의 타입을 제약 할 수 있습니다. `extends`를 사용하여 이 작업을 수행 할 수 있습니다.

```dart
class Foo<T extends SomeBaseClass> {
  // Implementation goes here...
  String toString() => "Instance of 'Foo<$T>'";
}

class Extender extends SomeBaseClass {...}
```

`SomeBaseClass` 또는 그 하위 클래스를 제네릭 인자로 사용하는 것은 괜찮습니다:

```dart
var someBaseClassFoo = Foo<SomeBaseClass>();
var extenderFoo = Foo<Extender>();
```

또한 제네릭 인수를 지정하지 않아도 좋습니다.

```dart
var foo = Foo();
print(foo); // 'Foo<SomeBaseClass>'의 인스턴스
```

`SomeBaseClass`가 아닌 타입을 지정하면 에러가 발생합니다 :

```dart
var foo = Foo<Object>();
```

<p id="using-generic-methods"/>

###  제네릭 타입 메소드 사용법 (Using generic methods)

처음에는 Dart의 제네릭 지원이 클래스에만 제한되었습니다. 제네릭 메소드라고 하는 새로운 구문을 사용하면 메서드 및 함수에 타입 인수를 사용할 수 있습니다.

```dart
T first<T>(List<T> ts) {
  // Do some initial work or error checking, then...
  T tmp = ts[0];
  // Do some additional checking or processing...
  return tmp;
}
```

여기에 `first`(`<T>`)의 제네릭 타입 매개 변수는 타입 인자 `T`를 여러 곳에서 사용할 수 있습니다 :

- 이 함수의 반환 타입 (`T`).
- 인수의 타입 (`List<T>`).
- 지역 변수의 타입 (`T tmp`).

---

이전: [클래스 (Classes)](./classes.md)

다음: [라이브러리와 가시성 (Libraries and visibility)](./libraries_and_visibility.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
