[Up](./index.md)

## dart:math - math and random

원문: [A tour of the core libraries](https://dart.dev/guides/libraries/library-tour)

dart:math 라이브러리 ([API Reference](https://api.dartlang.org/stable/dart-math/dart-math-library.html))는 사인 및 코사인, 최대 및 최소와 같은 공통 기능을 제공합니다. *pi* 및 *e*와 같은 상수. 수학 라이브러리의 대부분의 기능은 최상위 함수로 구현됩니다.

앱에서이 라이브러리를 사용하려면 dart:math를 임포트 하세요.

```dart
import 'dart:math';
```

###  삼각함수 (Trigonometry)

Math 라이브러리는 기본적인 삼각 함수를 제공합니다.

```dart
// Cosine
assert(cos(pi) == -1.0);

// Sine
var degrees = 30;
var radians = degrees * (pi / 180);
// radians is now 0.52359.
var sinOf30degrees = sin(radians);
// sin 30° = 0.5
assert((sinOf30degrees - 0.5).abs() < 0.01);
```

**Note:** 이 함수는 각도가 아닌 라디안을 사용합니다!

###  최대 및 최소 (Maximum and minimum)

Math 라이브러리는 `max()`와 `min()`메소드를 제공합니다:

```dart
assert(max(1, 1000) == 1000);
assert(min(1, -1000) == -1000);
```

###  수학 상수 (Math constants)

수학 라이브러리에서 상수 (*pi*, *e* 등)를 확인하세요.

```dart
// See the Math library for additional constants.
print(e); // 2.718281828459045
print(pi); // 3.141592653589793
print(sqrt2); // 1.4142135623730951
```

###  난수 (Random numbers)

[`Random`](https://api.dartlang.org/stable/dart-math/Random-class.html) 클래스로 난수를 생성하세요. 임의로 `Random` 생성자에 시드를 제공 할 수 있습니다.

```dart
var random = Random();
random.nextDouble(); // Between 0.0 and 1.0: [0, 1)
random.nextInt(10); // Between 0 and 9.
```

임의의 부울 값을 생성 할 수도 있습니다.

```dart
var random = Random();
random.nextBool(); // true or false
```

###  추가 정보 (More information)

메소드의 전체 목록을 보려면 [Math API reference](https://api.dartlang.org/stable/dart-math/dart-math-library.html)를 참조하십시오. [num](https://api.dartlang.org/stable/dart-core/num-class.html), [int](https://api.dartlang.org/stable/dart-core/int-class.html) 및 [double](https://api.dartlang.org/stable/dart-core/double-class.html)에 대한 API Reference도 참조하세요.