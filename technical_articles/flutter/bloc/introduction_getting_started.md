[Up](./index.md)

# 시작 (Getting Started)

block를 사용하려면 먼저 [Dart SDK](https://www.dartlang.org/install)를 당신의 머신에 설치해야 합니다.

<p id="overview"/>

## 개요 (Overview)

Bloc은 몇가지 pub 패키지로 구성됩니다.

- [bloc](https://pub.dartlang.org/packages/bloc) - 핵심 bloc 라이브러리.
- [flutter_bloc](https://pub.dartlang.org/packages/flutter_bloc) - 신속하게 Reactive 모바일 애플리케이션을 구축하기 위해 bloc으로 작업하도록 만들어진 강력한 Flutter 위젯.
- [angular_bloc](https://pub.dartlang.org/packages/angular_bloc) - 신속하게 반응성 웹 애플리케이션 구축하기 위해 bloc으로 작업하도록 만들어진 강력한 Angular 컴포넌트.

<p id="install"/>

## 설치 (Installation)

가장 먼저 해야 할 일은 bloc 패키지를 `pubspec.yaml`의 `dependencies`에 추가하는 것입니다.

```yaml
dependencies:
  bloc: ^0.13.0
```

[Flutter](https://flutter.io) 애플리케이션의 경우, `flutter_bloc` 패키지를 `pubspec.yaml`의 `dependencies`에 추가 해야 합니다.

```yaml
dependencies:
  bloc: ^0.13.0
  flutter_bloc: ^0.13.0
```

[AngularDart](https://webdev.dartlang.org/angular) 애플리케이션의 경우, `angular_bloc` 패키지를 `pubspec.yaml`의 `dependencies`에 추가 해야 합니다.

```yaml
dependencies:
  bloc: ^0.13.0
  angular_bloc: ^0.8.0
```

다음으로 우리는 bloc를 설치 해야 합니다.

`pubspec.yaml` 파일과 같은 디렉토리에서 다음 명령을 실행하세요.

Dart 또는 AngularDart는

```
pub get
```

을 실행하세요.

Flutter는 

```
flutter packages get
```

을 실행하세요.

<p id="import"/>

## 임포트 (Import)

이제 bloc을 성공적으로 설치 했으므로 `main.dart`를 만들어 bloc을 임포트 할 수 있습니다.

```dart
import 'package:bloc/bloc.dart';
```

Flutter 애플리케이션의 경우 flutter_bloc을 임포트 할 수 있습니다.

```dart
import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
```

AngularDart 애플리케이션의 경우 angular_bloc을 가져올 수 있습니다.

```dart
import 'package:bloc/bloc.dart';
import 'package:angular_bloc/angular_bloc.dart';
```

---

다음: [왜 Bloc인가? (Why Bloc?)](introduction_why_bloc.md)