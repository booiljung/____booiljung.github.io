# dart:async library 

원문: [dart:async](https://translate.google.com/#view=home&op=translate&sl=auto&tl=ko&text=%E0%BA%9D%E0%BA%B1%E0%BA%99%E0%BA%88%E0%BA%B0%E0%BB%84%E0%BA%9B%E0%BA%8A%E0%BA%B7%E0%BB%89%E0%BA%A5%E0%BA%AD%E0%BA%94%E0%BB%80%E0%BA%95%E0%BA%B5%E0%BA%A5%E0%BA%B5%E0%BB%88%E0%BA%81%E0%BB%8D%E0%BB%88%E0%BA%9A%E0%BB%8D%E0%BB%88%E0%BA%A1%E0%BA%B5%E0%BB%80%E0%BA%A7%E0%BA%A5%E0%BA%B2%E0%BB%84%E0%BA%9B)

Future 및 Stream과 같은 클래스를 사용한 비동기 프로그래밍 지원.

[Future](https://api.dartlang.org/stable/2.3.1/dart-async/Future-class.html) 및 [Stream](https://api.dartlang.org/stable/2.3.1/dart-async/Stream-class.html)에 대한 이해는 다트 프로그램에 관한 글을 작성하기 위한 전제 조건입니다.

코드에서 이 라이브러리를 사용하려면 다음을 수행하세요.

```dart
import 'dart:async';
```

## Future

Future 오브젝트는, 반환 값이 아직 이용 가능하지 않은 계산을 나타냅니다. Future는 미래의 어느 시점에 완료 될 때 계산의 가치를 반환합니다. Future은 종종 I/O 및 사용자와의 상호 작용 같은 잠재적으로 긴 계산에 사용됩니다.

Dart 라이브러리의 많은 메소드는 작업 수행시 Future을 반환합니다. 예를 들어 `HttpServer`를 호스트와 포트에 바인딩 할 때 `bind()`메소드는 `Future`를 반환합니다.

```dart
 HttpServer.bind('127.0.0.1', 4444)
     .then((server) => print('${server.isBroadcast}'))
     .catchError(print);
```

[Future.then](https://api.dartlang.org/stable/2.3.1/dart-async/Future/then.html)는 Future의 작업이 실행될 때 콜백 함수를 등록합니다. 이 경우에는 `bind()`메소드가 성공적으로 완료됩니다. 작업에서 반환 된 값은 콜백 함수로 전달됩니다. 이 예제에서, `bind()` 메소드는 `HttpServer` 객체를 리턴 합니다. 콜백 함수는 해당 속성 중 하나를 인쇄합니다. [`Future.catchError`](https://api.dartlang.org/stable/2.3.1/dart-async/Future/catchError.html)는 Future에 오류가 발생할 경우 실행되는 콜백 함수를 등록합니다.

## Stream

스트림은 비동기 데이터 시퀀스를 제공합니다. 데이터 시퀀스의 예로는 마우스 클릭과 같은 개별 이벤트 또는 마우스 클릭과 같은 파일의 내용이 포함 된 여러 바이트 목록과 파일에서 읽은 바이트 목록 스트림과 같은 더 큰 데이터 순차 청크가 있습니다. 다음 예제는 읽을 파일을 엽니다. [`Stream.listen`](https://api.dartlang.org/stable/2.3.1/dart-async/Stream/listen.html)은 더 많은 데이터를 사용할 수 있게 될 때마다 실행되는 콜백 함수를 등록합니다.

```dart
Stream<List<int>> stream = new File('quotes.txt').openRead();
stream.transform(utf8.decoder).listen(print);
```

스트림은 일련의 바이트 목록을 방출합니다. 프로그램은 바이트를 해석하거나 원시 바이트 데이터를 처리해야 합니다. 이 코드는 UTF-8 디코더 (`dart:convert` 라이브러리에서 제공)를 사용하여 바이트 시퀀스를 Dart 문자열 시퀀스로 변환합니다.

스트림의 또 다른 일반적인 용도는 웹 애플리케이션에서 사용자가 생성한 이벤트입니다. 다음 코드는 버튼에 대한 마우스 클릭을 수신합니다.

```dart
querySelector('#myButton').onClick.listen((_) => print('Click.'));
```

## Other resources

- The [dart:async section of the library tour](https://www.dartlang.org/docs/dart-up-and-running/ch03.html#dartasync---asynchronous-programming): A brief overview of asynchronous programming.
- [Use Future-Based APIs](https://www.dartlang.org/docs/tutorials/futures/): A closer look at Futures and how to use them to write asynchronous Dart code.
- [Futures and Error Handling](https://www.dartlang.org/articles/futures-and-error-handling/): Everything you wanted to know about handling errors and exceptions when working with Futures (but were afraid to ask).
- [The Event Loop and Dart](https://www.dartlang.org/articles/event-loop/): Learn how Dart handles the event queue and microtask queue, so you can write better asynchronous code with fewer surprises.
- [test package: Asynchronous Tests](https://pub.dartlang.org/packages/test): How to test asynchronous code.

