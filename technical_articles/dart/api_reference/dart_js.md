# dart:js libarary

원문: [dart:js library](https://api.dartlang.org/stable/2.3.1/dart-js/dart-js-library.html)

JavaScript 와의 상호 운용 지원.

이 라이브러리는 Dart의 JavaScript 객체에 대한 액세스를 제공하여 Dart 코드가 속성을 가져오고 설정하고 JavaScript 객체의 메서드를 호출하고 JavaScript 함수를 호출 할 수 있게 합니다. 라이브러리는 가능한 경우 Dart와 JavaScript 객체간의 변환을 처리하거나 변환이 불가능할 경우 프록시를 제공합니다.

이 라이브러리는 아직 Dart 객체를 JavaScript에서 사용하게 할 수 없습니다. Dart 함수를 JavaScript로 전달하고 호출 할 수 있게 하지만, 메소드와 프로퍼티에 액세스 할 수 없습니다.

[`JsObject`](https://api.dartlang.org/stable/2.3.1/dart-js/JsObject-class.html)는 핵심 타입이며 JavaScript 객체의 프록시를 나타냅니다. `JsObject`는 기본 JavaScript 객체의 속성 및 메서드에 대한 액세스를 제공합니다. `JsObject`는 JavaScript 호출을 통해 얻을 수 있거나 프록시에서 JavaScript 생성자로 생성 될 수 있습니다.

최상위 getter [context](https://api.dartlang.org/stable/2.3.1/dart-js/context.html)는 [`JsObject`](https://api.dartlang.org/stable/2.3.1/dart-js/JsObject-class.html)를 제공합니다. 일반적으로`window`를 사용합니다.

다음 예제는 JavaScript 호출을 통해 `alert()` 전역 함수에 대한 경고 대화 상자를 보여줍니다 :

```dart
import 'dart:js';

main() => context.callMethod('alert', ['Hello from Dart!']);
```

다음 예제는 JavaScript 생성자에서 [JsObject](https://api.dartlang.org/stable/2.3.1/dart-js/JsObject-class.html)를 만들고 해당 속성에 액세스하는 방법을 보여줍니다.

```dart
import 'dart:js';

main() {
  var object = new JsObject(context['Object']);
  object['greeting'] = 'Hello';
  object['greet'] = (name) => "${object['greeting']} $name";
  var message = object.callMethod('greet', ['JavaScript']);
  context['console'].callMethod('log', [message]);
}
```

## Proxying and automatic conversion

When setting properties on a JsObject or passing arguments to a Javascript method or function, Dart objects are automatically converted or proxied to JavaScript objects. When accessing JavaScript properties, or when a Dart closure is invoked from JavaScript, the JavaScript objects are also converted to Dart.

Functions and closures are proxied in such a way that they are callable. A Dart closure assigned to a JavaScript property is proxied by a function in JavaScript. A JavaScript function accessed from Dart is proxied by a [JsFunction](https://api.dartlang.org/stable/2.3.1/dart-js/JsFunction-class.html), which has a `apply` method to invoke it.

The following types are transferred directly and not proxied:

- Basic types: `null`, `bool`, `num`, `String`, `DateTime`
- `TypedData`, including its subclasses like `Int32List`, but *not* `ByteBuffer`
- When compiling for the web, also: `Blob`, `Event`, `ImageData`, `KeyRange`, `Node`, and `Window`.

`JsObject`에서 속성을 설정하거나 `Javascript` 메서드나 함수에 인수를 전달할 때 Dart 객체는 JavaScript 객체로 자동 변환되거나 프록시 됩니다. JavaScript 속성에 액세스하거나 JavaScript에서 Dart 클로저가 호출되면 JavaScript 객체도 Dart로 변환됩니다.

함수와 클로저는 호출 가능한 방식으로 프록시화(proxied) 됩니다. JavaScript 속성에 할당된 Dart 클로저는 JavaScript의 함수로 프록시 처리됩니다. Dart에서 접근한 JavaScript 함수는 [JsFunction](https://api.dartlang.org/stable/2.3.1/dart-js/JsFunction-class.html)에 의해 프록시되고, 이 함수는 그것을 호출하기 위한 `apply` 메소드를 가지고 습니다.

다음 유형은 직접 전송되며 프록시되지 않습니다.

- 기본 타입: `null`, `bool`, `num`, `String`, `DateTime`
- `TypedData`: `Int32List`와 같은 서브 클래스를 포함하지만  `ByteBuffer`는 아님.
웹용으로 컴파일 할 때 `Blob`, `Event`,` ImageData`, `KeyRange`, `Node`, `Window`도 있습니다.

## JsObject.jsify()으로 콜렉션을 컨버트 하기 (Converting collections with JsObject.jsify())

Dart 콜렉션에서 JavaScript 콜렉션을 작성하려면 [JsObject.jsify](https://api.dartlang.org/stable/2.3.1/dart-js/JsObject/JsObject.jsify.html) 생성자를 사용하세요. 이 생성자는 Dart [Map](https://api.dartlang.org/stable/2.3.1/dart-core/Map-class.html) 및 [Iterable](https://api.dartlang.org/stable/2.3. 1/dart-core / Iterable-class.html)을 JavaScript 객체 및 배열로 변환합니다.

다음 표현식은 `a` 및 `b` 속성이 정의된 새 JavaScript 객체를 만듭니다.

```dart
var jsMap = new JsObject.jsify({'a': 1, 'b': 2});
```

이 표현식은 JavaScript 배열을 만듭니다.

```dart
var jsArray = new JsObject.jsify([1, 2, 3]);
```

## Classes

- [`JavaScriptObject`](https://api.dartlang.org/stable/2.3.1/dart-js/JavaScriptObject-class.html): JavaScript 객체에 대한 인터셉터 기본 클래스 이며, 좀 더 고유한 네이티브 타입으로 인식되지 않습니다.

- [`JsArray<E>`](https://api.dartlang.org/stable/2.3.1/dart-js/JsArray-class.html): JavaScript 배열을 프록시하는 [List](https://api.dartlang.org/stable/2.3.1/dart-core/List-class.html)        
- [`JsFunction`](https://api.dartlang.org/stable/2.3.1/dart-js/JsFunction-class.html): JavaScript 함수를 프록시.    
- [JsObject](https://api.dartlang.org/stable/2.3.1/dart-js/JsObject-class.html): JavaScript 개체를 Dart로 프록시.

## Properties

- [context](https://api.dartlang.org/stable/2.3.1/dart-js/context.html) → [JsObject](https://api.dartlang.org/stable/2.3.1/dart-js/JsObject-class.html): final

## Functions

- [allowInterop](https://api.dartlang.org/stable/2.3.1/dart-js/allowInterop.html)<F extends [Function](https://api.dartlang.org/stable/2.3.1/dart-core/Function-class.html)>(F f)→ F                              

  ​                               

- [allowInteropCaptureThis](https://api.dartlang.org/stable/2.3.1/dart-js/allowInteropCaptureThis.html)([Function](https://api.dartlang.org/stable/2.3.1/dart-core/Function-class.html) f)→ [Function](https://api.dartlang.org/stable/2.3.1/dart-core/Function-class.html)                              