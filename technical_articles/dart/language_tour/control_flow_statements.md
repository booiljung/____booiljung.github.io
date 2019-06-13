[Up](./index.md)

##  흐름 제어문 (Control flow statements)

다음 중 하나를 사용하여 Dart 코드의 흐름을 제어 할 수 있습니다.

-  `if` 와 `else` 
-  `for` 루프
-  `while` 과 `do`-`while` 루프들
-  `break` 와 `continue` 
-  `switch` 와 `case` 
- `assert`

[Exception](exceptions.md)에서 설명한대로 `try-catch`와 `throw`를 사용하여 제어 흐름에 영향을 미칠 수 있습니다.

<p id="if-and-else"/>

###  If 와 else

Dart는 다음 예제에서 볼 수 있듯이 `else` 이 선택적인 `if` 문을 지원합니다. [conditional expression](control-flow-statements.md#conditional-expressions)도 참조하세요.

```dart
if (isRaining()) {
  you.bringRainCoat();
} else if (isSnowing()) {
  you.wearJacket();
} else {
  car.putTopDown();
}
```

JavaScript와 달리 조건은 부울 값을 사용해야 하며 그 밖의 것은 없습니다. 자세한 내용은 [Booleans](variables.md#booleans)를 참조하십시오.

<p id="for-loop"/>

###  For 루프

표준 `for` 루프로 반복 할 수 있습니다. 예 :

```dart
var message = StringBuffer('Dart is fun');
for (var i = 0; i < 5; i++) {
  message.write('!');
}
```

Dart의 for 루프의 클로저는 인덱스의 value를 포착하여 JavaScript에서 흔히 볼 수있는 함정을 피합니다. 예를 들어 다음을 보세요.

```dart
var callbacks = [];
for (var i = 0; i < 2; i++) {
  callbacks.add(() => print(i));
}
callbacks.forEach((c) => c());
```

예상대로 출력은 `0`이고 `1`입니다. 반대로 이 예제는 Dart에서 `2`와`2`를 출력합니다.

반복되는 객체가 반복 가능이라면 [forEach()](https://api.dartlang.org/stable/dart-core/Iterable/forEach.html) 메소드를 사용할 수 있습니다. `forEach()`를 사용하는 것은 현재 반복 카운터를 알 필요가 없는 경우에 좋은 옵션입니다 :

```dart
candidates.forEach((candidate) => candidate.interview());
```

리스트나 세트 같은 iterable 클래스는 for-in 형태의 [iteration](https://dart.dev/guides/libraries/library-tour#iteration)도 지원합니다 :

```dart
var collection = [0, 1, 2];
for (var x in collection) {
  print(x); // 0 1 2
}
```

<p id="while-and-do-while-loop"/>

###  While 과 do-while 루프

`while` 루프는 루프 앞에서 루프 조건을 평가합니다 :

```dart
while (!isDone()) {
  doSomething();
}
```

`do`-`while` 루프는 루프 뒤에서 루프 조건을 평가합니다:

```dart
do {
  printLine();
} while (!atEndOfPage());
```

<p id="break-and-continue"/>

###  Break 와 continue

루프를 중단하기 위해 `break`를 사용하십시오 :

```dart
while (true) {
  if (shutDownRequested()) break;
  processIncomingRequests();
}
```

다음 루프 이터레인션으로 건너 뛰려면 `continue`를 사용하십시오 :

```dart
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}
```

리스트나 세트 같은 [Iterable](https://api.dartlang.org/stable/dart-core/Iterable-class.html)을 사용하는 경우 해당 예제를 다르게 작성할 수 있습니다.

```dart
candidates
    .where((c) => c.yearsExperience >= 5)
    .forEach((c) => c.interview());
```

<p id="switch-and-case"/>

###  Switch 와 case

Dart의 switch 문은 `==`를 사용하여 정수, 문자열 또는 컴파일 타임 상수를 비교합니다. 비교된 객체는 모두 같은 클래스의 인스턴스가 되어야 하며 (그 하위 타입이 아닌) 클래스는 `==`을 오버라이드해서는 안됩니다. [Enumerated types](classes.md#enumerated-types)는 `switch` 문에서 잘 작동합니다.

**Note:** Dart의 `switch` 문은 인터프리터나 스캐너와 같이 제한된 상황에서만 사용할 수 있습니다.

비어 있지 않은 `case` 절은 원칙적으로 `break` 문으로 끝납니다. 비어 있지 않은 `case` 절을 끝내는 다른 유효한 방법은 `continue`, `throw` 또는 `return` 문입니다.

`case` 절이 일치하지 않을 때 코드를 실행하려면 `default` 절을 사용하십시오 :

```dart
var command = 'OPEN';
switch (command) {
  case 'CLOSED':
    executeClosed();
    break;
  case 'PENDING':
    executePending();
    break;
  case 'APPROVED':
    executeApproved();
    break;
  case 'DENIED':
    executeDenied();
    break;
  case 'OPEN':
    executeOpen();
    break;
  default:
    executeUnknown();
}
```

다음 예제는 `case` 절의 `break` 문을 생략하여 오류를 생성합니다 :

```dart
var command = 'OPEN';
switch (command) {
  case 'OPEN':
    executeOpen();
    // ERROR: break를 빠뜨림.

  case 'CLOSED':
    executeClosed();
    break;
}
```

그러나 Dart는 빈 `case` 절을 지원하여 fall-through의 한 형태를 허용합니다.

```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED': // 빈 cas는 falls through 함.
  case 'NOW_CLOSED':
    // CLOSED와 NOW_CLOSED case 둘다 실행됨.
    executeNowClosed();
    break;
}
```

fall-through를 정말로 원한다면, `continue` 문과 레이블을 사용할 수 있습니다 :

```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
    executeClosed();
    continue nowClosed;
  // nowClosed 레이블로 계속 실행 됨.

  nowClosed:
  case 'NOW_CLOSED':
    // CLOSED 와 NOW_CLOSED 둘다 실행 됨.
    executeNowClosed();
    break;
}
```

`case` 절은 그 절의 범위 안에서만 볼 수있는 지역 변수를 가질 수 있습니다.

<p id="assert"/>

###  Assert

부울 조건이 거짓 인 경우 정상 실행을 중단 시키려면 `assert` 문을 사용하세요. 이 예제에서 `assert` 문구의 예를 찾을 수 있습니다. 여기에 더 많은 것들이 있습니다:

```dart
// Make sure the variable has a non-null value.
assert(text != null);

// Make sure the value is less than 100.
assert(number < 100);

// Make sure this is an https URL.
assert(urlString.startsWith('https'));
```

**Note:** Assert 문은 프로덕션 코드에 영향을주지 않습니다. 그들은 단지 개발 용입니다. Flutter는 [debug mode](https://flutter.dev/docs/testing/debugging#debug-mode-assertions)에서 assertion 을 활성화합니다 [dartdevc](https://dart.dev/tools/dartdevc)와 같은 개발 전용 도구는 일반적으로 기본적으로 어설션을 지원합니다. [dart](https://dart.dev/server/tools/dart-vm) 및 [dart2js](https://dart.dev/tools/dart2js)와 같은 일부 도구는 명령 줄 플래그를 통해 어설션을 지원합니다:`--enable-asserts`.

메시지를 어설션에 첨부하려면 문자열을 두 번째 인수로 추가하십시오.

```dart
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

`assert`의 첫 번째 인수는 부울 값으로 해석되는 모든 표현식이 될 수 있습니다. 표현식의 값이 `true`이면 어설션이 성공하고 실행이 계속됩니다. false 인 경우, 어설션이 실패하고 예외 ([AssertionError](https://api.dartlang.org/stable/dart-core/AssertionError-class.html))가 발생합니다.

---

이전: [연산자 (Operators)](./operators.md)

다음: [예외 (Exceptions)](./exceptions.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
