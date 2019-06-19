https://aqueduct.io/docs/openapi/endpoint/

# Documenting Endpoint Controllers

이 문서에서는 엔드 포인트 컨트롤러를 문서화하는 방법을 학습합니다.

## ResourceController Auto-Documentation

`ResourceController`는 OpenAPI 문서를 생성 할 때 대부분 힘든 일을 합니다. OpenAPI 문서의 대다수를 제공하는 운영 방법의 바운드 변수를 반영합니다. 가능한 응답만 제공하면 됩니다. `ResourceController` 서브 클래스에서`documentOperationResponses`를 오버라이드하면됩니다. 아래는 모든 요청에 대해 본문이없는 200 OK를 반환하는 리소스 컨트롤러의 간단한 예를 보여줍니다.

```dart
class MyController extends ResourceController {
  ...

  Map<String, APIResponse> documentOperationResponses(APIDocumentContext context, Operation operation) {
    return {"200": APIResponse("Successful response.")};
  }
}
```

이 메소드는 맵을 반환해야하며, 각 키는 문자열 상태 코드이고 각 값은`APIResponse` 객체입니다. `APIResponse` 객체는 고도의 설정이 가능하지만, 대부분의 경우, 당신은 그 몸체의 스키마 만 선언하면됩니다. 이를 위해서 `APIResponse.schema`라는 편의 생성자가 존재합니다. 다음은 JSON 응답 본문에 'id'라는 단일 정수 필드가 들어있는 예제입니다.

```dart
Map<String, APIResponse> documentOperationResponses(APIDocumentContext context, Operation operation) {
  return {
    "200": APIResponse.schema("Successful response.", APISchemaObject.object({
      "id": APISchemaObject.integer()
    }))
  };
}
```

실제로는 요청 메소드와 경로 변수에 따라 다른 응답을 원할 것입니다. `operation` 인자는 당신이 문서화하고있는 작업을 알려줍니다.

```dart
Map<String, APIResponse> documentOperationResponses(APIDocumentContext context, Operation operation) {
  if (operation.method == "GET") {
    if (operation.pathVariables.contains("id")) {
      return {"200": APIResponse("An object by its id.")};
    } else {
      return {"200": APIResponse("All objects.")};
    }
  }
  return null;
}
```

리소스 컨트롤러가 코드에서 나머지 문서를 가져 오는 동안 때때로이 동작을 무시할 수 있습니다. 개별 요소는 `documentOperationParameters`와 같은 메소드를 오버라이드하여 수정할 수 있습니다. 그렇지 않으면`documentOperations`를 오버라이드하여 전체 프로세스를 대신 할 수 있습니다.

`ResourceController`를 사용하지 않는다면, 컨트롤러에서 `documentOperations`를 오버라이드하고 모든 조작 정보를 직접 제공해야 합니다.