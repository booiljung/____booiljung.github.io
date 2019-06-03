[Up](./index.md)

##  예외 (Exceptions)

Dart 코드는 예외를 던지고 잡을 수 있습니다. 예외는 예상치 못한 일이 발생했음을 나타내는 오류를 나타냅니다. 예외를 잡지(catch) 않으면, 예외를 발생시킨 격리(isolate)가 일시 중단(suspend)되며, 일반적으로 격리(isolate) 및 해당 프로그램이 종료됩니다.

Java와 달리 Dart의 모든 예외는 unchecked exceptions입니다. 메서드는 throw 할 수 있는 예외를 선언하지 않으며 예외를 catch 할 필요가 없습니다.

Dart는 [Exception](https://api.dartlang.org/stable/dart-core/Exception-class.html)과 [Error](https://api.dartlang.org/stable/dart-core/Error)를 제공합니다. 물론 할 수 있다면 예외를 정의할 수 있습니다. 그러나 다트 프로그램은 Exception 및 Error 객체뿐만 아니라 모든 null이 아닌 객체를 예외로 throw 할 수 있습니다.

<p id="throw"/>

###  throw

다음은 예외를 throw하거나 raise 하는 예제입니다.

```dart
throw FormatException('Expected at least 1 section');
```

임의의 객체를 throw 할 수도 있습니다.

```dart
throw 'Out of llamas!';
```

**Note:** 프로덕션 수준의 코드는 일반적으로 [Error](https://api.dartlang.org/stable/dart-core/Error-class.html) 또는 [Exception](https://api.dartlang.org/stable/dart-core/Exception-class.html)를 구현하는 타입을 throw합니다.

예외를 던지기 (throwing)는 표현식이므로, `=>` 명령문에서 뿐만 아니라 표현식을 허용하는 다른 곳에서도 예외를 던질 수 있습니다 :

```dart
void distanceTo(Point other) => throw UnimplementedError();
```

<p id="catch"/>

###  catch

예외를 catch 하거나 캡처하면 예외를 다시 발생시키지 않는 한 전파되는 것을 중지합니다. 예외를 catch하면 다룰 수 있습니다.

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

둘 이상의 예외 타입을 던질 수 있는 코드를 처리하려면 여러 `catch` 절을 지정할 수 있습니다. throw 된 객체의 타입과 일치하는 첫 번째 `catch` 절이 예외를 처리합니다. `catch` 절이 타입을 지정하지 않으면 해당 절은 모든 타입의 객체를 처리 할 수 있습니다.

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 지정한 타입의 예외
  buyMoreLlamas();
} on Exception catch (e) {
  // 그 밖의 예외
  print('Unknown exception: $e');
} catch (e) {
  // 지정된 타입 없음, 모두 처리
  print('Something really unknown: $e');
}
```

역자주: 위 예제에서 9번째 라인처럼 모든 예외를 잡는 코드는 위험하며, 처리되지 않은 예외는 반드시 다시 던질 것을 권합니다.

앞의 코드에서 볼 수 있듯이 `on` 또는 `catch` 또는 둘 다 사용할 수 있습니다. 예외 타입을 지정할 필요가 있을 때 `on`을 사용하십시오. 예외 처리기가 예외 객체를 필요로 할 때 `catch`를 사용하십시오.

`catch()`에 하나 또는 두 개의 매개 변수를 지정할 수 있습니다. 첫 번째 예외는 throw 된 예외이며 두 번째는 `StackTrace`입니다 ([StackTrace](https://api.dartlang.org/stable/dart-core/StackTrace-class.html) 개체).

```dart
try {
  // ···
} on Exception catch (e) {
  print('Exception details:\n $e');
} catch (e, s) {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
```

예외를 부분적으로 처리하려면 예외를 전파하는 동안 `rethrow` 키워드를 사용하십시오.

```dart
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // Runtime error
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    rethrow; // Allow callers to see the exception.
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
```

<p id="finally"/>

###  Finally

예외가 발생했는지 여부에 관계없이 일부 코드가 실행되도록하려면 `finally` 절을 사용하십시오. 예외와 일치하는 `catch` 절이 없으면 `finally` 절이 실행 된 후에 예외가 전파됩니다.

```dart
try {
  breedMoreLlamas();
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}
```

`finally` 절은 일치하는`catch` 절 이후에 실행됩니다 :

```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // Handle the exception first.
} finally {
  cleanLlamaStalls(); // Then clean up.
}
```

라이브러리 둘러보기의 [Exception](exceptions.md) 섹션을 읽어보세요.

---

이전: [흐름 제어문 (Control flow statements)](./control_flow_statements.md)

다음: [클래스 (Classes)](./classes.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
