https://aqueduct.io/docs/deploy/deploy_heroku/

# Deploying an Aqueduct Application on Heroku

다른 배포 옵션은 [Aqueduct 응용 프로그램 배포](https://aqueduct.io/docs/deploy/)를 참조하십시오.

### Purpose

Heroku에서 프로덕션 Aqueduct 응용 프로그램을 실행하려면. [Aqueduct Applications 테스트](https://aqueduct.io/docs/testing/)도 읽으십시오.

### Prerequisites

1. [Dart has been installed.](https://www.dartlang.org/install)
2. [A Heroku account.](https://signup.heroku.com)
3. [`git` has been installed.](https://git-scm.com/downloads)
4. [`heroku` has been installed.](https://devcenter.heroku.com/articles/heroku-cli)
5. [Aqueduct has been activated.](https://aqueduct.io/docs#getting_started)

### Overview

1. Setting up a Heroku application
2. Setting up an Aqueduct application to run on Heroku
3. Configuring application values
4. Running the Aqueduct application

Estimated Time: 5 minutes.

### Step 1: Setting up a Heroku Application

Heroku의 웹 포털에서 새 응용 프로그램을 만들고 데이터베이스를 사용하는 경우 'Heroku Postgres' 부가 기능을 추가하십시오.

Heroku 웹 인터페이스의 설정 탭으로 이동하여 '구성 변수 표시'를 클릭하십시오. PostgreSQL 데이터베이스의 연결 정보 인 DATABASE_URL 환경 변수가 있습니다. 값을 기억할 필요는 없으며 이 환경 변수 만 존재한다는 것입니다.

### Step 2: Setting up an Aqueduct Application to Run on Heroku

아직 로컬 컴퓨터에 새로운 Aqueduct 응용 프로그램을 만들고, 그 디렉토리로 가서, 이미 존재하지 않는 경우 git 저장소로 초기화하십시오 :

```sh
aqueduct create app_name
cd app_name
git init
```

다음 명령을 실행하여 Heroku 환경에서 프로젝트를 구성하십시오.

Heroku Application Name

다음 명령에서 `app_name`은 Aqueduct 응용 프로그램의 이름이 아닌 웹 포털에서 생성 된 Heroku 응용 프로그램 의 이름입니다.

```sh
# This is an interactive command that you have to enter your username and password.
heroku login

# This adds a remote repository hosted by Heroku for your application that you push to.
heroku git:remote -a app_name

# Specifies the Dart SDK to use
heroku config:set DART_SDK_URL=https://storage.googleapis.com/dart-archive/channels/stable/release/latest/sdk/dartsdk-linux-x64-release.zip

# Specifies the Heroku Buildpack to use
heroku config:set BUILDPACK_URL=https://github.com/stablekernel/heroku-buildpack-dart.git

# Specifies that aqueduct should be activated
heroku config:set DART_GLOBAL_PACKAGES="aqueduct@3.0.0"
heroku config:set PATH=/app/bin:/usr/local/bin:/usr/bin:/bin:/app/.pub-cache/bin:/app/dart-sdk/bin
heroku config:set PUB_CACHE=/app/pub-cache
```

그런 다음 프로젝트 디렉토리에 `Procfile`이라는 이름의 새 파일을 만들고 (접미어 없음) 다음을 입력하십시오:

```
release: /app/dart-sdk/bin/pub global run aqueduct:aqueduct db upgrade --connect $DATABASE_URL
web: /app/dart-sdk/bin/pub global run aqueduct:aqueduct serve --port $PORT --config-path heroku.yaml
```

이 파일은 Heroku에게 응용 프로그램을 실행하는 방법을 알려주고 릴리스를 푸시 할 때마다 데이터베이스 마이그레이션을 실행합니다. 이 파일이 버전 관리에 체크인되어 있는지 확인하십시오.

```sh
git commit -am "Adds Procfile"
```

### Step 3: Configuring Application Values

Heroku는 환경 변수를 통해 구성 값을 제공합니다. `Procfile`에서 설정을 위해 `heroku.yaml`이라는 파일을 사용할 것임을 나타 냈습니다. 이 파일은 응용 프로그램의 구성 값을 Heroku 플랫폼의 환경 변수에 매핑합니다. 구성 파일은 다를 수 있지만 데이터베이스를 사용하는 경우 데이터베이스 자격 증명은 `connection string`을 통해 제공된다는 점에 유의해야합니다. 연결 문자열은`postgres://user:password@host:5432/name`처럼 보이며 기본적으로 Heroku는 환경 변수 `DATABASE_URL`에 저장합니다.

프로젝트 디렉토리에서 생성해야 하는 `heroku.yaml`에서 환경 변수의 이름 앞에 `$`접두사를 붙여서 환경 변수를 참조 할 수 있습니다. 내장 된 `DatabaseConfiguration` 타입을 사용할 때 다음과 같이 연결 문자열을 지정할 수 있습니다 :

```
database: $DATABASE_URL
```

너의 주인공. 얌은 다를지도 몰라.

`heroku.yaml` 파일의 구조가 어플리케이션의 `Configuration` 서브 클래스에서 예상되는 구조와 일치하는지 확인하십시오.

`heroku.yaml`을 버전 관리에 확인하십시오.

```
git commit -am "add heroku.yaml"
```

### Step 4: Running the Aqueduct Application

응용 프로그램에서 데이터베이스를 사용하는 경우 마이그레이션 파일을 생성하고 버전 제어에 추가했는지 확인하십시오. `Procfile`은 당신의 앱을 실행하기 전에 어떤 소스가 소스 컨트롤에 체크인되었는지에 대한 데이터베이스가 최신인지 확인합니다. 프로젝트 디렉토리에서 다음 명령을 사용하여 마이그레이션 파일을 생성 한 다음 버전 제어를 확인하십시오.

```
aqueduct db generate
git commit -am "adds migration files"
```

이제 애플리케이션을 배치 할 수 있습니다. 다음과 같이 간단합니다.

```
git push heroku master
```

이 명령은 Heroku가 호스트하는 원격 자식 서버에 코드를 푸시합니다. 이 코드는 응용 프로그램이 릴리스 스크립트를 실행 하도록 합니다.

이제 애플리케이션의 데이터베이스 스키마가 업로드되었으므로 `package:aqueduct/managed_auth`를 사용하는 경우 클라이언트 식별자로 OAuth 2 서버를 구성 할 수 있습니다. 다음 명령은 응용 프로그램의 원격 환경에서 실행됩니다.

```
heroku run /app/dart-sdk/bin/pub global run aqueduct:aqueduct auth add-client --id com.app.standard --secret secret --connect \$DATABASE_URL
```

마지막으로 dyno를 확대하면 응용 프로그램에서 요청을 받기 시작합니다.

```
heroku ps:scale web=1
```

이제 애플리케이션이 실행 중입니다!