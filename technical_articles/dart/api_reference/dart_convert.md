# dart:convert library 

JSON 및 UTF-8을 비롯한 다양한 데이터 표현간에 변환하기 위한 인코더 및 디코더

공용 데이터 표현을 위한 컨버터 외에도 이 라이브러리는 컨버터를 쉽게 연결하고 스트림과 함께 사용할 수 있도록 컨버터를 구현할 수 있도록 지원합니다.

코드에서 이 라이브러리를 사용하려면 다음을 수행하세요:

```dart
import 'dart:convert';
```

가장 일반적으로 사용되는 컨버터는 [`JsonCodec`](https://api.dartlang.org/stable/2.3.1/dart-convert/JsonCodec-class.html)과 [`Utf8Codec`](https : //api.dartlang.org/stable/2.3.1/dart-convert/Utf8Codec-class.html)의 최상위 인스턴스로 이름으로  [`json`](https://api.dartlang.org/stable/2.3.1/dart-convert/json-constant.html) 및 [`utf8`](https://api.dartlang.org/stable/2.3.1/dart-convert/utf8-constant.html)을 참조하십시오.

JSON은 구조화 된 객체 및 컬렉션을 나타내는 간단한 텍스트 형식입니다. JSON 인코더/디코더는 JSON 형식을 사용하여 문자열과 객체 구조 (예: List 및 Map) 사이를 변환합니다.

UTF-8은 유니 코드 문자 집합의 모든 문자를 나타낼 수 있는 일반적인 가변 폭 인코딩입니다. UTF-8 인코더/디코더는 문자열과 바이트 사이를 변환합니다.

변환기는 종종 스트림과 함께 사용되어 스트림을 통해 제공되는 데이터를 변환 합니다. 다음 코드는 두 개의 컨버터를 사용합니다. 첫 번째는 UTF-8 디코더입니다. 이 디코더는 파일에서 읽은 바이트 수에서 UTF-8로 데이터를 변환합니다. 두 번째는 [`LineSplitter`](https://api.dartlang.org/stable/2.3.1/dart-convert/LineSplitter-class.html)의 인스턴스로 새 라인 경계에서 데이터를 분리합니다.

```dart
var lineNumber = 1;
var stream = File('quotes.txt').openRead();

stream.transform(utf8.decoder)
      .transform(const LineSplitter())
      .listen((line) {
        if (showLineNumbers) {
          stdout.write('${lineNumber++} ');
        }
        stdout.writeln(line);
      });
```

당신의 커스텀 컨버터를 만드려면 [Codec](https://api.dartlang.org/stable/2.3.1/dart-convert/Codec-class.html) 및 [Convert](https://api.dartlang.org/stable/2.3.1/dart-convert/Converter-class.html) 클래스 문서를 참조하세요.