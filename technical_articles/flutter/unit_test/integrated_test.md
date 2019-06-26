# Flutter Integrated Test

어느 유닛 테스트는 개발 환경에서 테스트 할 수 있지만, 어느 테스트들은 장치나 에뮬레이터에서 테스트 해야 하기도 합니다. 이런 경우를 위해 Flutter는 '통합 테스트'를 지원합니다. 이 통합 테스트는 `flutter_driver` 패키지에 의해 지원 됩니다.

`pubspec.yaml`을 편집하여 `dev_dependencies:`에 `flutter_driver`패키지를 추가 합니다.

```yaml
dev_dependencies:
  flutter_driver:
    sdk: flutter
  test: any
```

IDE를 사용중이라면 `pubspec.yaml`을 편집 후 저장하면 자동으로 패키지를 설치할 것입니다. 그렇지 않으면

```
flutter pub get
```

를 터미널에서 실행해 줍니다.

`lib/main.dart`가 버튼을 누르면 1이 증가하는 앱이라고 보겠습니다.

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Counter App',
      home: MyHomePage(title: 'Counter App Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              // Provide a Key to this specific Text widget. This allows
              // identifing the widget from inside the test suite,
              // and reading the text.
              key: Key('counter'),
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        // Provide a Key to this button. This allows finding this
        // specific button inside the test suite, and tapping it.
        key: Key('increment'),
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}
```

이 앱이 실행된 상태에서 `flutter_driver`로 단위 테스트가 진행 됩니다.

테스트 유닛 소스 파일은 조건이 구조가 정해져 있습니다.

-  `test_driver\` 폴더에 위치해야 합니다.
- 하나의 유닛 테스트는 런처 파일과 테스트 파일 쌍으로 만들어져야 합니다.
- 런처가 `파일명.dart` 이라면, 테스트 파일은 `파일명_test.dart` 가 됩니다.

예를 들어 디렉토리 구조를 보면 아래와 같습니다.

```
example\
	lib\
		main.dart
	test_driver\
		app.dart
		app_test.dart
		widget.dart
		widget_test.dart
```

이 디렉토리 구조를 보면 `test_driver\`에 4개의 파일이 있습니다.

- `app.dart`와 `app_test.dart`가 세트입니다.
- `widget.dart`와 `widget_test.dart`가 세트입니다.

런처 파일 내용은 아래와 같습니다.

```dart
import 'package:flutter_driver/driver_extension.dart';

// 테스트에 메인 소스 파일을 포함 합니다.
import 'package:example/main.dart' as app;

void main() {
	// flutter driver를 활성화하고
	enableFlutterDriverExtension();

  	// 앱의 main()을 호출합니다.
  	app.main();
}
```

그리고 유닛 테스트 파일은 아래와 같습니다.

```dart
// test와 flutter_driver 패키지를 임포트 합니ㅏㄷ.
import 'package:test/test.dart';
import 'package:flutter_driver/flutter_driver.dart';

void main() {
	group('Example App', () {
        // 테스트에서 액세스할 위젯을 미리 찾아 둡니다.
        final counterTextFinder = find.byValueKey('counter');
        final buttonFinder = find.byValueKey('increment');

        FlutterDriver driver;

        // flutter driver에 TCP/IP로 연결 합니다. 
        setUpAll(() async {
            driver = await FlutterDriver.connect();
        });

        // 테스트가 끝나면 flutter driver를 정리합니다.
        tearDownAll(() async {
            if (driver != null) {
                driver.close();
            }
        });

        test('starts at 0', () async {
            // `driver.getText` 메소드를 호출하여 위젯의 타이틀이 0에서 시작하는지 확인합니다.
            expect(
                await driver.getText(counterTextFinder),
                "0"
            );
        });

        test('increments the counter', () async {
            // 버튼을 탭 합니다.
            await driver.tap(buttonFinder);

            // 위젯의 타이틀이 1로 증가하였는지 확인 합니다.
            expect(
                await driver.getText(counterTextFinder),
                "1"
            );
        });
	});
}
```

이 유닛 테스트를 진행하려면 터미널에서

```sh
$ flutter drive --target=test_driver/app.dart
```

을 실행 합니다.

```
Using device Android SDK built for x86.
Starting application: test_driver/app.dart
Initializing gradle...                                              1.0s
Resolving dependencies...                                           1.6s
Installing build/app/outputs/apk/app.apk...                         2.6s
Running Gradle task 'assembleDebug'...                                  
Running Gradle task 'assembleDebug'... Done                         5.3s
Built build/app/outputs/apk/debug/app-debug.apk.
I/flutter (16238): Observatory listening on http://127.0.0.1:52941/-WARPx52Xpw=/
00:00 +0: App test (setUpAll)
[info ] FlutterDriver: Connecting to Flutter application at http://127.0.0.1:41223/-WARPx52Xpw=/
[trace] FlutterDriver: Isolate found with number: 799847553
[trace] FlutterDriver: Isolate is paused at start.
[trace] FlutterDriver: Attempting to resume isolate
[trace] FlutterDriver: Waiting for service extension
[info ] FlutterDriver: Connected to Flutter application.
00:01 +0: App test starts at 0
00:01 +1: App test increments the counter
00:02 +2: App test (tearDownAll)
00:02 +2: All tests passed!
Stopping application instance.
$
```

소스 파일 쌍을 추가하여 유닛 테스트를 추가 할 수 있습니다. 위에서 `widget.dart`와 `widget_test.dart`가 그것입니다.  `widget.dart `는 직접 위젯을 시작하도록 하겠습니다.

```dart
import 'package:flutter_driver/driver_extension.dart';
import 'package:flutter/material.dart';

import 'package:example/main.dart' as app;

void main() {
	enableFlutterDriverExtension();

    // main 함수를 호출하는 대신 테스트용 위젯을 생성하도록 하였습니다.
	runApp(
		TestApp()
	);
}


class TestApp extends StatelessWidget {
	...
}
```

그리고 `widget_test.dart`에서 단위 테스트를 수행합니다.

```dart
import 'package:test/test.dart';
import 'package:flutter_driver/flutter_driver.dart';

void main() {
	group('Example App', () {
        ...
	});
}
```

### CommonFider

`CommonFider`는 위젯을 찾는데 사용됩니다.

## FlutterDriver

`FlutterDriver` 의 테스트 주요 메소드들은 다음과 같습니다.

| 메소드               | 설명                                                         |      |
| -------------------- | ------------------------------------------------------------ | ---- |
| `enterText`          | 현재 **포커스된** Editable Text에 텍스트를 입력합니다.       |      |
| `getText`            | 지정한 `SerializableFinder`에서 문자열을 얻습니다.           |      |
| `scroll`             | 지정한 `SerializableFinder`를 일정한 거리만큼 스크롤 합니다. |      |
| `scrollIntoView`     | 지정한 `SerializableFinder`를 특정 좌표가 보이도록 스크롤 합니다. |      |
| `scrollUntilVisible` | 지정한 `SerializableFinder`를 지정한 위젯이 보이도록 스크롤 합니다. |      |
| `tab`                | 지정한 `SerializableFinder`을 탭 합니다.                     |      |
| `waitFor`            | 지정한 `SerializableFinder`가 위치할 때까지 대기합니다.      |      |
| `waitForAbsent`      | 지정한 `SerializableFinder`가 사라질 때까지 대기합니다.      |      |
| `checkHealth`        | 연결 상태를 확인 합니다.                                     |      |
| `forceGC`            | 가비지콜렉션을 강제로 진행합니다.                            |      |
| `screenshot`         | 화면을 캡쳐 합니다.                                          |      |

보다 자세한 내요은 참조를 확인하시기 바랍니다.

## 참조

[A introduction to integration testing](https://flutter.dev/docs/cookbook/testing/integration/introduction)