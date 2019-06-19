원문: https://aqueduct.io/docs/http/routing/

# Routing

## What is routing?

Every HTTP request has a URL. A URL identifies a *resource*. In the early days of the Internet, a resource was a file. For example, the URL `http://www.geocities.com/my_page/image.jpg` would return the file `image.jpg` from the folder `my_page` on the webserver located at `www.geocities.com`.  In a web application today, resources come from many other sources of  data, like a database or a connected device. The job of a web  application is to provide, create or modify a resource for a URL,  wherever that resource might come from.

A URL is made up of many parts, some of which are optional. The typical URL we see as humans looks like this: `http://stablekernel.com/about`.  Most people recognize that typing this URL into a browser would take  them to our company's "About" page. In other words, our "About" page is a  resource and the URL identifies it.

More generally, the "About" page URL has the three required components of a URL: a *scheme* (`http`), a *host* (`stablekernel.com`) and a *path* (`/about`).  The host specifies the computer responsible for providing the resource,  the path identifies the resource and the scheme lets both the requester  and the host know how they should exchange information.

An Aqueduct application receives requests when the scheme is `http` (or `https`)  and the host refers to a machine where the application is running.  Therefore, once the application gets the request, it only cares about  the remaining component: the path.

In Aqueduct, a `Router` routes `Request`s to a `Controller` based on the request path. This process is known as *routing*. When an application starts up, routes are registered in a subclass of `ApplicationChannel`. Each registered route creates a new [channel](https://aqueduct.io/docs/application/structure/) of `Controller`s that will handle the request.

## Route Specifications Match HTTP Request Paths

A route is registered by invoking `Router.route`. This method takes a *route specification* - a `String`  with some syntax rules that will match the path of a request. This  registration occurs when an application first starts by overriding `ApplicationChannel.entryPoint`. For example:

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