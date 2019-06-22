원문: https://aqueduct.io/docs/http/

# Overview

## Tasks

Aqueduct 응용 프로그램은 요청에 대한 응답을 보내 HTTP 클라이언트에게 서비스 합니다.

요청을 처리하기 위해 `Controller` 객체를 만들고 링크합니다. 공통 태스크를 처리하는 `Controller`의 많은 서브 클래스가 있으며, 종종 여러분 자신의 서브 클래스 인`Controller`를 생성하여 애플리케이션 로직을 구현합니다. 대부분의 로직은 REST API 엔드 포인트를 위한 컨트롤러 유형 인 `ResourceController`의 서브 클래스에서 구현됩니다.

응용 프로그램에서 사용하는 컨트롤러를 구성하려면 `ApplicationChannel`의 하위 클래스를 만듭니다. 또한 이 하위 클래스는 응용 프로그램이 데이터베이스 연결 또는 타사 API 연결과 같이 요청을 수행하는 데 사용할 서비스를 초기화합니다. 대부분의 경우 애플리케이션 채널의 진입 점에서 `Router`컨트롤러를 사용하여 애플리케이션 로직을 모듈화합니다.

응용 프로그램에는 구성 가능한 옵션이 많이 있을 수 있습니다. 이 구성은 응용 프로그램 채널에서 처리됩니다. 구성 파일 관리는 구성 파일에 유형 및 이름 안전성을 추가하는 응용 프로그램 특정 `Configration` 하위 클래스에 의해 제공됩니다.

응용 프로그램은 `aqueduct serve` 명령이나 `bin /main.dart` 스크립트를 사용하여 실행됩니다. 두 경우 모두 애플리케이션은 `ApplicationChannel`을 복제하는 메모리 고립 스레드를 여러 개 만들어서 시작합니다.

## Guides

- [Handling Requests and Sending Responsers](handling_requests_and_sending_reponse.md)
- [Serializing Request and Response Bodies](serializing_request_and_response_bodies.md)
- [Routing](routing.md)
- [Request Binding with ResourceControllers](request_binding_with_resource_controller.md)
- [Serving Files and Caching](serving_files_and_caching.md)
- [Websockets](using_websockets_in_aqueduct.md)

