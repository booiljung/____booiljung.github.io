# Starting and Stopping an Application

Learn how an application is initialized so it can serve requests.

## Overview

응용 프로그램은 Aqueduct 프로젝트 디렉토리에서 `aqueduct serve` 또는 `dart bin/main.script`를 실행함으로써 시작됩니다. 어느 쪽이든, 많은 스레드가 생성되고 각 스레드에서 `ApplicationChannel` 서브 클래스가 인스턴스화됩니다. 채널 하위 클래스는 종종 다음과 같은 응용 프로그램 비헤이비어를 초기화합니다.

- 환경 특정 설정을 위한 구성 데이터를 읽습니다.
- [데이터베이스 연결](https://aqueduct.io/docs/db/)과 같은 서비스 개체를 초기화합니다.
- 요청을 처리 할 [controller](https://aqueduct.io/docs/http/controller/) 개체를 설정합니다.

### Initializing ApplicationChannel Controllers

각 응용 프로그램 채널에는 들어오는 모든 요청을 처리하는 컨트롤러인  `entryPoint`가 있습니다. 이 컨트롤러는 대개 요청을 적절한 처리기로 라우팅하는 라우터입니다. 응용 프로그램에서 사용되는 모든 컨트롤러는 어떤 방식 으로든 엔트리 포인트 중 하나에 연결됩니다. 다음은 그 예입니다.

```dart
class AppChannel extends ApplicationChannel {
  @override
  Controller get entryPoint {
    final router = new Router();

    router
      .route("/users")
      .link(() => Authorizer())
      .link(() => UserController());

    return router;
  }
}
```

이 메소드는 `Router` -> `Authorizer` -> `UserController`를 연결합니다. 라우터는이 메서드에서 반환되기 때문에 들어오는 모든 요청을 처리합니다. 요청 경로가 `/users` 일 때 `Authorizer`에게 요청을 전달할 것입니다. `/users`는 승인 된 경우 `UserController`에 요청을 전달합니다.

응용 프로그램의 모든 컨트롤러는 직접 또는 간접적으로 진입 점에 연결됩니다.

컨트롤러 연결하기

`link()`메쏘드는 새로운 컨트롤러를 생성하는 클로저를 사용합니다. 일부 컨트롤러는 각 요청에 대해 인스턴스화되고 다른 컨트롤러는 모든 요청에 대해 재사용 됩니다. 자세한 내용은 [the chapter on controllers](https://aqueduct.io/docs/http/controller/)을 참조하십시오.

## Initializing Service Objects

컨트롤러는 종종 애플리케이션 외부에서 정보를 얻거나 생성해야 합니다. 가장 일반적인 예는 데이터베이스 액세스이지만 다른 REST API, 연결된 장치 등이 될 수 있습니다. *서비스 객체*는 외부 시스템과 작동하는 데 필요한 정보와 동작을 캡슐화합니다. 컨트롤러와 서비스 객체 간의 이러한 관심사의 분리는 구조화되고 테스트가 가능한 더 나은 코드를 허용합니다.

서비스 객체는 `ApplicationChannel.prepare`를 오버라이드함으로써 인스턴스화됩니다.

```dart
class AppChannel extends ApplicationChannel {
  PostgreSQLConnection database;

  @override
  Future prepare() async {
    database = PostgreSQLConnection();
  }

  @override
  Controller get entryPoint {
    final router = new Router();

    router
      .route("/users")
      .link(() => new Authorizer())
      .link(() => new UserController(database));

    return router;
  }
}
```

이 메소드는 `entryPoint` 전에 호출됩니다. 생성 된 서비스를 인스턴스 변수에 저장하여 `entryPoint`에 컨트롤러에 삽입 할 수 있습니다. 서비스는 컨트롤러의 생성자 인수를 통해 주입됩니다. 예를 들어 위의 코드는`UserController`에 주입 된 `database` 서비스를 보여줍니다.

## Application Channel Configuration

서비스 객체 사용의 이점은 응용 프로그램이 실행되는 환경에 따라 코드를 변경하지 않고도 변경할 수 있다는 것입니다. 예를 들어 테스트를 실행하는 것보다 프로덕션 환경에서 실행될 때 응용 프로그램이 연결될 데이터베이스가 달라집니다.

서비스 구성 외에도 응용 프로그램이 취하려고하는 다른 유형의 초기화가 있을 수 있습니다. 일반적인 작업은 코덱을`CodecRegistry`에 추가하거나 디폴트`CORSPolicy`를 설정하는 것입니다. 이 모든 초기화는`prepare()`에서 행해집니다.

응용 프로그램을 구성하는 데 필요한 일부 정보는 구성 파일이나 환경 변수에서 나옵니다. 구성 파일 및 환경 변수를 사용하여 초기화를 안내하는 방법에 대한 자세한 내용은 [이 가이드](https://aqueduct.io/docs/application/configure/)를 참조하십시오.

## Multi-threaded Aqueduct Applications

Aqueduct 응용 프로그램은 여러 스레드에 분산 될 수 있으며 분산되어야 합니다. 이를 통해 응용 프로그램은 여러 CPU를 활용하고 요청을보다 빠르게 처리 할 수 있습니다. Dart에서는 스레드를 *isolates*라고 합니다. 각 격리에 대해 `ApplicationChannel` 인스턴스가 생성됩니다. 응용 프로그램이 HTTP 요청을 받으면 요청은 이러한 인스턴스의 진입 점 중 하나로 전달됩니다. 이러한 인스턴스는 서로의 복제본이며 어떤 인스턴스가 요청을 처리하는지는 중요하지 않습니다. 이 격리 채널 아키텍처는 동일한 응용 프로그램을 실행하는 여러 서버를 실행하는 것과 매우 유사합니다.

응용 프로그램에서 사용할 isolates 수는 [aqueduct serve](https://aqueduct.io/docs/cli/running/) 명령을 사용할 때 시작할 때 구성 할 수 있습니다.

격리는 다른 격리와 메모리를 공유 할 수 없습니다. 오브젝트가 하나의 격리에서 작성되면 * 다른 오브젝트가 참조 할 수 없습니다. 따라서 각 ApplicationChannel 인스턴스에는 동일한 방식으로 구성된 자체 서비스 집합이 있습니다. 이 동작은 연결 풀링과 같은 디자인 패턴을 암시적으로 만듭니다. 데이터베이스 연결 풀 대신 자체 데이터베이스 연결이 있는 응용 프로그램 채널 풀이 있습니다.

이 아키텍처는 의도적으로 응용 프로그램에서 상태를 유지하지 못하게 합니다. 여러 서버로 확장 할 때 이미 단일 서버 노드에서 효과적으로 클러스터링 중이므로 클러스터가 올바르게 작동 함을 신뢰할 수 있습니다. 멀티 스레드 응용 프로그램 작성에 대한 자세한 내용은 [이 안내서](https://aqueduct.io/docs/application/threading/)를 참조하십시오.

## Initialization Callbacks

`prepare()`와 `entryPoint`는 모두 응용 프로그램 채널 초기화 프로세스의 일부입니다. 대부분의 응용 프로그램에는 이 두 가지 방법 만 필요합니다. 거의 사용되지 않는 다른 메소드는 `willStartReceivingRequests()`입니다. 이 메소드는 `prepare ()`와 `entryPoint`가 실행 된 후에 호출되며, 애플리케이션이 요청을 받기 바로 전에 호출됩니다.

이러한 세 가지 초기화 콜백은 분리 된 채널에서 실행중인 채널을 초기화하기 위해 분리 채널 당 한 번 호출됩니다. 응용 프로그램이 시작될 때마다 발생해야하는 초기화의 경우 (실행중인 분리 수에 관계없이), `ApplicationChannel` 서브 클래스는 `initializeApplication()`이라는 정적 메소드를 구현할 수 있습니다.

```dart
class AppChannel extends ApplicationChannel {
  static Future initializeApplication(ApplicationOptions options) async {
    ... do one time setup ...
  }

  ...
}
```

이 메소드는`ApplicationChannel` 인스턴스가 생성되기 전에 호출됩니다. `options`에 대한 모든 변경 사항은 각`ApplicationChannel`의 `options` 속성에서 사용할 수 있습니다.

예 :

```dart
class AppChannel extends ApplicationChannel {

  static Future initializeApplication(ApplicationOptions options) async {        
    options.context["special item"] = "xyz";
  }  

  Future prepare() async {
    var parsedConfigValues = options.context["special item"]; // == xyz
    ...
  }
}
```

각 isolate에는 자체 힙이 있습니다. `initializeApplication`은 주 격리에서 실행되는 반면, 각 `ApplicationChannel`은 자체 격리에서 인스턴스화됩니다. 즉, `ApplicationOptions`에 저장된 값은 분리 된 항목을 통과하는 것이 안전해야 합니다. 즉, 클로저에 대한 참조를 포함 할 수 없습니다.

또한 주 분리 자에 설정된 전역 변수 또는 정적 속성은 다른 격리에서 설정되지 않습니다. `CodecRegistry`와 같은 설정 유형은 정적 속성을 사용하여 코덱의 저장소에 대한 참조를 보유하기 때문에 분리 된 곳에서 값을 공유하지 않습니다. 그러므로 그들은 `ApplicationChannel.prepare()`에 설정 되어야 합니다.

또한 Dart에서 정적 메서드를 재정의 할 수 없으므로 `initializeApplication`의 이름과 서명이이 코드 샘플에 표시된 것과 정확히 일치하는지 확인하는 것이 중요합니다. 불행히도 분석기는 여기 당신을 도울 수 없습니다.                                                                                                                     

## Application Channel File

`ApplicationChannel` 서브 클래스는 `lib/channel.dart`라는 이름의 자체 파일에서 가장 자주 선언됩니다. 이 파일은 응용 프로그램 라이브러리 파일에서 내 보내야 합니다. 예를 들어, 응용 프로그램의 이름이 `wildfire`라면 응용 프로그램 라이브러리 파일은 `lib/wildfire.dart`입니다. 다음은 샘플 디렉토리 구조입니다.

```
wildfire/
  lib/
    wildfire.dart
    channel.dart
    controllers/
      user_controller.dart      
    ...
```

See [this guide](https://aqueduct.io/docs/application/structure/) for more details on how an Aqueduct application's files are structured.

## Lazy Services

많은 서비스 개체가 지속적인 네트워크 연결을 설정합니다. 때때로 네트워크 연결이 중단되어 다시 연결되어야 합니다. 이러한 연결이 응용 프로그램이 처음 시작될 때만 열리면 응용 프로그램은 응용 프로그램을 다시 시작하지 않고서는 이러한 연결을 다시 열 수 없습니다. 이것은 매우 나쁠 것입니다.

따라서 서비스는 자체적인 연결 동작을 관리해야 합니다. 예를 들어, 데이터베이스 연결은 쿼리를 실행하라는 메시지가 나타나면 연결해야 합니다. 이미 유효한 연결이 있으면 쿼리가 실행됩니다. 그렇지 않으면 연결을 설정 한 다음 쿼리를 실행합니다. 호출자는 관심이 없으며 원하는 결과를 가진 `Future`를 얻습니다.

의사 코드는 다음과 같이 보입니다:

```dart
Future execute(String sql) async {
  if (connection == null || !connection.isAvailable) {
    connection = new Connection(...);
    await connection.open();
  }

  return connection.executeSQL(sql);
}
```

## The Application Object

이 토론에는 `Application<T>`객체가 숨겨져 있습니다. `aqueduct serve` 명령은 `Application<T>`인스턴스를 생성하기 때문에 여러분의 코드는 거의 이 타입에 관심을 갖지 않습니다.

`Application<T>`는 Aqueduct 응용 프로그램의 최상위 객체입니다. HTTP 리스너를 설정하고 요청을 `ApplicationChannel`으로 보냅니다. `Application<T>`자체는 `ApplicationChannel`을 위한 일반적인 컨테이너 일뿐입니다; 모든 것을 걷어 차기(kick off)는 것 이외에 많은 것을 하지는 않습니다.

응용 프로그램의 `start` 메소드는 응용 프로그램의`ApplicationChannel` 인스턴스를 적어도 하나 이상 초기화 합니다. 이 초기화 프로세스 중에 문제가 발생하면 응용 프로그램에서 예외를 throw하고 서버 시작을 중지합니다. 예를 들어, `ApplicationChannel` 서브 클래스에서 유효하지 않은 라우트를 설정하면 이러한 유형의 시작 예외가 트리거됩니다.

`Application<T>`는 HTTP 요청을 수신 대기하는 포트나 사용할 SSL 인증서와 같은 HTTP 요청을 수신하는 방법을 결정하는 많은 옵션을 가지고 있습니다. 이 값은 채널의 `options` 속성 인 `ApplicationOptions`의 인스턴스에서 사용할 수 있습니다.

