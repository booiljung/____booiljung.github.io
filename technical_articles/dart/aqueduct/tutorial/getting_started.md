https://aqueduct.io/docs/tut/getting-started/

# 1. Getting Started

이 튜토리얼을 마치면 PostgreSQL 데이터베이스에서 가상의 heroes을 위한 Aqueduct 애플리케이션을 만들게 될 것입니다. 당신은 다음을 배울 것입니다 :

- Run an Aqueduct application
- Route HTTP requests to the appropriate handler in your code
- Store and retrieve database data
- Write automated tests for each endpoint
- Require authorization for HTTP requests

Getting Help

If at anytime you get stuck, hop on over to the [Aqueduct Slack channel](http://slackaqueductsignup.herokuapp.com). You can also see a finished version of this application [here](https://github.com/stablekernel/aqueduct_examples/tree/master/heroes).

## Installation

To get started, make sure you have the following software installed:

1. Dart ([Install Instructions](https://www.dartlang.org/install))
2. IntelliJ IDEA or any other Jetbrains IDE, including the free Community Edition ([Install Instructions](https://www.jetbrains.com/idea/download/))
3. The IntelliJ IDEA Dart Plugin ([Install Instructions](https://www.dartlang.org/tools/jetbrains-plugin))

Install the `aqueduct` command line tool by running the following command in your shell:

```
pub global activate aqueduct
```

If you get warning text about your `PATH`, make sure to read it before moving on.

## Creating a Project

Create a new project named `heroes` by entering the following in your shell:

```
aqueduct create heroes
```

This creates a `heroes` project directory. Open this directory with IntelliJ IDEA by dragging the project folder onto IntellIJ IDEA's icon.

In IntelliJ's project view, locate the `lib` directory; this is where your project's code will go. This project has two source files - `heroes.dart` and `channel.dart`. Open the file `heroes.dart`. Click `Enable Dart Support` in the top right corner of the editor.

## Handling HTTP Requests

브라우저에서 <http://aqueduct-tutorial.stablekernel.io>로 이동하십시오. 이 브라우저 응용 프로그램은 `Hero Manager`입니다. 사용자가 영웅을 보고, 만들고, 삭제하고, 업데이트 할 수 있습니다. (이것은 영웅 튜토리얼의 [AngularDart Tour](https://webdev.dartlang.org/angular/tutorial)의 약간 수정 된 버전입니다.) 가져 오기 위해 `http://localhost:8888`로 HTTP 요청을 하고 hero 데이터를 조작합니다. 이 자습서에서 빌드 할 응용 프로그램은 이러한 요청에 응답합니다.

Running the Browser Application Locally

브라우저 응용 프로그램은 HTTP를 통해 제공되므로 사용자 시스템에서 로컬로 실행될 때 Aqueduct 응용 프로그램에 액세스 할 수 있습니다. 실제로 안전하지 않기 때문에 브라우저가 안전하지 않은 웹 페이지로 이동하는 것에 대해 경고 할 수 있습니다. [여기](https://github.com/stablekernel/tour-of-heroes-dart)에서 소스 코드를 가져 와서 이 응용 프로그램을 로컬로 실행할 수 있습니다.

In this first chapter, you will write code to handle two requests:  one to get a list of heroes, and the other to get a single hero by its  identifier. Those requests are:

1. `GET /heroes` to the list of heroes
2. `GET /heroes/:id` to get an individual hero

HTTP Operation Shorthand

The term `GET /heroes` is called an operation. It is the  combination of the HTTP method and the path of the request. Each  operation is  unique to an application, so your code is segmented into  units for each operation. Sections with a colon, like the ':id' segment,  are  variable: they can be 1, 2, 3, and so on.

### Controller Objects Handle Requests

요청은 *컨트롤러 객체*에 의해 처리됩니다. 컨트롤러 객체는 요청에 응답 할 수 있습니다. 또한 다른 작업을 수행하고 다른 컨트롤러가 응답하게 할 수 있습니다. 예를 들어 요청이 승인되었는지 확인하거나 분석 데이터를 다른 서비스로 보낼 수 있습니다. 컨트롤러는 함께 구성되며 컴포지션의 각 컨트롤러는 논리를 순서대로 수행합니다. 이를 통해 일부 컨트롤러를 재사용하고 코드 구성을 개선 할 수 있습니다. 요청을 컨트롤러를 통해 한 방향으로 전달하기 때문에 컨트롤러들의 구성을 *채널*이라고합니다.

Our application will link two controllers:

- a `Router` that makes sure the request path is `/heroes` or `/heroes/:id`
- a `HeroesControllers` that responds with hero objects

응용 프로그램은 *응용 프로그램 채널*이라는 채널 객체로 시작합니다. 응용 프로그램의 컨트롤러를이 채널에 연결합니다.

![ApplicationChannel entryPoint](https://aqueduct.io/docs/img/entrypoint.png)

Each application has a subclass of `ApplicationChannel` that you override methods in to set up your controllers. This type is already declared in `lib/channel.dart` - open this file and find `ApplicationChannel.entryPoint`:

```dart
  @override
  Controller get entryPoint {
    final router = Router();

    router
      .route('/example')
      .linkFunction((request) async {
        return Response.ok({'key': 'value'});
      });

    return router;
  }
```

애플리케이션이 요청을 받으면 `entryPoint` 컨트롤러가 이를 처리하는 첫 번째 컨트롤러입니다. 여기서는`Controller`의 서브 클래스인`Router`입니다.

Controller Subclassing

여러분이 사용하는 모든 컨트롤러는 `Controller`의 하위 클래스가 될 것입니다. 공통적인 동작을 위해 Aqueduct에 이미있는 컨트롤러 하위 클래스가 있습니다.

라우터에서 `route` 메소드를 사용하여 컨트롤러를 *route*에 연결합니다. route는 요청 경로와 일치하는 문자열 구문입니다. 현재 구현에서, 경로는 모든 요청을 `/example` 경로와 일치시킵니다. 요청이 수신되면 링크된 함수가 실행되고 JSON 객체 본문과 함께 `200 OK` 응답을 반환합니다.

우리는 `/heroes` 경로를 우리 자신의 컨트롤러로 라우트 할 필요가 있기 때문에 어떤 일이 일어나는지 제어 할 수 있습니다. `HeroesController`를 만들어 봅시다. `lib/controller/heroes_controller.dart`에 새 파일을 만들고 다음 코드를 추가하십시오 (서브 디렉토리 `lib/controller/`를 만들어야 합니다) :

```dart
import 'package:aqueduct/aqueduct.dart';
import 'package:heroes/heroes.dart';

class HeroesController extends Controller {
  final _heroes = [
    {'id': 11, 'name': 'Mr. Nice'},
    {'id': 12, 'name': 'Narco'},
    {'id': 13, 'name': 'Bombasto'},
    {'id': 14, 'name': 'Celeritas'},
    {'id': 15, 'name': 'Magneta'},    
  ];

  @override
  Future<RequestOrResponse> handle(Request request) async {
    return Response.ok(_heroes);
  }
}
```

Right now, our `HeroesController` isn't hooked up to the application channel. We need to link it to the router. First, import our new file at the top of `channel.dart`.

현재, 우리의 `HeroesController`는 애플리케이션 채널에 연결되어 있지 않습니다. 라우터에 연결해야 합니다. 먼저, 새로운 파일을 `channel.dart` 맨 위로 가져옵니다.

```dart
import 'controller/heroes_controller.dart';
```

그런 다음 이 `HeroesController`를 경로 `/heroes`의 `Router`에 연결하십시오:

```dart
@override
Controller get entryPoint {
  final router = Router();

  router
    .route('/heroes')
    .link(() => HeroesController());

  router
    .route('/example')
    .linkFunction((request) async {
      return new Response.ok({'key': 'value'});
    });

  return router;
}
```

우리는 이제 영웅들의 목록을 돌려 줄 응용 프로그램을 가지고 있습니다. 프로젝트 디렉토리에서 명령 행에서 다음 명령을 실행하십시오.

```
aqueduct serve
```

그러면 응용 프로그램이 로컬에서 실행됩니다. 브라우저 페이지 `http://aqueduct-tutorial.stablekernel.io`을 새로 고침하십시오.  `http://localhost:8888/heroes`을 요청하면 응용 프로그램이 이를 제공합니다. 웹 브라우저에 영웅이 보입니다.

#### Screenshot of Heroes Application

![Aqueduct Heroes First Run](https://aqueduct.io/docs/img/run1.png)

You can also see the actual response of your request by entering the following into your shell:

```
curl -X GET http://localhost:8888/heroes
```

You'll get JSON output like this:

```
[
  {"id":11,"name":"Mr. Nice"},
  {"id":12,"name":"Narco"},
  {"id":13,"name":"Bombasto"},
  {"id":14,"name":"Celeritas"},
  {"id":15,"name":"Magneta"}
]
```

You'll also see this request logged in the shell that you started `aqueduct serve` in.

## Linking Controllers

컨트롤러가 요청을 처리 할 때 응답을 보내거나 연결된 컨트롤러 중 하나가 요청을 처리하도록 할 수 있습니다. 기본적으로 `Router` 는 모든 요청에 대해 404 찾을 수 없음 응답을 보냅니다. `Router` 에 경로를 추가하면 컨트롤러를 연결할 수있는 새 채널의 진입 점이 생깁니다. 우리의 응용 프로그램에서, `Router` 는 `/heroes` 경로와 연결되어 있습니다.

컨트롤러는 엔드 포인트와 미들웨어의 두 가지 종류로 나뉩니다. `HeroesController`와 같은 엔드 포인트 컨트롤러는 항상 응답을 보냅니다. 그들은 요청이 찾고 있는 행동을 구현합니다. `Router`와 같은 미들웨어 컨트롤러는 엔드 포인트 컨트롤러에 도달하기 전에 요청을 처리합니다. 예를 들어 라우터는 올바른 컨트롤러로 지시하여 요청을 처리합니다. `Authorizer`와 같은 컨트롤러는 요청의 승인을 확인합니다. 모든 종류의 컨트롤러를 만들어 원하는 동작을 제공 할 수 있습니다.

채널에는 0 개 또는 다수의 미들웨어 컨트롤러가 있을 수 있지만 엔드 포인트 컨트롤러에서 끝나야 합니다. 대부분의 컨트롤러는 하나의 연결된 컨트롤러만 가질 수 있지만 `Router`는 많은 컨트롤러를 허용합니다. 예를 들어, 더 큰 응용 프로그램은 다음과 같이 보일 수 있습니다.

```dart
@override
Controller get entryPoint {
  final router = Router();

  router
    .route('/users')
    .link(() => APIKeyValidator())
    .link(() => Authorizer.bearer())
    .link(() => UsersController());

  router
    .route('/posts')
    .link(() => APIKeyValidator())
    .link(() => PostsController());

  return router;
}
```

이 객체들 각각은 `Controller`의 하위 클래스로서 요청을 처리하기 위해 함께 연결될 수 있는 기능을 제공합니다. 요청은 연결된 순서대로 컨트롤러를 거칩니다. `/users` 경로에 대한 요청은 `APIKeyValidator`, `Authorizer` 그리고 마지막으로 `UsersController`를 거쳐 갈 것입니다. 이 컨트롤러들 각각은 응답 할 기회를 가지며 다음 컨트롤러가 요청을 받지 못하게합니다.

## Advanced Routing

지금 당장은 `GET /heroes` 요청을 처리합니다. 브라우저 응용 프로그램은이 목록을 사용하여 영웅 대시 보드를 채 웁니다. 개별 영웅을 클릭하면 브라우저 응용 프로그램에 개별 영웅이 표시됩니다. 이 페이지로 이동하면 브라우저 응용 프로그램이 서버에 개별 영웅을 요청합니다. 이 요청에는 경로에서 선택한 영웅의 고유 ID가 포함됩니다 (예 : `/heroes/11` 또는 `/heroes/13`.

우리 서버는 아직 이 요청을 처리하지 않습니다. `/heroes` 경로와 정확히 일치하는 요청만 처리합니다. 개별 영웅에 대한 요청에는 영웅에 따라 변경되는 경로가 있으므로 *path variable*를 포함하는 경로가 필요합니다.

경로 변수는 들어오는 요청 경로의 동일한 세그먼트 값과 일치하는 경로 세그먼트입니다. 경로 변수는 콜론 (`:`) 접두사가 붙은 세그먼트입니다. 예를 들어 `/heroes/:id` 경로는 `id`라는 경로 변수를 포함합니다. 요청 경로가 `/heroes/1`, `/heroes/2` 등이라면 요청은 `HeroesController`에게 보내집니다. `HeroesController`는 경로 변수의 값에 접근하여 어떤 영웅을 반환 할 것인지 결정할 것입니다.

딸꾹질이 하나 있네.  `/heroes/:id` 경로는 더 이상 `/heroes` 경로와 일치하지 않습니다. `/heroes`과 `/heroes/:id`가 모두 `HeroesController`에 들어간다면 코드를 편성하는 것이 훨씬 쉽습니다. 그것은 heroic stuff 을 합니다. 이런 이유 때문에 경로의 `:id` 부분을 대괄호로 싸서 선택적으로 선언 할 수 있습니다. `channel.dart`에서 `/heroes` 경로를 수정하십시오:

```dart
router
  .route('/heroes/[:id]')
  .link(() => HeroesController());
```

경로의 두 번째 부분은 선택 사항이므로 `/heroes` 경로는 여전히 경로와 일치합니다. 경로에 두 번째 세그먼트가 포함되어 있으면 해당 세그먼트의 값은 `id`라는 경로 변수에 바인딩 됩니다. 우리는 `Request` 객체를 통해 경로 변수에 접근 할 수 있습니다.  `heroes_controller.dart`에서 `handle`을 수정 하십시오 :

```dart
// In just a moment, we'll replace this code with something even better,
// but it's important to understand where this information comes from first!
@override
Future<RequestOrResponse> handle(Request request) async {
  if (request.path.variables.containsKey('id')) {
    final id = int.parse(request.path.variables['id']);
    final hero = _heroes.firstWhere((hero) => hero['id'] == id, orElse: () => null);
    if (hero == null) {
      return Response.notFound();
    }
    return Response.ok(hero);
  }
  return Response.ok(_heroes);
}
```

현재 응용 프로그램을 실행중인 셸에서 Ctrl-C를 눌러 응용 프로그램을 중지하십시오. 그런 다음 `aqueduct serve`를 다시 실행하십시오. 브라우저 응용 프로그램에서 영웅을 클릭하면 해당 영웅에 대한 세부 정보 페이지로 이동합니다.

![Screenshot of Hero Detail Page](https://aqueduct.io/docs/img/run2.png)

당신은 `curl -X GET http://localhost:8888/heroes/11`을 실행하여 서버가 올바르게 응답하고 있는지를 검증하여 단일 영웅 객체를 볼 수 있습니다. 존재하지 않는 영웅을 얻음으로써 404 찾을 수 없음 응답을 트리거 할 수도 있습니다.

## ResourceController와 Operation 메소드

우리의 `HeroesController`는 지금 당장 OK입니다. 그러나 곧 문제가 생길 것입니다: 우리가 새로운 영웅을 만들고 싶다면 어떻게 될까요? 또는 기존 영웅의 이름을 업데이트 하시겠습니까? 우리의`handle` 메쏘드는 관리하기 어려워지기 시작합니다.

그것은 `ResourceController`가 들어있는 곳입니다. `ResourceController`는 당신이 우리 영웅들에게 수행 할 수 있는 각 작업에 대해 다른 메소드를 생성 할 수 있게 해줍니다. 하나의 방법은 영웅의 목록을 얻는 것을 처리 할 것이고, 다른 하나는 영웅을 얻는 것을 처리 할 것입니다. 각 메소드에는 요청이 트리거해야 하는 HTTP 메소드와 경로 변수를 식별하는 주석이 있습니다.

`heroes_controller.dart`에서 `HeroesController`를 다음과 같이 바꿉니다:

```dart
class HeroesController extends ResourceController {
  final _heroes = [
    {'id': 11, 'name': 'Mr. Nice'},
    {'id': 12, 'name': 'Narco'},
    {'id': 13, 'name': 'Bombasto'},
    {'id': 14, 'name': 'Celeritas'},
    {'id': 15, 'name': 'Magneta'},
  ];

  @Operation.get()
  Future<Response> getAllHeroes() async {
    return Response.ok(_heroes);
  }

  @Operation.get('id')
  Future<Response> getHeroByID() async {
    final id = int.parse(request.path.variables['id']);
    final hero = _heroes.firstWhere((hero) => hero['id'] == id, orElse: () => null);
    if (hero == null) {
      return Response.notFound();
    }

    return Response.ok(hero);
  }
}
```

우리가 `ResourceController`에서 `handle`을 오버라이드 할 필요가 없음을 주목하십시오. `ResourceController`는 이 메소드를 구현하여 *operation 메소드* 중 하나를 호출합니다. `getAllHeroes` 및 `getHeroByID`와 같은 연산 메소드는`Operation` 주석을 가져야 합니다. 명명 된 생성자인 `Operation.get`은 요청 메소드가 GET 일 때 호출되는 것을 의미합니다. 연산 메소드는 또한 `Future<Response>`를 리턴 해야 한합니다.

`getHeroByID`의 어노테이션에는 인수 (경로 변수`id`의 이름)도 있습니다. 해당 경로 변수가 요청의 경로에 존재하면 `getHeroByID`가 호출됩니다. 존재하지 않으면 `getAllHeroes`가 호출됩니다.

Naming Operation Methods

`getHeroById`와 같은 일반적인 영어 문구는 operation 메소드에 있어 매우 유용한 이름이며 좋은 이름은 코드에서 OpenAPI 설명서를 생성 할 때 유용 합니다.

`aqueduct serve`를 실행하는 터미널에서 Ctrl-C를 눌러 응용 프로그램을 다시 로드 한 다음 `aqueduct serve`를 다시 실행하십시오. 브라우저 응용 프로그램은 여전히 동일하게 동작해야 합니다.

Browser Clients

`curl` 외에도 로컬로 실행중인 응용 프로그램에 대한 요청을 실행하는 SwaggerUI 브라우저 응용 프로그램을 만들 수 있습니다. 프로젝트 디렉토리에서 `aqueduct document client`를 실행하면 `client.html` 파일이 생성 됩니다. 브라우저에서 이 파일을 열어 응용 프로그램에서 지원하는 요청을 생성하고 실행하는 UI를 찾습니다.

## Request Binding

우리의 `getHeroByID` 메쏘드에서, 경로 변수 'id'가 정수로 파싱 될 수 있다는 위험한 가정을 합니다. 'id'가 문자열처럼 뭔가 다른 경우 'int.parse`는 예외를 throw합니다. 오퍼레이션 메소드에서 예외가 발생하면 컨트롤러는 이를 catch하고 500 Server Error 응답을 보냅니다. 500 대는 나쁩니다. 그들은 클라이언트에게 무엇이 잘못되었는지를 말하지 않습니다. 404 Not Found가 여기에 더 나은 응답이지만 예외를 잡아 내고 이 응답을 작성하는 코드를 작성하는 것은 번거로운 일입니다.

대신 *Request Binding*이라는 오퍼레이션 메소드의 기능에 의존 할 수 있습니다. 오퍼레이션 메소드는 매개 변수를 선언하고 요청의 특성에 바인드 할 수 있습니다. 우리의 오퍼레이션 메쏘드가 호출 될 때, 요청으로부터의 값이 인자로 전달 될 것입니다. 오퍼레이션 바인딩은 자동으로 값을 매개 변수 유형으로 구문 분석합니다 (구문 분석이 실패 할 경우 더 나은 오류 응답을 리턴합니다). `getHeroByID()`메소드를 변경하십시오 :

```dart
@Operation.get('id')
Future<Response> getHeroByID(@Bind.path('id') int id) async {
  final hero = _heroes.firstWhere((hero) => hero['id'] == id, orElse: () => null);

  if (hero == null) {
    return Response.notFound();
  }

  return Response.ok(hero);
}
```

경로 변수`id`의 값은 정수로 파싱되어 `id` 매개 변수에서 이 메소드에 사용 가능합니다. 오퍼레이션 메소드 매개 변수의 `@Bind` 주석은 Aqueduct에게 우리가 바운드 할 요청의 값을 알려 줍니다. 명명 된 생성자 `Bind.path`를 사용하면 경로 변수가 바인드 되고 해당 변수의 이름이이 생성자에 대한 인수에 표시됩니다.

경로 변수, 헤더, 쿼리 매개 변수 및 본문을 바인딩 할 수 있습니다. 경로 변수를 바인딩 할 때 `@Bind.path (pathVariableName)`의 인수를 가진 경로 변수를 지정해야 합니다.

Bound Parameter Names

바인드 된 매개 변수의 이름이 경로 변수의 이름과 일치 할 필요는 없습니다. 우리는 그것을`@Bind.path('id') int heroID`라고 선언 할 수 있었습니다. `Bind`의 생성자에 대한 인수 만이 path 변수의 실제 이름과 일치해야 합니다. 이것은 유효하지 않은 문자를 포함 할 수 있는 헤더와 같은 다른 유형의 바인딩에 유용합니다. 예를 들자면 `X-API-Key`.

## The More You Know: Multi-threading and Application State

이 간단한 실습에서 우리는 heroes 목록을 데이터 소스로 사용했습니다. getting-your-feet-wet 데모를 보려면 이 방법을 사용하십시오. 그러나 실제 응용 프로그램에서는 이 데이터를 데이터베이스에 저장합니다. 그렇게하면 데이터를 추가 할 수 있으며 응용 프로그램을 다시 시작할 때 데이터를 잃을 위험이 없습니다.

일반적으로 웹 서버는 변경 될 수 있는 데이터에 절대로 응답하지 않아야 합니다. 이전에는 모범 사례 였지만 stateless 웹 서버는 Kubernetes와 같은 컨테이너화 및 도구의 보급에 대한 요구 사항이 되었습니다. Aqueduct는 멀티 스레딩 (multi-threading) 전략을 사용하여 이 규칙 위반을 쉽게 감지 할 수 있습니다.

Aqueduct 응용 프로그램을 실행할 때 여러 스레드가 생성됩니다. 이 스레드들 각각은 메모리에 자체 격리 된 힙을 가지고 있습니다. 한 스레드에 있는 데이터는 다른 스레드에서 액세스 할 수 없음을 의미합니다. Dart에서 격리 스레드는 *isolates*라고합니다.

응용 프로그램 채널의 인스턴스가 각 격리에 대해 만들어집니다. 각각의 HTTP 요청은 처리 할 isolates 중 하나에 제공됩니다. 어떤 의미에서 하나의 응용 프로그램은 로드 밸런서 뒤에 있는 여러 서버에서 응용 프로그램을 실행하는 것과 동일하게 작동합니다. (또한 응용 프로그램을 훨씬 빠르게 만듭니다.)

애플리케이션에 데이터를 저장하는 경우 정말 빠릅니다. 왜냐하면, 데이터 변경 요청은 응용 프로그램 격리 중 하나에서만 해당 데이터를 변경합니다. 해당 데이터를 다시 요청하면 해당 변경 사항이 표시되지 않을 가능성이 높습니다. 다른 데이터로 격리 된 요청이 해당 요청을 처리 할 수 있습니다.