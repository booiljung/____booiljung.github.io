### Gettings Started

Dart 설치 확인

```
dart --version
```

`aqueduct` 설치

```
pub global activate aqueduct
```

### 프로젝트 생성

```
aquedcut create 플젝이름
```

버전 3.2.1 기준 폴더 구조는 다음과 같습니다.

```
플젝이름/
	.dart_tool/
	bin/
		main.dart		# 서버를 생성하고 애플리케이션을 시작 합니다.
	lib/
		channel.dart
		플젝이름.dart	  # 애플리케이션을 구성합니다.
	test/
		haness/
			app.dart
		example_test.dart
	.travis.yml
	analysis_options.yaml
	config.src.yaml
	config.yaml
	pubspec.lock
	pubspec.yaml		# 패키지를 구성합니다.
	README.md
```

### 서버 시작

로컬에서 서버를 시작하려면 다음과 같이 합니다.

```
aqueduct serve
```

또는

```
dart bin/main.dart
```

버전 3.2.1 기준으로 서버가 시작되면 다음이 출력 됩니다.

```
-- Aqueduct CLI Version: 3.2.1
-- Aqueduct project version: 3.2.1
-- Preparing...
-- Starting application '프로젝트이름/실행파일이름'
    Channel: PoomoapisChannel
    Config: /프로젝트경로/config.yaml
    Port: 8888
[INFO] aqueduct: Server aqueduct/1 started.  
[INFO] aqueduct: Server aqueduct/2 started. 
```

### localhost:8888/example

웹브라우저에서 `localhost:8888/example`를 접속해 봅니다.

```
{"key":"value"}
```

가 표시된다면 정상입니다. `lib/channel.dart`를 의 `entryPoint()` 메소드를 보면  `example/`을 처리하기 위한 채널을 볼 수 있습니다.

```dart
@override
Controller get entryPoint {
    final router = Router();
    router
        .route("/example")
        .linkFunction((request) async {
            return Response.ok({"key": "value"});
        });

    return router;
}
```



