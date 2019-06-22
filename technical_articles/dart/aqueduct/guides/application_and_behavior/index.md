원문: https://aqueduct.io/docs/application/

# Aqueduct: Guides: Application and Behavior

Aqueduct 응용 프로그램은 HTTP 서버를 시작하고 각 요청에 대해 코드를 호출합니다. 호출 된 코드는 요청의 경로, 메소드 및 기타 속성에 따라 달라질 수 있습니다. 예를 들어, `GET /heroes` 요청은 `POST /authors`와는 다른 코드를 호출합니다. `ApplicationChannel` 서브 클래스에서 호출 되는 코드를 설정합니다; 모든 어플리케이션은 정확히 하나의 `ApplicationChannel` 서브 클래스를 선언합니다.

또한 이 서브 클래스는 서비스를 설정하고 파일과 환경 변수에서 구성 데이터를 읽고 응용 프로그램에 대한 다른 초기화를 수행합니다. 예를 들어, 응용 프로그램 채널은 종종 환경 변수에서 데이터베이스 연결 정보를 읽은 다음 해당 데이터베이스에 대한 연결을 설정합니다.

Aqueduct 응용 프로그램은 여러 스레드를 만들고 각 스레드는 들어오는 요청을 처리합니다. 응용 프로그램 채널 하위 클래스는 각 스레드에 대해 만들어져 응용 프로그램의 복제 인스턴스를 만듭니다.

- [Starting and Stopping Applications](starting_and_stopping_an_application.md)
- [Configuring an Application and its Environment](configuraing_and_application_and_its_environment.md)
- [Application and Project Structure](application_and_project_structure.md)
- [Performance: Multi-threading](multi_threading_in_aqueduct.md)

