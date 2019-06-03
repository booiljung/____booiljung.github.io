[Up](./index.md)

## dart:io - 서버와 커맨드라인 앱에서 I/O (I/O for servers and command-line apps)

원문: [A tour of the core libraries](https://dart.dev/guides/libraries/library-tour)

[dart:io](https://api.dartlang.org/stable/dart-io/dart-io-library.html) 라이브러리는 파일, 디렉토리, 프로세스, 소켓, 웹 소켓 및 HTTP 클라이언트와 서버를 다루는 API를 제공합니다.

**Important:** 웹 앱이 아닌, [Flutter 모바일 앱](https://flutter.dev), 명령 행 스크립트 및 서버는  `dart:io`를 가져 와서 사용할 수 있습니다.

일반적으로 dart:io 라이브러리는 비동기 API를 구현하고 이를 증진(promote)합니다. 동기식 메소드는 애플리케이션을 쉽게 차단할 수 있으므로 확장하기가 어렵습니다. 따라서 대부분의 작업은 `Future` 또는 `Stream` 객체를 통해 결과를 반환합니다. 이는 Node.js와 같은 최신 서버 플랫폼에서 일반적인 패턴입니다.

dart:io 라이브러리의 몇 가지 동기 메소드에는 메소드 이름에 동기화 접미어가 명확하게 표시되어 있습니다. 동기 방법은 여기에서 다루지 않습니다.

dart:io 라이브러리를 사용하려면 임포트 해야 합니다:

```dart
import 'dart:io';
```

<p id = "files-and-diredotries"/>

###  파일과 디렉토리 (Files and directories)

I/O 라이브러리를 사용하면 명령 줄 프로그램에서 파일을 읽고 쓰고 디렉토리를 탐색 할 수 있습니다. 파일 내용을 읽는 방법은 한꺼번에 읽거나 또는 스트리밍하거나 중 두 가지가 있습니다. 한 번에 파일을 읽으려면 파일의 모든 내용을 저장하기에 충분한 메모리가 필요합니다. 파일이 매우 크거나 읽는 동안 파일을 처리하려면 [Streaming file contents](https://dart.dev/guides/libraries/library-tour#streaming-file-contents)에 설명 된대로 Stream을 사용해야합니다.).

<p id = "reading-a-fil-as-text"/>

#### 파일을 텍스트로 읽기 (Reading a file as text)

UTF-8을 사용하여 인코딩 된 텍스트 파일을 읽을 때 `readAsString()`을 사용하여 전체 파일 내용을 읽을 수 있습니다. 각 라인을 구분하려면  `readAsLines()`를 사용할 수 있습니다. 두 경우 모두 하나 이상의 문자열로 파일의 내용을 제공하는 `Future` 객체가 반환됩니다.

```dart
Future main() async {
  var config = File('config.txt');
  var contents;

  // Put the whole file in a single string.
  contents = await config.readAsString();
  print('The file is ${contents.length} characters long.');

  // Put each line of the file into its own string.
  contents = await config.readAsLines();
  print('The file is ${contents.length} lines long.');
}
```

<p id = "reading-a-file-as-binary"/>

#### 파일을 바이너리로 읽기 (Reading a file as binary)

다음 코드는 전체 파일을 바이트로 `int` 목록으로 읽습니다. `readAsBytes()`를 호출하면 `Future`가 반환되어 사용 가능할 때 결과를 제공합니다.

```dart
Future main() async {
  var config = File('config.txt');

  var contents = await config.readAsBytes();
  print('The file is ${contents.length} bytes long.');
}
```

<p id = "handling-errors"/>

#### 오류 다루기 (Handling errors)

catch되지 않는 예외가 발생하지 않도록 오류를 포착하려면 `Future`에 `catchError` 핸들러를 등록하거나 (비동기 함수에서) try-catch를 사용하세요.

```dart
Future main() async {
  var config = File('config.txt');
  try {
    var contents = await config.readAsString();
    print(contents);
  } catch (e) {
    print(e);
  }
}
```

<p id = "streaming-file-contents"/>

#### 파일 내용 스트리밍 하기 (Streaming file contents)

스트림을 사용하여 한 번에 조금씩 파일을 읽습니다. Dart의 [비동기 지원](../language_tour/asynchrony_support.md)의 일부인 [Stream API](https://dart.dev/guides/libraries/library-tour#stream) 또는 `await for`를 사용할 수 있습니다.

```dart
import 'dart:io';
import 'dart:convert';

Future main() async {
  var config = File('config.txt');
  Stream<List<int>> inputStream = config.openRead();

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
}
```

<p id = "writing-file-contents"/>

#### 파일 내용 쓰기 (Writing file contents)

[IOSink](https://api.dartlang.org/stable/dart-io/IOSink-class.html)를 사용하여 데이터를 파일에 쓸 수 있습니다. `File` 개체의 `openWrite()` 메소드를 사용하여 쓸 수 있는 IOSink를 얻습니다. 기본 모드인 `FileMode.write`는 파일의 기존 데이터를 완전히 덮어 씁니다.

```dart
var logFile = File('log.txt');
var sink = logFile.openWrite();
sink.write('FILE ACCESSED ${DateTime.now()}\n');
await sink.flush();
await sink.close();
```

파일의 끝 부분에 추가하려면 옵션 `mode` 매개 변수를 사용하여 `FileMode.append`를 지정하세요 :

```dart
var sink = logFile.openWrite(mode: FileMode.append);
```

바이너리 데이터를 쓰려면 `add(List<int> data)`를 사용 하세요.

<p id = "listing-files-in-a-directory"/?

#### 디렉토리 안의 파일들을 나열 (Listing files in a directory)

디렉토리의 모든 파일과 서브 디렉토리를 찾는 것은 비동기 작업입니다. `list()` 메소드는 파일이나 디렉토리가 발견 될 때 객체를 내보내는 `Stream`을 반환합니다.

```dart
Future main() async {
  var dir = Directory('tmp');

  try {
    var dirList = dir.list();
    await for (FileSystemEntity f in dirList) {
      if (f is File) {
        print('Found file ${f.path}');
      } else if (f is Directory) {
        print('Found dir ${f.path}');
      }
    }
  } catch (e) {
    print(e.toString());
  }
}
```

<p id = "other-common-functionality"/>

#### 기타 기능 (Other common functionality)

`File` 및 `Directory` 클래스에는 다음을 포함하되 이에 국한되지 않는 다른 기능이 포함되어 있습니다:

- 파일이나 디렉토리 생성: `File`과 `Directory`의 `create()`
- 파일이나 디렉토리 삭제: `File`과 `Directory`의 `delete()`
- 파일의 길이 얻기: `File`의  `length()`
- 파일에 무작위 액세스: `File`의 `open()`

Refer to the API docs for [File](https://api.dartlang.org/stable/dart-io/File-class.html) and [Directory](https://api.dartlang.org/stable/dart-io/Directory-class.html) for a full list of methods.

[File](https://api.dartlang.org/stable/dart-io/File-class.html) 및 [Directory](https://api.dartlang.org/stable/dart-io/Directory-class.html)에 대한 API 문서를 참조하세요.

<p id = "http-clients-and-servers"/>

###  HTTP 클라이언트와 서버 (HTTP clients and servers)

dart:io 라이브러리는 명령 행 애플리케이션이 HTTP 자원을 액세스하고 HTTP 서버를 실행하는 데 사용할 수 있는 클래스를 제공합니다.

<p id = "http-server"/>

#### HTTP 서버 (HTTP server)

[HttpServer](https://api.dartlang.org/stable/dart-io/HttpServer-class.html) 클래스는 웹 서버 구축을 위한 저수준 기능을 제공합니다. 요청 처리기를 일치시키고, 헤더를 설정하고, 데이터를 스트리밍하는 등의 작업을 수행 할 수 있습니다.

다음 샘플 웹 서버는 간단한 텍스트 정보를 반환합니다. 이 서버는`/dart` 경로에 대한 요청에 응답하여 포트 8888 및 주소 127.0.0.1 (localhost)에서 수신 대기합니다. 다른 경로의 경우 응답은 상태 코드 404 (페이지를 찾을 수 없음)입니다.

```dart
Future main() async {
  final requests = await HttpServer.bind('localhost', 8888);
  await for (var request in requests) {
    processRequest(request);
  }
}

void processRequest(HttpRequest request) {
  print('Got request for ${request.uri.path}');
  final response = request.response;
  if (request.uri.path == '/dart') {
    response
      ..headers.contentType = ContentType(
        'text',
        'plain',
      )
      ..write('Hello from the server');
  } else {
    response.statusCode = HttpStatus.notFound;
  }
  response.close();
}
```

<p id = "http-client"/>

#### HTTP 클라이언트 (HTTP client)

[HttpClient](https://api.dartlang.org/stable/dart-io/HttpClient-class.html) 클래스는 Dart 명령 줄 또는 서버 쪽 애플리케이션에서 HTTP 리소스에 연결할 수 있도록 도와줍니다. 헤더를 설정하고, HTTP 메소드를 사용하고, 데이터를 읽고 쓸 수 있습니다. 브라우저 기반 앱에서는 HttpClient 클래스가 작동하지 않습니다. 브라우저에서 프로그래밍 할 때 [dart:html HttpRequest class](https://api.dartlang.org/stable/dart-html/HttpRequest-class.html)를 사용하세요. 다음은 `HttpClient`를 사용하는 예입니다.

```dart
Future main() async {
  var url = Uri.parse('http://localhost:8888/dart');
  var httpClient = HttpClient();
  var request = await httpClient.getUrl(url);
  var response = await request.close();
  var data = await response.transform(utf8.decoder).toList();
  print('Response ${response.statusCode}: $data');
  httpClient.close();
}
```

<p id = "more-information"/>

###  더 많은 정보 (More information)

이 페이지는 [dart:io](https://api.dartlang.org/stable/dart-io/dart-io-library.html) 라이브러리의 주요 기능을 사용하는 방법을 보여줍니다. 이 섹션에서 논의 된 API 외에도 dart:io 라이브러리는 [processes](https://api.dartlang.org/stable/dart-io/Process-class.html) [sockets](https : //api.dartlang.org/stable/dart-io/Socket-class.html) 및 [web sockets](https://api.dartlang.org/stable/dart-io/WebSocket-class.html)에 대한 API도 제공합니다. 서버 측 및 명령 행 응용 프로그램 개발에 대한 자세한 내용은 [서버 측 Dart 개요](https://dart.dev/server)를 참조하세요.

