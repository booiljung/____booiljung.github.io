원문: https://aqueduct.io/docs/http/serving_files/

# Serving Files and Caching

Aqueduct can serve files by returning the contents of a file as an HTTP response body.

## FileController

`FileController`의 인스턴스는 HTTP 인터페이스를 통해 파일 시스템의 디렉토리를 제공합니다. `FileController`  로의 채널 요청은 모두 일치하는 토큰을 포함해야 합니다.

```dart
@override
Controller get entryPoint {
  final router = new Router();

  router.route("/files/*").link(() => new FileController("public/"));

  return router;
}
```

`FileController`에 대한 인수는 요청 경로가 해석 될 파일 시스템의 디렉토리입니다. 위의 예에서 `/files/image.jpg` 경로를 가진 HTTP 요청은 `public/image.jpg` 파일의 내용을 반환합니다.

`public/`에는 선행 슬래시가 없으므로 `public` 디렉토리는 Aqueduct 응용 프로그램이 제공된 디렉토리에 상대적이어야합니다. 실제로 이는 다음과 같은 디렉토리 구조를 가질 수 있음을 의미합니다.

```yaml
project/
  pubspec.yaml  
  lib/
    channel.dart
    ...
  test/
    ...
  public/
    image.jpg
```

`FileController`가 제공하는 디렉토리에 선행 슬래시를 추가하면 파일 시스템 루트와 관련하여 해결됩니다.

요청 된 경로가 디렉토리라면 반환 할 파일을 찾을 때 파일 이름 `index.html`이 경로에 추가됩니다.

파일이 존재하지 않으면 `FileController`는 `404 Not Found` 응답을 리턴합니다.

### Content-Type of Files

`FileController`는 서비스되는 파일 경로 확장자에 기반한 HTTP 응답의 content-type을 설정합니다. 기본적으로 `.html`, `.css`, `.jpg`, `.js`와 같은 많은 공통 확장자를 인식합니다. 인스턴스에 확장을 위한 컨텐트 유형을 추가 할 수 있습니다.

```dart
var controller = new FileController("public/")
  ..setContentTypeForExtension("xml", new ContentType("application", "xml"));
```

서비스 되고 있는 파일의 확장을 위한 엔트리가 없다면, content-type의 기본값은 `application/octet-stream`입니다. `FileController`는 `CodecRegistry`로부터 어떠한 엔코더도 호출하지 않을 것입니다, 그러나 저장소가 파일의 content-type을 위해 압축을 허용한다면 그것은 GZIP 데이터가 될 것입니다 (`CodecRegistry.add`와`CodecRegistry.setAllowsCompression`을 보십시오).

## Caching

`FileController`는 항상 응답의 Last-Modified 헤더를 파일 시스템에 따라 마지막 수정 날짜로 설정합니다. 요청이 If-Modified-Since 헤더를 보내고 해당 날짜 이후에 파일이 수정되지 않은 경우 304 Not Modified 응답이 해당 헤더와 함께 전송됩니다.

캐시되는 파일의 경로에 따라 Cache-Control 헤더를 제공 할 수 있습니다. 다음은 `Cache-Control: public, max-age=31536000`을 추가 한 예제입니다.

```dart
var policy = new CachePolicy(expirationFromNow: new Duration(days: 365));
var controller = new FileController("public/")
  ..addCachePolicy(policy, (path) => path.endsWith(".css"));
```

## File Serving and Caching Outside of FileController

파일은 그 내용으로 `Response`의 body 객체를 설정함으로써 어떤 컨트롤러에 의해서도 제공 될 수 있습니다:

```dart
var file = new File("index.html");

// By loading contents into memory first...
var response = new Response.ok(file.readAsStringSync())
  ..contentType = new ContentType("application", "html");

// Or by streaming the contents from disk
var response = new Response.ok(file.openRead())
  ..encodeBody = false
  ..contentType = new ContentType("application", "html");
```

파일 내용을 제공하기 위해 Aqueduct [uses content-types to manipulate response bodies](https://aqueduct.io/docs/http/request_and_response/) 을 이해하는 것이 중요합니다.

`Response`의`CachePolicy`를 설정할 수 있습니다. CachePolicy는 Last-Modified와 ETag 같은 헤더를 추가하지 않고 응답의 Cache-Control 헤더 만 수정합니다.

```dart
var response = new Response.ok("contents")
  ..cachePolicy = new CachePolicy();
```

