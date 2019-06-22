원문: https://aqueduct.io/docs/http/routing/

# Routing

## What is routing?

모든 HTTP 요청에는 URL이 있습니다. URL은 * resource *을 식별합니다. 초기 인터넷에서 리소스는 파일이었습니다. 예를 들어, URL  `http://www.geocities.com/my_page/image.jpg`는 www.geocities.com에 위치한 웹 서버의 my_page 폴더에서 image.jpg 파일을 반환 합니다. 오늘날 웹 응용 프로그램에서 자원은 데이터베이스 나 연결된 장치와 같은 많은 다른 데이터 원본에서 비롯됩니다. 웹 응용 프로그램의 작업은 해당 자원이 어디에서 왔는지에 관계없이 URL에 대한 자원을 제공, 작성 또는 수정하는 것입니다.

URL은 여러 부분으로 구성되며 일부는 선택 사항입니다. 우리가 인간으로 보는 전형적인 URL은`http://stablekernel.com/about`입니다. 대부분의 사람들은이 URL을 브라우저에 입력하면 회사의 "정보"페이지로 이동하게 됩니다. 즉, '정보'페이지는 리소스이고 URL은이를 식별합니다.

좀 더 일반적으로, "About"페이지 URL은 * scheme * (`http`), * host * (`stablekernel.com`), * path * (`/about`) URL의 세 가지 필수 구성 요소를 가지고 있습니다. . 호스트는 자원 제공을 담당하는 컴퓨터를 지정하고 경로는 자원을 식별하며 체계는 요청자와 호스트 모두 정보를 교환하는 방법을 알립니다.

Aqueduct 응용 프로그램은 스키마가 `http` (또는`https`)이고 호스트가 응용 프로그램이 실행중인 컴퓨터를 참조 할 때 요청을 수신합니다. 따라서 응용 프로그램이 요청을 받으면 나머지 구성 요소 인 경로 만 고려합니다.

Aqueduct에서 `Router`는 요청 경로에 따라 `Controller`에 `Request`s를 라우팅합니다. 이 프로세스를 * 라우팅 *이라고합니다. 응용 프로그램이 시작되면 경로는`ApplicationChannel` 하위 클래스에 등록됩니다. 등록 된 각 라우트는 요청을 처리 할`Controller`의 새로운 [channel](https://aqueduct.io/docs/application/structure/)을 만듭니다.

## Route Specifications Match HTTP Request Paths

A route is registered by invoking `Router.route`. This method takes a *route specification* - a `String`  with some syntax rules that will match the path of a request. This  registration occurs when an application first starts by overriding `ApplicationChannel.entryPoint`. For example:

라우트는 `Router.route`를 호출하여 등록됩니다. 이 메소드는 요청 경로와 일치하는 구문 규칙이 있는 `String`을 경로 지정으로 사용합니다. 이 등록은 응용 프로그램이`ApplicationChannel.entryPoint`를 재정의하여 처음 시작할 때 발생합니다. 예:

```dart
class MyApplicationChannel extends ApplicationChannel {
  @override
  Controller get entryPoint {
    final router = new Router();

    router
      .route("/users")
      .linkFunction((req) async => new Response.ok(await getAllUsers());

    return router;
  }
}
```

`route`에 대한 인수는 경로 지정 문자열입니다. 이 특정 경로는 `/users` 경로와 일치합니다. 즉, `http://myserver.com/users` URL에 대한 요청은 `linkFunction` 클로저에 의해 처리 될 것입니다. (경로가 컴파일 될 때 선행 및 후행 슬래시는 제거되므로 아무런 효과가 없지만 선행 슬래시를 표시하는 것이 좋습니다.)

경로에는 여러 세그먼트 (슬래시 사이의 문자)가 있을 수 있습니다. 예를 들어, `/users/foo` 경로는 `users`와 `foo`의 두 경로 세그먼트를 가지고 있습니다. 라우트 스펙은 경로의 각 세그먼트를 각 세그먼트와 일치시킵니다. 경로와 경로의 세그먼트 수는 같아야 합니다. 따라서 경로 지정 `/users/foo`는 `/users/foo` 경로와 일치하지만 `/users`, `/users/7` 또는 `/users/foo/1` 경로와 일치하지 않을 것입니다.

### Path Variables

라우트 스펙은 * 경로 변수 *를 가질 수 있습니다. 경로 변수는 경로 세그먼트에서 값을 캡처하여 코드에서 사용할 수 있도록합니다. 경로 변수는 `/users/1`과 `/users/2`와 같은 어떤 식별자로 리소스를 유일하게 식별하는데 사용됩니다.

경로 지정에서 경로 변수는 콜론 (`:`)으로 시작합니다. 변수의 이름은이 콜론 뒤에 옵니다. 예를 들어, `userID`라는 경로 변수를 선언하는 다음 경로를 고려하십시오.

```dart
router.route("/users/:userID")
```

이 경로 지정은 `/users/1`, `/users/2`, `/users/foo` 등과 일치합니다. `userID`의 값은 각각 `1`, `2`, `foo`입니다. 이 경로는 `/users` 또는 `/users/1/2`와 일치하지 않습니다.

### Optional Path Segments

경로는 선택적 경로 세그먼트를 가질 수 있습니다. 이렇게하면 모든 리소스 모음과 해당 리소스를 참조하는 라우팅 그룹이 동일한 컨트롤러로 이동할 수 있습니다. 예를 들어 `/users`와 `/users/1` 요청은 모두 하나의 라우트 명세로 다룰 수 있습니다.

선택적인 경로 부분은 그 주위에 대괄호 (`[]`)를 가지고있다. 대괄호는 슬래시 앞이나 뒤에 갈 수 있습니다. 예를 들어, 다음 두 구문은 `/users`와 `/users/:userID`를 모두 허용하는 경로를 등록합니다 :

```dart
route("/users/[:userID]")
route("/users[/:userID]")
```

개념적으로, `/users/1` 경로를 가진 요청은 단일 사용자를 식별합니다. 여기서 `/users`는 모든 사용자를 식별합니다. 선택적 세그먼트는 특정 유형의 자원을 처리하는 요청을 동일한 컨트롤러로 전달하여보다 나은 코드 구조를 만드는 데 사용됩니다. 따라서 한 사용자 또는 여러 사용자를 처리하는 코드가 같은 위치에 작성됩니다.

경로 지정에 원하는 개수의 선택적 세그먼트가있을 수 있습니다. 각 선택적 세그먼트는 중첩되어야 합니다. 다음 경로는 `/a`, `/a/b`와 `/a/b/c`와 일치합니다.  `/a/c`와는 일치하지 않습니다.

```dart
route("/a/[b/[c]]")
```

한 경로에 둘 이상의 선택적 세그먼트가 있는 것은 거의 없습니다. 예를 들어 다음 경로를 고려하십시오.

```dart
route("/users/[:id/[:subresource/[:subresourceid]]]");
```

하나 이상의 사용자를 처리하는 코드는 하위 리소스 중 하나를 처리하는 코드와 매우 다를 수 있습니다. 다른 데이터베이스 테이블이 쿼리되고 다른 권한이 필요할 수 있습니다. 따라서 더 나은 접근법은 컨트롤러 로직을 모듈화하여 유지하기 위해 하위 리소스를 자체 경로로 분할하는 것입니다.

```dart
// Matches /users and /users/:id
route("/users/[:id]")...;

// Matches /users/:userId/posts and /users/:userId/posts/:postId
route("/users/:userId/posts/[:postId]")...;

// Matches /users/:userId/posts and /users/:userId/notes/:noteId
route("/users/:userId/notes/[:noteId]")...;
```

### Restricting Path Variable Values

경로 변수는 가능한 값을 정규식으로 제한 할 수 있습니다. 표현식은 경로 변수 이름 다음에 괄호 안에 옵니다. 예를 들어, 다음 라우트 스펙은 `userID`를 숫자로만 제한합니다.

```dart
route("/users/:userID([0-9]+)")
```

이 정규식은 `:userID` 세그먼트에만 적용됩니다. 일반적으로 캡처 그룹 및 괄호는 경로의 정규 표현식에 포함될 수 없습니다.

괄호 사이의 모든 것은 정규 표현식으로 평가됩니다. 따라서 추가 공백이나 문자는 정규 표현식의 일부가 됩니다. 공백은 URL에서 유효하지 않으므로 정규식에 공백을 넣지 마십시오.

### Matching the Remaining Path

마지막으로, 라우트 스펙은 별개의 'match-all'토큰 인 별표 (`*`)를 가질 수 있습니다. 이 토큰은 내용이나 길이에 관계없이 나머지 요청 경로가 일치 되도록 합니다. 예를 들어, 경로 지정 `/users/*`는 다음 경로와 일치합니다 :

```
/users
/users/1
/users/foo
/users/foo/bar
/users/foo/bar/something/else/and/this/goes/on/forever
```

이 토큰은 다른 매체가 URL을 해석 할 때 사용됩니다. 예를 들어, 파일 시스템에서 파일을 읽는 `FileController`는 `/file/*`경로를 가질 수 있습니다. `/file` 다음의 모든 것을 사용하여 파일 시스템의 경로를 찾습니다.

## Accessing Path Variables

라우터가 경로 변수처럼 요청 경로에서 파싱하는 정보는 `Request.path`에 저장됩니다. `Request`가 라우터에 의해 처리 될 때, `path`는 이 유형의 인스턴스로 설정됩니다. 채널에서 더 깊은 컨트롤러는 요청이 식별하고 있는 리소스를 결정하는 데 도움이되는 `Request.path`에 액세스합니다. `path`는 `RequestPath`의 인스턴스입니다.

`RequestPath`는 `variables`의 맵을 포함합니다. 여기서 키는 경로 변수 이름이고 값은 요청에 있는 변수의 값입니다. 예를 들어, 경로 지정 `/users/:id`를 고려하십시오. `/users/1` 경로를 가진 요청이 라우팅 될 때, 값 `1`은 키 `id`에 대한이 맵에 저장됩니다:

```dart
final identifier = request.path.variables["id"];
// identifier = "1"
```

`variables`의 값은 요청 경로가 `String`이므로 항상 `String`입니다. `Controller`는 경로 변수를`int`와 같은 타입으로 파싱합니다.

[ResourceController](https://aqueduct.io/docs/http/resource_controller/)는 경로 변수를 사용하여 요청을 처리 할 연산 방법을 선택합니다.

## Failed Matches Return 404

요청에 대해 일치하는 라우트가 없으면 `Router`는 `404 Not Found`를 리턴합니다. 연결된 컨트롤러가 요청을 처리하지 않습니다. 이 동작은`Router`생성자에 클로저를 제공함으로써 오버라이드 될 수 있습니다.