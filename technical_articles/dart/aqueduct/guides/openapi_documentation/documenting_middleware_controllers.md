https://aqueduct.io/docs/openapi/middleware/

# Documenting Middleware Controllers

이 문서에서는 미들웨어 컨트롤러를 문서화하는 방법을 배우게 됩니다.

## Adding to an Operation

문서화를 위해 미들웨어 컨트롤러는 작업 요청 및 응답을 작성하지 않습니다. 오히려 엔드 포인트 컨트롤러가 제공하는 조작 세부 사항을 수정합니다. 미들웨어 컨트롤러를 작성할 때는`documentOperations`를 오버라이드 (override)하고 슈퍼 클래스의 구현을 호출해야 합니다. 이를 통해 미들웨어의 연결된 컨트롤러는 최종적으로 엔드 포인트 컨트롤러에 도달하는 작업을 문서화 할 수 있습니다.

일단 엔드 포인트 컨트롤러가 작업 문서의 내용을 반환하면 미들웨어 컨트롤러가이를 수정할 수 있습니다. 예를 들어 'key'라는 쿼리 매개 변수가 필요한 미들웨어는 다음과 같이됩니다.

```dart
class Middleware extends Controller {
  ...

  @override
  Map<String, APIOperation> documentOperations(APIDocumentContext context, String route, APIPath path) {
    final ops = super.documentOperations(context, route, path);

    // ops has been filled out by an endpoint controller,
    // add 'key' query parameter to each operation.
    ops.forEach((method, op) {
      op.addParameter(APIParameter.query("key", schema: APISchemaObject.string()));
    });

    return ops;
  }
}
```

작업 맵의 각 문자열 키는 HTTP 메소드의 소문자 이름입니다 (예 : 'get'또는 'post'. `APIOperation`은 요청 매개 변수와 응답을 캡슐화합니다.