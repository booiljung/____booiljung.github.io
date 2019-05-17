## dart:convert - decoding and encoding JSON, UTF-8, and more

The dart:convert library ([API reference](https://api.dartlang.org/stable/dart-convert/dart-convert-library.html)) has converters for JSON and UTF-8, as well as support for creating additional converters. [JSON](https://www.json.org/) is a simple text format for representing structured objects and collections. [UTF-8](https://en.wikipedia.org/wiki/UTF-8) is a common variable-width encoding that can represent every character in the Unicode character set.

The dart:convert library works in both web apps and command-line apps. To use it, import dart:convert.

```dart
import 'dart:convert';
```

###  Decoding and encoding JSON

Decode a JSON-encoded string into a Dart object with `jsonDecode()`:

```dart
// NOTE: Be sure to use double quotes ("),
// not single quotes ('), inside the JSON string.
// This string is JSON, not Dart.
var jsonString = '''
  [
    {"score": 40},
    {"score": 80}
  ]
''';

var scores = jsonDecode(jsonString);
assert(scores is List);

var firstScore = scores[0];
assert(firstScore is Map);
assert(firstScore['score'] == 40);
```

Encode a supported Dart object into a JSON-formatted string with `jsonEncode()`:

```dart
var scores = [
  {'score': 40},
  {'score': 80},
  {'score': 100, 'overtime': true, 'special_guest': null}
];

var jsonText = jsonEncode(scores);
assert(jsonText ==
    '[{"score":40},{"score":80},'
        '{"score":100,"overtime":true,'
        '"special_guest":null}]');
```

Only objects of type int, double, String, bool, null, List, or Map (with string keys) are directly encodable into JSON. List and Map objects are encoded recursively.

You have two options for encoding objects that aren’t directly encodable. The first is to invoke `encode()` with a second argument: a function that returns an object that is directly encodable. Your second option is to omit the second argument, in which case the encoder calls the object’s `toJson()` method.

For more examples and links to JSON-related packages, see [JSON Support](https://dart.dev/guides/json).

###  Decoding and encoding UTF-8 characters

Use `utf8.decode()` to decode UTF8-encoded bytes to a Dart string:

```dart
List<int> utf8Bytes = [
  0xc3, 0x8e, 0xc3, 0xb1, 0xc5, 0xa3, 0xc3, 0xa9,
  0x72, 0xc3, 0xb1, 0xc3, 0xa5, 0xc5, 0xa3, 0xc3,
  0xae, 0xc3, 0xb6, 0xc3, 0xb1, 0xc3, 0xa5, 0xc4,
  0xbc, 0xc3, 0xae, 0xc5, 0xbe, 0xc3, 0xa5, 0xc5,
  0xa3, 0xc3, 0xae, 0xe1, 0xbb, 0x9d, 0xc3, 0xb1
];

var funnyWord = utf8.decode(utf8Bytes);

assert(funnyWord == 'Îñţérñåţîöñåļîžåţîờñ');
```

To convert a stream of UTF-8 characters into a Dart string, specify `utf8.decoder` to the Stream `transform()` method:

```dart
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
```

Use `utf8.encode()` to encode a Dart string as a list of UTF8-encoded bytes:

```dart
List<int> encoded = utf8.encode('Îñţérñåţîöñåļîžåţîờñ');

assert(encoded.length == utf8Bytes.length);
for (int i = 0; i < encoded.length; i++) {
  assert(encoded[i] == utf8Bytes[i]);
}
```

###  Other functionality

The dart:convert library also has converters for ASCII and ISO-8859-1 (Latin1). For details, see the [API reference for the dart:convert library.](https://api.dartlang.org/stable/dart-convert/dart-convert-library.html)