https://aqueduct.io/docs/openapi/cli/

# Creating OpenAPI Documents

In this document, you'll learn how to use the `aqueduct` command line tool to generate an OpenAPI document for your application.

## OpenAPI Documents

OpenAPI 문서는 모든 요청의 세부 사항과 응용 프로그램이 가질 수 있는 가능한 응답을 설명합니다. 이 문서는 사양을 따르는 JSON 객체입니다. 이 명세는 문서가 가질 수 있거나 가질 수 있는 속성을 정의 합니다. 이 사양을 따르면 응용 프로그램은 설명서 뷰어 및 소스 코드 생성기와 같은 도구를 활용할 수 있습니다.

OpenAPI 문서에서 가장 중요한 두 개체는 구성 요소 및 경로 작업입니다. 경로 연산에는 예상되는 요청과 가능한 응답이 포함됩니다. 구성 요소는 경로 작업에서 사용 할 수 있는 재사용 가능한 정의입니다. 예를 들어, 이 응답을 전송할 수 있는 경로 작업 전반에 걸쳐 400 개의 잘못된 요청 응답 구성 요소를 재사용 할 수 있습니다.

대부분의 문서화 프로세스는 구성 요소를 등록하고 경로 작업을 생성하는 것을 중심으로 이루어집니다.

## The aqueduct document Command

문서는 손으로 작성할 수 있지만 시간이 많이 걸리고 코드와 동기화 하기가 어렵습니다. Aqueduct은 귀하의 코드를 분석하여 귀하를 위해 (대부분의) 문서를 만듭니다. 프로젝트 디렉토리에서`aqueduct document` 명령을 실행하면 콘솔에 JSON 문서가 인쇄됩니다.

```sh
cd my_project/
aqueduct document

-- Aqueduct CLI Version: 3.0.0
-- Aqueduct project version: 3.0.0
{"openapi":"3.0.0","info":...
```

출력을 복사하여 다른 도구에서 사용할 수 있습니다. 예를 들어 [Swagger Editor](https://editor.swagger.io)에 입력하면됩니다. 이 명령을 실행하지만 출력에서 버전 정보를 구문 분석하지 않으려는 도구를 작성하려면 `--machine` 플래그를 사용하십시오.

```sh
aqueduct document --machine
{"openapi":"3.0.0","info":...
```

OpenAPI 문서의 메타 데이터 (제목이나 버전 등)는 애플리케이션의 `pubspec.yaml`에서 파생됩니다. 파생 된 값을 오버라이드하거나 파생 될 수 없는 값을 제공하려면 `--title` 또는 `--license-name`과 같은 옵션을 사용하십시오. 모든 옵션에 대해서는 `aqueduct document --help`를 보십시오.

## How Applications are Documented

`aqueduct document` 명령을 실행하면 응용 프로그램의 객체가 채워지는 빈 `APIDocument`가 생성됩니다. 응용 프로그램은 정상적인 초기화 프로세스 (예 :`prepare` 및 `entryPoint`)를 거칩니다. 그런 다음 컨트롤러와 서비스 개체에 구성 요소를 등록하라는 메시지가 표시됩니다. 예를 들어, 모든 ManagedObject는 자신을 재사용 가능한 스키마 구성 요소로 등록합니다. 구성 요소를 등록한 후 응용 프로그램의 제어기에 처리하는 요청을 정의하는 경로 조작을 작성하라는 메시지가 표시됩니다.

Configuration Files

응용 프로그램은 응용 프로그램을 실행하는 것처럼 초기화를 수행하므로 문서화 할 때 유효한 구성 파일이 있어야합니다. 기본값은 `config.yaml.src`입니다.이 파일은 테스트를 실행하는 데 사용하는 것과 같습니다. 다른 파일을 사용하려면 `aqueduct document --help`를 보십시오.

### Documenting Components

구성 요소를 등록하는 객체는 `APIComponentDocumenter.documentComponents`를 구현합니다. 이 메소드를 구현하는 컨트롤러는 애플리케이션의 진입 점에 링크되어있는 한 컴포넌트를 자동으로 문서화합니다. 이 메소드를 구현하는 다른 유형의 객체는 `ApplicationChannel`의 속성으로 선언되면 자동으로 문서화 됩니다.

예를 들어 다음 코드에서 `AuthServer`, `Router` 및 `PathController`는 모두 자동으로 구성 요소를 문서화합니다.

```dart
class MyChannel extends ApplicationChannel {
  AuthServer authServer;

  @override
  Future prepare() async {
    authServer = new AuthServer(...);
  }

  @override
  Controller get entryPoint {
    final router = new Router();
    router.route("/path").link(() => new PathController());
    return router;
  }
}
```

대부분의 응용 프로그램에서는 자동으로 문서화 된 개체가 구성 요소를 등록하는 유일한 개체입니다. 구성 요소를 등록해야하지만 자동으로 문서화되지 않은 객체가있는 경우 응용 프로그램 채널에서 `documentComponents`를 재정 의하여 해당 객체가 구성 요소를 등록하도록 합니다. 수퍼 클래스의 구현을 호출해야합니다.

```dart
class MyChannel extends ApplicationChannel {
  ...
  @override
  void documentComponents(APIDocumentContext context) {
    super.documentComponents(context);

    objectWithComponents.documentComponents(context);
  }
}
```

여러분이 만든 컨트롤러와 서비스에서`documentComponents`를 오버라이드 할 수 있습니다. 자세한 내용은 [구성 요소 설명서](https://aqueduct.io/docs/openapi/components/)를 참조하십시오.

### Document Path Operations

경로 연산은 예상 요청 및 경로 (예 :`/users`) 및 요청 메소드 (예 :`GET`)에 대한 가능한 응답입니다. 각 작업은 포함 할 헤더 또는 쿼리 매개 변수와 같은 서버에 요청을 보내는 방법을 설명합니다. 응답은 보낼 수 있는 상태 코드, 헤더 및 본문을 설명합니다. 각 컨트롤러는 처리하는 요청에 대해이 정보를 정의하기 위해 `APIOperationDocumenter.documentOperations`를 구현합니다.

`Authorizer` 나 `ResourceController` 같은 빌트인 컨트롤러는 이미 이 메소드를 구현하고 있습니다. 일반적으로 자신의 미들웨어를 만들 때만 이 방법을 재정의합니다. 미들웨어 문서화에 대한 자세한 내용은 [이 안내서](https://aqueduct.io/docs/openapi/middleware/)를 참조하십시오.

`ResourceController`s에 대한 문서를 만들 때 요청 매개 변수는 바인딩에서 파생되지만 여전히 가능한 응답을 제공해야합니다. 엔드 포인트 컨트롤러의 문서화에 대한 자세한 정보는 [이 안내서](https://aqueduct.io/docs/openapi/endpoint/)를 참조하십시오.