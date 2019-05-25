# pubspec 파일 사용법

원문: [The pubspec file](https://dart.dev/tools/pub/pubspec)

## Contents                                

- [Supported fields](#supported_fileds)
- [Example](#example)
- Details
  - [Name](#name)
  - [Version](#version)
  - [Description](#description)
  - [Author/Authors](#authorauthors)
  - [Homepage](#homepage)
  - [Repository](#repository)
  - [Issue tracker](#issue-tracker)
  - [Documentation](#documentation)
  - [Dependencies](#dependencies)
  - [Executables](#executables)
  - [Publish_to](#publish_to)
  - [SDK constraints](#sdk-constraints)

모든 [pub package](https://dart.dev/guides/packages)에는 메타 데이터가 필요하므로 [dependencies](https://dart.dev/tools/pub/glossary#dependency)을 지정할 수 있습니다. 다른 사람들과 공유되는 Pub 패키지는 사용자가 찾을 수 있도록 다른 정보를 제공해야 합니다. 이 모든 메타 데이터는 패키지의  pubspec에 있으며, [YAML](http://www.yaml.org/) 언어로 작성된 'pubspec.yaml' 파일에 저장됩니다.

<p id = "supported_fileds"/>

## Supported fields

pubspec에는 다음과 같은 필드가 있습니다.

- `name`

  모든 패키지에 필요. [*Learn more.*](#name) 

- `version`

  Pub 사이트에서 호스팅되는 패키지에 필요. [*Learn more.*](#version) 

- `description`

  [Pub site](https://pub.dev) 에서 호스팅되는 패키지에 필요. [*Learn more.*](#description) 

-  `author` or `authors` 

  선택사항. [*Learn more.*](#authorauthors) 

- `homepage`

  선택사항. 패키지의 홈페이지 또는 소스코드 저장소를 가리키는 URL. [*Learn more.*](https://dart.dev/tools/pub/pubspec#homepage) 

- `repository`

  선택사항. 패키지의 소스코드 저장소를 가리키는 URL. [*Learn more.*](https://dart.dev/tools/pub/pubspec#repository) 

- `issue_tracker`

  선택사항. 패키지의 이슈 트래커를 가리키는 URL. [*Learn more.*](https://dart.dev/tools/pub/pubspec#issue-tracker) 

- `documentation`

  선택사항. 자동으로 생성되는 문서도 됨. [*Learn more.*](https://dart.dev/tools/pub/pubspec#documentation) 

- `dependencies`

  의존성이 없는 경우 생략할 수 있음. [*Learn more.*](https://dart.dev/tools/pub/pubspec#dependencies) 

- `dev_dependencies`

  dev 의존성이 없는 경우 생략 할 수 있음. [*Learn more.*](https://dart.dev/tools/pub/pubspec#dependencies) 

- `dependency_overrides`

  의존성을 오버라이드 할 필요 없으면 생략할 수 있음. [*Learn more.*](https://dart.dev/tools/pub/pubspec#dependencies) 

- `environment`

  Dart 2 에서 필수. [*Learn more.*](https://dart.dev/tools/pub/pubspec#sdk-constraints) 

- `executables`

  선택사항. PATH에 패키지의 실행 파일을 저장하는데 사용. [*Learn more.*](https://dart.dev/tools/pub/pubspec#executables) 

- `publish_to`

  선택사항. 패키지를 어디에 게시할지 지정. [*Learn more.*](https://dart.dev/tools/pub/pubspec#publish_to) 

Pub은 다른 모든 필드를 무시합니다.

**Flutter note:** Flutter apps](https://flutter.dev)의 Pubspec은 애셋 관리를 위해 [몇 가지 추가 필드](https://flutter.dev/assets-and-images)를 가질 수 있습니다.

<p id = "example"/>

## Example

간단하지만 완전한 pubspec은 다음과 유사합니다:

```yaml
name: newtify
version: 1.2.3
description: >-
  Have you been turned into a newt?  Would you like to be?
  This package can help. It has all of the
  newt-transmogrification functionality you have been looking
  for.
author: Natalie Weizenbaum <nweiz@google.com>
homepage: https://example-pet-store.com/newtify
documentation: https://example-pet-store.com/newtify/docs
environment:
  sdk: '>=2.0.0 <3.0.0'
dependencies:
  efts: ^2.0.4
  transmogrify: ^0.4.0
dev_dependencies:
  test: '>=0.6.0 <0.12.0'
```

<p id = "details"/>

## Detailsauthorauthors

이 섹션에는 대부분의 pubspec 필드에 대한 추가 정보가 있습니다.

<p id = "name"/>

### Name

모든 패키지에는 이름이 필요합니다. 다른 패키지가 귀하의 정보를 참조하는 방식이며 귀하가 게시해야하는 경우 전세계에 나타나는 방식입니다.

이름은 모두 소문자 이며 단어를 구분하기 위한 밑줄로 '`just_like_this`게 해야 합니다. 이렇게 `[a-z0-9_]` 기본 라틴 문자와 아랍어 숫자만 사용하세요. 또한 이름이 유효한 Dart 식별자인지 확인하십시오. 즉, 숫자로 시작하지 않으며 [예약어](https://dart.dev/guides/language/language-tour#keywords)가 아닙니다.

명료하고 간결하며 이미 사용되지 않은 이름을 선택하세요. [Pub site](https://pub.dev/packages)의 패키지를 빠르게 검색하여 다른 사람이 귀하의 이름을 사용하지 않는지 확인하는 것이 좋습니다.

<p id = "version"/>

### Version

모든 패키지에는 버전이 있습니다. Pub 사이트에서 패키지를 호스팅하려면 버전 번호가 필요하지만 로컬 전용 패키지에서는 버전 번호를 생략 할 수 있습니다. 이것을 생략하면 패키지는 `0.0.0`으로 암시 적으로 버전이 변경됩니다.

버전을 빠르게 재사용하면서 코드를 다시 사용하려면 버전 관리가 필요합니다. 버전 번호는 `0.2.43`과 같이 점으로 구분 된 세 개의 숫자입니다. 또한 선택적으로 빌드 (`+hotfix.oopsie`) 또는 시험판 (`-alpha.12`) 접미어를 가질 수 있습니다.

패키지를 게시 할 때마다 특정 버전으로 패키지를 게시합니다. 그 일이 끝나면 밀본(sealed) 된 것으로 간주하십시오. 더 이상 건드릴 수 없습니다. 더 많은 변경을하려면 새 버전이 필요합니다.

버전을 선택할 때 [semantic versioning](http://semver.org/spec/v2.0.0-rc.1.html)를 따르십시오.

<p id = "description"/>

### Description

이는 개인 패키지의 경우 선택 사항이지만 패키지를 게시하려면 설명을 제공해야 합니다. 이것은 상대적으로 짧아야 합니다 - 몇 문장, 어쩌면 전체 단락 - 캐주얼 독자에게 패키지에 대해 알고 싶은 내용을 알려줍니다.

설명을 패키지 sales pitch로 생각하세요. 사용자가 [browse for packages](https://pub.dev/packages)를 보게 됩니다. 간단한 일반 텍스트 여야합니다: 마크다운 또는 HTML이 없습니다. 그게 당신의 README입니다.

<p id = "authorauthors"/>

### Author/Authors

이 필드를 사용하여 패키지 작성자를 설명하고 연락처 정보를 제공하는 것이 좋습니다. 패키지에 단 하나의 저자가 있다면 `author`가 사용되어야하고 둘 이상의 사람이 패키지를 작성한다면 `authors`는 YAML리스트와 함께 사용해야 합니다. 각 저자는 하나의 이름 ( `Natalie Weizenbaum`)이거나 이름과 이메일 주소 (`Natalie Weizenbaum <nweiz@google.com>`) 일 수 있습니다.

```yaml
authors:
- Natalie Weizenbaum <nweiz@google.com>
- Bob Nystrom <rnystrom@google.com>
```

누구든지 귀하의 패키지를 Pub 사이트에 업로드하면 이메일 주소는 공개됩니다.

<p id = "homepage"/>

### Homepage

이 URL은 패키지 웹 사이트를 가리키는 URL이어야 합니다. [hosted packages](https://dart.dev/tools/pub/dependencies#hosted-packages)의 경우이 URL은 패키지 페이지에서 링크됩니다. `homepage`를 제공하는 것은 선택 사항이지만, 그것 또는 (아니면 둘다) `repository`를 제공하십시오. 사용자가 패키지의 출처를 파악하는 데 도움이 됩니다.

<p id = "repository"/>

### Repository

선택적 `repository` 필드는 패키지 소스 코드 저장소의 URL을 포함 해야 합니다 (예 :`https://github.com/<user>/<repository>`). Pub 사이트에 패키지를 게시하면 패키지 페이지에 저장소 URL이 표시 됩니다. `repository`를 제공하는 것은 선택 사항이지만, 그것 또는 (아니면 둘다) `homepage` 를 제공하십시오. 사용자가 패키지의 출처를 파악하는 데 도움이됩니다.

<p id = "issue_tracker"/>

### Issue tracker

선택적인 `issue_tracker` 필드에는 패키지의 이슈 추적기에 대한 URL이 포함되어야 합니다. 기존 버그를 보고 새 버그를 제출할 수 있습니다. Pub 사이트는 이 필드의 값을 사용하여 각 패키지의 이슈 트래커에 대한 링크를 표시하려고 시도합니다. `issue_tracker`가 빠졌지만 `repository`가 있고 GitHub를 가리키면 Pub 사이트는 기본 이슈 트래커 (https://github.com/ <user> / <repository> / issues`)를 사용합니다.

<p id = "documentation"/>

### Documentation

일부 패키지에는 메인 홈페이지와 별도의 문서를 호스팅하는 사이트가 있을 수 있습니다. 패키지에 있는 경우 해당 URL과 함께 `documentation:`필드를 추가 할 수도 있습니다. 제공되면 링크가 패키지 페이지에 표시됩니다.

빈 값으로`documentation:`필드를 지정하면 문서가 자동으로 만들어지고 [Pub site](https://pub.dev)에서 링크됩니다.

<p id = "dependencies"/>

### Dependencies

[Dependencies](https://dart.dev/tools/pub/glossary#dependency)은 pubspec의 존재 이유입니다. 이 절에서는 패키지가 작동하는 데 필요한 각 패키지를 나열합니다.

종속성은 두 가지 유형 중 하나에 속합니다. 정규 의존성은 `dependencies:`에 열거되어 있습니다. 이들은 패키지를 사용하는 사람도 필요로 하는 패키지 입니다. 패키지 자체의 개발 단계에서만 필요한 종속성은`dev_dependencies` 아래에 있습니다.

개발 프로세스 중에 종속성을 일시적으로 무시해야 할 수 있습니다. `dependency_overrides`를 사용하면 됩니다.

자세한 내용은 [Pub Dependencies](https://dart.dev/tools/pub/dependencies)를 참조하십시오.

<p id = "executables"/>

### Executables

패키지는 스크립트 중 하나 이상을 명령 줄에서 직접 실행할 수 있는 실행 파일로 표시 할 수 있습니다. 스크립트를 공개적으로 사용할 수 있게 하려면 `executables` 필드에 스크립트를 나열하세요. 항목은 키/값 쌍으로 나열됩니다.

```yaml
<name-of-executable>: <Dart-script-from-bin>
```

예를 들어, 다음 pubspec 항목은 두 개의 스크립트를 나열합니다.

```yaml
executables:
   polymer-new-element: new_element
   useful-script:
```

패키지가 `pub global activate`을 사용하여 활성화되면, `polymer-new-element`를 입력하여 `bin/new_element.dart`가 실행됩니다. `useful-script`를 입력하면 `bin/useful-script.dart`가 실행됩니다. 값을 지정하지 않으면 키에서 유추됩니다.

자세한 내용은 [pub global](https://dart.dev/tools/pub/cmd/pub-global#running-a-script-from-your-path)을 참조 하세요.

<p id = "publish_to"/>

### Publish_to

기본값은 [Pub site](https://pub.dev)를 사용합니다. 패키지가 게시되지 않도록 하려면 `none`을 지정하세요. 이 설정을 사용하여 게시 할 [custom pub package server](https://github.com/dart-lang/pub-dartlang-dart/)를 지정할 수 있습니다.

```yaml
publish_to: none
```

<p id = "sdk_constraints"/>

### SDK constraints

패키지는 지원하는 종속성의 버전을 나타낼 수 있지만 패키지에는 암시적 종속성 (다트 플랫폼 자체)이 있습니다. Dart 플랫폼은 시간이 지남에 따라 발전하며 패키지는 특정 버전의 플랫폼에서만 작동합니다.

패키지는 *SDK constratins*을 사용하여 해당 버전을 지정할 수 있습니다. 이 제약 조건은 pubspec의 별도의 최상위 `environment` 필드 안에 들어가며 동일한 [version constraint](https://dart.dev/tools/pub/dependencies#version-constraints) 구문을 의존성으로 사용합니다.

예를 들어 다음 제약 조건은이 패키지가 버전 2.0.0 이상인 **Dart 2** SDK와 함께 작동 함을 나타냅니다.

```yaml
environment:
  sdk: '>=2.0.0 <3.0.0'
```

Pub는 SDK 제약 조건이 설치된 Dart SDK 버전에서 작동하는 패키지의 최신 버전을 찾으려고 시도합니다.

SDK constraint에는 캐럿 구문 (`^`)을 사용하지 말고, **상한값** (보통 <3.0.0`)이 포함됩니다. 자세한 내용은 [Dart 2 page](https://dart.dev/dart-2)를 참조하세요

<p id = "flutter_sdk_constraints"/>

#### Flutter SDK constraints

Dart 1.19.0부터 pub는 `environment :` 필드 아래에 Flutter SDK constraints을 지원합니다.

```yaml
environment:
  sdk: '>=1.19.0 <3.0.0'
  flutter: ^0.1.2
```

Flutter SDK 제약 조건은 pub가 `flutter` 실행 파일의 컨텍스트에서 실행되고 Flutter SDK의 `version` 파일이 주어진 버전 제약 조건과 일치 할 때만 충족됩니다. 그렇지 않으면 패키지가 선택되지 않습니다.

Flutter SDK constraint이 있는 패키지를 게시하려면 이전 버전의 pub에서 Flutter가 필요한 패키지를 실수로 설치하지 않도록 최소 버전이 1.19.0 이상인 Dart SDK constraint을 지정해야합니다.

---

## 참조

- [The pubspec file](https://dart.dev/tools/pub/pubspec)