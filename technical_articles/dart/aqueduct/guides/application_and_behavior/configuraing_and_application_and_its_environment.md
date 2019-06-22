원문: https://aqueduct.io/docs/application/configure/

# Configuring an Application and its Environment

This guide covers configuring an Aqueduct application.

## Configuration Files

Aqueduct 응용 프로그램은 YAML 구성 파일을 사용하여 데이터베이스 연결 정보와 같은 환경 별 값을 제공합니다. 테스트 및 다른 전개 환경에 별도의 구성 파일을 사용하십시오.

설정 파일의 경로는 `options` 속성을 통해 `ApplicationChannel`에서 사용할 수 있습니다.

```dart
class TodoAppChannel extends ApplicationChannel {
  @override
  Future prepare() async {
    var config = TodoConfiguration(options.configurationFilePath);
    ...
  }
}
```

The default value is `config.yaml`.

설정 파일을 읽는 가장 좋은 방법은 `Configuration`을 서브 클래스 화하는 것입니다. `Configuration`은 설정 파일의 각 키에 대한 속성을 선언합니다. 예를 들어, 다음의`Configuration` 서브 클래스를보십시오:

```dart
class TodoConfiguration extends Configuration {
  TodoConfiguration(String fileName) : super.fromFile(fileName);

  DatabaseConnectionConfiguration database;
  String apiBaseURL;

  @optionalConfiguration
  int identifier;
}
```

This would read a YAML file like this:

```yaml
database:
  username: fred
  password: fredspassword
  host: db.myapp.com
  port: 5432
  databaseName: fredsdb
apiBaseURL: /api
identifier: 2
```

필요한 YAML 파일에서 속성을 생략하면 응용 프로그램 시작이 실패하고 정보적인 오류를 발생합니다.

### Environment Variables

구성 파일은 리터럴 값 대신 환경 변수를 사용할 수 있습니다. `config.yaml`에서 값 대신에 `$`-prefixed 환경 변수 이름을 사용하십시오 :

```yaml
database: $DATABASE_CONNECTION_URL
apiBaseURL: /api
```

If the environment variable `DATABASE_CONNECTION_URL`'s value were `"postgres://user:password@localhost:5432/test"`, the value of `TodoConfiguration.database` will be that string at runtime.

The [safe_config package](https://pub.dartlang.org/packages/safe_config) has instructions for more additional usages.

### Configuration Conventions and Deployment Options

Aqueduct uses two configuration files for a project: `config.yaml` and `config.src.yaml`. The latter is the *configuration source file*.  The configuration source file declares key-value pairs that will be  used when running the application tests. Deployed instances use `config.yaml`.

This pattern is used for two reasons:

- It is the template for the `config.yaml` that will be read on deployed applications, providing documentation for your application's configuration.
- It has the configuration values used during testing to inject mock dependencies.

For example, a production API instance might have the following `config.yaml` file with connection info for a production database:



```yaml
database: postgres://app_user:$%4jlkn#an*@mOZkea2@somedns.name.com:5432/production_db
```

Whereas `config.src.yaml` would have connection info for a local, test database:



```yaml
database: postgres://test:test@localhost:5432/temporary_db
```

The source configuration file should be checked into version control. Whether or not `config.yaml`  is checked in depends on how you are deploying your code. If you are  using environment variables to control application configuration, you  should check `config.yaml` into source control and provide `$`-prefixed environment variable values. If you are using managing configuration files on each deployed instance, do not check `config.yaml` into source control because it'll be a different file for each instance.

It can sometimes makes sense to have a `local.yaml` with values for running the application locally, e.g. when doing client testing. Use `--config-path` with `aqueduct serve` to use a non-default name.

## Preventing Resource Leaks

Aqueduct 응용 프로그램이 시작되면 응용 프로그램과 해당 `ApplicationChannel`은 요청에 응답하는 데 사용할 서비스를 생성합니다. 응용 프로그램 테스트가 성공적으로 완료 되려면 응용 프로그램이 중지 될 때 이러한 서비스를 "닫아야"합니다. `PostgreSQLPersistentStore`와 같은 내장 서비스의 경우, 이것은 `Application.stop()`이 호출 될 때 자동으로 발생합니다.

`ServiceRegistry`는 등록 된 서비스를 자동으로 중단합니다. 등록은 다음과 같습니다.

```dart
var connection = new ConnectionOfSomeKind();
await connection.open();
ServiceRegistry.defaultInstance
  .register<ConnectionOfSomeKind>(connection, (c) => c.close());
```

이 메소드는 서비스가 닫히고 닫히는 클로저를 닫습니다. 서비스는 클로저에 인수로 전달됩니다. 클로저가 `Future`를 반환하면 `Future`가 완료 될 때까지 `ServiceRegistry.close`가 완료되지 않습니다. `ServiceRegistry.defaultInstance`는 `Application.stop()`에서 닫혀 있습니다. 프로그래머가 작성한 레지스트리는 수동으로 닫아야합니다.

`ServiceRegistry.register`의 리턴 타입은 등록 된 객체입니다. 이렇게하면 등록 구문이 약간 더 멋지게됩니다.

```dart
var connection = ServiceRegistry.defaultInstance
  .register<ConnectionOfSomeKind>(
    new ConnectionOfSomeKind(), (c) => c.close());

await connection.open();
```

## dartConfiguring CORS Headers

모든 컨트롤러에는 브라우저에서 CORS 요청을 처리하기위한 기본 동작이 있습니다. 프리 플라이트 요청이 브라우저 (Access-Control-Request-Method 헤더와 Origin 헤더가있는 OPTIONS 요청)에서 수신되면 응답은 실제 요청에 응답 할 'Controller'의 정책을 평가하여 생성됩니다.

실제로 이는 채널의 마지막 컨트롤러 정책이 사용됨을 의미합니다. 예를 들어, `FooController`의 정책은 프리 플라이트 응답을 생성합니다 :

```dart
router
  .route("/foo")
  .link(() => new Authorizer(...))
  .link(() => new FooController());
```

모든 `Controller`는 `policy` 속성을 가지고 있습니다 (CORSPolicy 인스턴스). `policy`는 특정 엔드 포인트에 대한 CORS 옵션을 구성하기위한 특성을 가지고 있습니다. `policy`을 가짐으로써 모든 `Controller` 모든 추가 코드 없이 프리 플라이트 요청에 응답하는 로직을 자동으로 구현합니다.

정책은 컨트롤러 수준 또는 응용 프로그램 수준에서 설정할 수 있습니다. 정적 속성`CORSPolicy.defaultPolicy`는 모든 컨트롤러에 대한 CORS 옵션을 설정하기 위해 초기화시 수정 될 수 있습니다.

```dart
class MyApplicationChannel extends ApplicationChannel {
  @override
  Future prepare() async {
    CORSPolicy.defaultPolicy.allowedOrigins = ["http://mywebsite.com/"];
  }
}
```

기본 정책은 매우 엄격합니다 : POST, PUT, DELETE 및 GET은 허용 된 메소드입니다. 모든 기원은 유효합니다.

각각의 개별 컨트롤러는 생성자에서 자체 정책을 수정하여 기본 정책을 대체하거나 대체 할 수 있습니다.

```dart
class MyResourceController extends ResourceController {
  MyResourceController() {
    policy.allowedMethods = ["POST"];
  }
}
```

## Configuring HTTPS

기본적으로 Aqueduct 응용 프로그램은 HTTPS를 사용하지 않습니다. 대부분의 경우, Aqueduct 응용 프로그램은 SSL 사용 가능 로드 밸런서 또는 다른 프록시 뒤에 있습니다. 로드 밸런서의 트래픽은 HTTP를 통해 암호화 되지 않은 Aqueduct 응용 프로그램으로 전송됩니다.

그러나 Aqueduct는 HTTPS 연결 자체를 관리하도록 구성 될 수 있습니다. Aqueduct 애플리케이션은 `aqueduct serve`의 `--ssl-key-path` 와 ``--ssl-certificate-path`에 값으로 개인 키와 SSL 인증서 경로를 옵션으로 전달함으로써, Aqueduct 응용 프로그램은 HTTPS 만을 허용하도록 자신을 구성할 수 있습니다.

```sh
aqueduct serve --ssl-key-path server.key.pem --ssl-certificate-path server.cert.pem
```

키 파일과 인증서 파일은 모두 암호화되지 않은 PEM 파일이어야하며 둘 다 이 명령에 제공되어야합니다. 이러한 파일은 일반적으로 [letsencrypt.org](https://aqueduct.io/docs/application/letsencrypt.org)와 같은 "인증 기관"에서 발급됩니다.

이 옵션으로 응용 프로그램이 시작되면 `certificateFilePath`와 `keyFilePath`가 응용 프로그램이 실행되는 `ApplicationOptions`에 설정됩니다. (`aqueduct serve`를 사용하지 않는 경우,`ApplicationOptions`을 인스턴스화 할 때 직접이 값을 설정할 수 있습니다.)

HTTPS 서버 설정에 대한 세분화 된 제어를 위해, `ApplicationControl`을`ApplicationChannel`에서 오버라이드 할 수 있습니다. 기본적으로이 프로퍼티는 채널의`options`에서 `certificateFilePath`와 `keyFilePath`로부터 `SecurityContext`를 생성합니다. `SecurityContext`는 패스워드로 암호화 된 신임장(credential) 파일, 클라이언트 인증서(certificates) 및 기타 덜 사용되는 HTTPS 구성표를 허용합니다.

```dart
class MyApplicationChannel extends ApplicationChannel {
  @override
  SecurityContext get securityContext {
    return new SecurityContext()
      ..usePrivateKey("server.key", password: "1234")
      ..useCertificateChain("server.crt", password: "1234");
  }
}
```