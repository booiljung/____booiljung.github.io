https://aqueduct.io/docs/openapi/

# Overview

## Tasks

Aqueduct 응용 프로그램은 OpenAPI 3.0 문서를 자동 생성합니다.

대부분의 OpenAPI 문서는 애플리케이션 코드, 특히 `ResourceController` 하위 클래스를 반영하여 생성됩니다. `APIComponentDocumenter`와 `APIOperationDocumenter`의 메소드를 오버라이드 하여 커스터마이징이나 추가 정보를 추가 할 수 있습니다. 최소한, 여러분의 애플리케이션이 특정 엔드 포인트에 보낼 응답을 문서화하기 위해 `ResourceController` 메소드를 오버라이드 (override)하십시오.

`aqueduct document` 명령 줄 도구로 문서를 만듭니다.

- [Creating an OpenAPI Document](creating_openapi_documents.md)
- [Documenting Components](ducument_component.md)
- [Documenting Endpoint Controllers](documenting_endpoint_controllers.md)
- [Documenting Middleware Controllers](documenting_middleware_controllers.md)

