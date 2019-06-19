https://aqueduct.io/docs/testing/clients/

# Using Aqueduct when Writing Client Applications

클라이언트 응용 프로그램을 개발하는 동안 Aqueduct 서버를 로컬로 실행하는 것은 개발 프로세스의 중요한 부분입니다. `bin/main.dart` 스크립트 또는 `aqueduct serve`를 통해 응용 프로그램을 실행하십시오. 전자는 디버거를 사용하여 응용 프로그램을 [디버깅](https://aqueduct.io/docs/testing/debugger/) 할 수 있습니다.

## Enable Logging and Return Server Errors

`ApplicationChannel.logger`에 청취자를 등록하여 클라이언트 애플리케이션을 개발하는 동안 로깅이 켜져 있는지 확인하십시오.

```dart
class MyApplicationChannel extends ApplicationChannel {
  @override
  Future prepare() async {
    logger.onRecord.listen((record) {
      print("$record ${record.error ?? ""} ${record.stackTrace ?? ""}");
    });
  }
  ...
}
```

디버깅 중에 켜는 유용한 기능은 500 개의 서버 오류 응답에 대한 스택 추적을 보내는 것입니다. 디버깅하는 동안 `ApplicationChannel`에서 이 플래그를 켜십시오 :

```dart
class MyApplicationChannel extends ApplicationChannel {
  @override
  Future prepare() async {
    Controller.includeErrorDetailsInServerErrorResponses = true;
  }
  ...
}
```

500 오류가 발생하면 서버는 스택 추적을 클라이언트에 다시 보내서 터미널을 전환하지 않고 클라이언트 응용 프로그램을 개발하는 동안 이를 볼 수 있습니다. 프로덕션 코드에서는 이 속성을 절대로 사용하지 않아야합니다.

## Avoid Port Conflicts

응용 프로그램은 `aqueduct serve`를 기본으로 포트 8888로 실행됩니다. `--port` 명령 행 옵션을 사용하여 다른 포트를 선택할 수 있습니다:

```sh
aqueduct serve --port 4000
```

## Provision a Database for Client Testing

ORM을 사용하는 응용 프로그램의 경우 응용 프로그램의 데이터 모델과 일치하는 스키마가있는 로컬로 실행중인 데이터베이스가 있어야합니다.

OAuth 2.0을 사용하는 경우 클라이언트 식별자도 로컬에서 실행중인 데이터베이스에 추가해야합니다. [aqueduct auth](https://aqueduct.io/docs/auth/cli/) 명령 줄 도구로 클라이언트 식별자를 추가 할 수 있습니다.