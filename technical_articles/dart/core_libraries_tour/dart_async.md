[Up](./index.md)

## dart:async - 비동기 프로그래밍

원문: [A tour of the core libraries](https://dart.dev/guides/libraries/library-tour)

비동기 프로그래밍은 종종 콜백 함수를 사용하지만 Dart는 [`Future`](https://api.dartlang.org/stable/dart-async/Future-class.html)와 [`Stream`](https://api.dartlang .org/stable/dart-async/Stream-class.html) 객체를 사용합니다. `Future`란 결과가 미래에 언젠가 제공 될 것이라는 약속과 같습니다. `Stream`은 이벤트와 같은 일련의 값을 가져 오는 방법입니다. `Future`, `Stream` 등은 dart:async 라이브러리 ([API Reference](https://api.dartlang.org/stable/dart-async/dart-async-library.html))에 있습니다.

**Note:** `Future` 또는 `Stream` API를 직접 사용할 필요는 없습니다. Dart 언어는 `async` 및 `await`와 같은 키워드를 사용하여 비동기 코딩을 지원합니다. 자세한 내용은 language tour 에서 [asynchrony support](https://dart.dev/guides/language/language-tour#asynchrony-support)을 참조하십시오.

dart:async 라이브러리는 웹 앱과 커맨드라인 앱 모두에서 작동합니다. 그것을 사용하려면

```dart
import 'dart:async';
```

를 하세요.

**Version note:** Dart 2.1부터는 dart:core가 해당 클래스를 export 하기 때문에 `Future` 및 `Stream` API를 사용하기 위해 dart:async를 `import` 할 필요가 없습니다.

###  Future

`Future`의 객체는 Dart 라이브러리 전체에 나타나며 종종 비동기 메소드에 의해 반환 된 객체로 나타납니다. `Future 완료!, 그 가치를 사용할 준비가 되었습니다.

#### await 사용하기 (Using await)

Future API를 직접 사용하기 전에 대신 `await`을 사용해보십시오. `await` 표현식을 사용하는 코드는 `Future` API를 사용하는 코드보다 이해하기 쉽습니다.

다음 함수를 고려하십시오. `Future`의 `then()`메소드를 사용하여 한 행에 세 개의 비동기 함수를 실행하고, 다음 함수를 실행하기 전에 각 함수가 완료 될 때까지 기다립니다.

```dart
runUsingFuture() {
  // ...
  findEntryPoint().then((entryPoint) {
    return runExecutable(entryPoint, args);
  }).then(flushThenExit);
}
```

표현식을 기다리는 동치 코드(equivalent code)는 동기 코드(synchronous code)와 유사합니다.

```dart
runUsingAsyncAwait() async {
  // ...
  var entryPoint = await findEntryPoint();
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
}
```

`aysnc` 함수는 `Future` 에서 예외를 `catch` 할 수 있습니다. 예:

```dart
var entryPoint = await findEntryPoint();
try {
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
} catch (e) {
  // Handle the error...
}
```

**Important:** 비동기 함수는 퓨쳐를 반환합니다. 함수가 퓨처를 반환하지 않게 하려면 다른 솔루션을 사용하세요. 예를 들어 함수에서 비동기 함수를 호출 할 수 있습니다.

`await`과 관련 Dart 언어 기능에 대한 더 자세한 정보는 [Asynchrony support](../language_tour/asynchrony_support.md)를 참조하십시오.

#### 기본 사용법 (Basic usage)

`then()`을 사용하여 `Future`가 완료 될 때 실행 되는 코드를 스케줄 할 수 있습니다. 예를 들어, `HttpRequest.getString()`은 HTTP 요구가 오래 걸리기 때문에 `Future`를 반환합니다. `then()`을 사용하면 `Future`가 완료되고 약속 된 문자열 값을 사용할 수 있을 때 몇 가지 코드를 실행할 수 있습니다:

```dart
HttpRequest.getString(url).then((String result) {
  print(result);
});
```

`Future` 객체가 던질 수 있는 오류나 예외를 처리하려면 `catchError()`를 사용하십시오.

```dart
HttpRequest.getString(url).then((String result) {
  print(result);
}).catchError((e) {
  // Handle or ignore the error.
});
```

`then().catchError()`패턴은 `try`-`catch`의 비동기 버전입니다.

**Important:** 최초의 `Future`의 결과에 대해서 `then()`의 결과에 대해 `catchError()`를 호출하세요. 그렇지 않으면`catchError()`는 원래 `Future`의 계산에서만 오류를 처리 할 수 있지만 `then()`에 의해 등록 된 핸들러에서는 오류를 처리 할 수 없습니다.

#### 여러 비동기 메서드를 연쇄하기 (Chaining multiple asynchronous methods)

`then()`메서드는 `Future`를 반환하여 특정 순서로 여러 비동기 함수를 실행하는 유용한 방법을 제공합니다. `then()`에 등록 된 콜백이 `Future`를 반환하면 `then()`은 동등한 `Future`를 반환합니다. 콜백이 다른 타입의 값을 반환하면 `then()`은 그 값으로 완료(complete)되는 새로운 `Future`를 생성합니다.

```dart
Future result = costlyQuery(url);
result
    .then((value) => expensiveWork(value))
    .then((_) => lengthyComputation())
    .then((_) => print('Done!'))
    .catchError((exception) {
  /* Handle exception... */
});
```

앞의 예제에서 메서드는 다음 순서로 실행됩니다.

1. `costlyQuery()`
2. `expensiveWork()`
3. `lengthyComputation()`

다음은 `await`를 사용하여 작성된 동일한 코드입니다.

```dart
try {
  final value = await costlyQuery(url);
  await expensiveWork(value);
  await lengthyComputation();
  print('Done!');
} catch (e) {
  /* Handle exception... */
}
```

#### 다수의 퓨처를 기다리는 방법 (Waiting for multiple futures)

때때로 알고리즘은 많은 비동기 함수를 호출하고, 계속 진행하기 전에 모두 완료 될 때까지 기다려야합니다. [`Future.wait()`](https://api.dartlang.org/stable/dart-async/Future/wait.html) static 메소드를 사용하여 여러 퓨처를 관리하고 완료 될 때까지 기다리세요.

```dart
Future deleteLotsOfFiles() async =>  ...
Future copyLotsOfFiles() async =>  ...
Future checksumLotsOfOtherFiles() async =>  ...

await Future.wait([
  deleteLotsOfFiles(),
  copyLotsOfFiles(),
  checksumLotsOfOtherFiles(),
]);
print('Done with all the long steps!');
```

###  Stream

`Stream` 객체는 Dart API 전체에 나타나며 데이터 시퀀스를 나타냅니다. 예를 들어 버튼 클릭과 같은 HTML 이벤트는 `Stream`을 사용하여 전달됩니다. 파일을 `Stream`으로 읽을 수도 있습니다.

#### 루프에서 비동기 사용하기 (Using an asynchronous for loop)

때로는 스트림 API를 사용하는 대신 비동기 `for` 루프 ( `await for`)를 사용할 수 있습니다.

다음 함수를 보겠습니다. `Stream`의`listen()`메서드를 사용하여 파일 목록을 구독하고 각 파일이나 디렉토리를 검색하는 함수 리터럴을 전달합니다.

```dart
void main(List<String> arguments) {
  // ...
  FileSystemEntity.isDirectory(searchPath).then((isDir) {
    if (isDir) {
      final startingDir = Directory(searchPath);
      startingDir
          .list(
              recursive: argResults[recursive],
              followLinks: argResults[followLinks])
          .listen((entity) {
        if (entity is File) {
          searchFile(entity, searchTerms);
        }
      });
    } else {
      searchFile(File(searchPath), searchTerms);
    }
  });
}
```

비동기 for 루프 (`await for`)를 포함하여 표현식을 기다리는 코드는 동기 코드와 비슷합니다.

```dart
Future main(List<String> arguments) async {
  // ...
  if (await FileSystemEntity.isDirectory(searchPath)) {
    final startingDir = Directory(searchPath);
    await for (var entity in startingDir.list(
        recursive: argResults[recursive],
        followLinks: argResults[followLinks])) {
      if (entity is File) {
        searchFile(entity, searchTerms);
      }
    }
  } else {
    searchFile(File(searchPath), searchTerms);
  }
}
```

**Important:** `await for`를 사용하기 전에, 코드가 명확하고 스트림 결과 전체를 기다리고 싶는지 확인하세요. 예를 들어 DOM은 끝없이 발생하는 이벤트 스트림을 보내기 때문에 대개 DOM 이벤트 리스너에 대해 `await for`를 사용하지 않아야 합니다.  `await for`를 사용하여 두 개의 DOM 이벤트 리스너를 한 행에 등록하면 두 번째 종류의 이벤트는 절대로 처리되지 않습니다.

`await`과 관련 Dart 언어 기능에 대한 더 자세한 정보는 [Asynchrony support](../language_tour/asynchrony_support.md)를 참조하십시오.

#### 스트림 데이터 수신 하기 (Listening for stream data)

도착할 때마다 값을 얻으려면 `await for`를 사용하거나 `listen()`메소드를 사용하여 스트림에 등록하세요:

```dart
// ID로 버튼을 찾고 이벤트 처리기를 추가하세요..
querySelector('#submitInfo').onClick.listen((e) {
  // 버튼을 클릭하면 이 코드가 실행됩니다.
  submitData();
});
```

이 예제에서, `onClick` 속성은 "submitInfo" 버튼에 의해 제공되는 `Stream` 객체입니다.

하나의 이벤트에만 관심이 있다면 `first`, `last` 또는 `single`과 같은 속성을 사용하여 얻을 수 있습니다. 처리하기 전에 이벤트를 테스트하려면 `firstWhere()`, `lastWhere()` 또는 `singleWhere()`와 같은 메소드를 사용하십시오.

이벤트의 서브 세트에 관심이 있다면 `skip()`, `skipWhile()`, `take()`, `takeWhile()`, `where()`와 같은 메소드를 사용할 수 있습니다.

#### 스트림 데이터 변환 (Transforming stream data)

스트림 데이터를 사용하려면 먼저 스트림의 데이터 형식을 변경해야합니다. `transform()`메서드를 사용하여 다른 타입의 데이터를 가진 스트림을 생성하세요:

```dart
var lines = inputStream
    .transform(utf8.decoder)
    .transform(LineSplitter());
```

이 예제에서는 두 개의 변환기(transformer)를 사용합니다. 먼저 `utf8.decoder`를 사용하여 정수 스트림을 문자열 스트림으로 변환합니다. 그런 다음 `LineSplitter`를 사용하여 문자열 스트림을 별도의 줄로 변환합니다. 이 변환기는 dart:convert에서 가져온 것입니다 ([dart:convert section](https://dart.dev/guides/libraries/library-tour#dartconvert---decoding-and-encoding-json-utf-8-and-more)를 보세요.)

#### 에러 및 완료 다루기 (Handling errors and completion)

오류 및 완료 처리 코드를 지정하는 방법은 비동기 for 루프 (`await for`)를 사용하는지 Stream API를 사용하는지에 따라 다릅니다.

비동기 for 루프를 사용하는 경우 try-catch를 사용하여 오류를 처리하세요. 스트림이 닫힌 후에 실행되는 코드는 비동기 for 루프 후에 진행 됩니다.

```dart
Future readFileAwaitFor() async {
  var config = File('config.txt');
  Stream<List<int>> inputStream = config.openRead();

  var lines = inputStream
      .transform(utf8.decoder)
      .transform(LineSplitter());
  try {
    await for (var line in lines) {
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
  } catch (e) {
    print(e);
  }
}
```

스트림 API를 사용하는 경우 `onError`리스너를 등록하여 오류를 처리하십시오. `onDone` 리스너를 등록하여 스트림이 닫힌 후에 코드를 실행하십시오.

```dart
var config = File('config.txt');
Stream<List<int>> inputStream = config.openRead();

inputStream
    .transform(utf8.decoder)
    .transform(LineSplitter())
    .listen((String line) {
  print('Got ${line.length} characters from stream');
}, onDone: () {
  print('file is now closed');
}, onError: (e) {
  print(e);
});
```

###  추가 정보 (More information)

명령 줄 앱에서 Future와 Stream을 사용하는 몇 가지 예를 보려면 [dart:io tour](https://dart.dev/guides/libraries/library-tour#dartio)를 참조하십시오. 또한 다음 글 및 자습서를 참조하십시오.

- [Asynchronous Programming: Futures](https://dart.dev/tutorials/language/futures)
- [Futures and Error Handling](https://dart.dev/guides/libraries/futures-error-handling)
- [Asynchronous Programming: Streams](https://dart.dev/tutorials/language/streams)
- [Creating Streams in Dart](https://dart.dev/articles/libraries/creating-streams)