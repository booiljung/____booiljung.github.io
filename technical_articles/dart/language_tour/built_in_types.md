[Up](./index.md)

## 내장 타입 (Built in types)

다트는 다음 타입을 기본 지원합니다.

- numbers
- strings
- booleans
- lists (arrays)
- sets
- maps
- runes (문자열에서 유니코드 문자를 표현)
- symbols

리터럴을 사용하여 이러한 특수 타입의 객체를 초기화 할 수 있습니다. 예를 들어 `'this is a string'`은 문자열 리터럴이고 `true`는 부울 리터럴입니다.

다트의 모든 변수는 객체 (클래스의 인스턴스)를 참조하기 때문에 일반적으로 생성자를 사용하여 변수를 초기화 할 수 있습니다. 일부 기본 제공 타입에는 자체 생성자가 있습니다. 예를 들어 `Map()` 생성자를 사용하여 맵을 만들 수 있습니다.

<p id="numbers"/>

### Numbers

다트 숫자는 두 가지 종류로 나뉩니다.

`int`

플랫폼에 따라 64 비트 이하의 정수 값. 다트 VM에서 값은 $-2^{63}$부터 $2^{63 - 1}$까지 가능합니다. 자바스크리트로 컴파일 된 다트는 자바스크립트 수를 사용하므로 $-2^{53}$에서 $2^{53-1}$ 사이의 값을 허용합니다.

`double`

IEEE 754 표준에 지정된 64 비트 (배정도) 부동 소수점 숫자입니다.

`int`와 `double`은 모두 `num`의 서브타입입니다. `num` 타입은 `+`, `-`, `/` 및 `*`와 같은 기본 연산자를 포함하며, 다른 메소드 중 `abs()`, `ceil()` 및 `floor()`를 보게 될 것입니다. (`>>`와 같은 비트 연산자는 `int` 클래스에 정의됩니다.) `num` 및 해당 하위 타입에 원하는 값이 없는 경우 `dart:math` 라이브러리가 필요할 수 있습니다.

정수는 소수점이 없는 숫자입니다. 다음은 정수 리터럴을 정의하는 몇 가지 예입니다.

```dart
var x = 1;
var hex = 0xDEADBEEF;
```

숫자에 10 진수가 포함되어 있으면 `double`입니다. 다음은 double 리터럴을 정의하는 몇 가지 예입니다.

```dart
var y = 1.1;
var exponents = 1.42e5;
```

다트 2.1에서 정수 리터럴은 필요할 때 자동으로 `double`로 변환됩니다 :

```dart
double z = 1;
```

다음은 문자열을 숫자로 바꾸는 방법입니다.

```dart
// String -> int
var one = int.parse('1');
assert(1 == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');
```

`int` 타입은 전통적인 비트 시프트 (`<<`, `>>`), AND (&) 및 OR (`|`) 연산자를 지정합니다. 예 :

```dart
assert((3 << 1) == 6); // 0011 << 1 == 0110
assert((3 >> 1) == 1); // 0011 >> 1 == 0001
assert((3 | 4) == 7); // 0011 | 0100 == 0111
```

숫자 리터럴은 컴파일 타임 상수입니다. 많은 산술식은 피연산자가 숫자로 평가되는 컴파일 타임 상수이므로 컴파일 타임 상수이기도 합니다.

```dart
const msPerSecond = 1000;
const secondsUntilRetry = 5;
const msUntilRetry = secondsUntilRetry  msPerSecond;
```

<p id="strings"/>

### 문자열 (Strings)

다트 문자열은 UTF-16 코드 단위의 시퀀스입니다. 작은 따옴표나 큰 따옴표를 사용하여 문자열을 만들 수 있습니다.

```dart
var s1 = 'Single quotes work well for string literals.';
var s2 = "Double quotes work just as well.";
var s3 = 'It\'s easy to escape the string delimiter.';
var s4 = "It's even easier to use the other delimiter.";
```

`${expression}`을 사용하여 표현식의 값을 문자열에 넣을 수 있습니다. 표현식이 식별자인 경우 `{}`을 생략 할 수 있습니다. 다트는 객체에 해당하는 문자열을 가져 오려면 객체의 `toString()` 메서드를 호출합니다.

```dart
var s = 'string interpolation';

assert('Dart has $s, which is very handy.' ==
    'Dart has string interpolation, ' +
        'which is very handy.');
assert('That deserves all caps. ' +
        '${s.toUpperCase()} is very handy!' ==
    'That deserves all caps. ' +
        'STRING INTERPOLATION is very handy!');
```

문자열 리터럴을 인접하게 하거나 `+` 연산자를 사용하여 문자열을 연결할 수 있습니다.

```dart
var s1 = 'String '
    'concatenation'
    " works even over line breaks.";
assert(s1 ==
    'String concatenation works even over '
        'line breaks.');

var s2 = 'The + operator ' + 'works, as well.';
assert(s2 == 'The + operator works, as well.');
```

여러 줄 문자열을 만드는 또 다른 방법은 세개의 작은 따옴표나 큰 따옴표를 사용하는 것입니다.

```dart
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";
```

`r`의 앞에 붙여 "raw"문자열을 만들 수 있습니다.

```dart
var s = r'In a raw string, not even \n gets special treatment.';
```

문자열에서 유니 코드 문자를 표현하는 방법에 대한 자세한 내용은 '룬 Runes'를 참조하십시오.

문자열 리터럴은 보간된 표현식이 `null` 또는 숫자, 문자열 또는 부울 값으로 계산되는 컴파일 타임 상수인 경우 컴파일 타임 상수입니다.

```dart
// These work in a const string.
const aConstNum = 0;
const aConstBool = true;
const aConstString = 'a constant string';

// These do NOT work in a const string.
var aNum = 0;
var aBool = true;
var aString = 'a string';
const aConstList = [1, 2, 3];

const validConstString = '$aConstNum $aConstBool $aConstString';
// const invalidConstString = '$aNum $aBool $aString $aConstList';
```

<p id="booleans"/>

### Booleans

부울 값을 나타 내기 위해 다트에는 `bool`이라는 타입이 있습니다. 두 개의 객체만 부울 타입을 갖습니다. 부울 리터럴 `true` 및 `false`는 모두 컴파일 타임 상수입니다.

다트의 타입 안전성은 `if (논불린값)` 또는 `assert(논불린값)`와 같은 코드를 사용할 수 없다는 것을 의미합니다. 대신 다음과 같이 명시 적으로 값을 확인해야 합니다.

```dart
// Check for an empty string.
var fullName = '';
assert(fullName.isEmpty);

// Check for zero.
var hitPoints = 0;
assert(hitPoints <= 0);

// Check for null.
var unicorn;
assert(unicorn == null);

// Check for NaN.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```

<p id="lists"/>

### 리스트 (Lists)

아마도 거의 모든 프로그래밍 언어에서 가장 일반적인 컬렉션은 배열 또는 객체의 정렬된 그룹입니다. 다트에서 배열은 `List` 객체이므로 대부분의 사람들은 리스트로 호출합니다.

다트 리스트 리터럴은 자바스크립트의 배열 리터럴과 비슷합니다. 다음은 간단한 다트 리스트입니다.

```dart
var list = [1, 2, 3];
```

리스트는 0부터 시작되는 인덱스를 사용합니다. 0은 최초의 요소의 인덱스이며, `list.length - 1`는 마지막 요소의 인덱스입니다. 자바스크립트에서와 마찬가지로 리스트의 길이를 가져올 수 있고 리스트 요소를 참조 할 수 있습니다.

```dart
var list = [1, 2, 3];
assert(list.length == 3);
assert(list[1] == 2);

list[1] = 1;
assert(list[1] == 1);
```

다트 2.3은 스프레드 연산자 (`...`)와 널 인식 (null-aware) 스프레드 연산자 (`...?`)를 도입하여 컬렉션에 여러 요소를 삽입하는 간결한 방법을 제공합니다.

예를 들어 스프레드 연산자 (`...`)를 사용하여 리스트의 모든 요소를 다른 리스트에 삽입 할 수 있습니다.

```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

스프레드 연산자의 우측 식이 `null` 일 수 있는 경우 널 인식 스프레드 (null-aware spread) 연산자 `(...?)`를 사용하여 예외를 피할 수 있습니다.

```dart
var list;
var list2 = [0, ...?list];
assert(list2.length == 1);
```

스프레드 연산자 사용에 대한 자세한 내용 및 예는 [spread operator proposal](https://github.com/dart-lang/language/blob/master/accepted/future-releases/spread-collections/feature-specification.md)을 참조하십시오.

다트 2.3에서는 collection if 와 collection for를 소개했습니다.이 콜렉션은 조건부 (`if`) 및 반복 (`for`)을 사용하여 콜렉션을 빌드하는 데 사용할 수 있습니다.

다음은 collection if 를 사용하여 3 개 또는 4 개의 항목이 있는 리스트을 만드는 예입니다.

```dart
var nav = [
  'Home',
  'Furniture',
  'Plants',
  if (promoActive) 'Outlet'
];
```

다음은 리스트를 다른 리스트에 추가하기 전에 collection for를 사용하여 리스트의 항목을 조작하는 예입니다.

```dart
var listOfInts = [1, 2, 3];
var listOfStrings = [
  '#0',
  for (var i in listOfInts) '#$i'
];
assert(listOfStrings[1] == '#1');
```

<p id="sets"/>

### 세트 (Sets)

다트의 세트는 순차적이지 않은 유일한 아이템들의 모음입니다. 세트에 대한 다트 지원은 세트 리터럴과 `Set` 타입에 의해 제공됩니다.

다음은 세트 리터럴을 사용하여 만든 간단한 다트 세트입니다.

```dart
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
```

빈 세트을 만들려면 형식 인수 앞에 `{}`를 사용하거나 Set 형식의 변수에 `{}`를 할당하십시오.

```dart
var names = <String>{};
Set<String> names = {}; // 위와 동일합니다.
var names = {}; // 세트가 아닌 맵을 생성합니다.
```

> **세트일까요 맵일까요?** 맵 리터럴의 구문은 셋트 리터럴의 구문과 유사합니다. 맵 리터럴이 먼저 왔으므로 {} 기본값이 Map 유형입니다. {} 또는 할당 된 변수에서 타입 주석을 잊어 버린 경우 다트는 `Map<dynamic, dynamic>` 타입의 객체를 만듭니다.

셋트에  `add()` 또는 `addAll()` 메소드를 사용하여 아이템을 추가 합니다:

```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
```

`.length`를 사용하여 세트에 있는 아이템의 갯수를 얻습니다.

```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
assert(elements.length == 5);
```

컴파일 타임 상수인 세트를 만들려면 세트 리터럴 앞에 `const`를 추가하십시오.

```dart
final constantSet = const {
  'fluorine',
  'chlorine',
  'bromine',
  'iodine',
  'astatine',
};
// constantSet.add('helium'); // Uncommenting this causes an error.
```

다트 2.3에서, 세트는 리스트처럼 스프레스 연산자 (...와 ...?)와 `collection if`와 `collection for`를 지원합니다. 자세한 내용은 [리스트 스프레드 연산자](operators.md#list spread operator) 및 [리스트 콜렉션 연산자](operators.md#list collection operator) 토론을 참조하십시오.

<p id="maps"/>

### 맵 (Maps)

일반적으로 맵은 키와 값을 연결하는 객체입니다. 키와 값은 모든 유형의 객체가 될 수 있습니다. 각 키는 한 번만 발생하지만 동일한 값을 여러 번 사용할 수 있습니다. 맵에 대한 다트 지원은 맵 리터럴과 맵 타입에 의해 제공됩니다.

다음은 맵 리터럴을 사용하여 만든 몇 가지 간단한 다트 맵입니다.

```dart
var gifts = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};

var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

`Map` 생성자를 사용하여 동일한 객체를 만들 수 있습니다.

```dart
var gifts = Map();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = Map();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';
```

자바스크립트에서와 같이 기존 키-값 쌍을 기존 맵에 추가하십시오.

```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds'; // Add a key-value pair
```

자바스크립트에서와 같은 방식으로 맵에서 값을 가져옵니다.

````dart
var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');
````

맵에 키가 없다면 `null`를 얻게 됩니다.

```dart
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);
```

키-값-쌍의 갯수를 얻으려면  `.length` 를 사용합니다:

```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);
```

맵을 컴파일 타임 상수로 생성하려면 `const`를 맵 리터럴 앞에 추가해 합니다.

```dart
final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};

// constantMap[2] = 'Helium'; // Uncommenting this causes an error.
```

다트 2.3 부터 맵은 스프레드 연산자(`...` 와  `...?`) 와  `collection if` , `collection for`, 리스트처럼 지원합니다.

<p id="runes"/>

### 룬 (Runes)

다트에서 룬은 UTF-32 코드 문자열의 포인트입니다.

유니 코드는 세계의 모든 문자 체계에 사용되는 문자, 숫자 및 기호에 대해 고유한 숫자 값을 정의합니다. 다트 문자열은 UTF-16 코드 단위의 시퀀스이기 때문에 문자열 내에서 32 비트 유니 코드 값을 표현하려면 특수 구문이 필요합니다.

Unicode 코드 포인트를 표현하는 일반적인 방법은`\uXXXX`입니다. 여기서 `XXXX`는 4 자리 16 진수 값입니다. 예를 들어, 하트 문자 (♥)는`\u2665` 입니다. 16 진수보다 많거나 적은 16 진수를 지정하려면 값을 중괄호 안에 넣으십시오. 예를 들어, 웃는 이모티콘 (😆)은`\u{1f600}`입니다.

[String](https://api.dartlang.org/stable/dart-core/String-class.html) 클래스에는 룬 정보를 추출하는 데 사용할 수있는 몇 가지 속성이 있습니다. `codeUnitAt`와`codeUnit` 속성은 16 비트 코드 단위를 반환합니다. 문자열의 룬 문자를 얻으려면`runes` 속성을 사용하십시오.

다음 예제는 룬, 16 비트 코드 단위 및 32 비트 코드 점 간의 관계를 보여줍니다. 실행중인 룬을 보려면 실행 버튼 ![img](index.assets/red-run-50a66e01c7e7a877dbc06e799d5bc4b73c4dace2926ec17b4493d8c3e939c59a-1557834147488.png) 을 누르세요.

<iframe src="https://dartpad.dartlang.org/embed-inline.html?id=589bc5c95318696cefe5&amp;verticalRatio=65" style="border: 1px solid #ccc;" width="100%" height="333px">
</iframe>
<p id="symbols"/>

### 심볼 (Symbols)

심볼 객체는 다트 프로그램에서 선언 된 연산자 또는 식별자를 나타냅니다. 심볼을 사용할 필요는 없지만, 이름으로 식별자를 참조하는 API는 식별자 이름을 변경하지만 식별자 심볼은 변경하지 않으므로 가치가 없지 않습니다.

식별자에 대한 심볼을 얻으려면 심볼 리터럴을 사용하십시오. 심볼 리터럴은 단지 `#` 다음에 식별자가 옵니다.

```dart
#radix
#bar
```

심볼 리터럴은 컴파일 타임 상수입니다.

역자주: 심볼 리터럴은 다른 언어로 작성된 라이브러리의 식별자가 다트의 예약어와 충돌하는 문제를 해결할때 유용합니다. 

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
