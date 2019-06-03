[Up](./index.md)

## 함수 (Functions)

Dart는 진정한 객체 지향 언어이므로 함수도 객체이며 타입이 `Function`입니다. 즉, 함수를 변수에 할당하거나 다른 함수에 인수로 전달할 수 있습니다. Dart 클래스의 인스턴스를 함수처럼 호출 할 수도 있습니다. 자세한 내용은 Callable 클래스를 참조하세요.

다음은 함수 구현의 예입니다.

```dart
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

**Effective Dart**는 [type annotations for public APIs](https://dart.dev/guides/language/effective-dart/design#prefer-type-annotating-public-fields-and-top-level-variables-if-the-type-isnt-obvious) 를 권장하지만이 타입을 생략하여도 함수가 계속 작동합니다.

```dart
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

표현식이 하나 뿐인 함수의 경우 약식 구문을 사용할 수 있습니다.

```dart
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

`=> 표현식` 문법은`{ return 표현식; }`의 약식입니다. `=>`표기법은 때로 화살 구문(arrow syntax)이라고도 합니다.

---

함수는 필수 매개 변수와 선택적 매개 변수의 두 가지 유형의 매개 변수를 가질 수 있습니다. 필요한 매개 변수가 먼저 나열되고 그 뒤에 선택적 매개 변수가 나열됩니다. 명명된 선택적 매개 변수는 `@required`로 표시 될 수도 있습니다. 자세한 내용은 다음 섹션을 참조하십시오.

<p id="optional-parameters"/>

#### 선택적 매개 변수 (Optional parameters)

선택적 매개 변수는 위치 지정 또는 이름 지정 중 하나 일 수 있지만 둘 다를 지정할 수는 없습니다.

<p id="optional-named-parameters"/>

##### 선택적 명명 된 매개 변수 (Optional named parameters)

함수를 호출 할 때 `paramName:value`를 사용하여 명명된 매개변수를 지정할 수 있습니다. 예를 들면:

```dart
enableFlags(bold: true, hidden: false);
```

함수를 정의 할 때 `{param1, param2, ...}`를 사용하여 명명된 매개 변수를 지정하세요 :

```dart
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold, bool hidden}) {...}
```

[Flutter](https://flutter.dev) 인스턴스 생성 표현식은 복잡해질 수 있으므로 위젯 생성자는 명명된 매개변수를 독점적으로 사용합니다. 이렇게하면 인스턴스 작성 표현식을 읽기 쉽게 만듭니다.

[`@required`](https://pub.dev/documentation/meta/latest/meta/required-constant.html)를 사용하여 임의의 Dart 코드 (Flutter뿐 아니라)에서 명명 된 매개변수에 주석을 달아 필수 매개 변수임을 나타낼 수 있습니다. 예 :

```dart
const Scrollbar({Key key, @required Widget child})
```

`Scrollbar`가 생성 될 때, 분석기는 `child` 인자가 없을 때 이슈를 보고합니다.

[`Required`](https://pub.dev/documentation/meta/latest/meta/required-constant.html)는 [meta](https://pub.dev/packages/meta) 패키지에 정의되어 있습니다. `package:meta/meta.dart`를 직접 가져 오거나 Flutter의`package:flutter/material.dart`와 같이 `meta`를 `export` 하는 또 다른 패키지를 `import` 하세요.

<p id="optional-positional-parameters"/>

##### 선택적 위치 매개 변수 (Optional positional parameters)

`[]`에 함수 매개변수 세트를 래핑하면 선택적 위치 매개변수로 표시됩니다.

```dart
String say(String from, String msg, [String device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}
```

다음은 선택적 매개변수 없이 이 함수를 호출하는 예제입니다.

```dart
assert(say('Bob', 'Howdy') == 'Bob says Howdy');
```

다음은 이 함수를 세 번째 매개 변수와 함께 호출하는 예제입니다.

```dart
assert(say('Bob', 'Howdy', 'smoke signal') ==
    'Bob says Howdy with a smoke signal');
```

<p id="default-parameter-value"/>

#### 매개 변수 기본 값 (Default parameter value)

함수는 `= `을 사용하여 명명된 매개변수와 위치 매개변수에 대한 기본값을 정의 할 수 있습니다. 기본값은 컴파일타임 상수이어야 합니다. 기본값이 제공되지 않으면 기본값은 `null`입니다.

다음은 명명된 매개변수의 기본값을 설정하는 예제입니다.

```dart
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold = false, bool hidden = false}) {...}

// bold will be true; hidden will be false.
enableFlags(bold: true);
```

> 지원 중단 노트 :
>
> 이전 코드는 `=` 대신 콜론 (`:`)을 사용하여 명명 된 매개 변수의 기본값을 설정할 수 있습니다. 이유는 원래는 명명 된 매개 변수에 대해서만 `:`만이 지원 되었기 때문입니다. 이 지원은 더 이상 사용되지 않으므로 `=`를 사용하여 기본값을 지정하는 것이 좋습니다.

다음 예제는 위치 매개변수의 기본값을 설정하는 방법을 보여줍니다.

```dart
String say(String from, String msg,
    [String device = 'carrier pigeon', String mood]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  if (mood != null) {
    result = '$result (in a $mood mood)';
  }
  return result;
}

assert(say('Bob', 'Howdy') ==
    'Bob says Howdy with a carrier pigeon');
```

리스트나 맵을 기본 매개변수로 전달 할 수 있습니다. 다음 예제는  `doStuff()` 함수를 정의 하는데, 기본 리스트를 `list` 매개변수로, 기본 맵을 `gifts` 매개변수로 지정합니다. 

```dart
void doStuff({
  List<int> list = const [1, 2, 3],
  Map<String, String> gifts = const {
    'first': 'paper',
    'second': 'cotton',
    'third': 'leather'
  }}) {
  print('list:  $list');
  print('gifts: $gifts');
}
```

<p id="main-function"/>

### `main()` 함수

모든 앱은 app의 진입점 역할을 하는 최상위 `main()` 함수를 가져야 합니다.  `main()` 함수는 `void`를 리턴하고 인수를 위한 선택적 `List<String>` 매개변수를 갖습니다.

다음은 웹 앱을 위한 `main()` 함수의 예입니다:

```dart
void main() {
  querySelector('#sample_text_id')
    ..text = 'Click me!'
    ..onClick.listen(reverseText);
}
```

> 노트 :
> 위의 코드에서 `..`구문은 [캐스케이드](operators.md#cascade-notation)이라고합니다. 캐스케이드를 사용하면 단일 객체의 멤버에 대해 여러 작업을 수행 할 수 있습니다.

다음은 인수를 취하는 명령 행 응용 프로그램을 위한 `main()` 함수의 예입니다 :

```dart
// Run the app like this: dart args.dart 1 test
void main(List<String> arguments) {
  print(arguments);

  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
```

[args library](https://pub.dev/packages/args)를 사용하여 명령 줄 인수를 정의하고 구문 분석 할 수 있습니다.

<p id="functions-as-first-class-objects"/>

### 일급 객체 함수 (Functions as first-class objects)

함수를 매개 변수로 다른 함수에 전달할 수 있습니다. 예:

```dart
void printElement(int element) {
  print(element);
}

var list = [1, 2, 3];

// Pass printElement as a parameter.
list.forEach(printElement);
```

다음과 같은 변수에 함수를 지정할 수도 있습니다.

```dart
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
assert(loudify('hello') == '!!! HELLO !!!');
```

이 예제에서는 익명의 함수를 사용합니다. 다음 섹션에서 더 자세히 설명합니다.

<p id="anonymous-functions"/>

### 익명 함수 (Anonymous functions)

대부분의 함수는 `main()`이나 `printElement()`와 같이 이름이 붙어 있습니다. 익명 함수 또는 때로는 람다 또는 클로저라는 이름 없는(namedless) 함수를 만들 수도 있습니다. 익명 함수를 변수에 할당하여 컬렉션에 추가하거나 제거 할 수 있도록 할 수 있습니다.

익명 함수는 괄호 사이에 쉼표와 선택적 형식 주석으로 구분 된 0 개 이상의 매개 변수인 명명 된 함수와 유사하게 나타납니다.

다음 코드 블록에는 함수의 본문이 들어 있습니다.

```dart
 ([[Type] param1[, …]]) {
 	codeBlock;
 };  
```

다음 예제는 타입이 지정되지 않은 매개 변수 인 `item`을 사용하여 익명 함수를 정의 합니다. 리스트의 각 항목에 대해 호출 된 함수는 지정된 인덱스의 값을 포함하는 문자열을 인쇄합니다.

```dart
var list = ['apples', 'bananas', 'oranges'];
list.forEach((item) {
  print('${list.indexOf(item)}: $item');
});
```

실행 버튼 ![img](index.assets/red-run-50a66e01c7e7a877dbc06e799d5bc4b73c4dace2926ec17b4493d8c3e939c59a-1557834222343.png) 를 클릭하여 다음 코드를 실행하세요.

<iframe src="https://dartpad.dartlang.org/embed-inline.html?id=5d70bc1889d055c7a18d35d77874af88&amp;verticalRatio=60" style="border: 1px solid #ccc;" width="100%" height="250px">
</iframe>
함수에 하나의 명령문만 있으면 화살 표기법 `=>`을 사용하여 명령문을 단축 할 수 있습니다. [DartPad](https://dartpad.dartlang.org/)에 다음 줄을 붙여 넣고 실행을 클릭하여 기능상으로 동등한 지 확인하십시오.

```dart
list.forEach(
    (item) => print('${list.indexOf(item)}: $item'));
```

<p id="lexical-scope"/>

### 어휘 범위 (Lexical scope)

다트는 어휘 범위가 지정된 언어입니다. 즉, 변수의 범위는 코드의 레이아웃에 의해 정적으로 결정됩니다. 변수가 범위 내에 있는지 보려면 "중괄호 밖으로 따라가면 알 수 있습니다".

다음은 각 범위 수준에서 변수가 있는 중첩 함수의 예입니다.

```dart
bool topLevel = true;

void main() {
  var insideMain = true;

  void myFunction() {
    var insideFunction = true;

    void nestedFunction() {
      var insideNestedFunction = true;

      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNestedFunction);
    }
  }
}
```

`nestedFunction()`이 최상위 수준까지 모든 수준의 변수를 사용하는 방법에 주목하십시오.

<p id="lexical-closures"/>

###  어휘 클로저 (Lexical closures)

클로저는 함수가 원래 범위 외부에서 사용되는 경우에도 어휘 범위의 변수에 액세스 할 수 있는 함수 객체입니다.

함수는 주변 범위에 정의 된 변수를 닫을 수 있습니다. 다음 예제에서, `makeAdder()`는 변수 `addBy`를 캡쳐합니다. 반환 된 함수가 어디에서든지 `addBy`를 기억합니다.

```dart
/// 함수의 인수에 [addBy]를 더하는 함수를 반환합니다.
Function makeAdder(num addBy) {
  return (num i) => addBy + i;
}

void main() {
  // 2를 더하는 함수를 만듭니다.
  var add2 = makeAdder(2);

  // 4를 더하는 함수를 만듭니다.
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```

<p id="testing-functions-for-equality"/>

### 함수 항등성 테스트 (Testing functions for equality)

다음은 최상위 함수, 정적 메서드 및 인스턴스 메소드가 같은지 테스트하는 예제입니다.

```dart
void foo() {} // 최상위 함수

class A {
  static void bar() {} // 정적 메서드
  void baz() {} // 인스턴스 메서드
}

void main() {
  var x;

  // 최상위 함수 비교
  x = foo;
  assert(foo == x);

  // 정적 메서드 비교.
  x = A.bar;
  assert(A.bar == x);

  // 인스턴스 메서드 비교
  var v = A(); // Instance #1 of A
  var w = A(); // Instance #2 of A
  var y = w;
  x = w.baz;

  // 이러한 클로저는 동일한 인스턴스 (#2)를 참조하므로 동일합니다.
  assert(y.baz == x);

  // 이러한 클로저는 서로 다른 인스턴스를 참조하므로 동일하지 않습니다.
  assert(v.baz != w.baz);
}
```

<p id="return-values"/>

### 반환값 (Return values)

모든 함수는 값을 반환합니다. 리턴 값이 지정되지 않으면 `return null;`문이 암시적으로 함수 본문에 추가됩니다.

```dart
foo() {}

assert(foo() == null);
```

---

이전: [내장 타입 (Built in types)](./built_in_types.md)

다음: [연산자 (Operators)](./operators.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
