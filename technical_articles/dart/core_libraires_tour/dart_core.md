## dart:core - numbers, collections, strings  등

dart:core 라이브러리 (API reference](https://api.dartlang.org/stable/dart-core/dart-core-library.html))는 작지만 중요한 내장 기능들을 제공합니다. 이 라이브러리는 모든 Dart 프로그램에 자동으로 임포트됩니다.

###  콘솔에 인쇄하기 (Printing to the console)

최상위 `print()` 메소드는 하나의 인자 (임의의 객체)를 취하고 객체가 `toString()`에 의해 반환된 문자열 값을  콘솔에 표시합니다.

```dart
print(anObject);
print('I drink $tea.');
```

문자열에 대한 기본과 `toString()`을 더 많은 정보는 언어로의 여행에서 [Strings](https://dart.dev/guides/language/language-tour#strings) 을 보세요.

###  Numbers

dart:core 라이브러리는 num, int, 및 double 클래스를 정의합니다. 이것은 숫자로 작업하기 위한 기초적인유틸리티 입니다.

`parse()` 메소드를 사용하여 문자열을 정수나 배정도 숫자로 변환 할 수 있습니다:

```dart
assert(int.parse('42') == 42);
assert(int.parse('0x42') == 66);
assert(double.parse('0.50') == 0.5);
```

또는 가능하다면 정수를 생성하는 num의 `parse()` 메서드를 사용하고 그렇지 않으면 double을 사용합니다.

```dart
assert(num.parse('42') is int);
assert(num.parse('0x42') is int);
assert(num.parse('0.50') is double);
```

정수의 밑수를 지정하려면,`radix` 매개 변수를 추가하십시오 :

```dart
assert(int.parse('42', radix: 16) == 66);
```

Use the `toString()` method to convert an int or double to a string. To specify the number of digits to the right of the decimal, use [toStringAsFixed().](https://api.dartlang.org/stable/dart-core/num/toStringAsFixed.html) To specify the number of significant digits in the string, use [toStringAsPrecision():](https://api.dartlang.org/stable/dart-core/num/toStringAsPrecision.html)

```dart
// Convert an int to a string.
assert(42.toString() == '42');

// Convert a double to a string.
assert(123.456.toString() == '123.456');

// Specify the number of digits after the decimal.
assert(123.456.toStringAsFixed(2) == '123.46');

// Specify the number of significant figures.
assert(123.456.toStringAsPrecision(2) == '1.2e+2');
assert(double.parse('1.2e+2') == 120.0);
```

For more information, see the API documentation for [int,](https://api.dartlang.org/stable/dart-core/int-class.html) [double,](https://api.dartlang.org/stable/dart-core/double-class.html) and [num.](https://api.dartlang.org/stable/dart-core/num-class.html) Also see the [dart:math section](https://dart.dev/guides/libraries/library-tour#dartmath---math-and-random).

###  Strings and regular expressions

A string in Dart is an immutable sequence of UTF-16 code units. The language tour has more information about [strings](https://dart.dev/guides/language/language-tour#strings). You can use regular expressions (RegExp objects) to search within strings and to replace parts of strings.

The String class defines such methods as `split()`, `contains()`, `startsWith()`, `endsWith()`, and more.

#### Searching inside a string

You can find particular locations within a string, as well as check whether a string begins with or ends with a particular pattern. For example:

```dart
// Check whether a string contains another string.
assert('Never odd or even'.contains('odd'));

// Does a string start with another string?
assert('Never odd or even'.startsWith('Never'));

// Does a string end with another string?
assert('Never odd or even'.endsWith('even'));

// Find the location of a string inside a string.
assert('Never odd or even'.indexOf('odd') == 6);
```

#### Extracting data from a string

You can get the individual characters from a string as Strings or ints, respectively. To be precise, you actually get individual UTF-16 code units; high-numbered characters such as the treble clef symbol (‘\u{1D11E}’) are two code units apiece.

You can also extract a substring or split a string into a list of substrings:

```dart
// Grab a substring.
assert('Never odd or even'.substring(6, 9) == 'odd');

// Split a string using a string pattern.
var parts = 'structured web apps'.split(' ');
assert(parts.length == 3);
assert(parts[0] == 'structured');

// Get a UTF-16 code unit (as a string) by index.
assert('Never odd or even'[0] == 'N');

// Use split() with an empty string parameter to get
// a list of all characters (as Strings); good for
// iterating.
for (var char in 'hello'.split('')) {
  print(char);
}

// Get all the UTF-16 code units in the string.
var codeUnitList =
    'Never odd or even'.codeUnits.toList();
assert(codeUnitList[0] == 78);
```

#### Converting to uppercase or lowercase

You can easily convert strings to their uppercase and lowercase variants:

```dart
// Convert to uppercase.
assert('structured web apps'.toUpperCase() ==
    'STRUCTURED WEB APPS');

// Convert to lowercase.
assert('STRUCTURED WEB APPS'.toLowerCase() ==
    'structured web apps');
```

**Note:** These methods don’t work for every language. For example, the Turkish alphabet’s dotless *I* is converted incorrectly.

#### Trimming and empty strings

Remove all leading and trailing white space with `trim()`. To check whether a string is empty (length is zero), use `isEmpty`.

```dart
// Trim a string.
assert('  hello  '.trim() == 'hello');

// Check whether a string is empty.
assert(''.isEmpty);

// Strings with only white space are not empty.
assert('  '.isNotEmpty);
```

#### Replacing part of a string

Strings are immutable objects, which means you can create them but you can’t change them. If you look closely at the [String API reference,](https://api.dartlang.org/stable/dart-core/String-class.html) you’ll notice that none of the methods actually changes the state of a String. For example, the method `replaceAll()` returns a new String without changing the original String:

```dart
var greetingTemplate = 'Hello, NAME!';
var greeting =
    greetingTemplate.replaceAll(RegExp('NAME'), 'Bob');

// greetingTemplate didn't change.
assert(greeting != greetingTemplate);
```

#### Building a string

To programmatically generate a string, you can use StringBuffer. A StringBuffer doesn’t generate a new String object until `toString()` is called. The `writeAll()` method has an optional second parameter that lets you specify a separator—in this case, a space.

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

#### Regular expressions

The RegExp class provides the same capabilities as JavaScript regular expressions. Use regular expressions for efficient searching and pattern matching of strings.

```dart
// Here's a regular expression for one or more digits.
var numbers = RegExp(r'\d+');

var allCharacters = 'llamas live fifteen to twenty years';
var someDigits = 'llamas live 15 to 20 years';

// contains() can use a regular expression.
assert(!allCharacters.contains(numbers));
assert(someDigits.contains(numbers));

// Replace every match with another string.
var exedOut = someDigits.replaceAll(numbers, 'XX');
assert(exedOut == 'llamas live XX to XX years');
```

You can work directly with the RegExp class, too. The Match class provides access to a regular expression match.

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

#### More information

Refer to the [String API reference](https://api.dartlang.org/stable/dart-core/String-class.html) for a full list of methods. Also see the API reference for [StringBuffer,](https://api.dartlang.org/stable/dart-core/StringBuffer-class.html) [Pattern,](https://api.dartlang.org/stable/dart-core/Pattern-class.html) [RegExp,](https://api.dartlang.org/stable/dart-core/RegExp-class.html) and [Match.](https://api.dartlang.org/stable/dart-core/Match-class.html)

###  Collections

Dart ships with a core collections API, which includes classes for lists, sets, and maps.

#### Lists

As the language tour shows, you can use literals to create and initialize [lists](https://dart.dev/guides/libraries/library-tour#lists). Alternatively, use one of the List constructors. The List class also defines several methods for adding items to and removing items from lists.

```dart
// Use a List constructor.
var vegetables = List();

// Or simply use a list literal.
var fruits = ['apples', 'oranges'];

// Add to a list.
fruits.add('kiwis');

// Add multiple items to a list.
fruits.addAll(['grapes', 'bananas']);

// Get the list length.
assert(fruits.length == 5);

// Remove a single item.
var appleIndex = fruits.indexOf('apples');
fruits.removeAt(appleIndex);
assert(fruits.length == 4);

// Remove all elements from a list.
fruits.clear();
assert(fruits.length == 0);
```

Use `indexOf()` to find the index of an object in a list:

```dart
var fruits = ['apples', 'oranges'];

// Access a list item by index.
assert(fruits[0] == 'apples');

// Find an item in a list.
assert(fruits.indexOf('apples') == 0);
```

Sort a list using the `sort()` method. You can provide a sorting function that compares two objects. This sorting function must return < 0 for *smaller*, 0 for the *same*, and > 0 for *bigger*. The following example uses `compareTo()`, which is defined by [Comparable](https://api.dartlang.org/stable/dart-core/Comparable-class.html) and implemented by String.

```dart
var fruits = ['bananas', 'apples', 'oranges'];

// Sort a list.
fruits.sort((a, b) => a.compareTo(b));
assert(fruits[0] == 'apples');
```

Lists are parameterized types, so you can specify the type that a list should contain:

```dart
// This list should contain only strings.
var fruits = List<String>();

fruits.add('apples');
var fruit = fruits[0];
assert(fruit is String);
fruits.add(5); // Error: 'int' can't be assigned to 'String'
```

Refer to the [List API reference](https://api.dartlang.org/stable/dart-core/List-class.html) for a full list of methods.

#### Sets

A set in Dart is an unordered collection of unique items. Because a set is unordered, you can’t get a set’s items by index (position).

```dart
var ingredients = Set();
ingredients.addAll(['gold', 'titanium', 'xenon']);
assert(ingredients.length == 3);

// Adding a duplicate item has no effect.
ingredients.add('gold');
assert(ingredients.length == 3);

// Remove an item from a set.
ingredients.remove('gold');
assert(ingredients.length == 2);
```

Use `contains()` and `containsAll()` to check whether one or more objects are in a set:

```dart
var ingredients = Set();
ingredients.addAll(['gold', 'titanium', 'xenon']);

// Check whether an item is in the set.
assert(ingredients.contains('titanium'));

// Check whether all the items are in the set.
assert(ingredients.containsAll(['titanium', 'xenon']));
```

An intersection is a set whose items are in two other sets.

```dart
var ingredients = Set();
ingredients.addAll(['gold', 'titanium', 'xenon']);

// Create the intersection of two sets.
var nobleGases = Set.from(['xenon', 'argon']);
var intersection = ingredients.intersection(nobleGases);
assert(intersection.length == 1);
assert(intersection.contains('xenon'));
```

Refer to the [Set API reference](https://api.dartlang.org/stable/dart-core/Set-class.html) for a full list of methods.

#### Maps

A map, commonly known as a *dictionary* or *hash*, is an unordered collection of key-value pairs. Maps associate a key to some value for easy retrieval. Unlike in JavaScript, Dart objects are not maps.

You can declare a map using a terse literal syntax, or you can use a traditional constructor:

```dart
// Maps often use strings as keys.
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

// Maps can be built from a constructor.
var searchTerms = Map();

// Maps are parameterized types; you can specify what
// types the key and value should be.
var nobleGases = Map<int, String>();
```

You add, get, and set map items using the bracket syntax. Use `remove()` to remove a key and its value from a map.

```dart
var nobleGases = {54: 'xenon'};

// Retrieve a value with a key.
assert(nobleGases[54] == 'xenon');

// Check whether a map contains a key.
assert(nobleGases.containsKey(54));

// Remove a key and its value.
nobleGases.remove(54);
assert(!nobleGases.containsKey(54));
```

You can retrieve all the values or all the keys from a map:

```dart
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

// Get all the keys as an unordered collection
// (an Iterable).
var keys = hawaiianBeaches.keys;

assert(keys.length == 3);
assert(Set.from(keys).contains('Oahu'));

// Get all the values as an unordered collection
// (an Iterable of Lists).
var values = hawaiianBeaches.values;
assert(values.length == 3);
assert(values.any((v) => v.contains('Waikiki')));
```

To check whether a map contains a key, use `containsKey()`. Because map values can be null, you cannot rely on simply getting the value for the key and checking for null to determine the existence of a key.

```dart
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

assert(hawaiianBeaches.containsKey('Oahu'));
assert(!hawaiianBeaches.containsKey('Florida'));
```

Use the `putIfAbsent()` method when you want to assign a value to a key if and only if the key does not already exist in a map. You must provide a function that returns the value.

```dart
var teamAssignments = {};
teamAssignments.putIfAbsent(
    'Catcher', () => pickToughestKid());
assert(teamAssignments['Catcher'] != null);
```

Refer to the [Map API reference](https://api.dartlang.org/stable/dart-core/Map-class.html) for a full list of methods.

#### Common collection methods

List, Set, and Map share common functionality found in many collections. Some of this common functionality is defined by the Iterable class, which List and Set implement.

**Note:** Although Map doesn’t implement Iterable, you can get Iterables from it using the Map `keys` and `values` properties.

Use `isEmpty` or `isNotEmpty` to check whether a list, set, or map has items:

```dart
var coffees = [];
var teas = ['green', 'black', 'chamomile', 'earl grey'];
assert(coffees.isEmpty);
assert(teas.isNotEmpty);
```

To apply a function to each item in a list, set, or map, you can use `forEach()`:

```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

teas.forEach((tea) => print('I drink $tea'));
```

When you invoke `forEach()` on a map, your function must take two arguments (the key and value):

```dart
hawaiianBeaches.forEach((k, v) {
  print('I want to visit $k and swim at $v');
  // I want to visit Oahu and swim at
  // [Waikiki, Kailua, Waimanalo], etc.
});
```

Iterables provide the `map()` method, which gives you all the results in a single object:

```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

var loudTeas = teas.map((tea) => tea.toUpperCase());
loudTeas.forEach(print);
```

**Note:** The object returned by `map()` is an Iterable that’s *lazily evaluated*: your function isn’t called until you ask for an item from the returned object.

To force your function to be called immediately on each item, use `map().toList()` or `map().toSet()`:

```dart
var loudTeas =
    teas.map((tea) => tea.toUpperCase()).toList();
```

Use Iterable’s `where()` method to get all the items that match a condition. Use Iterable’s `any()` and `every()` methods to check whether some or all items match a condition.

```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

// Chamomile is not caffeinated.
bool isDecaffeinated(String teaName) =>
    teaName == 'chamomile';

// Use where() to find only the items that return true
// from the provided function.
var decaffeinatedTeas =
    teas.where((tea) => isDecaffeinated(tea));
// or teas.where(isDecaffeinated)

// Use any() to check whether at least one item in the
// collection satisfies a condition.
assert(teas.any(isDecaffeinated));

// Use every() to check whether all the items in a
// collection satisfy a condition.
assert(!teas.every(isDecaffeinated));
```

For a full list of methods, refer to the [Iterable API reference,](https://api.dartlang.org/stable/dart-core/Iterable-class.html) as well as those for [List,](https://api.dartlang.org/stable/dart-core/List-class.html) [Set,](https://api.dartlang.org/stable/dart-core/Set-class.html) and [Map.](https://api.dartlang.org/stable/dart-core/Map-class.html)

###  URIs

The [Uri class](https://api.dartlang.org/stable/dart-core/Uri-class.html) provides functions to encode and decode strings for use in URIs (which you might know as *URLs*). These functions handle characters that are special for URIs, such as `&` and `=`. The Uri class also parses and exposes the components of a URI—host, port, scheme, and so on.

#### Encoding and decoding fully qualified URIs

To encode and decode characters *except* those with special meaning in a URI (such as `/`, `:`, `&`, `#`), use the `encodeFull()` and `decodeFull()` methods. These methods are good for encoding or decoding a fully qualified URI, leaving intact special URI characters.

```dart
var uri = 'http://example.org/api?foo=some message';

var encoded = Uri.encodeFull(uri);
assert(encoded ==
    'http://example.org/api?foo=some%20message');

var decoded = Uri.decodeFull(encoded);
assert(uri == decoded);
```

Notice how only the space between `some` and `message` was encoded.

#### Encoding and decoding URI components

To encode and decode all of a string’s characters that have special meaning in a URI, including (but not limited to) `/`, `&`, and `:`, use the `encodeComponent()` and `decodeComponent()` methods.

```dart
var uri = 'http://example.org/api?foo=some message';

var encoded = Uri.encodeComponent(uri);
assert(encoded ==
    'http%3A%2F%2Fexample.org%2Fapi%3Ffoo%3Dsome%20message');

var decoded = Uri.decodeComponent(encoded);
assert(uri == decoded);
```

Notice how every special character is encoded. For example, `/` is encoded to `%2F`.

#### Parsing URIs

If you have a Uri object or a URI string, you can get its parts using Uri fields such as `path`. To create a Uri from a string, use the `parse()` static method:

```dart
var uri =
    Uri.parse('http://example.org:8080/foo/bar#frag');

assert(uri.scheme == 'http');
assert(uri.host == 'example.org');
assert(uri.path == '/foo/bar');
assert(uri.fragment == 'frag');
assert(uri.origin == 'http://example.org:8080');
```

See the [Uri API reference](https://api.dartlang.org/stable/dart-core/Uri-class.html) for more URI components that you can get.

#### Building URIs

You can build up a URI from individual parts using the `Uri()` constructor:

```dart
var uri = Uri(
    scheme: 'http',
    host: 'example.org',
    path: '/foo/bar',
    fragment: 'frag');
assert(
    uri.toString() == 'http://example.org/foo/bar#frag');
```

###  Dates and times

A DateTime object is a point in time. The time zone is either UTC or the local time zone.

You can create DateTime objects using several constructors:

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

The `millisecondsSinceEpoch` property of a date returns the number of milliseconds since the “Unix epoch”—January 1, 1970, UTC:

```dart
// 1/1/2000, UTC
var y2k = DateTime.utc(2000);
assert(y2k.millisecondsSinceEpoch == 946684800000);

// 1/1/1970, UTC
var unixEpoch = DateTime.utc(1970);
assert(unixEpoch.millisecondsSinceEpoch == 0);
```

Use the Duration class to calculate the difference between two dates and to shift a date forward or backward:

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

**Warning:** Using a Duration to shift a DateTime by days can be problematic, due to clock shifts (to daylight saving time, for example). Use UTC dates if you must shift days.

For a full list of methods, refer to the API reference for [DateTime](https://api.dartlang.org/stable/dart-core/DateTime-class.html) and [Duration.](https://api.dartlang.org/stable/dart-core/Duration-class.html)

###  Utility classes

The core library contains various utility classes, useful for sorting, mapping values, and iterating.

#### Comparing objects

Implement the [Comparable](https://api.dartlang.org/stable/dart-core/Comparable-class.html) interface to indicate that an object can be compared to another object, usually for sorting. The `compareTo()` method returns < 0 for *smaller*, 0 for the *same*, and > 0 for *bigger*.

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

Each object in Dart automatically provides an integer hash code, and thus can be used as a key in a map. However, you can override the `hashCode` getter to generate a custom hash code. If you do, you might also want to override the `==` operator. Objects that are equal (via `==`) must have identical hash codes. A hash code doesn’t have to be unique, but it should be well distributed.

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

The [Iterable](https://api.dartlang.org/stable/dart-core/Iterable-class.html) and [Iterator](https://api.dartlang.org/stable/dart-core/Iterator-class.html) classes support for-in loops. Extend (if possible) or implement Iterable whenever you create a class that can provide Iterators for use in for-in loops. Implement Iterator to define the actual iteration ability.

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

The Dart core library defines many common exceptions and errors. Exceptions are considered conditions that you can plan ahead for and catch. Errors are conditions that you don’t expect or plan for.

A couple of the most common errors are:

- [NoSuchMethodError](https://api.dartlang.org/stable/dart-core/NoSuchMethodError-class.html)

  ​     Thrown when a receiving object (which might be null) does not implement a method.   

- [ArgumentError](https://api.dartlang.org/stable/dart-core/ArgumentError-class.html)

  ​     Can be thrown by a method that encounters an unexpected argument.   

Throwing an application-specific exception is a common way to indicate that an error has occurred. You can define a custom exception by implementing the Exception interface:

```dart
class FooException implements Exception {
  final String msg;

  const FooException([this.msg]);

  @override
  String toString() => msg ?? 'FooException';
}
```

For more information, see [Exceptions](https://dart.dev/guides/language/language-tour#exceptions)  (in the language tour) and the [Exception API reference.](https://api.dartlang.org/stable/dart-core/Exception-class.html)

