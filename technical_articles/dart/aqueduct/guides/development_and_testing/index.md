[Up](../index.md)
# Aqueduct: Development and Testing

##  Tasks

Aqueduct 응용 프로그램은 실행, 테스트, 디버깅 및 프로파일링이 가능합니다.

응용 프로그램의 `test/` 디렉토리에 `TestHarness<T>` 하위 클래스를 만듭니다. 각 테스트 스위트에 대해이 하네스를 설치하여 '테스트'모드에서 애플리케이션을 시작 및 중지하십시오. 테스트 하니스는 실제 애플리케이션처럼 애플리케이션을 실행합니다.

에이전트 개체를 사용하여 테스트중인 응용 프로그램에 HTTP 요청을 보냅니다. 에이전트는 권한 정보와 같은 모든 요청에 기본 정보를 추가합니다. `hasResponse` 또는 `hasStatus`와 같은 테스트 matcher를 사용하여 애플리케이션이 주어진 요청에 대해 보내는 응답을 검증합니다.

응용 프로그램과 통신하는 외부 서비스에 모의 서비스를 제공합니다. 이들은 종종 설정 파일의 내용에 의해 결정됩니다. 관습에 따라 테스트를위한 설정 파일은 `config.src.yaml`로 명명 됩니다. 테스트 중에 사용할 MockHTTPServer를 사용하여 모의 서비스를 만들 수도 있습니다.

## Guides

- [Writing Tests with a Test Harness](testing_in_aqueduct.md)
- [Testing with the ORM and OAuth 2.0](testing_applications_that_use_orm_and_oauth2.md)
- [Developing Client Applications](using_aqueduct_when_writing_client_applications.md)
- [Using the Debugger and Profiling](https://aqueduct.io/docs/testing/debugger/)
- [Use Mock Services](mocking_external_services.md)