https://aqueduct.io/docs/deploy/deploy_aws/

# Deploying an Aqueduct Application on Remote VMs

다른 배포 옵션은 [Aqueduct 응용 프로그램 배포](https://aqueduct.io/docs/deploy/)를 참조하십시오.

### Purpose

이 문서는 여러분이 `ssh`를 할 수있는 원격 기계에 Aqueduct 응용 프로그램을 전개하는 단계를 설명 할 것입니다. 아마존 웹 서비스 (AWS) EC2 인스턴스, Google 클라우드 컴퓨팅 인스턴스, Azure 가상 머신 및 디지털 오션 또는 기타 클라우드 공급자와 같은 플랫폼의 대여 된 상자가 여기에 해당됩니다.

이 방법으로 응용 프로그램을 배포하는 것에 익숙하지 않은 사용자에게는 좋은 초보자 안내서가 아니며 응용 프로그램을 배포하는 데 필요한 많은 단계를 다루지는 않습니다. Heroku 또는 Docker를 지원하는 플랫폼을 선호하십시오. 더 나은 옵션을 보려면 [Heroku](https://aqueduct.io/docs/deploy/deploy_heroku/) 및 [Docker](https://aqueduct.io/docs/deploy/deploy_docker/)의 가이드를 참조하십시오.

### Summary

Aqueduct 응용 프로그램은`aqueduct serve` 또는 `dart bin/main.dart`를 사용하여 실행됩니다.

```sh
# in project directory
aqueduct serve

# or

# in project directory
dart bin/main.dart
```

대상 컴퓨터에는 Dart가 설치되어 있어야합니다. `aqueduct serve`를 사용하고 있다면, 타겟 머신에서도 CLI를 활성화해야합니다 :

```sh
pub global activate aqueduct
```

소스 코드를 대상 시스템에서도 사용할 수 있어야 합니다. `ftp`, `scp`, `rsync`, `git`와 같은 도구를 사용하여 소스를 기계로 옮길 수 있습니다.

Aqueduct는 기본적으로 포트 8888에서 수신 대기합니다. 이 값을 CLI `aqueduct serve --port 80` 또는 `bin/main.dart`에서 변경하십시오. 인스턴스의 보안 제어가 Aqueduct가 수신 대기중인 포트에서 연결을 허용 할 수 있는지 확인하십시오. 응용 프로그램을 직접 제공하는 대신 리버스 프록시 (예 : nginx 또는로드 밸런서)를 사용하는 것이 좋습니다.

`supervisord`와 같은 도구를 사용하여 VM이 손상된 경우 응용 프로그램이 다시 시작되는지 확인하십시오.

## Configuration Management

VM에 직접 배포하는 경우 구성 파일을 관리하는 것은 사용자의 책임입니다. 이것은 종종 환경 고유의 `config.yaml` 파일을 타겟 머신으로 전송하고 그것을 원격 머신의 프로젝트 디렉토리에 저장함으로써 수행 될 수 있습니다.

## CLI Tools

많은 배포에서는 `aqueduct` CLI로 데이터베이스 마이그레이션과 OAuth 2.0 클라이언트 식별자 관리를 수행해야 합니다. 데이터베이스 인스턴스의 위치를 지정하기 위해 `--connect` 플래그를 사용하여이 도구들을 로컬로 실행할 수 있습니다. 로컬 시스템의 데이터베이스 인스턴스에 액세스 할 수있는 적절한 보안 제어 기능이 있는지 확인하십시오.