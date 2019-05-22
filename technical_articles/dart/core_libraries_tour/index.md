# A tour of the core libraries

원문: [A tour of the core libraries](https://dart.dev/guides/libraries/library-tour)

이 페이지는 다트의 코어 라이브러리에서 주요 기능을 사용하는 방법을 보여 줍니다. 이것은 단지 개요이며 포괄적인 것은 아닙니다. 클래스에 대한 자세한 내용이 필요하면 [Dart API reference](https://api.dartlang.org/stable)를 참조하십시오.

- [dart:core](./dart_core.md)

  내장 타입, 컬렉션 및 기타 코어 기능. 이 라이브러리는 모든 다트 프로그램에 자동으로 임포트 됩니다.

- [dart:async](./dart_async.md)

  퓨처 및 스트림과 같은 클래스를 사용한 비동기 프로그래밍 지원.

- [dart:math](./dart_math.md)

  수학 상수 및 함수, 난수 발생기.

- [dart:convert](./dart_convert.md)

  JSON 및 UTF-8을 비롯한 다양한 데이터 표현간에 변환하기 위한 인코더 및 디코더

- [dart:html](./dart_html.md)

  브라우저 기반 앱 용 DOM 및 기타 API

- [dart:io](./dart_io.md)

  Flutter 응용 프로그램, 서버 및 명령 줄 스크립트를 포함하여 다트 VM을 사용할 수 있는 프로그램의 I/O

이 페이지는 단지 개요일 뿐입니다. 이것은 극소의 dart: 라이브러리만을 포함하며 서드파트 라이브러리들은 다루지 않습니다.

라이브러리 정보를 찾을 수있는 다른 곳은 [Pub site](https://pub.dev)와 [Dart web developer library guide](https://dart.dev/web/libraries)입니다. [Dart API reference](https://api.dartlang.org/stable)의 모든 dart:* 라이브러리에 대한 API 문서를 찾을 수 있습니다. 또는 Flutter를 사용하는 경우 [Flutter API reference.](https://api.flutter.dev)를 보세요.

## 요약

이 페이지는 Dart의 내장 라이브러리에서 가장 일반적으로 사용되는 기능을 소개합니다. 이것은 모든 내장 라이브러리를 커버하지 않습니다. 더 필요한 정보 [dart:collection](https://api.dartlang.org/stable/dart-collection/dart-collection-library.html) 과 [dart:typed_data,](https://api.dartlang.org/stable/dart-typed_data/dart-typed_data-library.html) 이나 플랫폼에 특화된 라이브러리인 [Dart web development libraries](https://dart.dev/web/libraries) 를 [Flutter libraries.](https://api.flutter.dev) 참조 하세요.

[pub package manager](https://dart.dev/guides/packages) 를 사용하여 더 많은 라이브러리를 얻을 수 있습니다. [collection,](https://pub.dev/packages/collection) [crypto,](https://pub.dev/packages/crypto) [http,](https://pub.dev/packages/http) [intl,](https://pub.dev/packages/intl) 및 [test](https://pub.dev/packages/test) 라이브러리들은 pub를 사용하여 얻을 수 있는 샘플일 뿐입니다.

다트 언어에 대해 알고 싶으면 [language tour](../language_tour/index.md) 참고하세요.`