[Up](./index.md)

# [Flutter Firebase Login Tutorial](https://felangel.github.io/bloc/#/flutterfirebaselogintutorial?id=flutter-firebase-login-tutorial)

원문: [Bloc / Tutorial / Firebase Login](https://felangel.github.io/bloc/#/flutterfirebaselogintutorial)

![advanced](tutorials_flutter_ firebase_login.assets/level-advanced-red.svg)

> 이 튜터리얼에서 우리는 Flutter에서 Bloc 라이브러리를 사용하여 Firebase Login Flow를 만들어 보도록 하겠습니다.

![demo](tutorials_flutter_ firebase_login.assets/flutter_firebase_login.gif)

<p id = "setup"/>

## 준비 (Setup)

새 이름으로 Flutter 프로젝트를 생성하면서 시작하겠습니다.

```bash
flutter create flutter_firebase_login
```

`pubspec.yaml`을 아래처럼 수정합니다.

```yaml
name: flutter_firebase_login
description: A new Flutter project.

version: 1.0.0+1

environment:
  sdk: ">=2.0.0-dev.68.0 <3.0.0"

dependencies:
  flutter:
    sdk: flutter
  cloud_firestore: ^0.9.7
  firebase_auth: ^0.8.1+4
  google_sign_in: ^4.0.1+1
  flutter_bloc: ^0.13.0
  equatable: ^0.2.0
  meta: ^1.1.6
  font_awesome_flutter: ^8.4.0

dev_dependencies:
  flutter_test:
    sdk: flutter

flutter:
  uses-material-design: true
  assets:
    - assets/
```

애플리케이션의 모든 로컬 애셋에 대해 assets 디렉토리를 지정한다는 점에 유의하세요. 프로젝트의 루트에`assets` 디렉토리를 만들고 [flutter logo](https://github.com/felangel/bloc/blob/master/examples/flutter_firebase_login/assets/flutter_logo.png) 애셋을 추가 하세요. 나중에 사용할 것입니다.

터미널에서 아래를 입력하여 모든 의존성을 설치합니다.

```bash
flutter packages get
```

마지막으로 [firebase_auth instroductions](https://pub.dartlang.org/packages/firebase_auth#usage)을 따라 애플리케이션을 firebase에 연결하고 [google_signin](https://pub.dartlang.org/packages/google_sign_in).

<p id="userrespository"/>

## UserRepository

[flutter login tutorial](https://felangel.github.io/bloc/#/./flutterlogintutorial)처럼, 는 사용자 정보를 인증하고 검색하는 방법에 대한 기본적인 구현을 추상화 하는 `UserRepository`를 만들어야 할 것입니다.

`user_repository.dart` 파일을 만들고 시작하겠습니다.

우리는 `UserRepository` 클래스를 정의하고 생성자를 구현함으로써 시작할 수 있습니다. `UserRepository`가 `FirebaseAuth`와 `GoogleSignIn` 모두에 의존성을 가질 것이라는 것을 바로 알 수 있습니다.

```dart
import 'package:firebase_auth/firebase_auth.dart';
import 'package:google_sign_in/google_sign_in.dart';

class UserRepository {
  final FirebaseAuth _firebaseAuth;
  final GoogleSignIn _googleSignIn;

  UserRepository({FirebaseAuth firebaseAuth, GoogleSignIn googleSignin})
      : _firebaseAuth = firebaseAuth ?? FirebaseAuth.instance,
        _googleSignIn = googleSignin ?? GoogleSignIn();
}
```

**Note:** `FirebaseAuth` 이나 `GoogleSignIn` 이 `UserRepository`에 주입되지 않으면, 우리는 내부적으로 그것들을 인스턴스화합니다. 이것은 우리가 `UserRepository`를 쉽게 테스트 할 수 있도록 mock 인스턴스를 삽입 할 수있게합니다.

우리가 구현할 첫 번째 메소드는 `signInWithGoogle`을 호출하고 `GoogleSignIn` 패키지를 사용하여 사용자를 인증합니다.

```dart
Future<FirebaseUser> signInWithGoogle() async {
  final GoogleSignInAccount googleUser = await _googleSignIn.signIn();
  final GoogleSignInAuthentication googleAuth =
      await googleUser.authentication;
  final AuthCredential credential = GoogleAuthProvider.getCredential(
    accessToken: googleAuth.accessToken,
    idToken: googleAuth.idToken,
  );
  await _firebaseAuth.signInWithCredential(credential);
  return _firebaseAuth.currentUser();
}
```

다음으로 `signInWithCredentials` 메소드를 구현하여 사용자가 `FirebaseAuth`를 사용하여 자신의 자격 증명(credentials)으로 로그인 할 수있게 할 것입니다.

```dart
Future<void> signInWithCredentials(String email, String password) {
  return _firebaseAuth.signInWithEmailAndPassword(
    email: email,
    password: password,
  );
}
```

다음으로, 사용자가 Google Sign In을 사용하지 않기로 결정한 경우, 계정을 만들 수 있는 `signUp` 메소드를 구현해야 합니다.

```dart
Future<void> signUp({String email, String password}) async {
  return await _firebaseAuth.createUserWithEmailAndPassword(
    email: email,
    password: password,
  );
}
```

사용자가 로그 아웃하고 프로파일 정보를 장치에서 지울 수 있도록 `signOut` 메소드를 구현해야 합니다.

```dart
Future<void> signOut() async {
  return Future.wait([
    _firebaseAuth.signOut(),
    _googleSignIn.signOut(),
  ]);
}
```

마지막으로, 사용자가 이미 인증 받았는지 확인하고 정보를 검색 할 수 있게 해주는 `isSignedIn`과 `getUser` 메소드가 필요합니다.

```dart
Future<bool> isSignedIn() async {
  final currentUser = await _firebaseAuth.currentUser();
  return currentUser != null;
}

Future<String> getUser() async {
  return (await _firebaseAuth.currentUser()).email;
}
```

**Note:** `getUser`는 단순화를 위해 현재 사용자의 전자 메일 주소를 반환 만합니다. 그러나 우리는 우리 자신의 사용자 모델을 정의하고 더 복잡한 애플리케이션에서 사용자에 대한 더 많은 정보를 채울 수 있습니다.

우리의 완성 된 `user_repository.dart`는 다음과 같아야 합니다 :

```dart
import 'dart:async';
import 'package:firebase_auth/firebase_auth.dart';
import 'package:google_sign_in/google_sign_in.dart';

class UserRepository {
  final FirebaseAuth _firebaseAuth;
  final GoogleSignIn _googleSignIn;

  UserRepository({FirebaseAuth firebaseAuth, GoogleSignIn googleSignin})
      : _firebaseAuth = firebaseAuth ?? FirebaseAuth.instance,
        _googleSignIn = googleSignin ?? GoogleSignIn();

  Future<FirebaseUser> signInWithGoogle() async {
    final GoogleSignInAccount googleUser = await _googleSignIn.signIn();
    final GoogleSignInAuthentication googleAuth =
        await googleUser.authentication;
    final AuthCredential credential = GoogleAuthProvider.getCredential(
      accessToken: googleAuth.accessToken,
      idToken: googleAuth.idToken,
    );
    await _firebaseAuth.signInWithCredential(credential);
    return _firebaseAuth.currentUser();
  }

  Future<void> signInWithCredentials(String email, String password) {
    return _firebaseAuth.signInWithEmailAndPassword(
      email: email,
      password: password,
    );
  }

  Future<void> signUp({String email, String password}) async {
    return await _firebaseAuth.createUserWithEmailAndPassword(
      email: email,
      password: password,
    );
  }

  Future<void> signOut() async {
    return Future.wait([
      _firebaseAuth.signOut(),
      _googleSignIn.signOut(),
    ]);
  }

  Future<bool> isSignedIn() async {
    final currentUser = await _firebaseAuth.currentUser();
    return currentUser != null;
  }

  Future<String> getUser() async {
    return (await _firebaseAuth.currentUser()).email;
  }
}
```

다음은`AuthenticationEvents`에 대한 응답으로 어플리케이션의 `AuthenticationState`를 처리 할 책임이있는 `AuthenticationBloc`을 빌드 할 것입니다.

<p id = "authenticationstate"/>

## AuthenticationState

우리는 애플리케이션 상태를 어떻게 관리하고 필요한 Bloc (Business Logic Component)을 만들지 결정해야 합니다.

높은 수준에서 우리는 사용자의 인증 상태를 관리해야 할 것입니다. 사용자의 인증 상태는 다음 중 하나 일 수 있습니다:

- uninitialized - 사용자가 앱 시작시 인증 여부 대기중
- authenticated - 성공적으로 증 됨
- unauthenticated - 인증되지 않음

이 각각의 상태들은 사용자에게 보여 주는 것에 의미가 있습니다.

예를 들어:

- 인증 상태가 초기화되지 않은 경우 사용자에게 스플래시 화면이 표시 될 수 있습니다.
- 인증 상태가 인증되면 사용자가 홈 화면을 볼 수 있습니다.
- 인증 상태가 인증되지 않은 경우 사용자는 로그인 양식을 볼 수 있습니다.

> 구현에 들어가기 전에 다른 상태들이 무엇인지 파악하는 것이 중요합니다.

인증 상태 식별자들이 정의 되었으므로 이제 우리는 `AuthenticationState` 클래스를 구현할 수 있습니다.

Create a folder/directory called `authentication_bloc` and we can create our authentication bloc files.

`authentication_bloc` 폴더를 생성하면 인증 bloc 파일을 생성 할 수 있습니다.

```sh
├── authentication_bloc
│   ├── authentication_bloc.dart
│   ├── authentication_event.dart
│   ├── authentication_state.dart
│   └── bloc.dart
```

**Tip:** [IntelliJ](https://plugins.jetbrains.com/plugin/12129-bloc-code-generator) 또는 [VSCode](https://marketplace.visualstudio.com/items?itemName=FelixAngelov.bloc#overview) 같은 확장 프로그램을 사용하여 파일을 자동 생성합니다.

```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';

@immutable
abstract class AuthenticationState extends Equatable {
  AuthenticationState([List props = const []]) : super(props);
}

class Uninitialized extends AuthenticationState {
  @override
  String toString() => 'Uninitialized';
}

class Authenticated extends AuthenticationState {
  final String displayName;

  Authenticated(this.displayName) : super([displayName]);

  @override
  String toString() => 'Authenticated { displayName: $displayName }';
}

class Unauthenticated extends AuthenticationState {
  @override
  String toString() => 'Unauthenticated';
}
```

**Note**: [`equatable`](https://pub.dartlang.org/packages/equatable) 패키지는 `AuthenticationState`의 두 인스턴스를 비교할 수 있게 하기 위해 사용됩니다. 기본적으로 `==`는 두 객체가 같은 인스턴스 인 경우에만 `true`를 반환합니다.

**Note**: `toString`은 콘솔이나 `Transitions`에 출력 할 때 `AuthenticationState`를 읽기 쉽게하기 위해 재정의 됩니다.

우리는 `Equatable`을 사용하여 `AuthenticationState`의 다른 인스턴스를 비교할 수 있기 때문에 어떠한 프로퍼티라도 수퍼 클래스에 전달해야 합니다.  `super([displayName])`이 없으면 우리는 `Authenticated`의 다른 인스턴스들을 적절히 비교할 수 없을 것입니다.

<p id = "authenticationEvent"/>



## AuthenticationEvent

이제 우리는 우리의 `AuthenticationState`를 정의했습니다. 우리는`AuthenticationBloc`이 반응 할`AuthenticationEvents`를 정의 할 필요가 있습니다.

우리가 해야 할 것은:

- 사용자가 현재 인증 여부를 체크 할 필요가 있다는 것을 Bloc에 알리는`AppStarted` 이벤트.
- 사용자가 성공적으로 로그인 했음을 bloc에 알리는 `LoggedIn` 이벤트.
- 사용자가 성공적으로 로그 아웃했다는 것을 bloc에 알리는`LoggedOut` 이벤트.

```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';

@immutable
abstract class AuthenticationEvent extends Equatable {
  AuthenticationEvent([List props = const []]) : super(props);
}

class AppStarted extends AuthenticationEvent {
  @override
  String toString() => 'AppStarted';
}

class LoggedIn extends AuthenticationEvent {
  @override
  String toString() => 'LoggedIn';
}

class LoggedOut extends AuthenticationEvent {
  @override
  String toString() => 'LoggedOut';
}
```

<p id = "authentication-barrel-file"/>

## Authentication 배럴 파일

`AuthenticationBloc` 구현을  하기전에 모든 authentication bloc 파일을 `authentication_bloc/bloc.dart` 배럴 파일에서 export 할 것입니다. 이렇게 하면 나중에 하나의 import로 `AuthenticationBloc`, `AuthenticationEvents`, `AuthenticationState`를 가져올 수 있습니다.

```dart
export 'authentication_bloc.dart';
export 'authentication_event.dart';
export 'authentication_state.dart';
```

<p id = "authenticationbloc"/>

## AuthenticationBloc

이제 우리는 `AuthenticationState`와 `AuthenticationEvents`를 정의 했으므로 `AuthenticationEvents`에 대한 응답으로 사용자의 `AuthenticationState`를 검사하고 업데이트하는 `AuthenticationBloc`을 구현할 수 있습니다.

`AuthenticationBloc` 클래스를 생성하며 출발 하겠습니다.

```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'package:meta/meta.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';

class AuthenticationBloc
    extends Bloc<AuthenticationEvent, AuthenticationState> {
  final UserRepository _userRepository;

  AuthenticationBloc({@required UserRepository userRepository})
      : assert(userRepository != null),
        _userRepository = userRepository;
```

**Note**: 클래스 정의를 읽어 보고, 우리는 이 bloc이 `AuthenticationEvents`를 `AuthenticationStates`로 변환 할 것이라는 것을 이미 알고 있습니다.

**Note**: 우리의 `AuthenticationBloc`은 `UserRepository`에 의존합니다.

우리는 `initialState`를 `AuthenticationUninitialized()`상태로 재정의하여 시작할 수 있습니다.

```dart
@override
AuthenticationState get initialState => Uninitialized();
```

이제 남겨진 것은 `mapEventToState`를 구현하는 것입니다.

```dart
@override
Stream<AuthenticationState> mapEventToState(
  AuthenticationEvent event,
) async* {
  if (event is AppStarted) {
    yield* _mapAppStartedToState();
  } else if (event is LoggedIn) {
    yield* _mapLoggedInToState();
  } else if (event is LoggedOut) {
    yield* _mapLoggedOutToState();
  }
}

Stream<AuthenticationState> _mapAppStartedToState() async* {
  try {
    final isSignedIn = await _userRepository.isSignedIn();
    if (isSignedIn) {
      final name = await _userRepository.getUser();
      yield Authenticated(name);
    } else {
      yield Unauthenticated();
    }
  } catch (_) {
    yield Unauthenticated();
  }
}

Stream<AuthenticationState> _mapLoggedInToState() async* {
  yield Authenticated(await _userRepository.getUser());
}

Stream<AuthenticationState> _mapLoggedOutToState() async* {
  yield Unauthenticated();
  _userRepository.signOut();
}
```

`mapEventToState`를 깨끗하고 읽기 쉬운 상태로 유지하기 위해, 각 `AuthenticationEvent`를 적절한 `AuthenticationState`로 변환하는 개별 헬퍼 함수를 만들었습니다.

**Note:** 우리는 `mapEventToState`에서 `yield*`(yield-each)를 사용하여 이벤트 핸들러를 자신의 함수로 분리합니다.  `yield*`는 서브시퀀스의 모든 원소를 현재 생성된 시퀀스에 삽입합니다.

우리의 완성된 `authentication_bloc.dart`는 다음과 같습니다:

```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'package:meta/meta.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';

class AuthenticationBloc
    extends Bloc<AuthenticationEvent, AuthenticationState> {
  final UserRepository _userRepository;

  AuthenticationBloc({@required UserRepository userRepository})
      : assert(userRepository != null),
        _userRepository = userRepository;

  @override
  AuthenticationState get initialState => Uninitialized();

  @override
  Stream<AuthenticationState> mapEventToState(
    AuthenticationEvent event,
  ) async* {
    if (event is AppStarted) {
      yield* _mapAppStartedToState();
    } else if (event is LoggedIn) {
      yield* _mapLoggedInToState();
    } else if (event is LoggedOut) {
      yield* _mapLoggedOutToState();
    }
  }

  Stream<AuthenticationState> _mapAppStartedToState() async* {
    try {
      final isSignedIn = await _userRepository.isSignedIn();
      if (isSignedIn) {
        final name = await _userRepository.getUser();
        yield Authenticated(name);
      } else {
        yield Unauthenticated();
      }
    } catch (_) {
      yield Unauthenticated();
    }
  }

  Stream<AuthenticationState> _mapLoggedInToState() async* {
    yield Authenticated(await _userRepository.getUser());
  }

  Stream<AuthenticationState> _mapLoggedOutToState() async* {
    yield Unauthenticated();
    _userRepository.signOut();
  }
}
```

이제 우리는 `AuthenticationBloc`을 완전히 구현 했으므로, 이제는 프리젠테이션 레이어를 작업 해 보겠습니다.

<p id = "app"/>

## App

`main.dart`에서 모든 코드를 삭제하고 `main` 함수를 구현하는 것으로 시작하겠습니다.

```dart
import 'package:flutter/material.dart';

main() {
  runApp(App());
}
```

다음으로 `App` 위젯을 구현해야 합니다.

> `App`은 `AuthenticationBloc`을 관리해야 하기 때문에 `StatefulWidget`이 될 것입니다.

```dart
import 'package:flutter/material.dart';
import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';

main() {
  runApp(App());
}

class App extends StatefulWidget {
  State<App> createState() => _AppState();
}

class _AppState extends State<App> {
  final UserRepository _userRepository = UserRepository();
  AuthenticationBloc _authenticationBloc;

  @override
  void initState() {
    super.initState();
    _authenticationBloc = AuthenticationBloc(userRepository: _userRepository);
    _authenticationBloc.dispatch(AppStarted());
  }

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      bloc: _authenticationBloc,
      child: MaterialApp(
        home: BlocBuilder(
          bloc: _authenticationBloc,
          builder: (BuildContext context, AuthenticationState state) {
            return Container();
          },
        ),
      ),
    );
  }

  @override
  void dispose() {
    _authenticationBloc.dispose();
    super.dispose();
  }
}
```

우리는 `_AppState` 클래스에 `UserRepository`의 인스턴스를 생성하고 `initState`의`AuthenticationBloc`에 그것을 주입합니다. 우리는 `_AppState`에 `AuthenticationBloc`을 만들었으므로  `_AppState`가 폐기 될때 삭제 해야 합니다.

우리는 `_authenticationBloc` 인스턴스를 `Widget` 서브 트리 전체에서 사용할 수 있도록 하기 위해`BlocProvider`를 사용하고 있습니다. 우리는 또한 `_authenticationBloc` 상태를 기반으로 UI를 렌더링하기 위해 `BlocBuilder`를 사용하고 있습니다.

지금까지 우리는 렌더링 할 위젯이 없지만 `SplashScreen`, `LoginScreen`, `HomeScreen`을 만들면 다시 이 코드로 돌아올 것입니다.

<p id="blocdelegate"/>

## BlocDelegate

Before we get too far along, it's always handy to implement our own `BlocDelegate` which allows us to override `onTransition` and `onError` and will help us see all bloc state changes (transitions) and errors in one place!

Create `simple_bloc_delegate.dart` and let's quickly implement our own delegate.

우리가 너무 멀어지기 전에 `onTransition`과 `onError`를 재정의 할 수 있는 우리 자신의 `BlocDelegate`를 구현하는 것이 언제나 편리합니다. 그리고 bloc 모든 상태 전환과 에러를 한 곳에서 볼 수있게 도와줍니다!

`simple_bloc_delegate.dart` 파일을 만들고 우리 자신의 델리게이트를 빨리 구현해 봅시다.

```dart
import 'package:bloc/bloc.dart';

class SimpleBlocDelegate extends BlocDelegate {
  @override
  void onEvent(Bloc bloc, Object event) {
    super.onEvent(bloc, event);
    print(event);
  }

  @override
  void onError(Bloc bloc, Object error, StackTrace stacktrace) {
    super.onError(bloc, error, stacktrace);
    print(error);
  }

  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    print(transition);
  }
}
```

이제 우리는 `main.dart`에서 `BlockDelegate`를 연결할 수 있습니다.

```dart
import 'package:flutter_firebase_login/simple_bloc_delegate.dart';

main() {
  BlocSupervisor().delegate = SimpleBlocDelegate();
  runApp(App());
}
```

<p id = "splashscreen"/>

## SplashScreen

다음으로, `AuthenticationBloc`이 사용자의 로그인 여부를 결정하는 동안 렌더링 될 `SplashScreen` 위젯을 만들어야 합니다.

`splash_screen.dart`를 만들고 구현 하세요.

```dart
import 'package:flutter/material.dart';

class SplashScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(child: Text('Splash Screen')),
    );
  }
}

```

당신이 말할 수 있듯이, 이 위젯은 super minimal 이고, 더 멋지게 보이게 하기 위해 일종의 이미지 또는 애니메이션을 추가하고 싶을 것입니다. 간단히하기 위해, 우리는 그것을 그냥 그대로 두려고 합니다.

자, 이제 `main.dart`에 연결 해보겠습니다.

```dart
import 'package:flutter/material.dart';
import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/splash_screen.dart';
import 'package:flutter_firebase_login/simple_bloc_delegate.dart';

main() {
  BlocSupervisor().delegate = SimpleBlocDelegate();
  runApp(App());
}

class App extends StatefulWidget {
  State<App> createState() => _AppState();
}

class _AppState extends State<App> {
  final UserRepository _userRepository = UserRepository();
  AuthenticationBloc _authenticationBloc;

  @override
  void initState() {
    super.initState();
    _authenticationBloc = AuthenticationBloc(userRepository: _userRepository);
    _authenticationBloc.dispatch(AppStarted());
  }

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      bloc: _authenticationBloc,
      child: MaterialApp(
        home: BlocBuilder(
          bloc: _authenticationBloc,
          builder: (BuildContext context, AuthenticationState state) {
            if (state is Uninitialized) {
              return SplashScreen();
            }
            return Container();
          },
        ),
      ),
    );
  }

  @override
  void dispose() {
    _authenticationBloc.dispose();
    super.dispose();
  }
}

```

이제 우리의 `AuthenticationBloc`이 `currentState`가 `Uninitialized` 일 때마다 우리는 `SplashScreen` 위젯을 렌더링 할 것입니다!

<p id = "homescreen"/>

## HomeScreen

다음으로, 성공적으로 로그인하면 사용자를 탐색 할 수 있도록 `HomeScreen`을 생성해야 합니다. 이 경우 `HomeScreen`은 사용자가 로그 아웃 할 수 있게하고 현재 이름(전자 메일)도 표시합니다.

`home_screen.dart` 파일을 만들고 시작하겠습니다.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';

class HomeScreen extends StatelessWidget {
  final String name;

  HomeScreen({Key key, @required this.name}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Home'),
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.exit_to_app),
            onPressed: () {
              BlocProvider.of<AuthenticationBloc>(context).dispatch(
                LoggedOut(),
              );
            },
          )
        ],
      ),
      body: Column(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: <Widget>[
          Center(child: Text('Welcome $name!')),
        ],
      ),
    );
  }
}

```

`HomeScreen`은 웰컴 메시지를 출력 할 수 있도록 `name`을 삽입 해야 하는 `StatelessWidget`입니다. 또한 사용자가 로그 아웃 버튼을 눌렀을 때 `LoggedOut` 이벤트를 보낼 수 있도록 `BuildContext`를 통해`AuthenticationBloc`에 접근하기 위해 `BlocProvider`를 사용합니다.

이제 `AuthenticationState`가 `Authentication` 인 경우 `App`을 업데이트하여 `HomeScreen`을 렌더링하겠습니다.

```dart
import 'package:flutter/material.dart';
import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/home_screen.dart';
import 'package:flutter_firebase_login/splash_screen.dart';
import 'package:flutter_firebase_login/simple_bloc_delegate.dart';

main() {
  BlocSupervisor().delegate = SimpleBlocDelegate();
  runApp(App());
}

class App extends StatefulWidget {
  State<App> createState() => _AppState();
}

class _AppState extends State<App> {
  final UserRepository _userRepository = UserRepository();
  AuthenticationBloc _authenticationBloc;

  @override
  void initState() {
    super.initState();
    _authenticationBloc = AuthenticationBloc(userRepository: _userRepository);
    _authenticationBloc.dispatch(AppStarted());
  }

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      bloc: _authenticationBloc,
      child: MaterialApp(
        home: BlocBuilder(
          bloc: _authenticationBloc,
          builder: (BuildContext context, AuthenticationState state) {
            if (state is Uninitialized) {
              return SplashScreen();
            }
            if (state is Authenticated) {
              return HomeScreen(name: state.displayName);
            }
          },
        ),
      ),
    );
  }

  @override
  void dispose() {
    _authenticationBloc.dispose();
    super.dispose();
  }
}

```

<p id = "loginstate"/>

## LoginState

마침내 로그인 플로우 작업을 시작할 시간입니다. 우리는 우리가 가질 다른 `LoginStates`를 식별함으로써 시작할 것입니다.

`login` 디렉토리를 만들고 표준 bloc 디렉토리와 파일들을 만듭니다.

```sh
├── lib
│   ├── login
│   │   ├── bloc
│   │   │   ├── bloc.dart
│   │   │   ├── login_bloc.dart
│   │   │   ├── login_event.dart
│   │   │   └── login_state.dart

```

우리의 `login/bloc/login_state.dart`는 다음과 같습니다:

```dart
import 'package:meta/meta.dart';

@immutable
class LoginState {
  final bool isEmailValid;
  final bool isPasswordValid;
  final bool isSubmitting;
  final bool isSuccess;
  final bool isFailure;

  bool get isFormValid => isEmailValid && isPasswordValid;

  LoginState({
    @required this.isEmailValid,
    @required this.isPasswordValid,
    @required this.isSubmitting,
    @required this.isSuccess,
    @required this.isFailure,
  });

  factory LoginState.empty() {
    return LoginState(
      isEmailValid: true,
      isPasswordValid: true,
      isSubmitting: false,
      isSuccess: false,
      isFailure: false,
    );
  }

  factory LoginState.loading() {
    return LoginState(
      isEmailValid: true,
      isPasswordValid: true,
      isSubmitting: true,
      isSuccess: false,
      isFailure: false,
    );
  }

  factory LoginState.failure() {
    return LoginState(
      isEmailValid: true,
      isPasswordValid: true,
      isSubmitting: false,
      isSuccess: false,
      isFailure: true,
    );
  }

  factory LoginState.success() {
    return LoginState(
      isEmailValid: true,
      isPasswordValid: true,
      isSubmitting: false,
      isSuccess: true,
      isFailure: false,
    );
  }

  LoginState update({
    bool isEmailValid,
    bool isPasswordValid,
  }) {
    return copyWith(
      isEmailValid: isEmailValid,
      isPasswordValid: isPasswordValid,
      isSubmitting: false,
      isSuccess: false,
      isFailure: false,
    );
  }

  LoginState copyWith({
    bool isEmailValid,
    bool isPasswordValid,
    bool isSubmitEnabled,
    bool isSubmitting,
    bool isSuccess,
    bool isFailure,
  }) {
    return LoginState(
      isEmailValid: isEmailValid ?? this.isEmailValid,
      isPasswordValid: isPasswordValid ?? this.isPasswordValid,
      isSubmitting: isSubmitting ?? this.isSubmitting,
      isSuccess: isSuccess ?? this.isSuccess,
      isFailure: isFailure ?? this.isFailure,
    );
  }

  @override
  String toString() {
    return '''LoginState {
      isEmailValid: $isEmailValid,
      isPasswordValid: $isPasswordValid,
      isSubmitting: $isSubmitting,
      isSuccess: $isSuccess,
      isFailure: $isFailure,
    }''';
  }
}

```

우리가 표현하고자 하는 상태는 다음과 같습니다.

`empty`는 `LoginForm`의 초기 상태입니다.

`loading`은 자격 증명의 유효성을 검증 할 때 `LoginForm`의 상태입니다.

`failed`는 로그인 시도가 실패했을 때 `LoginForm`의 상태입니다.

`success`은 로그인 시도가 성공했을 때 `LoginForm`의 상태입니다.

편의(convenience)를 위해 `copyWith`와 `update` 함수를 정의했습니다 (곧 사용할 것입니다).

이제 `LoginState`가 정의되었으므로 `LoginEvent` 클래스를 살펴 보겠습니다.

<p id = "loginevent"/>

## LoginEvent

`login/bloc/login_event.dart`를 열고 우리의 이벤트를 정의하고 구현하겠습니다.

```dart
import 'package:meta/meta.dart';
import 'package:equatable/equatable.dart';

@immutable
abstract class LoginEvent extends Equatable {
  LoginEvent([List props = const []]) : super(props);
}

class EmailChanged extends LoginEvent {
  final String email;

  EmailChanged({@required this.email}) : super([email]);

  @override
  String toString() => 'EmailChanged { email :$email }';
}

class PasswordChanged extends LoginEvent {
  final String password;

  PasswordChanged({@required this.password}) : super([password]);

  @override
  String toString() => 'PasswordChanged { password: $password }';
}

class Submitted extends LoginEvent {
  final String email;
  final String password;

  Submitted({@required this.email, @required this.password})
      : super([email, password]);

  @override
  String toString() {
    return 'Submitted { email: $email, password: $password }';
  }
}

class LoginWithGooglePressed extends LoginEvent {
  @override
  String toString() => 'LoginWithGooglePressed';
}

class LoginWithCredentialsPressed extends LoginEvent {
  final String email;
  final String password;

  LoginWithCredentialsPressed({@required this.email, @required this.password})
      : super([email, password]);

  @override
  String toString() {
    return 'LoginWithCredentialsPressed { email: $email, password: $password }';
  }
}

```

The events we defined are:

`EmailChanged` - notifies the bloc that the user has changed the email

`PasswordChanged` - notifies the bloc that the user has changed the password

`Submitted` - notifies the bloc that the user has submitted the form

`LoginWithGooglePressed` - notifies the bloc that the user has pressed the Google Sign In button

`LoginWithCredentialsPressed` - notifies the bloc that the user has pressed the regular sign in button.

우리가 정의한 이벤트는 다음과 같습니다.

`EmailChanged` - 사용자가 이메일을 변경했다는 것을 Bloc에 알림

`PasswordChanged` - 사용자가 암호를 변경했다는 것을 Bloc에 알림

`Submitted` - 사용자가 양식(form)을 제출했다는 것을 Bloc에 알림

`LoginWithGooglePressed` - 사용자가 Google Sign In 버튼을 눌렀다는 것을 Bloc에 알림

`LoginWithCredentialsPressed` - 사용자가 regular sign in 버튼을 눌렀다는 것을 Bloc에 알림

<p id = "login-barrel-file"/>

## Login Barrel File

`LoginBloc`을 구현하기 전에, single import로 모든 `LoginBloc` 관련 파일을 쉽게 가져올 수 있도록 우리의 배럴 파일이 완료되었는지 확인합니다.

```dart
export 'login_bloc.dart';
export 'login_event.dart';
export 'login_state.dart';
```

<p id = "loginbloc"/>

## LoginBloc

이제 `LoginBloc`을 구현할 차례입니다. 항상 그렇듯이 `Bloc`을 확장하고 `mapState`뿐만 아니라`initialState`를 정의해야 합니다.

```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'package:meta/meta.dart';
import 'package:rxdart/rxdart.dart';
import 'package:flutter_firebase_login/login/login.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/validators.dart';

class LoginBloc extends Bloc<LoginEvent, LoginState> {
  UserRepository _userRepository;

  LoginBloc({
    @required UserRepository userRepository,
  })  : assert(userRepository != null),
        _userRepository = userRepository;

  @override
  LoginState get initialState => LoginState.empty();

  @override
  Stream<LoginState> transform(
    Stream<LoginEvent> events,
    Stream<LoginState> Function(LoginEvent event) next,
  ) {
    final observableStream = events as Observable<LoginEvent>;
    final nonDebounceStream = observableStream.where((event) {
      return (event is! EmailChanged && event is! PasswordChanged);
    });
    final debounceStream = observableStream.where((event) {
      return (event is EmailChanged || event is PasswordChanged);
    }).debounceTime(Duration(milliseconds: 300));
    return super.transform(nonDebounceStream.mergeWith([debounceStream]), next);
  }

  @override
  Stream<LoginState> mapEventToState(LoginEvent event) async* {
    if (event is EmailChanged) {
      yield* _mapEmailChangedToState(event.email);
    } else if (event is PasswordChanged) {
      yield* _mapPasswordChangedToState(event.password);
    } else if (event is LoginWithGooglePressed) {
      yield* _mapLoginWithGooglePressedToState();
    } else if (event is LoginWithCredentialsPressed) {
      yield* _mapLoginWithCredentialsPressedToState(
        email: event.email,
        password: event.password,
      );
    }
  }

  Stream<LoginState> _mapEmailChangedToState(String email) async* {
    yield currentState.update(
      isEmailValid: Validators.isValidEmail(email),
    );
  }

  Stream<LoginState> _mapPasswordChangedToState(String password) async* {
    yield currentState.update(
      isPasswordValid: Validators.isValidPassword(password),
    );
  }

  Stream<LoginState> _mapLoginWithGooglePressedToState() async* {
    try {
      await _userRepository.signInWithGoogle();
      yield LoginState.success();
    } catch (_) {
      yield LoginState.failure();
    }
  }

  Stream<LoginState> _mapLoginWithCredentialsPressedToState({
    String email,
    String password,
  }) async* {
    yield LoginState.loading();
    try {
      await _userRepository.signInWithCredentials(email, password);
      yield LoginState.success();
    } catch (_) {
      yield LoginState.failure();
    }
  }
}
```

**Note:** `EmailChanged` 이벤트와 `PasswordChanged` 이벤트를 디버깅하기 위해 `transform`을 재정의하여 입력을 검증하기 전에 타이핑을 중단 할 시간을 줍니다.

우리는 다음에 구현할 전자 메일과 암호의 유효성을 검사하는 `Validators` 클래스를 사용하고 있습니다.

<p id = "validators"/>

## Validators

`validators.dart` 파일을 만들고 이메일과 패스워드 검증을 구현하겠습니다.

```dart
class Validators {
  static final RegExp _emailRegExp = RegExp(
    r'^[a-zA-Z0-9.!#$%&’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$',
  );
  static final RegExp _passwordRegExp = RegExp(
    r'^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$',
  );

  static isValidEmail(String email) {
    return _emailRegExp.hasMatch(email);
  }

  static isValidPassword(String password) {
    return _passwordRegExp.hasMatch(password);
  }
}
```

여기에는 특별한 일이 없습니다. 정규 표현식을 사용하여 전자 메일과 암호의 유효성을 검사하는 평범한 Dart 코드입니다. 이 시점에서 우리는 UI에 연결할 수 있는 모든 기능을 갖춘 `LoginBloc`을 가지고 있어야 합니다.

<p id = "loginscreen"/>

## LoginScreen

이제 `LoginBloc`을 마쳤으니 `LoginScreen` 위젯을 만들어야 합니다. 이 위젯은 `LoginBloc`을 만들고 처리하며 `LoginForm` 위젯을 위한 `Scaffold`를 제공 할 책임이 있습니다.

`login/login_screen.dart` 파일을 만들고 그것을 구현 하겠습니다.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/login/login.dart';

class LoginScreen extends StatefulWidget {
  final UserRepository _userRepository;

  LoginScreen({Key key, @required UserRepository userRepository})
      : assert(userRepository != null),
        _userRepository = userRepository,
        super(key: key);

  State<LoginScreen> createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  LoginBloc _loginBloc;

  UserRepository get _userRepository => widget._userRepository;

  @override
  void initState() {
    super.initState();
    _loginBloc = LoginBloc(
      userRepository: _userRepository,
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Login')),
      body: BlocProvider<LoginBloc>(
        bloc: _loginBloc,
        child: LoginForm(userRepository: _userRepository),
      ),
    );
  }

  @override
  void dispose() {
    _loginBloc.dispose();
    super.dispose();
  }
}
```

다시, `StatefulWidget`을 확장하여 `initState`에서 `LoginBloc`을 초기화하고 `dispose` 재정의 할 수 있습니다. 서브트리 내의 모든 위젯이 `_loginBloc` 인스턴스를 사용할 수 있도록 하기 위해 다시 `BlocProvider`를 사용하고 있음을 알 수 있습니다.

이 시점에서 우리는 사용자가 자신을 인증 할 수 있도록 양식(form)과 제출(submission) 버튼을 표시 할 책임이있는 `LoginForm` 위젯을 구현해야 합니다.

<p id = "loginform"/>

## LoginForm

`login/login_form.dart` 파일을 만들고 우리의 양식을 완성 해 보겠습니다.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';
import 'package:flutter_firebase_login/login/login.dart';

class LoginForm extends StatefulWidget {
  final UserRepository _userRepository;

  LoginForm({Key key, @required UserRepository userRepository})
      : assert(userRepository != null),
        _userRepository = userRepository,
        super(key: key);

  State<LoginForm> createState() => _LoginFormState();
}

class _LoginFormState extends State<LoginForm> {
  final TextEditingController _emailController = TextEditingController();
  final TextEditingController _passwordController = TextEditingController();

  LoginBloc _loginBloc;

  UserRepository get _userRepository => widget._userRepository;

  bool get isPopulated =>
      _emailController.text.isNotEmpty && _passwordController.text.isNotEmpty;

  bool isLoginButtonEnabled(LoginState state) {
    return state.isFormValid && isPopulated && !state.isSubmitting;
  }

  @override
  void initState() {
    super.initState();
    _loginBloc = BlocProvider.of<LoginBloc>(context);
    _emailController.addListener(_onEmailChanged);
    _passwordController.addListener(_onPasswordChanged);
  }

  @override
  Widget build(BuildContext context) {
    return BlocListener(
      bloc: _loginBloc,
      listener: (BuildContext context, LoginState state) {
        if (state.isFailure) {
          Scaffold.of(context)
            ..hideCurrentSnackBar()
            ..showSnackBar(
              SnackBar(
                content: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [Text('Login Failure'), Icon(Icons.error)],
                ),
                backgroundColor: Colors.red,
              ),
            );
        }
        if (state.isSubmitting) {
          Scaffold.of(context)
            ..hideCurrentSnackBar()
            ..showSnackBar(
              SnackBar(
                content: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    Text('Logging In...'),
                    CircularProgressIndicator(),
                  ],
                ),
              ),
            );
        }
        if (state.isSuccess) {
          BlocProvider.of<AuthenticationBloc>(context).dispatch(LoggedIn());
        }
      },
      child: BlocBuilder(
        bloc: _loginBloc,
        builder: (BuildContext context, LoginState state) {
          return Padding(
            padding: EdgeInsets.all(20.0),
            child: Form(
              child: ListView(
                children: <Widget>[
                  Padding(
                    padding: EdgeInsets.symmetric(vertical: 20),
                    child: Image.asset('assets/flutter_logo.png', height: 200),
                  ),
                  TextFormField(
                    controller: _emailController,
                    decoration: InputDecoration(
                      icon: Icon(Icons.email),
                      labelText: 'Email',
                    ),
                    autovalidate: true,
                    autocorrect: false,
                    validator: (_) {
                      return !state.isEmailValid ? 'Invalid Email' : null;
                    },
                  ),
                  TextFormField(
                    controller: _passwordController,
                    decoration: InputDecoration(
                      icon: Icon(Icons.lock),
                      labelText: 'Password',
                    ),
                    obscureText: true,
                    autovalidate: true,
                    autocorrect: false,
                    validator: (_) {
                      return !state.isPasswordValid ? 'Invalid Password' : null;
                    },
                  ),
                  Padding(
                    padding: EdgeInsets.symmetric(vertical: 20),
                    child: Column(
                      crossAxisAlignment: CrossAxisAlignment.stretch,
                      children: <Widget>[
                        LoginButton(
                          onPressed: isLoginButtonEnabled(state)
                              ? _onFormSubmitted
                              : null,
                        ),
                        GoogleLoginButton(),
                        CreateAccountButton(userRepository: _userRepository),
                      ],
                    ),
                  ),
                ],
              ),
            ),
          );
        },
      ),
    );
  }

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  void _onEmailChanged() {
    _loginBloc.dispatch(
      EmailChanged(email: _emailController.text),
    );
  }

  void _onPasswordChanged() {
    _loginBloc.dispatch(
      PasswordChanged(password: _passwordController.text),
    );
  }

  void _onFormSubmitted() {
    _loginBloc.dispatch(
      LoginWithCredentialsPressed(
        email: _emailController.text,
        password: _passwordController.text,
      ),
    );
  }
}
```

우리의 `LoginForm` 위젯은 이메일과 패스워드 입력을 위한 `TextEditingControllers`를 유지 해야 하기 때문에 `StatefulWidget`입니다.

우리는 상태 변경에 대한 응답으로 일회성 액션(one-time actions)을 실행하기 위해 `BlocListener` 위젯을 사용합니다. 이 경우 pending/failure 상태에 대한 응답으로 다른 `SnackBar` 위젯을 보여 줍니다. 또한 제출(submission)이 성공하면 `listener` 메소드를 사용하여 사용자가 성공적으로 로그인했음을 `AuthenticationBloc`에 알립니다.

**Tip:** 자세한 내용은 [`BlocListener` Recipe] (https://felangel.github.io/bloc/#/recipesbloclistener)를 확인하십시오.

다른 `LoginStates`에 대한 응답으로 UI를 다시 빌드하기 위해 우리는 `BlocBuilder` 위젯을 사용합니다.

전자 메일이나 암호가 변경 될 때마다 현재 폼 상태의 유효성을 검사하고 새 폼 상태를 반환하기 위해 이벤트를 `LoginBloc`에 전달합니다.

**Note:** 우리는 `assets` 디렉토리에서 Flutter 로고를 로드하기 위해 `Image.asset`을 사용하고 있습니다.

이 시점에서 우리는 `LoginButton`,` GoogleLoginButton` 또는 `CreateAccountButton`을 구현하지 않았 음을 알 수 있습니다.

<p id = "loginbutton"/>

## LoginButton

`login/login_button.dart` 파일을 만들고 `LoginButton` 위젯을 빨리 구현해 봅시다.

```dart
import 'package:flutter/material.dart';

class LoginButton extends StatelessWidget {
  final VoidCallback _onPressed;

  LoginButton({Key key, VoidCallback onPressed})
      : _onPressed = onPressed,
        super(key: key);

  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(30.0),
      ),
      onPressed: _onPressed,
      child: Text('Login'),
    );
  }
}
```

여기에는 특별한 일이 없습니다. 버튼을 누를 때마다 커스텀 `VoidCallback`을 가질 수 있도록`stylelessWidget`과 `onPressed` 콜백이 있습니다.

<p id = "google-login-button"/>

## Google Login Button

`login/google_login_button.dart` 파일을 만들고 Google Sign In에 대한 작업을 시작하십시오.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/login/login.dart';
import 'package:font_awesome_flutter/font_awesome_flutter.dart';

class GoogleLoginButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RaisedButton.icon(
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(30.0),
      ),
      icon: Icon(FontAwesomeIcons.google, color: Colors.white),
      onPressed: () {
        BlocProvider.of<LoginBloc>(context).dispatch(
          LoginWithGooglePressed(),
        );
      },
      label: Text('Sign in with Google', style: TextStyle(color: Colors.white)),
      color: Colors.redAccent,
    );
  }
}
```

Again, there's not too much going on here. We have another `StatelessWidget`; however, this time we are not exposing an `onPressed` callback. Instead, we're handling the onPressed internally and dispatching the `LoginWithGooglePressed` event to our `LoginBloc` which will handle the Google Sign In process.

**Note:** We're using [font_awesome_flutter](https://pub.dartlang.org/packages/font_awesome_flutter) for the cool google icon.

다시 말하지만, 여기서 너무 많이 진행되지 않습니다. 또 다른 `StatelessWidget`이 있다. 그러나 이번에는 `onPressed` 콜백을 노출하지 않을 것입니다. 대신 내부적으로 `onPressed`를 처리하고 Google Sign In  프로세스를 처리 할 `LoginBloc`에 `LoginWithGooglePressed` 이벤트를 보냅니다.

**Note:** 우리는  [font_awesome_flutter](https://pub.dartlang.org/packages/font_awesome_flutter)에 있는 멋진 Google 아이콘을 사용하고 있습니다.

<p id = "createaccountbutton"/>

## CreateAccountButton

마지막 세 버튼은 `CreateAccountButton`입니다. `login/create_account_button.dart` 파일을 만들고 작업 해 보겠습니다.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/register/register.dart';

class CreateAccountButton extends StatelessWidget {
  final UserRepository _userRepository;

  CreateAccountButton({Key key, @required UserRepository userRepository})
      : assert(userRepository != null),
        _userRepository = userRepository,
        super(key: key);

  @override
  Widget build(BuildContext context) {
    return FlatButton(
      child: Text(
        'Create an Account',
      ),
      onPressed: () {
        Navigator.of(context).push(
          MaterialPageRoute(builder: (context) {
            return RegisterScreen(userRepository: _userRepository);
          }),
        );
      },
    );
  }
}
```

In this case, again we have a `StatelessWidget` and again we're handling the `onPressed` callback internally. This time, however, we're pushing a new route in response to the button press to the `RegisterScreen`. Let's build that next!

이 경우, 다시 우리는 `StatelessWidget`을 가지며 `onPressed` 콜백을 내부적으로 처리합니다. 그러나 이번에는 `RegisterScreen`에 대한 버튼 누름에 대한 응답으로 새로운 루트를 push하고 있습니다. 이제 작업을 하겠습니다. (이상함. 뒷부분 읽고 재검토)

<p id = "registerstate"/>

## RegisterState

로그인과 마찬가지로 진행하기 전에 `RegisterStates`를 정의해야 합니다.

`register` 디렉토리를 만들고 표준 bloc 디렉토리와 파일을 만듭니다.

```sh
├── lib
│   ├── register
│   │   ├── bloc
│   │   │   ├── bloc.dart
│   │   │   ├── register_bloc.dart
│   │   │   ├── register_event.dart
│   │   │   └── register_state.dart
```

우리의 `register/bloc/register_state.dart`는 다음과 같습니다 :

```dart
import 'package:meta/meta.dart';

@immutable
class RegisterState {
  final bool isEmailValid;
  final bool isPasswordValid;
  final bool isSubmitting;
  final bool isSuccess;
  final bool isFailure;

  bool get isFormValid => isEmailValid && isPasswordValid;

  RegisterState({
    @required this.isEmailValid,
    @required this.isPasswordValid,
    @required this.isSubmitting,
    @required this.isSuccess,
    @required this.isFailure,
  });

  factory RegisterState.empty() {
    return RegisterState(
      isEmailValid: true,
      isPasswordValid: true,
      isSubmitting: false,
      isSuccess: false,
      isFailure: false,
    );
  }

  factory RegisterState.loading() {
    return RegisterState(
      isEmailValid: true,
      isPasswordValid: true,
      isSubmitting: true,
      isSuccess: false,
      isFailure: false,
    );
  }

  factory RegisterState.failure() {
    return RegisterState(
      isEmailValid: true,
      isPasswordValid: true,
      isSubmitting: false,
      isSuccess: false,
      isFailure: true,
    );
  }

  factory RegisterState.success() {
    return RegisterState(
      isEmailValid: true,
      isPasswordValid: true,
      isSubmitting: false,
      isSuccess: true,
      isFailure: false,
    );
  }

  RegisterState update({
    bool isEmailValid,
    bool isPasswordValid,
  }) {
    return copyWith(
      isEmailValid: isEmailValid,
      isPasswordValid: isPasswordValid,
      isSubmitting: false,
      isSuccess: false,
      isFailure: false,
    );
  }

  RegisterState copyWith({
    bool isEmailValid,
    bool isPasswordValid,
    bool isSubmitEnabled,
    bool isSubmitting,
    bool isSuccess,
    bool isFailure,
  }) {
    return RegisterState(
      isEmailValid: isEmailValid ?? this.isEmailValid,
      isPasswordValid: isPasswordValid ?? this.isPasswordValid,
      isSubmitting: isSubmitting ?? this.isSubmitting,
      isSuccess: isSuccess ?? this.isSuccess,
      isFailure: isFailure ?? this.isFailure,
    );
  }

  @override
  String toString() {
    return '''RegisterState {
      isEmailValid: $isEmailValid,
      isPasswordValid: $isPasswordValid,
      isSubmitting: $isSubmitting,
      isSuccess: $isSuccess,
      isFailure: $isFailure,
    }''';
  }
}
```

**Note:** `RegisterState`는 `LoginState`와 매우 유사합니다. 그리고 우리는 하나의 상태를 만들어 두 상태 사이에서 공유 할 수 있었습니다; 그러나 로그인 및 등록 기능(features)이 서로 겹칠 가능성이 매우 높으며 대부분의 경우 해당 기능을 분리하는 것이 가장 좋습니다.

다음으로, 우리는`RegisterEvent` 클래스로 넘어갈 것입니다.

<p id = "registerevent"/>

## RegisterEvent

`register/bloc/register_event.dart`를 열고 우리의 이벤트를 구현 하겠습니다.

```dart
import 'package:equatable/equatable.dart';
import 'package:meta/meta.dart';

@immutable
abstract class RegisterEvent extends Equatable {
  RegisterEvent([List props = const []]) : super(props);
}

class EmailChanged extends RegisterEvent {
  final String email;

  EmailChanged({@required this.email}) : super([email]);

  @override
  String toString() => 'EmailChanged { email :$email }';
}

class PasswordChanged extends RegisterEvent {
  final String password;

  PasswordChanged({@required this.password}) : super([password]);

  @override
  String toString() => 'PasswordChanged { password: $password }';
}

class Submitted extends RegisterEvent {
  final String email;
  final String password;

  Submitted({@required this.email, @required this.password})
      : super([email, password]);

  @override
  String toString() {
    return 'Submitted { email: $email, password: $password }';
  }
}
```

**Note:**  다시, `RegisterEvent` 구현은 `LoginEvent` 구현과 매우 비슷하게 보입니다. 그러나 이 두 가지는 별개의 기능이기 때문에 이 예제에서 독립적입니다.

<p id = "register-barrel-file"/>

## Register Barrel File

다시 말하지만, 로그인과 마찬가지로 register bloc 관련 파일을 내보내는 배럴 파일을 만들어야합니다.

우리의`register/bloc` 디렉토리에서 `bloc.dart`를 열고 세 파일을 export 하세요.

```dart
export 'register_bloc.dart';
export 'register_event.dart';
export 'register_state.dart';
```

<p id = "registerbloc"/>

## RegisterBloc

이제, `register/bloc/register_bloc.dart`를 열고 `RegisterBloc`을 구현 하겠습니다.

```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'package:meta/meta.dart';
import 'package:rxdart/rxdart.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/register/register.dart';
import 'package:flutter_firebase_login/validators.dart';

class RegisterBloc extends Bloc<RegisterEvent, RegisterState> {
  final UserRepository _userRepository;

  RegisterBloc({@required UserRepository userRepository})
      : assert(userRepository != null),
        _userRepository = userRepository;

  @override
  RegisterState get initialState => RegisterState.empty();

  @override
  Stream<RegisterState> transform(
    Stream<RegisterEvent> events,
    Stream<RegisterState> Function(RegisterEvent event) next,
  ) {
    final observableStream = events as Observable<RegisterEvent>;
    final nonDebounceStream = observableStream.where((event) {
      return (event is! EmailChanged && event is! PasswordChanged);
    });
    final debounceStream = observableStream.where((event) {
      return (event is EmailChanged || event is PasswordChanged);
    }).debounceTime(Duration(milliseconds: 300));
    return super.transform(nonDebounceStream.mergeWith([debounceStream]), next);
  }

  @override
  Stream<RegisterState> mapEventToState(
    RegisterEvent event,
  ) async* {
    if (event is EmailChanged) {
      yield* _mapEmailChangedToState(event.email);
    } else if (event is PasswordChanged) {
      yield* _mapPasswordChangedToState(event.password);
    } else if (event is Submitted) {
      yield* _mapFormSubmittedToState(event.email, event.password);
    }
  }

  Stream<RegisterState> _mapEmailChangedToState(String email) async* {
    yield currentState.update(
      isEmailValid: Validators.isValidEmail(email),
    );
  }

  Stream<RegisterState> _mapPasswordChangedToState(String password) async* {
    yield currentState.update(
      isPasswordValid: Validators.isValidPassword(password),
    );
  }

  Stream<RegisterState> _mapFormSubmittedToState(
    String email,
    String password,
  ) async* {
    yield RegisterState.loading();
    try {
      await _userRepository.signUp(
        email: email,
        password: password,
      );
      yield RegisterState.success();
    } catch (_) {
      yield RegisterState.failure();
    }
  }
}
```

이전처럼 `Bloc`을 확장하고, `initialState`와 `mapEventToState`를 구현해야 합니다. 선택적으로, 우리는 양식을 검증하기 전에 타이핑을 마칠 수 있는 시간을 주기 위해 `transform`을 다시 재정의 합니다.

이제는 `RegisterBloc`이 완전히 기능했기 때문에 프리젠테이션 레이어를 구축하면됩니다.

<p id = "registerscreen"/>

## RegisterScreen

`LoginScreen`과 비슷하게, 우리의 `RegisterScreen`은 `RegisterBloc`을 초기화하고 처리하는`StatefulWidget` 일 것입니다. 그것은 `RegisterForm`을 위한 `Scaffold`를 제공 할 것입니다.

`register/register_screen.dart`를 만들고 그것을 구현하겠습니다.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/register/register.dart';

class RegisterScreen extends StatefulWidget {
  final UserRepository _userRepository;

  RegisterScreen({Key key, @required UserRepository userRepository})
      : assert(userRepository != null),
        _userRepository = userRepository,
        super(key: key);

  State<RegisterScreen> createState() => _RegisterScreenState();
}

class _RegisterScreenState extends State<RegisterScreen> {
  RegisterBloc _registerBloc;

  @override
  void initState() {
    super.initState();
    _registerBloc = RegisterBloc(
      userRepository: widget._userRepository,
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Register')),
      body: Center(
        child: BlocProvider<RegisterBloc>(
          bloc: _registerBloc,
          child: RegisterForm(),
        ),
      ),
    );
  }

  @override
  void dispose() {
    _registerBloc.dispose();
    super.dispose();
  }
}
```

<p id = "registerform"/>

## RegisterFrom

다음으로 사용자가 자신의 계정을 만들 수 있는 양식 필드를 제공하는 `RegisterForm`을 만듭니다.

`register/register_form.dart` 파일을 만들고 그것을 빌드합시다.

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';
import 'package:flutter_firebase_login/register/register.dart';

class RegisterForm extends StatefulWidget {
  State<RegisterForm> createState() => _RegisterFormState();
}

class _RegisterFormState extends State<RegisterForm> {
  final TextEditingController _emailController = TextEditingController();
  final TextEditingController _passwordController = TextEditingController();

  RegisterBloc _registerBloc;

  bool get isPopulated =>
      _emailController.text.isNotEmpty && _passwordController.text.isNotEmpty;

  bool isRegisterButtonEnabled(RegisterState state) {
    return state.isFormValid && isPopulated && !state.isSubmitting;
  }

  @override
  void initState() {
    super.initState();
    _registerBloc = BlocProvider.of<RegisterBloc>(context);
    _emailController.addListener(_onEmailChanged);
    _passwordController.addListener(_onPasswordChanged);
  }

  @override
  Widget build(BuildContext context) {
    return BlocListener(
      bloc: _registerBloc,
      listener: (BuildContext context, RegisterState state) {
        if (state.isSubmitting) {
          Scaffold.of(context)
            ..hideCurrentSnackBar()
            ..showSnackBar(
              SnackBar(
                content: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    Text('Registering...'),
                    CircularProgressIndicator(),
                  ],
                ),
              ),
            );
        }
        if (state.isSuccess) {
          BlocProvider.of<AuthenticationBloc>(context).dispatch(LoggedIn());
          Navigator.of(context).pop();
        }
        if (state.isFailure) {
          Scaffold.of(context)
            ..hideCurrentSnackBar()
            ..showSnackBar(
              SnackBar(
                content: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    Text('Registration Failure'),
                    Icon(Icons.error),
                  ],
                ),
                backgroundColor: Colors.red,
              ),
            );
        }
      },
      child: BlocBuilder(
        bloc: _registerBloc,
        builder: (BuildContext context, RegisterState state) {
          return Padding(
            padding: EdgeInsets.all(20),
            child: Form(
              child: ListView(
                children: <Widget>[
                  TextFormField(
                    controller: _emailController,
                    decoration: InputDecoration(
                      icon: Icon(Icons.email),
                      labelText: 'Email',
                    ),
                    autocorrect: false,
                    autovalidate: true,
                    validator: (_) {
                      return !state.isEmailValid ? 'Invalid Email' : null;
                    },
                  ),
                  TextFormField(
                    controller: _passwordController,
                    decoration: InputDecoration(
                      icon: Icon(Icons.lock),
                      labelText: 'Password',
                    ),
                    obscureText: true,
                    autocorrect: false,
                    autovalidate: true,
                    validator: (_) {
                      return !state.isPasswordValid ? 'Invalid Password' : null;
                    },
                  ),
                  RegisterButton(
                    onPressed: isRegisterButtonEnabled(state)
                        ? _onFormSubmitted
                        : null,
                  ),
                ],
              ),
            ),
          );
        },
      ),
    );
  }

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  void _onEmailChanged() {
    _registerBloc.dispatch(
      EmailChanged(email: _emailController.text),
    );
  }

  void _onPasswordChanged() {
    _registerBloc.dispatch(
      PasswordChanged(password: _passwordController.text),
    );
  }

  void _onFormSubmitted() {
    _registerBloc.dispatch(
      Submitted(
        email: _emailController.text,
        password: _passwordController.text,
      ),
    );
  }
}
```

다시 말하지만, `RegisterForm`이 `StatefulWidget` 일 필요가 있도록 텍스트 입력을 위해`TextEditingControllerers`를 관리해야합니다. 또한 우리는 등록이 보류 중이거나 실패 할 때 `SnackBar`를 보여주는 것과 같은 상태 변경에 응답하여 일회성 액션을 실행하기 위해 다시 `BlocListener`를 사용합니다. 우리가 즉시 사용자를 로그인 할 수 있도록 등록이 성공했다면 `LoggedIn` 이벤트를 `AuthenticationBloc`에 보냅니다.

**Note:** 우리는 UI가 `RegisterBloc` 상태의 변화에 반응하도록 하기 위해 `BlocBuilder`를 사용하고 있습니다.

다음에 우리의`RegisterButton` 위젯을 구현해 보겠습니다.

<p id = "registerbutton"/>

## RegisterButton

`register/register_button.dart` 파일을 만들고 시작합니다.

```dart
import 'package:flutter/material.dart';

class RegisterButton extends StatelessWidget {
  final VoidCallback _onPressed;

  RegisterButton({Key key, VoidCallback onPressed})
      : _onPressed = onPressed,
        super(key: key);

  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(30.0),
      ),
      onPressed: _onPressed,
      child: Text('Register'),
    );
  }
}
```

`LoginButton`을 설정하는 것과 매우 유사하게, `RegisterButton`은 사용자 정의 스타일을 가지고 있고 사용자가 부모 위젯의 버튼을 누를 때마다 처리 할 수 있도록 `VoidCallback`을 노출합니다.

`AuthenticationState`가 `Unauthenticated` 인 경우 `LoginScreen`을 보여주기 위해 `main.dart`에서 `App` 위젯을 업데이트하는 것만 남았습니다.

```dart
import 'package:flutter/material.dart';
import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_firebase_login/authentication_bloc/bloc.dart';
import 'package:flutter_firebase_login/user_repository.dart';
import 'package:flutter_firebase_login/home_screen.dart';
import 'package:flutter_firebase_login/login/login.dart';
import 'package:flutter_firebase_login/splash_screen.dart';
import 'package:flutter_firebase_login/simple_bloc_delegate.dart';

main() {
  BlocSupervisor().delegate = SimpleBlocDelegate();
  runApp(App());
}

class App extends StatefulWidget {
  State<App> createState() => _AppState();
}

class _AppState extends State<App> {
  final UserRepository _userRepository = UserRepository();
  AuthenticationBloc _authenticationBloc;

  @override
  void initState() {
    super.initState();
    _authenticationBloc = AuthenticationBloc(userRepository: _userRepository);
    _authenticationBloc.dispatch(AppStarted());
  }

  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      bloc: _authenticationBloc,
      child: MaterialApp(
        home: BlocBuilder(
          bloc: _authenticationBloc,
          builder: (BuildContext context, AuthenticationState state) {
            if (state is Uninitialized) {
              return SplashScreen();
            }
            if (state is Unauthenticated) {
              return LoginScreen(userRepository: _userRepository);
            }
            if (state is Authenticated) {
              return HomeScreen(name: state.displayName);
            }
          },
        ),
      ),
    );
  }

  @override
  void dispose() {
    _authenticationBloc.dispose();
    super.dispose();
  }
}
```

이 시점에서 Firebase를 사용하여 매우 견고한 로그인 구현이 이루어졌으며 Bloc 라이브러리를 사용하여 비즈니스 로직 레이어에서 프레젠테이션 계층을 분리했습니다.

이 예제의 전체 소스는 [여기](https://github.com/felangel/Bloc/tree/master/examples/flutter_firebase_login)에서 찾을 수 있습니다.

---

이전:

다음:
