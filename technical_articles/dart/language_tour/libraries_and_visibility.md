[Up](./index.md)

##  라이브러리와 가시성 (Libraries and visibility)

`import`와 `library` 지시어는 모듈화 되고 공유 가능한 코드 기반을 만드는 데 도움이 될 수 있습니다. 라이브러리는 API를 제공 할뿐만 아니라 프라이버시 단위이기도 합니다. 밑줄 (`_`)로 시작하는 식별자는 라이브러리 내부에서만 볼 수 있습니다. 모든 Dart 응용 프로그램은 라이브러리가 아니며 `library` 지시문을 사용하지 않습니다.

라이브러리는 [packages](https://dart.dev/guides/packages)를 사용하여 배포 할 수 있습니다.

<p id="using-libraries"/>

###  라이브러리 사용법 (Using libraries)

한 라이브러리의 네임스페이스를 다른 라이브러리의 스코프에서 사용하는 방법을 지정하려면 `import`를 사용하세요.

예를 들어 다트 웹 앱은 일반적으로 [dart:html](https://api.dartlang.org/stable/dart-html) 라이브러리를 사용하며 다음과 같이 가져올 수 있습니다.

```dart
import 'dart:html';
```

`import`에 유일한 필수 인수는 라이브러리를 지정하는 URI입니다. 내장 라이브러리의 경우, URI는 특별한 `dart:`스키마(scheme)를 가지고 습니다. 다른 라이브러리의 경우 파일 시스템 경로 또는 `package:` 스키마를 사용할 수 있습니다.  `package:` 스키마는 pub 도구와 같은 패키지 관리자가 제공하는 라이브러리를 지정합니다. 예 :

```dart
import 'package:test/test.dart';
```

> **Note:** URI는 유일한(uniform) 자원 식별자를 나타냅니다. URLs (uniform resource locator)는 common kind of URI입니다.

<p id="specifying-a-library-prefix"/>

#### 라이브러리 전위자 지정 (Specifying a library prefix)

충돌하는 식별자가 있는 두 라이브러리를 가져 오는 경우 하나 또는 두 라이브러리의 전위자(prefix)를 지정할 수 있습니다. 예를 들어, library1과 library2에 모두 `Element` 클래스가 있는 경우 다음과 같은 코드가 있을 수 있습니다.

```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

// Uses Element from lib1.
Element element1 = Element();

// Uses Element from lib2.
lib2.Element element2 = lib2.Element();
```

<p id="importing-only-part-of-a-library"/>

#### 라이브러리의 일부를 임포트 (Importing only part of a library)

라이브러리의 일부만 사용하려면 선택적으로 라이브러리를 가져올 수 있습니다. 예 :

```dart
// foo 만을 임포트.
import 'package:lib1/lib1.dart' show foo;

// foo를 제외한 모든 이름을 임포트.
import 'package:lib2/lib2.dart' hide foo;
```

<p id="lazily-loading-a-library"/>

#### 라이브러리의 지연 로딩 (Lazily loading a library)

Deferred loading (lazy loading 이라고도 함)을 사용하면 필요할 때 라이브러리를 로드 할 수 있습니다. 지연로드를 사용할 수 있는 몇 가지 경우가 있습니다.

- 앱의 시작시 초기화 시간을 줄이고자 할때.
- A/B 테스트를 위해 다른 알고리즘을 구현하고 시도 할때.
- 옵션 화면 및 대화 상자와 같이 거의 사용하지 않는 기능을 로드 할때.

라이브러리를 지연 로드하려면 먼저 `deferred as` 를 사용하여 라이브러리를 가져와야 합니다.

```dart
import 'package:greetings/hello.dart' deferred as hello;
```

라이브러리가 필요할때 라이브러리의 식별자를 사용하여 `loadLibrary()`를 호출하세요.

```dart
Future greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```

앞의 코드에서 `await` 키워드는 라이브러리가 로드 될 때까지 실행을 일시 중지합니다. `async`와`await`에 대한 더 자세한 정보는 [asynchrony support](asynchrony_support.md)를 참조하십시오.

라이브러리에서 `loadLibrary()`를 여러번 호출 할 수 있습니다. 라이브러리는 한 번만 로드됩니다.

지연로드를 사용할 때 다음 사항을 유의하십시오.

- 지연 라이브러리의 상수는 임포트 파일의 상수가 아닙니다. 지연 라이브러리가 로드 될 때까지 이러한 상수는 존재하지 않는다는 것을 염두하세요.
- 임포트 파일에서 지연 라이브러리의 타입을 사용할 수 없습니다. 대신 지연 라이브러리와 임포트 파일 모두에서 가져온 라이브러리로 인터페이스 타입을 이동하는 것을 고려해야 합니다.
- 다트는 `loadLibrary()`를 `named as namespace `를 사용하여 정의한 네임 스페이스에 암묵적으로 삽입합니다. `loadLibrary()`함수는 [Future](asynchrony_support.md#handling-future)를 반환합니다.

> **Dart VM difference:** 다트VM은 `loadLibrary()`를 호출하기 전에 지연된 라이브러리의 멤버들에게 접근을 허용 합니다. 이 동작은 변경 될 수 있으므로 **현재 VM 동작에 종속되지 않습니다.** 자세한 내용은 [issue # 33118.]을 참조하십시오.

<p id="implementing-libraries"/>

###  라이브러리 구현하기 (Implementing libraries)

라이브러리 패키지를 구현하는 방법에 대한 조언은 [Create Library Packages](https://dart.dev/guides/libraries/create-library-packages) 를 보세요. 이것은 다음을 포함하고 있습니다:

- 라이브러리 소스 코드를 조직화 하는 방법.
- `export` 지시자를 사용하는 방법.
- `part` 지시자를 사용하는 방법.
- `library` 지시자를 사용하는 시기.

---

이전: [제네릭 (Generics)](./generics.md)

다음: [비동기 지원 (Asynchorony support)](./asynchrony_support.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
