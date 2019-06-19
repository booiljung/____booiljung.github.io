https://aqueduct.io/docs/deploy/script/

# Deploying without aqueduct serve

`aqueduct serve`  대신 독립 스크립트로 Aqueduct 응용 프로그램을 실행할 수도 있습니다. 실제로 `aqueduct serve`는 응용 프로그램을 실행하기 위한 임시 Dart 스크립트를 만듭니다. `aqueduct create`로 응용 프로그램을 만든 경우, 독립 실행형 응용 프로그램이 이미 `bin/main.dart`라는 이름으로 프로젝트에 존재합니다.

샘플 스크립트는 다음과 같습니다.

```dart
import 'dart:async';
import 'dart:io';

import 'package:aqueduct/aqueduct.dart';
import 'package:my_application/my_application.dart';

Future main() async {
  var app = new Application<MyApplicationChannel>()
    ..options.port = 8888
    ..options.configurationFilePath = "config.yaml";

  await app.start(numberOfInstances: 3);    
}
```

이 스크립트는`aqueduct serve` 대신에 사용할 수 있지만 CLI가 아닌 이 스크립트에서 모든 `ApplicationOptions`를 설정해야 합니다.