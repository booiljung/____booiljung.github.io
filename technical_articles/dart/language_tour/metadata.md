[Up](./index.md)

##  메타데이터 (Metadata)

메타데이터를 사용하여 코드에 대한 추가 정보를 제공하십시오. 메타데이터 주석은 `@`문자로 시작하고, 컴파일 타임 상수 (`deprecated`와 같은) 또는 상수 생성자에 대한 호출이 따라옵니다.

두 개의 주석은 모든 다트 코드에서 사용할 수 있습니다: `@deprecated`와 `@override`.  `@override`의 사용 예는 [Extending a class](classes.md#extending-a-class)를 보십시오. 다음은 `@deprecated` 주석 사용의 예입니다 :

```dart
class Television {
  /// _Deprecated: Use [turnOn] instead._
  @deprecated
  void activate() {
    turnOn();
  }

  /// Turns the TV's power on.
  void turnOn() {...}
}
```

사용자가 메타데이터 주석을 정의 할 수 있습니다. 다음은 두 개의 인수를 취하는 `@todo` 주석을 정의하는 예입니다.

```dart
library todo;

class Todo {
  final String who;
  final String what;

  const Todo(this.who, this.what);
}
```

그리고 그 `@todo` 주석을 사용하는 예제는 다음과 같습니다.

```dart
import 'todo.dart';

@Todo('seth', 'make this do something')
void doSomething() {
  print('do something');
}
```

메타데이터는 라이브러리, 클래스, typedef, 타입 매개 변수, 생성자, 팩토리, 함수, 필드, 매개 변수 또는 변수 선언 앞에, 또는 `import` 또는 `export` 지시문 앞에 나타날 수 있습니다. 리플렉션을 사용하여 런타임에 메타데이터를 검색 할 수 있습니다.

---

이전: [타입 정의 (Typedefs)](./typedefs.md)

다음: [주석 (Comments)](./comments.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
