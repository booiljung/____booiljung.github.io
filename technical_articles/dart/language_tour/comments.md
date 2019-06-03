[Up](./index.md)

##  주석 (Comments)

다트는 한 줄 주석, 여러 줄 주석 및 문서 주석을 지원합니다.

<p id="single-line-comments"/>

###  한 줄 주석 (Single-line comments)

한 줄 주석은  `//`으로 시작합니다. 주석 내용은 `//` 와 라인 끝 사이에 있어야 하며, 이것은 Dart 컴파일러가 무시합니다.

```dart
void main() {
  // TODO: refactor into an AbstractLlamaGreetingFactory?
  print('Welcome to my Llama farm!');
}
```

<p id="multi-line-comments"/>

###  여러 줄 주석 (Multi-line comments)

여러 줄 주석은  `/*` 로 시작하여  `*/`로 끝납니다. 주석 내용은  `/*` 와  `*/` 사이에 있어야 하며, 이것은 (주석이 문서 주석이 아닌 한; 다음 섹션을 참조) Dart 컴파일러가 무시 합니다. 여러 줄 주석은 중첩 될 수 있습니다.

```dart
void main() {
  /*
   * This is a lot of work. Consider raising chickens.

  Llama larry = Llama();
  larry.feed();
  larry.exercise();
  larry.clean();
   */
}
```

<p id="documentation-comments"/>

###  문서 주석 (Documentation comments)

문서 주석은`///`또는`/ **`로 시작하는 여러 줄 또는 한 줄 주석입니다. 연속 된 행에 `///`을 사용하면 여러 줄로 된 문서 주석과 동일한 효과를 얻을 수 있습니다.

Dart 컴파일러는 문서 주석에서 대괄호로 묶이지 않은 한 모든 텍스트를 무시합니다. 괄호를 사용하면 클래스, 메소드, 필드, 최상위 변수, 함수 및 매개 변수를 참조 할 수 있습니다. 괄호 안의 이름은 문서화 된 프로그램 요소의 어휘 범위(lexical scope)에서 결정됩니다.

다음은 다른 클래스 및 인수에 대한 레퍼런스가 포함 된 문서 주석의 예입니다:

```dart
/// A domesticated South American camelid (Lama glama).
///
/// Andean cultures have used llamas as meat and pack
/// animals since pre-Hispanic times.
class Llama {
  String name;

  /// Feeds your llama [Food].
  ///
  /// The typical llama eats one bale of hay per week.
  void feed(Food food) {
    // ...
  }

  /// Exercises your llama with an [activity] for
  /// [timeLimit] minutes.
  void exercise(Activity activity, int timeLimit) {
    // ...
  }
}
```

생성 된 문서에서 `[Food]`는 Food 클래스의 API 문서에 대한 링크가 됩니다.

다트 코드를 파싱하고 HTML 문서를 생성하려면 SDK의 [documentation generation tool](https://github.com/dart-lang/dartdoc#dartdoc)를 사용할 수 있습니다. 생성 된 문서의 예는 [Dart API documentation](https://api.dartlang.org/stable)을 보세요. 주석을 구조화 하는 방법에 대한 조언은 [Guidelines for Dart Doc Comments.](https://dart.dev/guides/language/effective-dart/documentation)를 보세요.

---

이전: [메타데이터 (Metadata)](./metadata.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
