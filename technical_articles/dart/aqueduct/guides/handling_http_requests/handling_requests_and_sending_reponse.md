https://aqueduct.io/docs/http/controller/

# Handling Requests: Fundamentals

Aqueduct에서 HTTP 요청과 응답은 `Request`와 `Response`의 인스턴스입니다. 응용 프로그램이 수신하는 각 HTTP 요청에 대해 `Request` 인스턴스가 생성됩니다. 각 요청에 대해 '응답'을 만들어야합니다. 응답은 [컨트롤러 개체](https://aqueduct.io/docs/http/controller/)에 의해 생성됩니다. 이 가이드에서는 컨트롤러의 동작 및 초기화에 대해 설명합니다. 요청 및 응답 개체 [여기](https://aqueduct.io/docs/http/request_and_response/)에 대한 자세한 내용을 볼 수 있습니다.

## Overview

컨트롤러는 Aqueduct 애플리케이션의 기본 빌딩 블록입니다. 컨트롤러는 HTTP 요청을 어떤 식 으로든 처리합니다. 예를 들어 컨트롤러는 JSON으로 인코딩 된 도시 이름 목록을 사용하여 200 OK 응답을 반환 할 수 있습니다. 컨트롤러는 권한 부여 헤더에 올바른 자격 증명이 있는지 확인하기위한 요청을 확인할 수도 있습니다.

컨트롤러는 서로 연결되어 동작을 * 채널 *로 구성합니다. 채널은 컨트롤러의 각 동작을 순서대로 수행하여 요청을 처리합니다. 예를 들어 채널은 요청의 자격 증명을 확인한 다음 각 작업을 수행하는 두 개의 컨트롤러를 구성하여 도시 이름 목록을 반환 할 수 있습니다.

![Aqueduct Channel](https://aqueduct.io/docs/img/simple_controller_diagram.png)

당신은 그들의 요청 처리 로직을 제공하기 위해 컨트롤러를 서브 클래스하고 Aqueduct에 공통 컨트롤러 서브 클래스가 있습니다.

## Linking Controllers

컨트롤러는 `link` 메소드로 링크됩니다. 이 메서드는 채널의 다음 컨트롤러를 반환하는 클로저를 사용합니다. 다음은 두 개의 컨트롤러로 구성된 채널을 보여줍니다.

```dart
final controller = VerifyController();
controller.link(() => ResponseController());
```

위의 경우, `VerifyController`는 `ResponseController`를 링크 합니다. verifying 컨트롤러가 처리하는 요청은 요청에 응답하거나 응답 컨트롤러가 처리하도록 할 수 있습니다. verifying 컨트롤러가 응답을 보내면 응답 컨트롤러는 절대로 요청을 받지 못합니다. 원하는 수의 컨트롤러를 연결할 수 있지만 링크 된 마지막 컨트롤러는 요청에 응답해야합니다. 항상 요청에 응답하는 컨트롤러를 엔드 포인트 컨트롤러라고 합니다. *미들웨어 컨트롤러* 요청을 확인 또는 수정하고 일반적으로 오류가 발생했을 때만 응답합니다.

연결은 [응용 프로그램 채널](https://aqueduct.io/docs/application/channel/)에서 발생하며 응용 프로그램을 시작할 때 완료됩니다 (예 : 컨트롤러를 설정하고 나면 다시 변경할 수 없음). 응용 프로그램이 요청 수신을 시작합니다).

## Subclassing Controller to Handle Requests

모든 `Controller`는 요청을 처리하기 위해 `handle` 메소드를 구현합니다. 컨트롤러에서이 메서드를 재정의하여 컨트롤러에 대한 논리를 제공합니다. 다음은 항상 응답을 전송하기 때문에 엔드 포인트의 예제 입니다.

```dart
class NoteController extends Controller {
  @override
  Future<RequestOrResponse> handle(Request request) async {
    final notes = await fetchNotesFromDatabase();

    return Response.ok(notes);
  }
}
```

이 `handle` 메쏘드는 `Response` 객체를 반환합니다. 컨트롤러가 응답을 반환 할 때마다 Aqueduct는 클라이언트에 응답을 보내고 다른 컨트롤러가 처리 할 수 없도록 요청을 종료합니다.

미들웨어 컨트롤러는 응답을 제공 할 수 있거나 오류 조건에 대한 응답을 반환합니다. 예를 들어,`Authoriser` 컨트롤러는 요청의 자격 증명이 유효하지 않으면 `401 Unauthorized` 응답을 반환합니다. 요청을 다음 컨트롤러로 전달하려면 요청 오브젝트를 리턴해야합니다.

예를 들어 'Authorizer'의 의사 코드는 다음과 같습니다.

```dart
class Authorizer extends Controller {
  @override
  Future<RequestOrResponse> handle(Request request) async {
    if (isValid(request)) {
      return request;
    }

    return Response.unauthorized();
  }
}
```

Endpoint Controllers

대부분의 경우, 엔드 포인트 컨트롤러는 [ResourceController](https://aqueduct.io/docs/http/resource_controller/)를 서브 클래 싱하여 작성됩니다. 이 컨트롤러를 사용하면 컨트롤러에서 하나 이상의 핸들러 메서드를 선언하여 논리를보다 잘 구성 할 수 있습니다. 예를 들어, 한 메소드는 POST 요청을 처리 할 수 있고 다른 메소드는 GET 요청을 처리 할 수 있습니다.

### Modifying a Response with Middleware

미들웨어 컨트롤러는 요청에 *응답 modifier*를 추가 할 수 있습니다. 엔드 포인트 컨트롤러가 결국 응답을 작성할 때,이 modifier는 송신되기 전에 응답에 적용 됩니다. modifier들은 요청에 대해 `addResponseModifier`를 호출하여 추가됩니다.

```dart
class Versioner extends Controller {
  Future<RequestOrResponse> handle(Request request) async {
    request.addResponseModifier((response) {
      response.headers["x-api-version"] = "2.1";
    });
    return request;
  }
}
```

임의의 수의 컨트롤러가 요청에 응답 수정자를 추가 할 수 있습니다. 추가 된 순서대로 처리됩니다. 응답 수정자는 응답 본문이 인코딩되기 전에 적용되어 본문 개체를 조작 할 수 있게합니다. 응답 수정자는 엔드 포인트 컨트롤러에 의해 생성 된 응답에 관계없이 호출됩니다. 응답 수정자를 추가하는 동안 캐치되지 않은 오류가 발생하면 나머지 응답 수정자는 호출되지 않고 500 Server Error 응답이 전송됩니다.

## Linking Functions

간단한 동작을 위해, `handle`과 같은 서명을 가진 함수는 컨트롤러에 링크 될 수 있습니다 :

```dart
  router
    .route("/path")
    .linkFunction((req) async => req);
    .linkFunction((req) async => Response.ok(null));
```

함수를 링크하는 것은 `Controller.handle`과 같은 행동을합니다 : 요청이나 응답을 반환하고, 예외를 자동으로 처리하며, 컨트롤러 (및 함수)를 링크 할 수 있습니다.

## Controller Instantiation and Recycling

`link`가 컨트롤러 객체 대신 클로저를 사용하는 이유를 이해하는 것이 중요합니다. Aqueduct는 객체 지향 프레임워크입니다. 객체는 상태와 동작 모두를 가집니다. 응용 프로그램은 동일한 유형의 컨트롤러에서 처리 할 여러 요청을 받습니다. 변경 가능한 컨트롤러 객체가 여러 요청을 처리하기 위해 재사용 되는 경우 요청간에 여러 상태가 유지 될 수 있습니다. 이렇게하면 디버그하기 어려운 문제점이 발생할 수 있습니다.

대부분의 컨트롤러는 변경 불가능 합니다. 즉, 모든 속성이 최종적이며 설정자가 없습니다. 이것은 (대부분) 컨트롤러가 요청 간의 동작을 변경하지 않도록 합니다. 컨트롤러가 불변 인 경우, `link` 클로저가 한 번 호출되어 컨트롤러 오브젝트를 생성하고 연결 한 다음 클로저를 버립니다. 동일한 컨트롤러 객체는 모든 요청에 대해 재사용됩니다.

컨트롤러는 여러 요청에 대해 재사용 할 수 없다는 경고와 함께 변경할 수 있습니다. 예를 들어, `ResourceController`는 요청 값에 바인딩되는 속성을 가질 수 있습니다. 따라서 이러한 속성은 변경 될 것이고 요청마다 새로운 인스턴스가 생성되어야 합니다.

변경 가능한 `Controller` 서브 클래스는 `Recyclable<T>`를 구현해야 합니다. 각 요청에 대해 `link` 클로저가 호출되어 요청을 처리 할 재활용 가능한 컨트롤러의 새로운 인스턴스를 생성합니다. 컨트롤러에 값 비싼 초기화 프로세스가있는 경우 해당 초기화 결과를 한 번 계산할 수 있으며 `Recyclable<T>`의 메소드를 구현하여 각 컨트롤러 인스턴스에 다시 사용할 수 있습니다.

```dart
class MyControllerState {
  dynamic stuff;
}

class MyController extends Controller implements Recyclable<MyControllerState> {
  @override
  MyControllerState get recycledState => expensiveCalculation();

  @override
  void restore(MyControllerState state) {
    _restoredState = state;
  }

  MyControllerState _restoredState;

  @override
  FutureOr<RequestOrResponse> handle(Request request) async {
    /* use _restoredState */
    return new Response.ok(...);
  }
}
```

`recycledState` getter는 컨트롤러가 처음 링크 될 때 한 번 호출됩니다. 재활용 가능한 컨트롤러의 새로운 인스턴스마다 요청을 처리하기 전에 `restore` 메소드가 호출되고 `recycledState`에 의해 리턴 된 데이터가 인수로 전달됩니다. 예를 들어, `ResourceController`는 그것의 연산 메소드를 컴파일합니다. 컴파일 된 제품은 재활용 상태로 저장되므로 향후 인스턴스가 요청 데이터를보다 효율적으로 바인딩 할 수 있습니다.

## Exception Handling

요청 처리 중에 예외 또는 오류가 발생하면 현재 요청을 처리하는 컨트롤러가 이를 catch합니다. 포착 된 대부분의 값에 대해 컨트롤러는 500 서버 응답을 보냅니다. 예외 또는 오류의 세부 정보는 [logged](https://aqueduct.io/docs/application/configure/)됩니다. 요청은 채널에서 제거되며 링크 된 컨트롤러로 전달되지 않습니다.

이것은`Response`와 `HandlerException`을 제외한 모든 던진 값에 대한 기본 동작입니다.

### Throwing Responses

`Response`는 언제든지 던져 질 수 있습니다; 요청을 처리하는 컨트롤러가 이를 포착하여 클라이언트에 보냅니다. 이것으로 요청이 완료됩니다. 이것은 유용하지 않을 수 있습니다. 예를 들어, 다음은 이 동작을 어리석게 사용하는 경우입니다.

```dart
class Thrower extends Controller {
  @override
  Future<RequestOrResponse> handle(Request request) async {
    if (!isForbidden(request)) {
      throw new Response.forbidden();
    }
    return Response.ok(null);
  }
}
```

### Throwing HandlerExceptions

예외는 던져 질 때 디폴트 이외의 응답을 제공하기 위해 `HandlerException`을 구현할 수 있습니다. 예를 들어 은행 거래를 처리하는 응용 프로그램은 잘못된 인출에 대한 예외를 선언 할 수 있습니다.

```dart
enum WithdrawalProblem {
  insufficientFunds,
  bankClosed
}
class WithdrawalException implements Exception {
  WithdrawalException(this.problem);
  final WithdrawalProblem problem;
}
```

컨트롤러 코드는 이 예외를 포착하여 인출과 관련된 정확한 문제에 따라 다른 상태 코드를 반환 할 수 있습니다. 이 코드를 여러 곳에서 작성해야한다면 `WithdrawalException`이 `HandlerException`을 구현하는 것이 유용합니다. 구현자는 `response`에 대한 구현을 제공해야합니다 :

```dart
class WithdrawalException implements HandlerException {
  WithdrawalException(this.problem);

  final WithdrawalProblem problem;

  @override
  Response get response {
    switch (problem) {
      case WithdrawalProblem.insufficientFunds:
        return new Response.badRequest(body: {"error": "insufficient_funds"});
      case WithdrawalProblem.bankClosed:
        return new Response.badRequest(body: {"error": "bank_closed"});
    }
  }
}
```

## CORS Headers and Preflight Requests

`Controller`에는 CORS 요청을 처리하기 위한 기본 동작이 있습니다. 그들은 자동으로 `OPTIONS` 프리 플라이트 요청에 응답하고 다른 응답에 CORS 헤더를 첨부합니다. 자세한 내용은 [the chapter on CORS](https://aqueduct.io/docs/application/configure/)을 참조하십시오.