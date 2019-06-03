[Up](./index.md)

## dart:convert - 디코딩 또는 인코딩 (decoding and encoding JSON, UTF-8, and more)

원문: [A tour of the core libraries](https://dart.dev/guides/libraries/library-tour)

dart:convert 라이브러리 ([API reference](https://api.dartlang.org/stable/dart-convert/dart-convert-library.html))는 JSON 및 UTF-8 용 컨버터와 추가 컨버터를 생성합니다. [JSON](https://www.json.org/)은 구조화 된 객체 및 컬렉션을 나타내는 간단한 텍스트 형식입니다. [UTF-8](https://en.wikipedia.org/wiki/UTF-8)은 유니코드 문자 집합의 모든 문자를 나타낼 수 있는 일반적인 가변 길이(variable-width) 인코딩입니다.

dart:convert 라이브러리는 웹 앱과 명령 줄 앱 모두에서 작동합니다. 그것을 사용하려면, `import dart:convert`를 하세요

```dart
import 'dart:convert';
```

<p id = "decoding-and-encoding-json"/?

###  JSON 디코딩과 인코등 (Decoding and encoding JSON)

`jsonDecode()`를 사용하여 JSON 인코딩 문자열을 Dart 객체로 디코딩:

```dart
// NOTE: JSON 문자열 안에는
// 작은 따옴표(')가 아닌 큰 따옴표 (")를 사용해야 합니다.
// 이것은 JSON 문자열이지 Dart 문자열이 아닙니다.
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

지원되는 Dart 객체를 JSON 형식의 문자열로 `jsonEncode()`로 인코딩하세요:

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

`int`, `double`, `String`, `bool`, `null`, `List` 또는 `Map` (문자열 키 포함)의 객체만 JSON으로 직접 인코딩 할 수 있습니다. `List` 및 `Map` 객체는 재귀적으로 인코딩됩니다.

직접 인코딩 할 수 없는 객체를 인코딩하는 두 가지 옵션이 있습니다. 첫 번째는 두 번째 인자를 지정하여  `encode()`를 호출하는 것이고, 바로 인코딩을 하는 객체를 반환하는 함수입니다. 두 번째 옵션은 두 번째 인수를 생략하는 것이며, 이 경우 엔코더는 객체의 `toJson()` 메소드를 호출합니다.

JSON 관련 패키지에 대한 예제와 링크는 [JSON Support](https://dart.dev/guides/json)를 참조하세요.

<p id = "decoding-and-encoding-utf-8-characters"/>

###  UTF-8 문자 디코딩 하기 (Decoding and encoding UTF-8 characters)

UTF8로 인코딩 된 바이트를 Dart 문자열로 디코딩하려면 `utf8.decode()`를 사용 하세요:

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

UTF-8 문자의 스트림을 Dart 문자열로 변환하려면 `Stream`의  `transform()`메소드에 `utf8.decoder`를 지정하세요.

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

`utf8.encode()`를 사용하여 Dart 문자열을 UTF8로 인코딩 된 바이트 목록으로 인코딩 하세요:

```dart
List<int> encoded = utf8.encode('Îñţérñåţîöñåļîžåţîờñ');

assert(encoded.length == utf8Bytes.length);
for (int i = 0; i < encoded.length; i++) {
  assert(encoded[i] == utf8Bytes[i]);
}
```

###  기타 기능 (Other functionality)

dart:convert 라이브러리에는 ASCII 및 ISO-8859-1 (Latin1) 용 변환기도 있습니다. 자세한 내용은 [API reference for the dart:convert library](https://api.dartlang.org/stable/dart-convert/dart-convert-library.html)를 참조하십시오.