[Up](./index.md)

## 연산자 (Operators)

Dart는 다음 표에 표시된 연산자를 정의합니다. [Overridable operators](#overridable-operators)에 설명 된대로 이러한 연산자를 재정의 할 수 있습니다.

<p id="operators"/>


| Description                                  | Operator                                                     |
| -------------------------------------------- | ------------------------------------------------------------ |
| 후위 단항 unary postfix                      | `표현식++`    `표현식--`    `()`    `[]`    `.`    `?.`      |
| 전위 단항 unary prefix                       | `-표현식`    `!표현식`   `~표현식`   `++표현식`   `--표현식` |
| 승산 multiplicative                          | `/`    `%`    `~/`                                           |
| 가산 additive                                | `+` `-`                                                      |
| 비트열 쉬프트 shift                          | `<<` `>>` `>>>`                                              |
| 비트열 곱 bitwise AND                        | `&`                                                          |
| 배타적 비트열 합 bitwise XOR                 | `^`                                                          |
| 비트열 합 bitwise OR                         | `|`                                                          |
| 관계 및 타입 테스트 relational and type test | `>=` `>` `<=` `<` `as` `is` `is!`                            |
| 항등 equality                                | `==` `!=`                                                    |
| 논리 곱 logical AND                          | `&&`                                                         |
| 논리 합 logical OR                           | `||`                                                         |
| 널 테스트 if null                            | `??`                                                         |
| 조건 condition                               | `표현식1 ? 표현식2 : 표현식3`                                |
| 캐스케이드 cascade                           | `..`                                                         |
| 대입 assignment                              | `=` `=` `/=` `+=` `-=` `&=` `^=` etc...                      |

연산자를 사용하면 표현식을 만들 수 있습니다. 다음은 연산자 표현식의 몇 가지 예입니다.

```dart
a++
a + b
a = b
a == b
c ? a : b
a is T
```

[연산자 테이블](#operators)에서 각 연산자는 그 뒤에 오는 행의 연산자보다 우선 순위가 높습니다. 예를 들어, 곱셈 연산자`%`는 우선 순위가 같고 논리적 AND 연산자 `&&`보다 우선 순위가 높은 항등 연산자 `==`를 사용합니다. 우선 순위는 다음 두 줄의 코드가 같은 방식으로 실행됨을 의미합니다.

```dart
// 괄호는 가독성을 향상시킵니다.
if ((n % i == 0) && (d % i == 0)) ...

// 읽기가 어렵지만 동급.
if (n % i == 0 && d % i == 0) ...
```

<p id="arithmetic-operator"/>

### 산술 연산자 (Arithmetic operator)

다트는 다음 표와 같이 일반적인 산술 연산자를 지원합니다.

| Operator  | Meaning              |
| --------- | -------------------- |
| `+`       | 가산                 |
| `-`       | 감산                 |
| `-표현식` | 부호 반전 단항 연산  |
| ``        | 승산                 |
| `/`       | 제산                 |
| `~/`      | 제산, 정수부 구하기. |
| `%`       | 제산, 나머지 구하기. |

예:

```dart
assert(2 + 3 == 5);
assert(2 - 3 == -1);
assert(2  3 == 6);
assert(5 / 2 == 2.5); // Result is a double
assert(5 ~/ 2 == 2); // Result is an int
assert(5 % 2 == 1); // Remainder

assert('5/2 = ${5 ~/ 2} r ${5 % 2}' == '5/2 = 2 r 1');
```

다트는 또한 전위와 후위, 그리고 증가 및 감소 연산자를 모두 지원합니다.

| Operator | Meaning                                    |
| -------- | ------------------------------------------ |
| `++var`  | `var = var + 1` (표현식의 값은  `var + 1`) |
| `var++`  | `var = var + 1` (표현식의 값은 `var`)      |
| `--var`  | `var = var – 1` (표현식의 값은 `var – 1`)  |
| `var--`  | `var = var – 1` (표현식의 값은 `var`)      |

예:

```dart
var a, b;

a = 0;
b = ++a; // b가 값을 얻기 전에 a를 증가시킵니다.
assert(a == b); // 1 == 1

a = 0;
b = a++; // b가 값을 얻은 후 a를 증가 시킵니다.
assert(a != b); // 1 != 0

a = 0;
b = --a; // b가 값을 얻기 전에 a를 감소시킵니다.
assert(a == b); // -1 == -1

a = 0;
b = a--; // b가 값을 얻은 후에 a를 감소시킵니다.
assert(a != b); // -1 != 0
```

<p id="equality-and-relational-operators"/>

### 항등 및 관계 연산자 (Equality and relational operators)

다음 표는 항등 및 관계 연산자의 의미를 나열합니다.

| Operator | Meaning       |
| -------- | ------------- |
| `==`     | 같은가        |
| `!=`     | 다른가        |
| `>`      | 큰가          |
| `<`      | 작은가        |
| `>=`     | 크거나 같은가 |
| `<=`     | 작거나 같은가 |

두 객체 x와 y가 같은 것을 나타내는지 테스트하려면 `==` 연산자를 사용하세요. 두 개의 객체가 정확히 동일한 객체인지 여부를 알아야하는 경우는 [`identical()`](https://api.dartlang.org/stable/dart-core/identical.html) 함수를 대신 사용하세요. 다음은 `==` 연산자가 작동하는 방식입니다 :

1. x 또는 y가 `null`일때, 양쪽 모두가 `null`의 경우는 `true`를 돌려 주며, 1개 만이 `null`의 경우는 `false`를 돌려줍니다.
2. 메소드 호출 `x.==(y)`의 결과를 반환하세요. (즉, `==`와 같은 연산자는 첫 번째 피연산자에서 호출되는 메소드입니다. [재정의 연산자](#overridable-operators)에서 볼 수 있듯이 `==`를 비롯한 많은 연산자를 재정의 할 수도 있습니다.

다음은 항등 연산자와 관계 연산자를 사용하는 예입니다.

```dart
assert(2 == 2);
assert(2 != 3);
assert(3 > 2);
assert(2 < 3);
assert(3 >= 3);
assert(2 <= 3);
```

<p id="type-test-operators"/>

### 타입 테스트 연산자 (Type test operators)

`as`, `is`, `is!` 연산자는 런타임에 타입을 검사 할 때 편리합니다.

| Operator | Meaning                                                      |
| -------- | ------------------------------------------------------------ |
| `as`     | Typecast (also used to specify [library prefixes](https://dart.dev/guides/language/language-tour#specifying-a-library-prefix)) |
| `is`     | 객체가 지정된 타입을 갖는 경우 참                            |
| `is!`    | 객체에 지정된 타입이 있는 경우 거짓                          |

`obj is T`의 결과는 `obj`가 `T`에 의해 지정된 인터페이스를 구현하면 `true`입니다. 예를 들어 `obj is Object`는 항상 `true`입니다.

`as` 연산자를 사용하여 객체를 특정 타입으로 변환하세요. 일반적으로, 그 객체를 사용하는 표현식 다음에 오는 객체에 대한 `is` 테스트의 단축으로 사용해야합니다. 예를 들어 다음 코드를 보겠습니다.

```dart
if (emp is Person) {
  // Type check
  emp.firstName = 'Bob';
}
```

`as` 연산자를 사용하여 코드를 짧게 만들 수 있습니다 :

```dart
(emp as Person).firstName = 'Bob';
```

**Note:** 위 코드는 동일하지 않습니다. `emp`가 `null`이거나 `Person`이 아니라면, 첫 번째 예제 (`is`)는 아무 것도하지 않습니다. 두 번째 (`as `와 함께)는 예외를 던집니다.

<p id="assignment-operators"/>

###  대입 연산자 (Assignment operators)

이미 보았 듯이 `= ` 연산자를 사용하여 값을 대입 할 수 있습니다. assigned-to variable가 `null` 인 경우에만 대입하려면`?? = ` 연산자를 사용하십시오.

```dart
// a에 value를 대입합니다.
a = value;
// b가 null이면 b에 value를 대입합니다. null이 아니면 b는 기존 값을 유지 합니다.
b ??= value;
```

`+= `와 같은 복합 대입 연산자는 연산과 대입을 결합합니다.

| `=`  | `–=` | `/=`  | `%=`  | `>>=` | `^=` |
| ---- | ---- | ----- | ----- | ----- | ---- |
| `+=` | `*=` | `~/=` | `<<=` | `&=`  | `|=` |

복합 대입 연산자가 작동하는 방법은 다음과 같습니다.

|                         | Compound assignment | Equivalent expression |
| ----------------------- | ------------------- | --------------------- |
| **For an operator op:** | `a op= b`           | `a = a op b`          |
| **Example:**            | `a += b`            | `a = a + b`           |

다음 예제에서는 대입 연산자와 복합 대입 연산자를 사용합니다.

```dart
var a = 2; // =를 사용하여 대입
a *= 3; // 곱하며 대입: a = a * 3
assert(a == 6);
```

<p id="logical-operators"/>

###  논리 연산자 (Logical operators)

논리 연산자를 사용하여 부울 표현식을 반전 또는 결합 할 수 있습니다.

| Operator  | Meaning                                                      |
| --------- | ------------------------------------------------------------ |
| `!표현식` | 우측의 표현식의 결과를 반전합니다 (`false`를 `true`로 변경하고 `false`를 `true`로 변경 함). |
| `||`      | 논리합                                                       |
| `&&`      | 논리곱                                                       |

다음은 논리 연산자를 사용하는 예입니다.

```dart
if (!done && (col == 0 || col == 3)) {
  // ...Do something...
}
```

<p id="bitwise-and-shift-operators"/>

###  비트열 및 시프트 연산자 (Bitwise and shift operators)

Dart에서 숫자의 개별 비트를 조작 할 수 있습니다. 보통, 이 비트 단위와 시프트 연산자는 정수로 사용합니다.

| Operator  | Meaning                                       |
| --------- | --------------------------------------------- |
| `&`       | AND                                           |
| `|`       | OR                                            |
| `^`       | XOR                                           |
| `~표현식` | 전체 비트열 반전 (0s become 1s; 1s become 0s) |
| `<<`      | Shift left                                    |
| `>>`      | Shift right                                   |

다음은 비트 연산자와 시프트 연산자의 사용 예입니다.

```dart
final value = 0x22;
final bitmask = 0x0f;

assert((value & bitmask) == 0x02); // AND
assert((value & ~bitmask) == 0x20); // AND NOT
assert((value | bitmask) == 0x2f); // OR
assert((value ^ bitmask) == 0x2d); // XOR
assert((value << 4) == 0x220); // Shift left
assert((value >> 4) == 0x02); // Shift right
```

<p id="conditional-expressions"/>

###  조건식 (Conditional expressions)

다트는 두 개의 연산자를 사용하여 [if-else](control_flow_statements.md#if-and-else) 구문을 필요로하는 표현식을 간결하게 평가할 수 있습니다.

- `condition ? 표현식1 : 표현식2`

  condition이 `true`이면 `표현식1`을 평가하고 (그 값을 반환합니다.); 그렇지 않으면 ` 표현식2` 값을 평가하여 리턴합니다.

- `표현식1 ?? 표현식2`

  `표현식1` 가 `null`가 아닌 경우는, 그 값을 돌려 줍니다. 그렇지 않으면 `표현식2 ` 값을 평가하여 리턴합니다.

부울 식을 기반으로 값을 지정해야하는 경우 `?:`를 사용합니다.

```dart
var visibility = isPublic ? 'public' : 'private';
```

부울 표현식에서 `null`을 테스트하는 경우`??` 를 사용하는 것이 좋습니다.

```dart
String playerName(String name) => name ?? 'Guest';
```

이전 예제는 최소한 두 가지 다른 방법으로 작성되었을 수 있지만 간결하지는 않습니다.

```dart
// 약간 긴 버전은 ?: 연산자를 사용합니다.
String playerName(String name) => name != null ? name : 'Guest';

// 매우 긴 버전은 if-else 문을 사용합니다.
String playerName(String name) {
  if (name != null) {
    return name;
  } else {
    return 'Guest';
  }
}
```

<p id="cascade-notation"/>

###  캐스케이드 표기법 (Cascade notation) (`..`)

캐스케이드 (`..`)는 동일한 객체에 대해 일련의 연산을 수행 할 수 있게 합니다. 함수 호출 외에도 동일한 객체의 필드에 액세스 할 수 있습니다. 이것은 종종 임시 변수를 만드는 단계를 생략하고 보다 유동적인 코드를 작성할 수 있도록 합니다.

다음 코드를 보겠습니다.

```dart
querySelector('#confirm') // 객체를 얻어서
  ..text = 'Confirm' // 멤버들을 사용합니다.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
```

첫 번째 메소드 호출 `querySelector()`는  selector 객체를 반환합니다. 캐스케이드 표기법을 따르는 코드는 반환 될 수 있는 후속 값을 무시하고 이 selector 객체에서 작동합니다.

이전 예제는 다음과 같습니다.

```dart
var button = querySelector('#confirm');
button.text = 'Confirm';
button.classes.add('important');
button.onClick.listen((e) => window.alert('Confirmed!'));
```

캐스케이드 안에 캐스케이드를 중첩할 수 있습니다. 예를 들면:

```dart
final addressBook = (AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
```

실제 객체를 반환하는 함수에서 계단식 구조를 만드는 데 주의하십시오. 예를 들어 다음 코드는 실패합니다.

```dart
var sb = StringBuffer();
sb.write('foo')
  ..write('bar'); // 오류: 'write' 메소드는 'void'에 정의되지 않았습니다.
```

`sb.write()` 호출은 `void`를 리턴하고,  `void`에 캐스케이드를 생성 할 수 없습니다.

> **Note:** 엄밀히 말하면, 캐스케이드의 "이중 점"표기법은 연산자가 아닙니다. 이는 다트 구문의 일부일뿐 입니다.

번역주: 그래서 연산자(operator)가 아니고 표기법 (notation)이라고 합니다.

<p id="other-operators"/>

###  그외의 연산자 (Other operators)

이전의 다른 예제에서는 대부분의 나머지 연산자를 이미 보았습니다.

| Operator | Name                      | Meaning                                                      |
| -------- | ------------------------- | ------------------------------------------------------------ |
| `()`     | Function application      | 함수 호출을 나타냅니다.                                      |
| `[]`     | List access               | 리스트의 지정된 색인에 있는 값을 참조합니다.                 |
| `.`      | Member access             | 표현식의 속성을 참조합니다. 예 :`foo.bar`는 표현식`foo`에서 속성`bar`을 선택합니다. |
| `?.`     | Conditional member access | `.`과 비슷하지만 맨 왼쪽 피연산자는 null 일 수 있습니다. 예 :`foo?.bar`는`foo`가 null이 아닌 한 표현식`foo`에서 속성`bar`를 선택합니다 (이 경우`foo?.bar`의 값은 null입니다) |

---

이전: [함수 (Functions)](./functions.md)

다음: [흐름 제어문 (Control flow statements)](./control_flow_statements.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.

