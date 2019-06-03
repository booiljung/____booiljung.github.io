[Up](./index.md)

##  비동기 지원 (Asynchrony support)

Dart 라이브러리는 [Future](#handling-futures) 또는 [Stream](#handling-streams)개체를 반환하는 함수로 가득합니다.  이러한 함수는 **비동기(asynchrony)**입니다. 시간이 많이 소요되는 작업 (예: I/O)을 설정 한 후 해당 작업이 완료 될 때까지 기다리지 않고 반환됩니다.

`async` 및 `await` 키워드는 비동기 프로그래밍을 지원하므로 동기 코드와 비슷한 비동기 코드를 작성할 수 있습니다.

<p id="handling-futres"/>

###  퓨처를 다루는 법 (Handling Futures)

완료된 퓨처의 결과가 필요하면 두 가지 옵션이 있습니다.

-  `async` 와  `await`를 사용합니다.
- 퓨처 API를 사용합니다. 이것은 [in the library tour](https://dart.dev/guides/libraries/library-tour#future)에 설명되어 있습니다.

`async`와 `await`를 사용하는  코드는 비동기적입니다. 하지만 동기식 코드와 비슷합니다. 예를 들어, 다음은 비동기 함수의 결과를 기다리는 `await`을 사용하는 코드입니다:

```dart
await lookUpVersion();
```

`await`을 사용하려면 코드가 `async`로 표시된 비동기 함수에 있어야 합니다.

```dart
Future checkVersion() async {
  var version = await lookUpVersion();
  // version으로 뭔가를 하면 됩니다.
}
```

> **Note:** 비동기 함수는 시간을 소모하는 연산을 수행 할 수 있게 하지만, 이러한 연산을 기다리지는 않습니다. 대신 async 함수는 첫 번째 `await` 표현식을 만나기 전까지만 실행됩니다 ([자세한 내용](https://github.com/dart-lang/sdk/blob/master/docs/newsletter/20170915.md#synchronous-async -스타트)). 그런 다음 `Future` 객체를 반환하고, `await` 표현식이 완료된 후에 실행을 다시 시작합니다.

`try,` `catch`, `finally`를 사용하여 `await`를 사용하는 코드에서 에러와 클린업을 처리하세요:

```dart
try {
  version = await lookUpVersion();
} catch (e) {
  // version을 찾지 못한 것에 대한 반응.
}
```

비동기 함수에서 `await`를 여러 번 사용할 수 있습니다. 예를 들어 다음 코드는 함수 결과를 세 번 기다립니다.

```dart
var entrypoint = await findEntrypoint();
var exitCode = await runExecutable(entrypoint, args);
await flushThenExit(exitCode);
```

`await 표현식`에서 `표현식`의 값은 대개 `Future`입니다; 그렇지 않은 경우 값은 자동으로 `Future`에 래핑됩니다. 이 `Future` 객체는 객체를 반환하겠다는 약속을 나타냅니다. `await 표현식`의 값은 반환된 객체입니다. 표현식을 기다리면 해당 객체를 사용할 수 있을 때까지 실행이 일시 중지됩니다.

**await을 사용할 때 컴파일 타임 오류가 발생하면 비동기 함수를 기다려야합니다.** 예를 들어 앱의 `main()`함수에서`await`을 사용하려면 `main()`의 본문을 `async`로 표시해야 합니다:

```dart
Future main() async {
  checkVersion();
  print('In main: version is ${await lookUpVersion()}');
}
```

<p id="declaring-async-functions"/>

###  비동기 함수 선언 (Declaring async functions)

async 함수는 본문이 `async` 한정자로 표시되는 함수입니다.

함수에 `async` 키워드를 추가하면 `Future`를 리턴 할 수 있습니다. 예를 들어 `String`을 반환하는 동기 함수를 생각해보십시오:

```dart
String lookUpVersion() => '1.0.0';
```

비동기 함수로 변경하면, 예를들어 퓨처 구현은 시간을 소모하기 때문에, 반환 값은 `Future`입니다.

```dart
Future<String> lookUpVersion() async => '1.0.0';
```

함수의 본문에는 퓨처 API를 사용할 필요가 없습니다. Dart는 필요한 경우 `Future` 개체를 만듭니다.

함수가 유용한 값을 반환하지 않으면 반환 형식을 `Future<void>`로 만드세요.

<p id="handling-streams"/>

###  스트림 다루기 (Handling Streams)

스트림에서 값을 가져와야 하는 경우 두 가지 옵션이 있습니다.

- 루프에서 `async`와 `await for`를 사용합니다.
- 스트림 API를 사용합니다. 이것은 [in the library tour](https://dart.dev/guides/libraries/library-tour#stream)에 설명되어 있습니다.

**Note:** `await for`를 사용하기 전에, 코드가 명확 해지고 실제로 모든 스트림 결과를 기다리고 싶는지 확인하십시오. 예를 들어 UI 프레임 워크는 무한한 이벤트 흐름을 보내기 때문에 일반적으로 UI 이벤트 리스너에 대해 `await for`를 사용하지 않아야 합니다.

비동기 for 루프의 형식은 다음과 같습니다.

```dart
await for (varOrType identifier in expression) {
  // 스트림이 값을 내보낼 때마다 실행합니다.
}
```

`expression`의 값은 반드시 `Stream`이어야 합니다. 실행은 다음과 같이 진행됩니다.

1. 스트림에서 값이 나올 때까지 기다립니다.
2. 해당 도출(emitted)된 값에 설정된 변수로 for 루프 본문을 실행하십시오.
3. 스트림이 닫힐 때까지 1과 2를 반복합니다.

스트림을 듣는 것을 멈추려면 for 루프와 unsubscribes를 스트림에서 분리하는 `break` 또는 `return` 문을 사용할 수 있습니다.

**비동기 for 루프를 구현할 때 컴파일 타임 오류가 발생하면 기다리는 것이 비동기 함수인지 확인하십시오.** 예를 들어, 앱의`main()`함수에서 비동기 for 루프를 사용하려면 `main()`의 본문을 `async`로 표시해야 합니다 :

```dart
Future main() async {
  // ...
  await for (var request in requestServer) {
    handleRequest(request);
  }
  // ...
}
```

비동기 프로그래밍에 대한 자세한 내용은 일반적으로 라이브러리 둘러보기의 [dart:async](https://dart.dev/guides/libraries/library-tour#dartasync---asynchronous-programming) 섹션을 참조하십시오.

---

이전: [라이브러리와 가시성 (Libraries and visibility)](./libraries_and_visibility.md)

다음: [제너레이터 (Generators)](./generators.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
