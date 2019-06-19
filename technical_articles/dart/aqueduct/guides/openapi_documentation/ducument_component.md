https://aqueduct.io/docs/openapi/components/

# Document Components

이 문서에서는 응용 프로그램 설명서에 OpenAPI 구성 요소를 등록하고 사용하는 방법을 배우게 됩니다.

## Registering Components with APIDocumentContext

응용 프로그램이 문서화 될 때, `APIDocumentContext`의 단일 인스턴스가 생성되어 모든 문서화 메소드에 전달됩니다. 컨텍스트는 생성되는 문서를 저장하지만 더 중요한 것은 재사용 가능한 구성 요소의 컨테이너입니다. `APIComponentDocumenter`를 구현하고 추상 메소드를 구현하여 컴포넌트를 등록 할 수 있습니다. 예를 들어, 다음 코드는 재사용 가능한 스키마 객체를 등록합니다.

```dart
class SourceRepository implements APIComponentDocumenter {
  @override
  void documentComponents(APIDocumentContext context) {
    super.documentComponents(context);

    context.schema.register("SourceRepository",
        APISchemaObject.object({
          "id": APISchemaObject.integer(),
          "name": APISchemaObject.string()
        });          
  }
}
```

"SourceRepository"는 "id"(정수)와 "name"(문자열)의 두 필드가 들어있는 객체입니다. 이 구성 요소는 스키마 개체를 사용할 수 있는 모든 위치에서 사용할 수 있습니다. 스키마 개체는 일반적으로 '모델 개체'로 간주되는 것을 문서화하는 구성 요소의 한 유형입니다. 대부분 요청 및 응답 본문에 스키마 개체가 표시됩니다. 기본적으로 각 `ManagedObject`는 스키마 객체로 등록됩니다. 다른 유형의 구성 요소는 응답, 요청 본문, 매개 변수, 헤더, 보안 구성표 및 콜백입니다.

구성 요소는 이름으로 등록되어야 하지만 추가로 유형으로 등록 될 수 있습니다. 이를 통해 구성 요소 사용자는 다트 유형으로 참조 할 수 있습니다. 오브젝트에 대한 유형 참조를 포함하는 것은 등록 할 때 선택적 인수입니다.

```dart
context.schema.register("SourceRepository",
    APISchemaObject.object({
      "id": APISchemaObject.integer(),
      "name": APISchemaObject.string()
    }, representation: SourceRepository);          
```

구성 요소가 등록되고 참조되는 순서는 중요하지 않습니다. 문서 처리 과정에서 나중에 생성 된 구성 요소를 참조하면 문서가 완성되기 전에 해결됩니다. 참조 된 구성 요소가 등록되지 않은 경우 오류가 발생하고 문서가 생성되지 않습니다.

## Using Components

경로 작업을 선언 할 때 또는 다른 구성 요소의 일부로 구성 요소를 사용할 수 있습니다. 예를 들어 본문이 "SourceRepository"라는 구성 요소 인 응답을 설명하려면 다음과 같이 표시됩니다.

```dart
class RepositoryController extends ResourceController {
  ...

  @override
  Map<String, APIResponse> documentOperationResponses(APIDocumentContext context, Operation operation) {
    if (operation.method == "GET") {
      return {
        "200": APIResponse.schema(context.schema["SourceRepository"])
      };
    }
    return null;
  }  
}
```

오브젝트가 그 형태로 등록되어있는 경우는, `getObjectWithType`를 사용할 수 있습니다.

```dart
class RepositoryController extends ResourceController {
  ...

  @override
  Map<String, APIResponse> documentOperationResponses(APIDocumentContext context, Operation operation) {
    if (operation.method == "GET") {
      return {
        "200": APIResponse.schema(context.schema.getObjectWithType(SourceRepository))
      };
    }
    return null;
  }  
}
```

## Component Discovery

모든 컨트롤러는 엔트리 포인트에 링크되어있을 때 컴포넌트를 문서화 할 수 있습니다. 콘트롤러가 아닌 다른 객체는 `APIComponentDocumenter` 를 구현하고 자신의 `ApplicationChannel`의 프로퍼티가 선언 된 경우 자동으로 컴포넌트를 문서화합니다. (다른 옵션에 대해서는 [이 가이드](https://aqueduct.io/docs/openapi/cli/)를 참조하십시오.)

내장 Aqueduct 유형은 적용 가능한 모든 구성 요소를 등록합니다. 여기에는 OAuth2와 애플리케이션의 모든 `ManagedObject` 하위 클래스를 처리하는 유형이 포함됩니다.

### ManagedObject Discovery

`ManagedContext`를 `ApplicationChannel`의 속성으로 선언하면 응용 프로그램의 관리 대상 객체가 스키마 구성 요소로서 자동으로 문서화됩니다.

### Serializable Discovery

`Serializable` 객체가 `ResourceController`에서 요청 본문에 바인딩되면, 그것은 자동으로 스키마 구성 요소로서 문서화됩니다. 디폴트에서는, `Serializable` 오브젝트의 프롭퍼티가 이 컴포넌트를 생성하기 위해서 반영됩니다. 이 동작을 무시하고 자신 만의 컴포넌트 문서를 제공하려면`Serializable` 서브 클래스에 다음 메소드를 구현하십시오 :

```dart
class Person extends Serializable {
  String name;

  Map<String, dynamic> asMap() => {"name": name};
  void readFromMap(Map<String, dynamic> map) {
    name = map['name'];
  }

  APISchemaObject documentSchema(APIDocumentContext context) {
    return APISchemaObject.object(properties: {
      "name": APISchemaObject.string()
    });
  }
}
```

`Serializable` 타입이 리소스 컨트롤러 연산에 바인드 되지 않았다면 직접 등록해야 합니다. 이것은 전형적으로 타입을 사용하는 컨트롤러에서 `documentComponents`를 오버라이드 (override)함으로써 발생합니다.

```dart
class MyController extends ResourceController {
  ...

  @override
  void documentComponents(APIDocumentContext context) {
    super.documentComponents(context);

    final personSchema = Person().documentSchema(context);
    context.schema.register(
      "Person",
      personSchema,
      representation: Person);          
  }
}
```

