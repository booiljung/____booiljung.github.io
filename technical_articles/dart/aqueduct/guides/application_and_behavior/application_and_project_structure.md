https://aqueduct.io/docs/application/structure/

# Application and Project Structure

이 문서의 목적은 Aqueduct 응용 프로그램을 구성하는 객체와 해당 객체가 서로 협력하여 HTTP 요청을 처리하는 방법을 이해하는 것입니다. 또한 파일 시스템의 프로젝트 구조에 대해서도 설명합니다.

## Controllers are Building Blocks

Aqueduct 응용 프로그램의 구성 요소는 [Controllers](https://aqueduct.io/docs/http/controller/)입니다. 각 컨트롤러 유형에는 어떤 식 으로든 HTTP 요청을 처리하는 논리가 있습니다. 컨트롤러는 함께 연결되어 * 채널 *을 형성합니다. 일련의 일련의 컨트롤러. 채널은 컨트롤러의 동작을 구성합니다.

예를 들어 요청의 권한 정보가 올바른지 확인하는 `Authorizer`컨트롤러와 비밀 정보가 포함 된 응답을 보내는 `SecretController`를 생각해보십시오. 이 두 컨트롤러를 함께 구성하여 비밀을 보내기 전에 자격 증명을 확인하는 채널이 있습니다. 컨트롤러와 채널의 이점은 컨트롤러가 여러 채널에서 재사용 될 수 있다는 것입니다. `Authorizer`는 로직을 변경하지 않고 다른 유형의 컨트롤러를 보호 할 수 있습니다.

![Simple Controller Diagram](https://aqueduct.io/docs/img/simple_controller_diagram.png)

채널의 마지막 컨트롤러는 항상 요청에 응답해야합니다. 이러한 유형의 제어기를 엔드 포인트 컨트롤러라고 하며 응용 프로그램의 엔드 포인트에 대한 비즈니스 로직을 구현합니다. 예를 들어, 엔드 포인트 컨트롤러는 데이터베이스에서 책 목록을 가져 와서 응답으로 보낼 수 있습니다.

채널의 다른 컨트롤러는 *미들웨어 컨트롤러*라고합니다. 이러한 유형의 컨트롤러는 일반적으로 채널의 다음 컨트롤러가 요청을 처리하기 전에 요청에 대해 확인합니다. 미들웨어 컨트롤러는 요청에 응답 할 수 있지만 이렇게하면 채널의 나머지 컨트롤러가 요청을 처리하지 못하게 됩니다.

예를 들어, "권한 부여"컨트롤러는 승인되지 않은 요청으로부터 엔드 포인트 컨트롤러를 보호하는 `401 Unauthorized` 응답을 보낼 수 있습니다. "캐싱"컨트롤러는 캐시의 정보로 응답을 보낼 수 있으므로 엔드 포인트 컨트롤러가 값 비싼 쿼리를 수행하지 못합니다.

미들웨어와 엔드 포인트 컨트롤러는 모두 `Controller` (또는 서브 클래스)의 인스턴스입니다. 미들웨어 컨트롤러는 일반적으로 재사용 가능하지만 엔드 포인트 컨트롤러는 일반적으로 재사용 할 수 없습니다. 미들웨어 컨트롤러를 재사용 할 수 없는 경우 해당 논리는 채널에서 선행하는 끝점 컨트롤러에 더 적합 할 수 있습니다.

대부분의 엔드 포인트 컨트롤러는 [ResourceController](https://aqueduct.io/docs/http/resource_controller/) 서브 클래스 (자체는 '컨트롤러'의 서브 클래스)에 의해 작성됩니다. 이 클래스를 사용하면 주어진 엔드 포인트에 대해 각 HTTP 메소드 (GET 또는 POST 등)에 대한 메소드를 구현할 수 있습니다.

## The Application Channel and Entry Point

각 응용 프로그램은 하나의 A 트롤러를 응용 프로그램의 * 진입 점 *으로 지정합니다. 이 컨트롤러는 처음으로 새 요청을 수신하며 응용 프로그램 채널의 헤드입니다. 대부분의 응용 프로그램에서 진입 점은 [라우터](https://aqueduct.io/docs/http/routing/)입니다. 이 컨트롤러는 여러 채널을 연결하여 효과적으로 채널을 하위 채널로 분할합니다.

![Structure](https://aqueduct.io/docs/img/structure.png)

위의 다이어그램은 코드에서 다음과 같습니다.

```dart
class AppChannel extends ApplicationChannel {
  @override
  Controller get entry {
    final router = new Router();

    router
      .route("/a")
      .link(() => new AController());

    router
      .route("/b")
      .link(() => new Authorizer(...))
      .link(() => new BController());

    router
      .route("/c")
      .link(() => new Authorizer(...))
      .link(() => new CController());   

    return router;
  }
}
```

응용 프로그램 채널 및 진입 점에 대한 자세한 내용은 [이 가이드](https://aqueduct.io/docs/application/channel/)를 참조하십시오.

## Aqueduct Project Structure and Organization

Aqueduct 프로젝트는 최소한 다음 파일 구조가 포함 된 디렉토리입니다.

```yaml
pubspec.yaml
lib/
  application_name.dart
```

Dart 응용 프로그램의 이름은 `pubspec.yaml`의 `name` 키에 의해 정의됩니다. `aqueduct serve`가 여러분의 응용 프로그램을 실행하기 위해서는 같은 이름을 가진 `lib /`에 `.dart` 파일이 있어야 합니다. 이것은 응용 프로그램 라이브러리 파일이며 `ApplicationChannel` 하위 클래스를 선언하거나 수행하는 파일을 가져와야 합니다. 이것은 Aqueduct 응용 프로그램을 실행하기위한 최소 요구 사항입니다. (실행중인 응용 프로그램에 대한 자세한 내용은 [Deploying](https://aqueduct.io/docs/deploy/)을 참조하십시오.)

합리적인 크기의 응용 프로그램을 구성하려면 다음 구조를 사용하는 것이 좋습니다.

```yaml
pubspec.yaml
config.src.yaml
config.yaml
lib/
  application_name.dart
  channel.dart  
  controller/
    user_controller.dart
  model/
    user.dart
test/
  user_controller_test.dart
  harness/
    app.dart
```

The required `pubspec.yaml` and `lib/application_name.dart` files are present alongside a few others:

- `config.yaml`: A [configuration file](https://aqueduct.io/docs/application/configure/) for the running application.
- `config.src.yaml`: A [template for config.yaml](https://aqueduct.io/docs/application/configure/).
- `channel.dart`: A file solely for the `ApplicationChannel` of an application. This file should be *exported* from `application_name.dart`.
- `controller/`: A directory for `Controller` subclass files.
- `model/`: A directory for `ManagedObject<T>` subclass files.
- `test/harness/app.dart`: A [test harness](https://aqueduct.io/docs/testing/tests/)) for automated testing.

Feel free to create other subdirectories in `lib/` for organizing other types of files.

## Aqueduct and dart:io

Aqueduct는 `dart:io`를 기반으로 실행되며 `HttpServer` 구현에 의존합니다. Aqueduct 응용 프로그램이 시작되면 하나 이상의 `HttpServer` 인스턴스가`aqueduct serve`에 지정된 포트에 바인딩됩니다. 각 HTTP 요청에 대해 `dart:io`에서 `HttpRequest`를 래핑하기 위해`Request` 인스턴스가 생성됩니다. `Request`는 `ApplicationChannel`에 추가되고 응답 될 때까지 `Controller` 채널을 통해 보내집니다.

드문 경우지만, 응용 프로그램 채널에서 `Request`를 제거하고 `dart:io`만으로 요청을 조작 할 수 있습니다. 일단 제거되면, `HttpRequest.response`를 닫고 속성을 설정함으로써 요청에 응답해야 할 책임이 있습니다. 채널에서 요청을 받으려면 `Controller`에서`null`을 반환하면됩니다:

```dart
@override
Controller get entryPoint {
  final router = new Router();

  router
    .route("/bypass_aqueduct")
    .linkFunction((req) async {
      req.response.statusCode = 200;
      req.response.close();

      return null;
    });

  return router;
}
```

This technique is valuable when Aqueduct can't do something you want it to do or when using [websockets](https://aqueduct.io/docs/http/websockets/).