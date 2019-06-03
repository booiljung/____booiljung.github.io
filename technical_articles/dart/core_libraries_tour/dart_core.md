[Up](./index.md)

## dart:core - numbers, collections, strings  등

원문: [A tour of the core libraries](https://dart.dev/guides/libraries/library-tour)

dart:core 라이브러리 [API reference](https://api.dartlang.org/stable/dart-core/dart-core-library.html))는 작지만 중요한 내장 기능들을 제공합니다. 이 라이브러리는 모든 Dart 프로그램에 자동으로 임포트 됩니다.

<p id = "printing-to-the-console"/>

###  콘솔에 인쇄하기 (Printing to the console)

최상위 `print()` 메소드는 하나의 인자 (임의의 객체)를 취하고 객체가 `toString()`에 의해 반환된 문자열 값을  콘솔에 표시합니다.

```dart
print(anObject);
print('I drink $tea.');
```

문자열에 대한 기본과 `toString()`을 더 많은 정보는 언어로의 여행에서 [Strings](https://dart.dev/guides/language/language-tour#strings) 을 보세요.

<p id = "numbers"/>

###  Numbers

dart:core 라이브러리는 num, int, 및 double 클래스를 정의합니다. 이것은 숫자로 작업하기 위한 기초적인 유틸리티 입니다.

`parse()` 메소드를 사용하여 문자열을 정수나 배정도 숫자로 변환 할 수 있습니다:

```dart
assert(int.parse('42') == 42);
assert(int.parse('0x42') == 66);
assert(double.parse('0.50') == 0.5);
```

또는 가능하다면 정수를 생성하는 `num`의 `parse()` 메서드를 사용하고 그렇지 않으면 double을 사용합니다.

```dart
assert(num.parse('42') is int);
assert(num.parse('0x42') is int);
assert(num.parse('0.50') is double);
```

정수의 밑수를 지정하려면,`radix` 매개 변수를 추가하십시오 :

```dart
assert(int.parse('42', radix: 16) == 66);
```

`toString()`메서드를 사용하여 `int` 또는 `double`을 문자열로 변환합니다. 10진수 오른쪽의 자릿수를 지정하려면 [toStringAsFixed()`](https://api.dartlang.org/stable/dart-core/num/toStringAsFixed.html)를 사용하세요. 유효 자릿수를 지정하려면 문자열에서 [toStringAsPrecision()](https://api.dartlang.org/stable/dart-core/num/toStringAsPrecision.html)을 사용하세요.

```dart
// int를 String으로 변환 합니다.
assert(42.toString() == '42');

// double을 String으로 변환 합니다.
assert(123.456.toString() == '123.456');

// 10진수의 자리수를 지정 합니다.
assert(123.456.toStringAsFixed(2) == '123.46');

// 유효자리수를 지정합니다.
assert(123.456.toStringAsPrecision(2) == '1.2e+2');
assert(double.parse('1.2e+2') == 120.0);
```

자세한 내용은 [int](https://api.dartlang.org/stable/dart-core/int-class.html), [double](https://api.dartlang.org)에 대한 API 문서를 참조하세요. [dart:math section](https://dart.dev/guides/libraries/library-tour#dartmath---math-and-random)도 보세요.

<p id = "strings-and-regular-expressions"/>

###  문자열과 정규식 (Strings and regular expressions)

Dart의 문자열은 UTF-16 코드 단위의 불변 시계열(immutable sequence)입니다. language tour에는 [strings](../language_tour/variables#strings)에 대한 자세한 정보가 있습니다. 정규 표현식 (`RegExp` 객체)을 사용하여 문자열 내에서 검색하고 문자열 부분을 대체 할 수 있습니다.

String 클래스는 `split()`, `contains()`, `startsWith()`, `endsWith()`등의 메소드를 정의합니다.

#### 문장열 내부에서 검색 (Searching inside a string)

문자열 내의 특정 위치를 찾을 수 있을뿐만 아니라 문자열이 특정 패턴으로 시작하는지 아니면 끝나는지를 확인할 수 있습니다. 예 :

```dart
// 문자열에 다른 문자열이 들어 있는지 확인
assert('Never odd or even'.contains('odd'));

// 문자열이 다른 문자열로 시작합니까?
assert('Never odd or even'.startsWith('Never'));

// 문자열이 다른 문자열로 끝나나요?
assert('Never odd or even'.endsWith('even'));

// 문자열 안에있는 문자열의 위치를 찾습니다.
assert('Never odd or even'.indexOf('odd') == 6);
```

#### 문자열에서 데이터 추출 (Extracting data from a string)

문자열에서 개별 문자를 각각 String 또는 int로 가져올 수 있습니다. 정확하게 말하자면 실제로는 개별 UTF-16 코드 단위를 얻습니다. 높은 음자리표 기호 ( '\u{1D11E}')와 같은 높은 번호의 문자는 각각 두 개의 코드 단위입니다.

하위 문자열을 추출하거나 문자열을 하위 문자열 목록으로 분할 할 수도 있습니다.

```dart
// 하위 문자열을 grap
assert('Never odd or even'.substring(6, 9) == 'odd');

// 문자열 패턴을 사용하여 문자열을 분할
var parts = 'structured web apps'.split(' ');
assert(parts.length == 3);
assert(parts[0] == 'structured');

// 인덱스에 의해 UTF-16 코드 단위 (문자열)를 꺼냄.
assert('Never odd or even'[0] == 'N');

// 빈 문자열 매개 변수와 함께 split()을 사용하여
// 모든 (문자열로) 문자 목록을 가져옵니다. iterating에 좋습니다.
for (var char in 'hello'.split('')) {
  print(char);
}

// 문자열에있는 모든 UTF-16 코드 단위를 가져옵니다.
var codeUnitList =
    'Never odd or even'.codeUnits.toList();
assert(codeUnitList[0] == 78);
```

#### 대분자나 소문자로 변환 (Converting to uppercase or lowercase)

문자열을 대문자 및 소문자로 쉽게 변환 할 수 있습니다:

```dart
// 대문자로 변환
assert('structured web apps'.toUpperCase() ==
    'STRUCTURED WEB APPS');

// 소문자로 변환
assert('STRUCTURED WEB APPS'.toLowerCase() ==
    'structured web apps');
```

**Note:** 이 메소드가 모든 자연어에서 동작하지는 않습니다. 예를들어 터키 알파벳의 **dotless/**는 잘못 변환 됩니다.

#### 공백 제거 또는 빈문자열 확인 (Trimming and empty strings)

`trim()`으로 선행 및 후행 공백을 모두 제거하세요. 문자열이 비어 있는지 (길이가 0인지) 확인하려면 `isEmpty`를 사용하세.

```dart
// Trim a string.
assert('  hello  '.trim() == 'hello');

// Check whether a string is empty.
assert(''.isEmpty);

// Strings with only white space are not empty.
assert('  '.isNotEmpty);
```

#### 일부 문자열을 대치 (Replacing part of a string)

`String`은 변경 할 수 없는(immutable) 개체이므로 만들 수는 있지만 변경할 수는 없습니다. [String API reference](https://api.dartlang.org/stable/dart-core/String-class.html)를 면밀히 살펴보면 메소드가 실제로 `String`의 상태를 변경하지 않음을 알 수 있습니다. 예를 들어, `replaceAll()` 메소드는 원래 `String`을 변경하지 않고 새로운 `String`을 반환 합니다.

```dart
var greetingTemplate = 'Hello, NAME!';
var greeting =
    greetingTemplate.replaceAll(RegExp('NAME'), 'Bob');

// greetingTemplate가 바뀌지 않았나?
assert(greeting != greetingTemplate);
```

#### 문자열 만들기 (Building a string)

프로그래밍 방식으로 문자열을 생성하려면 `StringBuffer`를 사용할 수 있습니다. `StringBuffer`는`toString()`이 호출 될 때까지 새로운 `String` 객체를 생성하지 않습니다. `writeAll()`메쏘드는 분리자를 지정할 수있는 선택적인 두번째 매개 변수를 가지고 있습니다. 이 경우는 공백입니다.

역자주: `+` 연산자를 사용하여 다수의 문자열을 연결 할 수 있습니다. 하지만 이 경우 다수의 인스턴스가 생성되고 반복적으로 복사됩니다. 다수의 문자열을 연산할때는 `StringBuffer`가 성능 개선에 이익입니다.

```dart
var sb = StringBuffer();
sb
  ..write('Use a StringBuffer for ')
  ..writeAll(['efficient', 'string', 'creation'], ' ')
  ..write('.');

var fullString = sb.toString();

assert(fullString ==
    'Use a StringBuffer for efficient string creation.');
```

#### 정규식 (Regular expressions)

`RegExp` 클래스는 `JavaScript` 정규식과 동일한 기능을 제공합니다. 문자열의 효율적인 검색 및 패턴 일치를 위해 정규식을 사용하세요.

```dart
// 다음은 하나 이상의 숫자에 대한 정규 표현식입니다.
var numbers = RegExp(r'\d+');

var allCharacters = 'llamas live fifteen to twenty years';
var someDigits = 'llamas live 15 to 20 years';

// contains()는 정규식을 사용할 수 있습니다.
assert(!allCharacters.contains(numbers));
assert(someDigits.contains(numbers));

// 일치하는 문자열을 다른 문자열로 바꿉니다.
var exedOut = someDigits.replaceAll(numbers, 'XX');
assert(exedOut == 'llamas live XX to XX years');
```

`RegExp` 클래스로 직접 작업 할 수도 있습니다. `Match` 클래스는 정규식 일치에 대한 액세스를 제공합니다.

```dart
var numbers = RegExp(r'\d+');
var someDigits = 'llamas live 15 to 20 years';

// Check whether the reg exp has a match in a string.
assert(numbers.hasMatch(someDigits));

// Loop through all matches.
for (var match in numbers.allMatches(someDigits)) {
  print(match.group(0)); // 15, then 20
}
```

#### 더 많은 정보 (More information)

Refer to the [String API reference](https://api.dartlang.org/stable/dart-core/String-class.html) for a full list of methods. Also see the API reference for [StringBuffer,](https://api.dartlang.org/stable/dart-core/StringBuffer-class.html) [Pattern,](https://api.dartlang.org/stable/dart-core/Pattern-class.html) [RegExp,](https://api.dartlang.org/stable/dart-core/RegExp-class.html) and [Match.](https://api.dartlang.org/stable/dart-core/Match-class.html)

###  콜렉션들 (Collections)

Dart ships with a core collections API, which includes classes for lists, sets, and maps.

Dart는 `List`, `Set` 및 `Map` 클래스를 포함하는 핵심 콜렉션 API를 제공합니다.

#### Lists

As the language tour shows, you can use literals to create and initialize [lists](https://dart.dev/guides/libraries/library-tour#lists). Alternatively, use one of the List constructors. The List class also defines several methods for adding items to and removing items from lists.

language tour가 보여 주듯이 리터럴을 사용하거나 `List`생성자중 하나를 사용하여 [`List`](https://dart.dev/guides/libraries/library-tour#lists)을 만들고 초기화 할 수 있습니다. `List` 클래스는 또한 `List`에 항목을 추가하거나 `List`에서 항목을 제거하는 몇 가지 메서드를 정의합니다.

```dart
// List 생성자를 사용
var vegetables = List();

// 또는 단순히 List 리터럴을 사용하십시오.
var fruits = ['apples', 'oranges'];

// List에 추가
fruits.add('kiwis');

// 여러 항목을 List에 추가
fruits.addAll(['grapes', 'bananas']);

// 리스트의 길이
assert(fruits.length == 5);

// 항목 하나를 제거
var appleIndex = fruits.indexOf('apples');
fruits.removeAt(appleIndex);
assert(fruits.length == 4);

// 모든 항목 제거
fruits.clear();
assert(fruits.length == 0);
```

`indexOf()`를 사용하여 리스트에서 객체의 인덱스를 얻습니다.

```dart
var fruits = ['apples', 'oranges'];

// Access a list item by index.
assert(fruits[0] == 'apples');

// Find an item in a list.
assert(fruits.indexOf('apples') == 0);
```

`sort()` 메소드를 사용하여 리스트를 정렬하십시오. 두 객체를 비교하는 정렬 함수를 제공 할 수 있습니다. 이 정렬 함수는 더 작은 항목에 대해 < 0을 리턴하고,  동일한 항목에 대해 0을, 보다 큰 항목에 대해 `> 0`을 반환해야합니다. 다음 예제는 [Comparable](https://api.dartlang.org/stable/dart-core/Comparable-class.html)에 정의되고 `String`으로 구현 된 `compareTo()`를 사용합니다.

```dart
var fruits = ['bananas', 'apples', 'oranges'];

// Sort a list.
fruits.sort((a, b) => a.compareTo(b));
assert(fruits[0] == 'apples');
```

`List` 은 매개변수화 된 타입이므로 List에 포함해야하는 타입을 지정할 수 있습니다.

```dart
// 이 List에는 문자열 만 포함되어야 합니다.
var fruits = List<String>();

fruits.add('apples');
var fruit = fruits[0];
assert(fruit is String);
fruits.add(5); // Error: 'int'은 'String'에 대입할 수 없습니다.
```

리스트에 대해 [List API reference](https://api.dartlang.org/stable/dart-core/List-class.html) 를 참조하세요

#### Sets

다트의 세트는 순차적이지 않은 고유 항목 모음입니다. 집합은 순서가 지정되지 않으므로 색인 (위치)별로 집합의 항목을 가져올 수 없습니다.

역자주: Set은 삽입 순서에 대해 순차적이지 않으며, 정렬 알고리즘들에 의해 정렬 되어, 주어진 항목을 빠르게 조회하거나 변경 또는 제거 할 수 있습니다.

```dart
var ingredients = Set();
ingredients.addAll(['gold', 'titanium', 'xenon']);
assert(ingredients.length == 3);

// 중복 된 항목을 추가해도 아무 효과가 없습니다.
ingredients.add('gold');
assert(ingredients.length == 3);

// 세트에서 항목을 제거.
ingredients.remove('gold');
assert(ingredients.length == 2);
```

`contains()`와 `containsAll()`을 사용하여 하나 이상의 객체가 세트에 있는지 확인하세요:

```dart
var ingredients = Set();
ingredients.addAll(['gold', 'titanium', 'xenon']);

// 항목이 세트에 있는지 확인
assert(ingredients.contains('titanium'));

// 모든 항목이 세트에 있는지 확인
assert(ingredients.containsAll(['titanium', 'xenon']));
```

교집합은 항목이 다른 두 `Set`에 있는 `Set`입니다.

```dart
var ingredients = Set();
ingredients.addAll(['gold', 'titanium', 'xenon']);

// 두 세트의 교집합을 만듭니다.
var nobleGases = Set.from(['xenon', 'argon']);
var intersection = ingredients.intersection(nobleGases);
assert(intersection.length == 1);
assert(intersection.contains('xenon'));
```

메소드에 대한 전체 목록을 보려면  [Set API reference](https://api.dartlang.org/stable/dart-core/Set-class.html) 를 참조하세요.

#### Maps

일반적으로 Dictionary 또는 Hash라고하는 `Map`는 Key-value pair의 순서가 지정되지 않은 콜렉션입니다. Map는 쉽게 검색 할 수 있도록 키와 값을 연결합니다. JavaScript와 달리 Dart 객체는 Map이 아닙니다.

간결한 리터럴 구문을 사용하여 `Map`을 선언하거나 기존 생성자를 사용할 수 있습니다.

```dart
// 맵은 종종 문자열을 키로 사용합니다.
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

// 맵은 생성자로 만들어질 수 있습니다.
var searchTerms = Map();

// 맵은 타입 매개변수화 될 수 있으며 키와 값의 타입을 지정할 수 있습니다.
var nobleGases = Map<int, String>();
```

대괄호 구문을 사용하여 지도 항목을 추가하거나, 가져오고 설정합니다. `remove()`를 사용하여 키와 그 값을 맵에서 제거합니다.

```dart
var nobleGases = {54: 'xenon'};

// 키를 지정하여 값을 가져옵니다.
assert(nobleGases[54] == 'xenon');

// 지정한 키가 있는지 확인 합니다.
assert(nobleGases.containsKey(54));

// 지정한 키와 그에 대한 값을 제거 합니다.
nobleGases.remove(54);
assert(!nobleGases.containsKey(54));
```

맵에서 모든 값 또는 모든 키를 검색 할 수 있습니다.

```dart
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

// 모든 키를 순서가없는 컬렉션으로 가져옵니다.
// (an Iterable).
var keys = hawaiianBeaches.keys;

assert(keys.length == 3);
assert(Set.from(keys).contains('Oahu'));

// 모든 값을 정렬되지 않은 컬렉션으로 가져옵니다.
// (an Iterable of Lists).
var values = hawaiianBeaches.values;
assert(values.length == 3);
assert(values.any((v) => v.contains('Waikiki')));
```

맵에 키가 포함되어 있는지 확인하려면 `containsKey()`를 사용하세요. 맵 값은 `null`이 될 수 있기 때문에 키 값을 얻고 `null`이 있는지 확인하여 키의 존재 여부를 결정할 수는 없습니다.

```dart
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

assert(hawaiianBeaches.containsKey('Oahu'));
assert(!hawaiianBeaches.containsKey('Florida'));
```

키가 맵에 존재하지 않는 경우에만 키에 값을 할당하려면 `putIfAbsent()`메소드를 사용하세요. 값을 리턴하는 함수를 제공해야 합니다.

```dart
var teamAssignments = {};
teamAssignments.putIfAbsent(
    'Catcher', () => pickToughestKid());
assert(teamAssignments['Catcher'] != null);
```

전체 메소드에 대한 목록을 보려면 [Map API reference](https://api.dartlang.org/stable/dart-core/Map-class.html)  를 참조하세요.

#### 콜렉션 공통 메소드들 (Common collection methods)

List, Set 및 Map은 많은 컬렉션에 있는 공통 기능을 공유합니다. 이 공통 기능 중 일부는 `List` 및 `Set`이 구현하는 `Iterable` 클래스에 의해 정의됩니다.

**Note:** Map은 Iterable을 구현하지 않지만, `Map`의 `keys`와 `values` 속성을 사용하여 `Iterable`을 얻을 수 있습니다.

`isEmpty` 또는 `isNotEmpty`를 사용하여 `List`, `Set` 또는 `Map`에 항목이 있는지 확인하세요.

```dart
var coffees = [];
var teas = ['green', 'black', 'chamomile', 'earl grey'];
assert(coffees.isEmpty);
assert(teas.isNotEmpty);
```

목록, 설정 또는지도의 각 항목에 함수를 적용하려면 `forEach()`를 사용할 수 있습니다.

```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

teas.forEach((tea) => print('I drink $tea'));
```

맵에서 `forEach()`를 호출 할 때 함수는 두 개의 인수 (키와 값)를 취해야 합니다 :

```dart
hawaiianBeaches.forEach((k, v) {
  print('I want to visit $k and swim at $v');
  // 나는 Oahu를 방문하여 [Waikiki, Kailua, Waimanalo] 등에서 수영하고 싶습니다.
});
```

반복문은 `map()`메소드를 제공하는데, 이 메소드는 모든 결과를 하나의 객체로 제공합니다 :

```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

var loudTeas = teas.map((tea) => tea.toUpperCase());
loudTeas.forEach(print);
```

**Note:** `map()`에 의해 반환되는 객체는 *lazily evaluated* `Iterable`입니다 : 반환 된 객체에서 항목을 요청할 때까지는 함수가 호출되지 않습니다.

각 항목에서 즉시 함수를 호출하려면 `map().toList ()`또는 `map().toSet()`을 사용하십시오:

```dart
var loudTeas =
    teas.map((tea) => tea.toUpperCase()).toList();
```

조건과 일치하는 모든 항목을 얻으려면 `Iterable`의 `where()`메소드를 사용하십시오. `Iterable`의 `any ()`와 `every()`메소드를 사용하여 일부 또는 모든 항목이 조건과 일치하는지 확인하세요.

```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

// chamomile은 카페인이 없습니다.
bool isDecaffeinated(String teaName) =>
    teaName == 'chamomile';

// 제공된 함수에서 true를 반환하는 항목만 찾으려면 where()를 사용하십시오.
var decaffeinatedTeas =
    teas.where((tea) => isDecaffeinated(tea));
// or teas.where(isDecaffeinated)

// any()를 사용하여 콜렉션의 적어도 하나의 항목이 조건을 충족하는지 확인하십시오.
assert(teas.any(isDecaffeinated));

// every()를 사용하여 컬렉션의 모든 항목이 조건을 만족하는지 확인합니다.
assert(!teas.every(isDecaffeinated));)
```

메소드의 전체 목록은 [`Iterable` API reference](https://api.dartlang.org/stable/dart-core/Iterable-class.html) 및 [`List`](https : //api.dartlang.org/stable/dart-core/List-class.html), [`Set`](https://api.dartlang.org/stable/dart-core/Set-class.html) 및 [`Map`](https://api.dartlang.org/stable/dart-core/Map-class.html)을 참조하세요 .

###  URIs

[`Uri` class](https://api.dartlang.org/stable/dart-core/Uri-class.html)는 (*URL*로도 알려진) URI에서 사용할 문자열을 인코딩하고 디코딩하는 함소들을 제공합니다. 이 함수는 `&`및 `=`와 같이 URI에 특수한 문자를 처리합니다. 또한 `Uri` 클래스는 URI 호스트, 포트, 스키마 등의 컴포넌트를 파싱하고 노출(expose)합니다.

#### 완전한 URI 인코딩 디코딩 (Encoding and decoding fully qualified URIs)

`/`, `:`, `&`, `#`와 같은 특별한 의미를 가진 문자를 제외한 모든 문자를 인코딩하고 디코딩하려면 `encodeFull()`과 `decodeFull()`메소드를 사용하세요. 이러한 메소드는 완전한 URI를 인코딩하거나 디코딩 할 때 유용하며 손상되지 않은 특수 URI 문자를 남겨 둡니다.

```dart
var uri = 'http://example.org/api?foo=some message';

var encoded = Uri.encodeFull(uri);
assert(encoded ==
    'http://example.org/api?foo=some%20message');

var decoded = Uri.decodeFull(encoded);
assert(uri == decoded);
```

`some`과 `message` 사이의 공백 만이 어떻게 인코딩되었는지 주목하세요.

#### URI  컴포넌트의 인코딩과 디코딩 (Encoding and decoding URI components)

`/`, `&`, `:`를 (제한 없이)  포함하여  URI에서 특별한 의미를 갖는 문자열의 모든 문자를 인코딩하고 디코딩하려면`encodeComponent()`와 `decodeComponent()`를 사용하세요.

```dart
var uri = 'http://example.org/api?foo=some message';

var encoded = Uri.encodeComponent(uri);
assert(encoded ==
    'http%3A%2F%2Fexample.org%2Fapi%3Ffoo%3Dsome%20message');

var decoded = Uri.decodeComponent(encoded);
assert(uri == decoded);
```

모든 특수 문자가 어떻게 인코딩되는지 주목하세요. 예를 들어 `/`는 `%2F`로 인코딩됩니다.

#### Parsing URIs

`Uri` 객체나 URI 문자열이 있다면 `path`와 같은 `Uri` 필드를 사용하여 그 일부를 가져올 수 있습니다. 문자열로부터 `Uri`를 생성하려면, `parse()`정적 메소드를 사용하세요.

```dart
var uri =
    Uri.parse('http://example.org:8080/foo/bar#frag');

assert(uri.scheme == 'http');
assert(uri.host == 'example.org');
assert(uri.path == '/foo/bar');
assert(uri.fragment == 'frag');
assert(uri.origin == 'http://example.org:8080');
```

더 많은 URI 컴포넌트에 대해 얻으려면 [Uri API reference](https://api.dartlang.org/stable/dart-core/Uri-class.html) 를 보세요.

#### Building URIs

`Uri()` 생성자를 사용하여 개별 부분으로부터 URI를 만들 수 있습니다:

```dart
var uri = Uri(
    scheme: 'http',
    host: 'example.org',
    path: '/foo/bar',
    fragment: 'frag');
assert(
    uri.toString() == 'http://example.org/foo/bar#frag');
```

###  날짜와 시간 (Dates and times)

`DateTime` 객체는 특정 시점입니다. 표준 시간대는 UTC 또는 현지 표준 시간대입니다.

여러 생성자를 사용하여 `DateTime` 객체를 만들 수 있습니다.

```dart
// Get the current date and time.
var now = DateTime.now();

// Create a new DateTime with the local time zone.
var y2k = DateTime(2000); // January 1, 2000

// Specify the month and day.
y2k = DateTime(2000, 1, 2); // January 2, 2000

// Specify the date as a UTC time.
y2k = DateTime.utc(2000); // 1/1/2000, UTC

// Specify a date and time in ms since the Unix epoch.
y2k = DateTime.fromMillisecondsSinceEpoch(946684800000,
    isUtc: true);

// Parse an ISO 8601 date.
y2k = DateTime.parse('2000-01-01T00:00:00Z');
```

날짜의 `millisecondsSinceEpoch` 속성은 "Unix epoch"(1970 년 1 월 1 일, UTC) 이후의 밀리 초 수를 반환합니다.

```dart
// 1/1/2000, UTC
var y2k = DateTime.utc(2000);
assert(y2k.millisecondsSinceEpoch == 946684800000);

// 1/1/1970, UTC
var unixEpoch = DateTime.utc(1970);
assert(unixEpoch.millisecondsSinceEpoch == 0);
```

`Duration` 클래스를 사용하여 두 날짜의 차이를 계산하고 날짜를 앞이나 뒤로 이동합니다.

```dart
var y2k = DateTime.utc(2000);

// Add one year.
var y2001 = y2k.add(Duration(days: 366));
assert(y2001.year == 2001);

// Subtract 30 days.
var december2000 = y2001.subtract(Duration(days: 30));
assert(december2000.year == 2000);
assert(december2000.month == 12);

// Calculate the difference between two dates.
// Returns a Duration object.
var duration = y2001.difference(y2k);
assert(duration.inDays == 366); // y2k was a leap year.
```

**Warning:** `Duration`을 사용하여 `DateTime`을 일 단위로 변경하면 시계 변경 (clock shift 예 : 일광 절약 시간제)으로 인해 문제가 될 수 있습니다. 요일을 이동해야하는 경우 UTC 날짜를 사용하세요.

메소드의 전체 목록은 [DateTime](https://api.dartlang.org/stable/dart-core/DateTime-class.html) 및 [Duration](https://api.dartlang.org/stable/2.3.1/dart-core/Duration-class.html)에 대한 API 참조서를 참조하세요.

###  Utility classes

코어 라이브러리에는 정렬(sorting), 매핑 값(mappping values) 및 반복(iterating)에 유용한 다양한 유틸리티 클래스가 포함되어 있습니다.

#### 객체 비교 (Comparing objects)

[Comparable](https://api.dartlang.org/stable/dart-core/Comparable-class.html) 인터페이스를 구현하여 개체를 다른 개체와 비교할 수 있음을 나타냅니다 (일반적으로 정렬 목적). `compareTo()`메소드는 더 작은 항목에 대해 0을, 같은 항목에 대해 0을, 큰 항목에 대해 > 0을 반환합니다.

```dart
class Line implements Comparable<Line> {
  final int length;
  const Line(this.length);

  @override
  int compareTo(Line other) => length - other.length;
}

void main() {
  var short = const Line(1);
  var long = const Line(100);
  assert(short.compareTo(long) < 0);
}
```

#### Implementing map keys

Dart의 각 객체는 자동으로 정수 해시 코드를 제공하므로 Map의 키로 사용할 수 있습니다. 그러나 'hashCode' getter를 재정의 하여 사용자 정의 해시 코드를 생성 할 수 있습니다. 그렇게 한다면, 당신은 또한 `==`연산자를 재정의하고 싶을 수도 있습니다. 동일한 (==를 통해) 객체는 동일한 해시 코드를 가져야 합니다. 해시 코드는 고유 할 필요는 없지만 잘 분산되어 있어야합니다.

```dart
class Person {
  final String firstName, lastName;

  Person(this.firstName, this.lastName);

  // Override hashCode using strategy from Effective Java,
  // Chapter 11.
  @override
  int get hashCode {
    int result = 17;
    result = 37 * result + firstName.hashCode;
    result = 37 * result + lastName.hashCode;
    return result;
  }

  // You should generally implement operator == if you
  // override hashCode.
  @override
  bool operator ==(dynamic other) {
    if (other is! Person) return false;
    Person person = other;
    return (person.firstName == firstName &&
        person.lastName == lastName);
  }
}

void main() {
  var p1 = Person('Bob', 'Smith');
  var p2 = Person('Bob', 'Smith');
  var p3 = 'not a person';
  assert(p1.hashCode == p2.hashCode);
  assert(p1 == p2);
  assert(p1 != p3);
}
```

#### Iteration

[Iterable](https://api.dartlang.org/stable/dart-core/Iterable-class.html) 및 [Iterator](https://api.dartlang.org/stable/dart-core/Iterator- class.html) 클래스는 for-in 루프를 지원합니다. for-in 루프에서 사용할 반복자를 제공 할 수 있는 클래스를 만들 때마다 가능한 경우 `Iterable`을 확장하거나 구현하세요. 실제 반복 기능을 정의하는 `Iterator`를 구현하세요.

```dart
class Process {
  // Represents a process...
}

class ProcessIterator implements Iterator<Process> {
  @override
  Process get current => ...
  @override
  bool moveNext() => ...
}

// A mythical class that lets you iterate through all
// processes. Extends a subclass of [Iterable].
class Processes extends IterableBase<Process> {
  @override
  final Iterator<Process> iterator = ProcessIterator();
}

void main() {
  // Iterable objects can be used with for-in.
  for (var process in Processes()) {
    // Do something with the process.
  }
}
```

###  Exceptions

Dart 핵심 라이브러리는 많은 일반적인 예외 및 오류를 정의합니다. 예외는 미리 계획하고 캐치 할 수 있는 조건으로 간주됩니다. 오류는 기대하지 않거나 계획하지 않은 조건입니다.

몇 가지 가장 일반적인 오류는 다음과 같습니다:

- [NoSuchMethodError](https://api.dartlang.org/stable/dart-core/NoSuchMethodError-class.html)

  수신 측의 (null의 가능성이 있는) 오브젝트가 메소드를 구현하고 있지 않는 경우에 Throw 됩니다. 

- [ArgumentError](https://api.dartlang.org/stable/dart-core/ArgumentError-class.html)

  예기치 않은 인수를 받은 메서드에 의해 throw 될 수 있습니다.

애플리케이션 정의 (application-specific) 예외를 throw하는 것은 오류가 발생했음을 나타내는 일반적인 방법입니다. `Exception` 인터페이스를 구현하여 사용자 정의 예외를 정의 할 수 있습니다.

```dart
class FooException implements Exception {
  final String msg;

  const FooException([this.msg]);

  @override
  String toString() => msg ?? 'FooException';
}
```

더 많은 정보에 대해 language tour에서 [Exceptions](../language_tour/exceptions.md) 나 [Exception API reference.](https://api.dartlang.org/stable/dart-core/Exception-class.html)를 보세요.

