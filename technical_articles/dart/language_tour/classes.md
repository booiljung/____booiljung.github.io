[Up](./index.md)

##  클래스 (Classes)

Dart는 클래스 및 믹스인 기반 상속을 사용하는 객체 지향 언어입니다. 모든 객체는 클래스의 인스턴스이며 모든 클래스는 [Object](https://api.dartlang.org/stable/dart-core/Object-class.html)에서 파생됩니다. 믹스인 기반 상속은 모든 클래스 (Object를 제외하고)는 정확히 하나의 수퍼 클래스를 가지므로 클래스 본문은 여러 클래스 계층에서 다시 사용할 수 있습니다.

<p id="using-class-members"/>

###  클래스 멤버를 사용하기 (Using class members)

객체는 함수와 데이터로 구성된 멤버를 가집니다 (각각 메소드와 인스턴스 변수). 메소드를 호출하면 객체에서 호출 합니다. 메소드는 해당 객체의 함수 및 데이터에 액세스 할 수 있습니다. 도트(`.`)를 사용하여 인스턴스 변수 또는 메소드를 참조 합니다.

```dart
var p = Point(2, 2);

// 인스턴스 변수 y의 값을 설정.
p.y = 3;

// y의 값을 점검.
assert(p.y == 3);

// p에서 distanceTo()를 호출.
num distance = p.distanceTo(Point(4, 4));
```

최좌측 피연산자가 `null` 일 때 예외를 피하려면 `.` 대신 `?.`를 사용하세요:

```dart
// p가 null가 아닌 경우, y 값을 4로 설정.
p?.y = 4;
```

<p id="using-constructors"/>

###  생성자 사용하기 (Using constructors)

생성자를 사용하여 객체를 만들 수 있습니다. 생성자 이름은 `클래스이름` 또는`클래스이름.식별자` 일 수 있습니다. 예를 들어, 다음 코드는 `Point()`와 `Point.fromJson()` 생성자를 사용하여 `Point` 객체를 생성합니다 :

```dart
var p1 = Point(2, 2);
var p2 = Point.fromJson({'x': 1, 'y': 2});
```

다음 코드는 동일한 효과를 갖지만 생성자 이름 앞에 선택적인 `new` 키워드를 사용합니다:

```dart
var p1 = new Point(2, 2);
var p2 = new Point.fromJson({'x': 1, 'y': 2});
```

> **Version note:** Dart 2에서는`new` 키워드가 선택 사항이되었습니다.

일부 클래스는 [상수 생성자(constant constructors)](classes.md#constant-constructors)를 제공합니다. 상수 생성자를 사용하여 컴파일 타임 상수를 생성하려면 생성자 이름 앞에 `const` 키워드를 넣으세요:

```dart
var p = const ImmutablePoint(2, 2);
```

두 개의 동일한 컴파일 타임 상수를 생성하면 표준 인스턴스가 단일(canonical) 인스턴스가 됩니다.

```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // 이것들은 동일한 인스턴스입니다! 역자주: 동일한 인스턴스를 가리킵니다.
```

상수 컨텍스트(constant context) 내에서 생성자나 리터럴 앞에 `const`를 생략 할 수 있습니다. 예를 들어 const 맵을 만드는 이 코드를 살펴보십시오.

```dart
// 많은 const 예약어가 사용 되었습니다.
const pointAndLine = const {
  'point': const [const ImmutablePoint(0, 0)],
  'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
};
```

`const` 키워드의 첫 번째 사용을 제외하고 모두 생략 할 수 있습니다:

```dart
// Only one const, which establishes the constant context.
const pointAndLine = {
  'point': [ImmutablePoint(0, 0)],
  'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
};
```

상수 생성자가 상수 컨텍스트 외부에 있고 `const` 없이 호출되는 경우 상수 생성자가 생성됩니다:

```dart
var a = const ImmutablePoint(1, 1); // Creates a constant
var b = ImmutablePoint(1, 1); // Does NOT create a constant

assert(!identical(a, b)); // NOT the same instance!
```

> **Version note:** `const` 키워드는 Dart 2의 상수 컨텍스트 내에서 선택 사항이되었습니다.

<p id="getting-an-object's-type"/>

###  개체 타입 얻기 (Getting an object’s type)

런타임에 객체의 타입을 얻으려면 [Type](https://api.dartlang.org/stable/dart-core/Type-class.html) 객체를 반환하는 Object의 `runtimeType` 속성을 사용할 수 있습니다.

```dart
print('The type of a is ${a.runtimeType}');
```

---

여기까지 클래스를 사용하는 방법을 보았습니다. 이 섹션의 나머지 부분에서는 클래스를 구현하는 방법을 보여줍니다.

<p id="instance-variables"/>

###  인스턴스 변수 (Instance variables)

인스턴스 변수를 선언하는 방법은 다음과 같습니다.

```dart
class Point {
  num x; // 인스턴스 변수 x를 선언합니다. null로 초기화 됩니다.
  num y; // 인스턴스 변수 y를 선언하니다. null로 초기화 됩니다.
  num z = 0; // z를 선언합니다. 0으로 초기화 됩니다.
}
```

모든 초기화되지 않은 인스턴스 변수는 `null` 값을 갖습니다.

모든 인스턴스 변수는 암시적 `getter` 메소드를 생성합니다. Non-final 인스턴스 변수는 암시적 `setter` 메소드도 생성합니다. 자세한 내용은 [게터와 세터](classes.md#gatters-and-setters)를 참조하십시오.

```dart
class Point {
  num x;
  num y;
}

void main() {
  var point = Point();
  point.x = 4; // x에 setter 메서드를 사용.
  assert(point.x == 4); // x에 getter 메소드를 사용.
  assert(point.y == null); // 기본값은 null입니다.
}
```

(생성자 또는 메서드 대신) 선언 된 인스턴스 변수를 초기화하면 생성자 및 초기화 목록이 실행되기 전에 인스턴스가 만들어 질 때 값이 설정됩니다.

<p id="constructors"/>

###  생성자 (Constructors)

클래스와 같은 이름의 함수를 생성하여 생성자를 선언합니다 (또 선택적으로 [Named constructors](classes.md#named-constructors)에 설명 된 추가 식별자). 생성자의 가장 일반적인 형태인 제너레이티브 생성자(generative constructor)는 클래스의 새 인스턴스를 만듭니다.

```dart
class Point {
  num x, y;

  Point(num x, num y) {
    // There's a better way to do this, stay tuned.
    this.x = x;
    this.y = y;
  }
}
```

`this` 키워드는 현재 인스턴스를 참조합니다.

> **Note:** 이름 충돌이있는 경우에만 `this`를 사용하십시오. 그렇지 않으면 Dart 스타일은 `this`를 생략합니다.

인스턴스 변수에 생성자 인수를 할당하는 패턴은 매우 일반적이므로 Dart는 쉽게 만들 수있는 구문적 설탕(syntactic sugar)을 가지고 있습니다:

```dart
class Point {
  num x, y;

  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
}
```

<p id="default-constructors"/>

#### 기본 생성자 (Default constructors)

생성자를 선언하지 않으면 기본 생성자가 제공됩니다. 기본 생성자는 인수가 없으며 수퍼 클래스에서 인수가 없는 생성자를 호출합니다.

<p id="constructors-aren't-inherited"/>

#### 생성자는 상속되지 않음. (Constructors aren’t inherited)

서브 클래스는 슈퍼 클래스에서 생성자를 상속받지 않습니다. 생성자를 선언하지 않는 서브 클래스는, 디폴트 (인수 없음, 이름 없음)의 생성자만을 가집니다.

<p id="named-constructors"/>

#### 명명 된 생성자 (Named constructors)

명명 된 생성자를 사용하여 클래스에 대한 여러 생성자를 구현하거나 추가 명확성을 제공하십시오.

```dart
class Point {
  num x, y;

  Point(this.x, this.y);

  // Named constructor
  Point.origin() {
    x = 0;
    y = 0;
  }
}
```

생성자는 상속되지 않는다는 것을 기억하세요. 즉, 수퍼 클래스의 이름 지정된 생성자가 서브 클래스에 상속되지 않는다는 것을 의미합니다. 수퍼 클래스에 정의 된 이름 지정된 생성자로 서브 클래스를 만들려면 해당 생성자를 서브 클래스에 구현해야합니다.

<p id="invoking-a-non-default-superclass-constructor"/>

#### 디폴트가 아닌 수퍼 클래스 생성자 호출하기 (Invoking a non-default superclass constructor)

디폴트에서는, 서브 클래스의 생성자는, 슈퍼 클래스의 이름이 없는 인수 없는 생성자을 호출합니다. 수퍼 클래스의 생성자는 생성자 본문의 처음에 호출됩니다. [초기자 목록](classes.md#initializer-list)도 사용중인 경우 수퍼 클래스가 호출되기 전에 실행됩니다. 요약하면 실행 순서는 다음과 같습니다.

1. 초기자 목록
2. 수퍼 클래스의 인자 없는 생성자.
3. 메인 클래스의 인자 없는 생성자.

수퍼 클래스에 이름 없는 인수가 없는 생성자가 없으면 수퍼 클래스에서 생성자 중 하나를 수동으로 호출해야합니다. 슈퍼 클래스 생성자를 콜론 (`:`) 다음에 생성자 본문 (있는 경우) 바로 앞에 지정하십시오. 다음 예제에서 `Employee` 클래스의 생성자는 수퍼 클래스 `Person`의 명명 된 생성자를 호출합니다. 코드를 실행 하려면 실행 버튼 ![img](index.assets/red-run-50a66e01c7e7a877dbc06e799d5bc4b73c4dace2926ec17b4493d8c3e939c59a-1557833292775.png) 를 누르세요.

<iframe src="https://dartpad.dartlang.org/embed-inline.html?id=e57aa06401e6618d4eb8&amp;verticalRatio=80" style="border: 1px solid #ccc;" width="100%" height="500px">
</iframe>
수퍼 클래스 생성자에 대한 인수는 생성자를 호출하기 전에 평가되므로 인수는 함수 호출과 같은 표현식이 될 수 있습니다.

```dart
class Employee extends Person {
  Employee() : super.fromJson(getDefaultData());
  // ···
}
```

> **Warning:** 수퍼 클래스 생성자에 대한 인수는 `this`에 접근 할 수 없습니다. 예를 들어, 인수는 정적 메소드를 호출 할 수 있지만 인스턴스 메소드는 호출 할 수 없습니다.

<p id="initializer-list"/>

#### 초기자 목록 (Initializer list)

수퍼 클래스 생성자를 호출하는 것 외에도 생성자 본문이 실행되기 전에 인스턴스 변수를 초기화 할 수 있습니다. 쉼표로 초기자를 구분하십시오.

```dart
// 초기자 목록은 본문이 실행되기 전에
// 인스턴스 변수를 설정합니다.
Point.fromJson(Map<String, num> json)
    : x = json['x'],
      y = json['y'] {
  print('In Point.fromJson(): ($x, $y)');
}
```

> **Warning:** 초기화 리스트의 오른쪽은 `this`에 접근 할 수 없습니다.

개발하는 동안 초기자 리스트에서 `assert`를 사용하여 입력을 검증 할 수 있습니다.

```dart
Point.withAssert(this.x, this.y) : assert(x >= 0) {
  print('In Point.withAssert(): ($x, $y)');
}
```

초기자 리스트는 `final` 필드를 설정할 때 편리합니다. 다음 예제에서는 초기자 리스트의 마지막 세 필드를 초기화합니다. 코드를 실행 하려면 실행 버튼 ![img](index.assets/red-run-50a66e01c7e7a877dbc06e799d5bc4b73c4dace2926ec17b4493d8c3e939c59a-1557833292775.png) 를 누르세요.

<iframe src="https://dartpad.dartlang.org/embed-inline.html?id=7a9764702c0608711e08&amp;verticalRatio=85" style="border: 1px solid #ccc;" width="100%" height="420px">
</iframe>
<p id="redirecting constructors"/>

<p id="redirecting-constuctors"/>

#### 생성자 리디렉션 (Redirecting constructors)

때로는 생성자의 유일한 목적은 동일한 클래스의 다른 생성자로 리디렉션하는 것입니다. 리디렉션 생성자의 본문은 비어 있으며 콜론 (`:`) 다음에 생성자 호출이 나타납니다.

```dart
class Point {
  num x, y;

  // The main constructor for this class.
  Point(this.x, this.y);

  // Delegates to the main constructor.
  Point.alongXAxis(num x) : this(x, 0);
}
```

<p id="constant-constructors"/>

#### 상수 생성자 (Constant constructors)

클래스가 변경되지 않는 객체를 생성하면 이러한 객체를 컴파일 타임 상수로 만들 수 있습니다. 이렇게 하려면 `const` 생성자를 정의하고 모든 인스턴스 변수가 `final`인지 확인하세요.

```dart
class ImmutablePoint {
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);

  final num x, y;

  const ImmutablePoint(this.x, this.y);
}
```

상수 생성자는 항상 상수를 생성하지는 않습니다. 자세한 내용은 [using constructors](classes.md#using-constructors) 섹션을 참조하십시오.

<p id="factory-constuctors"/>

#### 팩토리 생성자 (Factory constructors)

항상 클래스의 새 인스턴스를 생성하지는 않는 생성자를 구현할 때는 `factory` 키워드를 사용하세요. 예를 들어 팩토리 생성자는 캐시에서 인스턴스를 반환하거나 하위 유형의 인스턴스를 반환 할 수 있습니다.

다음 예제는 캐시에서 객체를 반환하는 팩토리 생성자를 보여줍니다.

```dart
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to
  // the _ in front of its name.
  static final Map<String, Logger> _cache =
      <String, Logger>{};

  factory Logger(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

**Note:** 팩토리 생성자는 `this`에 접근 할 수 없습니다.

다른 생성자와 마찬가지로 `factory` 생성자를 호출하세요.

```dart
var logger = Logger('UI');
logger.log('Button clicked');
```

<p id="methods"/>

###  메소드 (Methods)

메서드는 개체에 동작(behavior) 제공하는 함수입니다.

<p id="instance-methods"/>

#### 인스턴스 메소드 (Instance methods)

객체의 인스턴스 메소드는 인스턴스 변수와 `this`에 접근 할 수 있습니다. 다음 예제의 `distanceTo()`메소드는 인스턴스 메소드의 예입니다 :

```dart
import 'dart:math';

class Point {
  num x, y;

  Point(this.x, this.y);

  num distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

<p id="getters-and-setters"/>

#### 게터와 세터 (Getters and setters)

getter 및 setter는 객체 속성에 대한 읽기 및 쓰기 액세스를 제공하는 특수 메소드입니다. 각 인스턴스 변수에는 암시적인 getter와 적절한 경우 setter가 있습니다. `get` 및 `set` 키워드를 사용하여 getter 및 setter를 구현하여 추가 속성을 만들 수 있습니다.

```dart
class Rectangle {
  num left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // Define two calculated properties: right and bottom.
  num get right => left + width;
  set right(num value) => left = value - width;
  num get bottom => top + height;
  set bottom(num value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```

getter와 setter를 사용하면 클라이언트 코드를 변경하지 않고 나중에 인스턴스 변수로 시작하고 나중에 메소드로 래핑 할 수 있습니다.

> **Note:** 증분(`++`)과 같은 연산자는 getter가 명시적으로 정의되었는지 여부에 관계없이 예상대로 작동합니다. 예기치 않은 부작용을 피하기 위해 연산자는 getter를 한 번만 호출하여 해당 값을 임시 변수에 저장합니다.

<p id="abstract-methods"/>

#### 추상 메소드 (Abstract methods)

인스턴스의 getter 및 setter 메서드는 추상화 될 수 있으며, 인터페이스를 정의하지만, 구현을 다른 클래스로 남겨 둡니다. 추상 메소드는 [추상 클래스 (abstract classes)](classes.md#abstract-classes)에만 존재할 수 있습니다.

메소드 추상화를 만들려면 메소드 본문 대신 세미콜론(;)을 사용하십시오.

```dart
abstract class Doer {
  // Define instance variables and methods...

  void doSomething(); // Define an abstract method.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // Provide an implementation, so the method is not abstract here...
  }
}
```

<p id="abstract-classes"/>

###  추상 클래스 (Abstract classes)

`abstract` 한정자를 사용하여 인스턴스화 할 수 없는 추상 클래스를 정의하세요. 추상 클래스는 인터페이스를 정의 할 때 유용합니다. 추상 클래스를 인스턴스화 할 수 있게 하려면 [factory constructor](classes.md#factory-constructors)을 정의하세요.

추상 클래스는 [추상 메소드 (abstract method)](classes.md#abstract-methods)를 사용하는 경우가 많습니다. 다음은 추상 메소드가 있는 추상 클래스를 선언하는 예제입니다.

```dart
// 이 클래스는 abstract로 선언되므로 인스턴스화 할 수 없습니다.
abstract class AbstractContainer {
  // 생성자, 필드, 메소드 정의 ...

  void updateChildren(); // 추상 메소드
}
```

<p id="implicit-interfaces"/>

###  암시적 인터페이스 (Implicit interfaces)

모든 클래스는 클래스의 모든 인스턴스 멤버와 구현하는 인터페이스의 모든 인스턴스를 포함하는 인터페이스를 암시적으로 정의합니다. B의 구현을 상속하지 않고 클래스 B의 API를 지원하는 클래스 A를 만들려면 클래스 A가 B 인터페이스를 구현해야합니다.

클래스는 하나 이상의 인터페이스를 `implements` 절에서 선언하고 인터페이스에 필요한 API를 제공함으로써 구현합니다. 예 :

```dart
// Person 클래스. 묵시적 인터페이스로 greet()를 가짐.
class Person {
  // In the interface, but visible only in this library.
  final _name;

  // Not in the interface, since this is a constructor.
  Person(this._name);

  // In the interface.
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// An implementation of the Person interface.
class Impostor implements Person {
  get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```

다음은 클래스가 다중 인터페이스를 구현하도록 지정하는 예제입니다.

```dart
class Point implements Comparable, Location {...}
```

<p id="extending-a-class"/>

###  클래스를 확장하기 (Extending a class)

서브 클래스를 생성하기 위해서 `extends`를 사용하고 수퍼 클래스를 참조하기 위해 `super`를 사용하세요:

```dart
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ···
}

class SmartTelevision extends Television {
  void turnOn() {
    super.turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ···
}
```

<p id="overriding-members"/>

#### 멤버 재정의 (Overriding members)

서브 클래스는 인스턴스 메소드, getter 및 setter를 오버라이딩 할 수 있습니다. `@override` 주석을 사용하여 의도적으로 멤버를 오버라이드하고 있음을 나타낼 수 있습니다 :

```dart
class SmartTelevision extends Television {
  @override
  void turnOn() {...}
  // ···
}
```

[type safe](https://dart.dev/guides/language/sound-dart) 코드에서 메소드 매개 변수 또는 인스턴스 변수의 타입을 줄이려면 [covariant 키워드](https://dart.dev/guides/language/sound-problems#the-covariant-keyword)를 사용할 수 있습니다.

<p id="overridable-operators"/>

#### 재정의 가능한 연산자 (Overridable operators)

다음 표에 표시된 연산자를 재정의 할 수 있습니다. 예를 들어 `Vector` 클래스를 정의하면 두 개의 벡터를 추가하는 `+`메서드를 정의 할 수 있습니다.

| `<`  | `+`  | `|`  | `[]`  |
| ---- | ---- | ---- | ----- |
| `>`  | `/`  | `^`  | `[]=` |
| `<=` | `~/` | `&`  | `~`   |
| `>=` | `*`  | `<<` | `==`  |
| `–`  | `%`  | `>>` |       |

> **Note:** `!=`가 오버라이드 할 수 있는 연산자가 아니라는 것을 눈치 챘을 것입니다. `e1 != e2`라는 표현은 `!(e1 == e2)`의 syntactic sugar 일뿐입니다.

다음은 `+`와 `-` 연산자를 재정의 하는 클래스의 예입니다:

```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  // Operator == and hashCode not shown. For details, see note below.
  // ···
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```

`==`를 오버라이드(override)하면, Object의 `hashCode` getter도 오버라이드 (override) 할 필요가 있습니다. `==`와 `hashCode`를 오버라이드하는 예제는 [Implementing map keys](https://dart.dev/guides/libraries/library-tour#implementing-map-keys)를 보십시오.

재정의에 대한 자세한 내용은 일반적으로 [클래스 확장 (Extending a class)](classes.md#extending-a-class)을 참조하십시오.

<p id="noSuchMethod"/>

#### noSuchMethod()

코드가 존재하지 않는 메소드나 인스턴스 변수를 사용하려고 할 때마다 감지하거나 반응하기 위해 `noSuchMethod()`를 오버라이드 할 수 있습니다 :

```dart
class A {
  // noSuchMethod를 재정의 하지 않고 존재하지 않은 멤버를 사용하면 NoSuchMethodError가 발생합니다.
  @override
  void noSuchMethod(Invocation invocation) {
    print('You tried to use a non-existent member: ' +
        '${invocation.memberName}');
  }
}
```

다음 중 하나가 참이지 않는 한 구현되지 않은 메소드를 호출 할 수 없습니다.

- 수신자(receiver)의 정적 타입이 `dynamic` 입니다.
- 수신자(receiver)는 구현되지 않은 메소드를 정의하는 정적 타입을 가집니다. (추상은 OK입니다.) 수신자의 동적 타입은 `Object` 클래스의 것과는 다른 `noSuchMethod()`구현을 가지고 있습니다.

자세한 내용은 비공식 [noSuchMethod forward specification](https://github.com/dart-lang/sdk/blob/master/docs/language/informal/nosuchmethod-forwarding.md)을 참조하십시오.

<p id="enumerated-types"/>

###  열거 타입 (Enumerated types)

enumerations 또는 enums이라고도하는 열거 타입은 고정 된 수의 상수 값을 나타내는 데 사용되는 특별한 종류의 클래스입니다.

<p id="using-enums"/>

#### 열거형 사용 (Using enums)

`enum` 키워드를 사용하여 열거 타입을 선언하십시오 :

```dart
enum Color { red, green, blue }
```

열거 타입의 각 값에는 열거 타입 getter가 있으며 이 열거 타입은 enum 선언에서 값의 0부터 시작하는 위치를 반환합니다. 예를 들어 첫 번째 값은 인덱스 0이고 두 번째 값은 인덱스 1입니다.

```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```

열거 타입의 모든 값 목록을 얻으려면 열거 형의 `values` 상수를 사용하십시오.

```dart
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```

[`switch` 문](https://dart.dev/guides/language/language-tour#switch-and-case)에서 열거 타입을 사용할 수 있으며, 모든 열거 타입을 처리하지 않으면 경고 메시지가 표시됩니다.

```dart
var aColor = Color.blue;

switch (aColor) {
  case Color.red:
    print('Red as roses!');
    break;
  case Color.green:
    print('Green as grass!');
    break;
  default: // Without this, you see a WARNING.
    print(aColor); // 'Color.blue'
}
```

열거 타입에는 다음과 같은 제한이 있습니다.

- 열거 타입을 서브 클래스화, 믹스인 또는 구현할 수 없습니다.
- 열거 타입을 명시적으로 인스턴스화 할 수 없습니다.

자세한 내용은 [Dart language specification](https://dart.dev/guides/language/spec) 를 참조하세요.

<p id="adding-features-to-a-class-mixins"/>

###  클래스에 피처 추가하기 : 믹스인 (Adding features to a class: mixins)

믹스인(mixin)은 여러 클래스 계층에서 클래스의 코드를 재사용하는 방법입니다.

믹스인을 사용하려면, 하나 이상의 믹스인 이름 뒤에 `with` 예약어를 사용하십시오. 다음 예제는 믹스인을 사용하는 두 개의 클래스를 보여줍니다 :

믹스인을 사용하려면, 하나 이상의 믹스인 이름 뒤에 `with` 키워드를 사용하십시오. 다음 예제는 믹스인를 사용하는 두 개의 클래스를 보여줍니다 :

```dart
class Musician extends Performer with Musical {
  // ···
}

class Maestro extends Person
    with Musical, Aggressive, Demented {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
```

믹스인을 구현하려면 Object를 확장하고 생성자를 선언하지 않는 클래스를 만듭니다. 믹스인을 일반 클래스로 사용하지 않으려면 `class` 대신 `mixin` 키워드를 사용하십시오. 예 :

```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

특정 타입만 믹스인을 사용할 수 있도록 지정하려면 - 예를 들어, 믹스인이 정의하지 않은 메소드를 호출 할 수 있도록하려면 `on`을 사용하여 필수 수퍼 클래스를 지정하십시오.

```dart
mixin MusicalPerformer on Musician {
  // ···
}
```

**Version note:** `mixin` 키워드 지원은 Dart 2.1에서 소개되었습니다. 이전 버전의 코드는 대개 대신`추상 클래스 '를 사용했습니다. 2.1 mixin 변경 사항에 대한 자세한 내용은 [Dart SDK changelog](https://github.com/dart-lang/sdk/blob/master/CHANGELOG.md) 및 [2.1 mixin specification](https://github.com/dart-lang/language/blob/master/accepted/2.1/super-mixins/feature-specification.md#dart-2-mixin-declarations).

<p id="class-variables-and-methods"/>

###  클래스 변수 및 메소드 (Class variables and methods)

class-wide 변수와 메소드를 구현하려면 `static` 키워드를 사용하십시오.

<p id="static-variables"/>

#### 정적 변수 (Static variables)

정적 변수 (클래스 변수)는 클래스 전체의 상태 및 상수에 유용합니다.

```dart
class Queue {
  static const initialCapacity = 16;
  // ···
}

void main() {
  assert(Queue.initialCapacity == 16);
}
```

정적 변수는 사용될 때까지 초기화되지 않습니다.

> **Note:** 이 페이지는 상수 이름에 `lowerCamelCase`를 선호하는 [stype guide recommendation](https://dart.dev/guides/language/effective-dart/style#identifiers)을 따릅니다.

<p id="static-methods"/>

#### 정적 메소드 (Static methods)

정적 메서드 (클래스 메서드)는 인스턴스에서 작동하지 않으므로 `this`에 액세스 할 수 없습니다. 예:

```dart
import 'dart:math';

class Point {
  num x, y;
  Point(this.x, this.y);

  static num distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

void main() {
  var a = Point(2, 2);
  var b = Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(2.8 < distance && distance < 2.9);
  print(distance);
}
```

**Note:**  자주 사용되거나 널리 사용되는 유틸리티와 기능에 대해서는 정적 메소드 대신 최상위 함수를 사용하는 것이 좋습니다.

정적 메서드를 컴파일 타임 상수로 사용할 수 있습니다. 예를 들어, 정적 메소드를 매개변수로 상수 생성자에 전달할 수 있습니다.

---

이전: [예외 (Exceptions)](./exceptions.md)

다음: [제네릭 (Generics)](./generics.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
