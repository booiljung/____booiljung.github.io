[Up](./index.md)

# Flutter Login Tutorial

원문: [Bloc / Tutorial / Login](https://felangel.github.io/bloc/#/flutterlogintutorial)

![intermediate](tutorials_flutter_login.assets/level-intermediate-orange.svg)

> 이 튜토리얼에서는, Flutter에서 Bloc 라이브러리를 사용하여 Login Flow를 빌드 하겠습니다.

![demo](tutorials_flutter_login.assets/flutter_login.gif)

<p id = "setup"/>

## 준비 (Setup)

새 브랜드의 프로젝트를 생성하며 시작하겠습니다.

```bash
flutter create flutter_login
```

`pubspec.yaml`의 내용을 변경하여 계속 진행합니다.

```yaml
name: flutter_login
description: A new Flutter project.
version: 1.0.0+1

environment:
  sdk: ">=2.0.0-dev.68.0 <3.0.0"

dependencies:
  flutter:
    sdk: flutter
  flutter_bloc: ^0.13.0
  meta: ^1.1.6
  equatable: ^0.2.0

dev_dependencies:
  flutter_test:
    sdk: flutter

flutter:
  uses-material-design: true
```

그리고, 의존하는 패키지들을 설치합니다.

```bash
flutter packages get
```

<p id="user-repository"/>

## 사용자 저장소 (User Repository)

사용자 정보를 관리하기 위한  `UserRepository`를 생성할 필요가 있습니다.

```dart
class UserRepository {
  Future<String> authenticate({
    @required String username,
    @required String password,
  }) async {
    await Future.delayed(Duration(seconds: 1));
    return 'token';
  }

  Future<void> deleteToken() async {
    /// delete from keystore/keychain
    await Future.delayed(Duration(seconds: 1));
    return;
  }

  Future<void> persistToken(String token) async {
    /// write to keystore/keychain
    await Future.delayed(Duration(seconds: 1));
    return;
  }

  Future<bool> hasToken() async {
    /// read from keystore/keychain
    await Future.delayed(Duration(seconds: 1));
    return false;
  }
}
```

**Note**:  우리의 사용자 저장소는 단순화를 위해 모든 다른 구현을 모사(mock)하고 있지만 실제 응용 프로그램에서는 [`HttpClient`](https://pub.dartlang.org/packages/http)와 [Flutter Secure Store](https://pub.dartlang.org/packages/flutter_secure_storage)를 사용하여 토큰을 요청하고 키스토어/키체인에 읽고 쓸 수 있습니다.

<p id = "authentication-state"/>

## 인증 상태 (Authentication State)

다음으로 우리는 애플리케이션 상태를 어떻게 관리하고 필요한 Bloc (Business Logic Component)을 만들지 결정해야 할 것입니다.

높은 수준에서 우리는 사용자의 인증 상태를 관리해야 할 것입니다. 사용자의 인증 상태는 다음 중 하나 일 수 있습니다.

- uninitialized (초기화 되지 않음) - 사용자가 앱 시작시 인증되는지 여부를 기다리는 중
- loading (로딩 중) - 토큰 보관(유지) 및 삭제 중
- authenticated - 인증 성공
- unauthenticated - 인증 되지 않음

이 각 상태들은 사용자가 보는 것에 대해 의미를 나태내야 할 것입니다.

예를 들어:

예를 들어:

- 인증 상태가 초기화되지 않은 경우 사용자에게 스플래시 화면이 표시 될 수 있습니다.
- 인증 상태가 로드 중이면 사용자에게 진행 상태 표시기(indicator)가 표시 될 수 있습니다.
- 인증 상태가 인증되면 사용자가 홈 화면을 볼 수 있습니다.
- 인증 상태가 인증되지 않은 경우 사용자는 로그인 양식을 볼 수 있습니다.

> 구현에 들어가기 전에 다른 상태들에 무엇인지 파악하는 것이 중요합니다.

인증 상태가 식별되었으므로 이제 우리는 `AuthenticationState` 클래스를 구현할 수 있습니다.

```dart
import 'package:equatable/equatable.dart';

abstract class AuthenticationState extends Equatable {}

class AuthenticationUninitialized extends AuthenticationState {
  @override
  String toString() => 'AuthenticationUninitialized';
}

class AuthenticationAuthenticated extends AuthenticationState {
  @override
  String toString() => 'AuthenticationAuthenticated';
}

class AuthenticationUnauthenticated extends AuthenticationState {
  @override
  String toString() => 'AuthenticationUnauthenticated';
}

class AuthenticationLoading extends AuthenticationState {
  @override
  String toString() => 'AuthenticationLoading';
}
```

**Note**: [`equatable`(https://pub.dartlang.org/packages/equatable) 패키지는 `AuthenticationState`의 두 인스턴스를 비교할 수 있게 하기 위해 사용됩니다. 기본적으로 `==`는 두 객체가 같은 인스턴스인 경우에만 true를 반환합니다.

**Note**: `toString`은 콘솔이나 `Transitions`에 출력 할 때 `AuthenticationState`를 읽기 쉽게 하기 위해 무시됩니다.

<p id = "authenticationevent"/>

## AuthenticationEvent

이제 우리는 우리의 `AuthenticationState`를 정의했습니다. 우리는 `AuthenticationBloc`이 반응 할`AuthenticationEvents`를 정의 할 필요가 있습니다.

우리가 필요한 것은:

- 사용자가 현재 인증되었는지 아닌지를 체크 할 필요가 있다는 것을 Bloc에 알리는 `AppStarted` 이벤트.
- 사용자가 성공적으로 로그인 했음을 블록에 알리는 `LoggedIn` 이벤트.
- 사용자가 성공적으로 로그 아웃했다는 것을 블럭에 알리는 `LoggedOut` 이벤트.

```dart
import 'package:meta/meta.dart';
import 'package:equatable/equatable.dart';

abstract class AuthenticationEvent extends Equatable {
  AuthenticationEvent([List props = const []]) : super(props);
}

class AppStarted extends AuthenticationEvent {
  @override
  String toString() => 'AppStarted';
}

class LoggedIn extends AuthenticationEvent {
  final String token;

  LoggedIn({@required this.token}) : super([token]);

  @override
  String toString() => 'LoggedIn { token: $token }';
}

class LoggedOut extends AuthenticationEvent {
  @override
  String toString() => 'LoggedOut';
}
```

**Note**: `meta` 패키지는 `AuthenticationEvent` 매개 변수에 `@required`로 주석을 달기 위해 사용됩니다. 이렇게 하면 Dart 분석기(analyzer)가 필요한 매개 변수를 제공하지 않으면 개발자에게 경고합니다.

<p id = "authenticationblock"/>

## AuthenticationBloc

이제 우리는 `AuthenticationState`와 `AuthenticationEvents`를 정의 했으므로`AuthenticationEvents`에 대한 응답으로 사용자의 `AuthenticationState`를 검사하고 업데이트하는`AuthenticationBloc`을 구현할 수 있습니다.

우리는 `AuthenticationBloc` 클래스를 작성하는 것으로 시작합니다.

```dart
class AuthenticationBloc extends Bloc<AuthenticationEvent, AuthenticationState> {
  final UserRepository userRepository;

  AuthenticationBloc({@required this.userRepository}): assert(userRepository != null);
}
```

**Note**: 클래스 정의를 읽는 것에서부터, 우리는 이 블록이 `AuthenticationEvents`를 `AuthenticationStates`로 변환 할 것이라는 것을 이미 알고 있습니다.

**Note**: 우리의 `AuthenticationBloc`은 `UserRepository`에 의존합니다.

우리는`initialState`를 `AuthenticationUninitialized()`상태로 재정의하여 시작할 수 있습니다.

```dart
@override
AuthenticationState get initialState => AuthenticationUninitialized();
```

이제 남겨진 것은 `mapEventToState`를 구현하는 것입니다.

```dart
@override
Stream<AuthenticationState> mapEventToState(
  AuthenticationEvent event,
) async* {
  if (event is AppStarted) {
    final bool hasToken = await userRepository.hasToken();

    if (hasToken) {
      yield AuthenticationAuthenticated();
    } else {
      yield AuthenticationUnauthenticated();
    }
  }

  if (event is LoggedIn) {
    yield AuthenticationLoading();
    await userRepository.persistToken(event.token);
    yield AuthenticationAuthenticated();
  }

  if (event is LoggedOut) {
    yield AuthenticationLoading();
    await userRepository.deleteToken();
    yield AuthenticationUnauthenticated();
  }
}
```

훌륭합니다! 우리의 마지막 `AuthenticationBloc`은 다음과 같이 보일 것입니다.

```dart
import 'dart:async';

import 'package:meta/meta.dart';
import 'package:bloc/bloc.dart';
import 'package:user_repository/user_repository.dart';

import 'package:flutter_login/authentication/authentication.dart';

class AuthenticationBloc
    extends Bloc<AuthenticationEvent, AuthenticationState> {
  final UserRepository userRepository;

  AuthenticationBloc({@required this.userRepository})
      : assert(userRepository != null);

  @override
  AuthenticationState get initialState => AuthenticationUninitialized();

  @override
  Stream<AuthenticationState> mapEventToState(
    AuthenticationEvent event,
  ) async* {
    if (event is AppStarted) {
      final bool hasToken = await userRepository.hasToken();

      if (hasToken) {
        yield AuthenticationAuthenticated();
      } else {
        yield AuthenticationUnauthenticated();
      }
    }

    if (event is LoggedIn) {
      yield AuthenticationLoading();
      await userRepository.persistToken(event.token);
      yield AuthenticationAuthenticated();
    }

    if (event is LoggedOut) {
      yield AuthenticationLoading();
      await userRepository.deleteToken();
      yield AuthenticationUnauthenticated();
    }
  }
}
```

이제 우리는 `AuthenticationBloc`을 완전히 구현 했으므로, 이제는 프리젠테이션 레이어를 작업 해 보겠습니다.

<p id="splashpage"/>

## SplashPage

우선 우리가 필요로 하는 것은 `SplashPage` 위젯입니다. 우리의 `AuthenticationBloc`은 사용자가 로그인했는지 여부를 결정하는 동안 우리의 스플래쉬 화면으로 사용됩니다.

```dart
import 'package:flutter/material.dart';

class SplashPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text('Splash Screen'),
      ),
    );
  }
}
```

<p id = "homepage"/>

## HomePage

다음으로, 성공적으로 로그인 한 사용자를 탐색 할 수 있도록 `HomePage`를 생성해야 합니다.

```dart
import 'package:flutter/material.dart';

import 'package:flutter_bloc/flutter_bloc.dart';

import 'package:flutter_login/authentication/authentication.dart';

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final AuthenticationBloc authenticationBloc =
        BlocProvider.of<AuthenticationBloc>(context);

    return Scaffold(
      appBar: AppBar(
        title: Text('Home'),
      ),
      body: Container(
        child: Center(
            child: RaisedButton(
          child: Text('logout'),
          onPressed: () {
            authenticationBloc.dispatch(LoggedOut());
          },
        )),
      ),
    );
  }
}
```

**Note**: 이것은 `flutter_bloc`을 사용하는 첫 번째 클래스입니다. `BlocProvider.of<AuthenticationBloc> (context)`에 곧 들어가 겠지만, 이제는 우리의 `HomePage`가 우리의 `AuthenticationBloc`에 액세스 할 수 있다는 것을 알고 있습니다.

**Note**: 사용자가 로그 아웃 버튼을 눌렀을 때 `AuthenticationBloc`에 `LoggedOut` 이벤트를 보냅니다.

다음으로, 우리는 `Login Page`와 `LoginForm`을 생성 할 필요가 있습니다.

`LoginForm`은 사용자 입력(Login Button Pressed)을 처리해야하고 비즈니스 로직 (주어진 사용자 이름 / 암호에 대한 토큰을 가져와야 함)이 필요하기 때문에, 우리는 `LoginBloc`을 생성해야 합니다.

`AuthenticationBloc`처럼 우리는 `LoginState`와`LoginEvents`를 정의 할 필요가 있습니다. `LoginState`를 시작 하겠습니다.

<p id = "loginstates"/>

## LoginState

```dart
import 'package:meta/meta.dart';
import 'package:equatable/equatable.dart';

abstract class LoginState extends Equatable {
  LoginState([List props = const []]) : super(props);
}

class LoginInitial extends LoginState {
  @override
  String toString() => 'LoginInitial';
}

class LoginLoading extends LoginState {
  @override
  String toString() => 'LoginLoading';
}

class LoginFailure extends LoginState {
  final String error;

  LoginFailure({@required this.error}) : super([error]);

  @override
  String toString() => 'LoginFailure { error: $error }';
}
```

`LoginInitial` 은 `LoginForm`의 초기 상태 입니다.

`LoginLoading`은 자격 증명의 유효성(credentials)을 검사(validating) 할 때 `LoginForm`의 상태입니다.

`LoginFailure`는 로그인 시도가 실패했을 때 `LoginForm`의 상태입니다.

이제`LoginState`가 정의되었으므로 `LoginEvent` 클래스를 살펴 보겠습니다.

<p id="login-event"/>

## LoginEvent

```dart
import 'package:meta/meta.dart';
import 'package:equatable/equatable.dart';

abstract class LoginEvent extends Equatable {
  LoginEvent([List props = const []]) : super(props);
}

class LoginButtonPressed extends LoginEvent {
  final String username;
  final String password;

  LoginButtonPressed({
    @required this.username,
    @required this.password,
  }) : super([username, password]);

  @override
  String toString() =>
      'LoginButtonPressed { username: $username, password: $password }';
}
```

`LoginButtonPressed`는 사용자가 로그인 버튼을 눌렀을 때 전달됩니다. `LoginBloc`에게 주어진 자격증명을위한 토큰을 요청할 필요가 있음을 알릴 것입니다.

이제 우리는 `LoginBloc`을 구현할 수 있습니다.

## LoginBloc

```dart
import 'dart:async';

import 'package:meta/meta.dart';
import 'package:bloc/bloc.dart';
import 'package:user_repository/user_repository.dart';

import 'package:flutter_login/authentication/authentication.dart';
import 'package:flutter_login/login/login.dart';

class LoginBloc extends Bloc<LoginEvent, LoginState> {
  final UserRepository userRepository;
  final AuthenticationBloc authenticationBloc;

  LoginBloc({
    @required this.userRepository,
    @required this.authenticationBloc,
  })  : assert(userRepository != null),
        assert(authenticationBloc != null);

  LoginState get initialState => LoginInitial();

  @override
  Stream<LoginState> mapEventToState(LoginEvent event) async* {
    if (event is LoginButtonPressed) {
      yield LoginLoading();

      try {
        final token = await userRepository.authenticate(
          username: event.username,
          password: event.password,
        );

        authenticationBloc.dispatch(LoggedIn(token: token));
        yield LoginInitial();
      } catch (error) {
        yield LoginFailure(error: error.toString());
      }
    }
  }
}
```

**Note**: `LoginBloc`은 사용자 이름과 패스워드를 가진 사용자를 인증하기 위해 `UserRepository`에 의존합니다.

**Note**: `LoginBloc` has a dependency on `AuthenticationBloc` in order to update the AuthenticationState when a user has entered valid credentials.

`LoginBloc`는 사용자가 유효한 인증서를 입력했을 때 AuthenticationState를 업데이트하기 위해`AuthenticationBloc`에 의존합니다.

이제 우리는 `LoginBloc`을 가지고 `LoginPage`와 `LoginForm`에 대한 작업을 시작할 수 있습니다.

<p id = "loginpage"/>

## LoginPage

`LoginPage` 위젯은 컨테이너 위젯으로 사용되며 `LoginForm` 위젯에 필요한 의존성을 제공합니다 (`LoginBloc`과`AuthenticationBloc`).

```dart
import 'package:flutter/material.dart';

import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:user_repository/user_repository.dart';

import 'package:flutter_login/authentication/authentication.dart';
import 'package:flutter_login/login/login.dart';

class LoginPage extends StatefulWidget {
  final UserRepository userRepository;

  LoginPage({Key key, @required this.userRepository})
      : assert(userRepository != null),
        super(key: key);

  @override
  State<LoginPage> createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  LoginBloc _loginBloc;
  AuthenticationBloc _authenticationBloc;

  UserRepository get _userRepository => widget.userRepository;

  @override
  void initState() {
    _authenticationBloc = BlocProvider.of<AuthenticationBloc>(context);
    _loginBloc = LoginBloc(
      userRepository: _userRepository,
      authenticationBloc: _authenticationBloc,
    );
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Login'),
      ),
      body: LoginForm(
        authenticationBloc: _authenticationBloc,
        loginBloc: _loginBloc,
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

**Note**: `LoginPage`는 `StatefulWidget`입니다.  `LoginPage` 위젯은 상태의 일부로 `LoginBloc`을 생성하고 그것을 처리합니다.

**Note**: 우리는 `LoginBloc`을 만들기 위해 삽입 된 `UserRepository`를 사용하고 있습니다.

**Note**: 우리는`LoginPage`에서 `AuthenticationBloc`에 접근하기 위해 다시 `BlocProvider.of <AuthenticationBloc>(context)`를 사용하고 있습니다.

다음으로, 우리의 `LoginForm`을 만들어 보겠습니다.

<p id = "loginform"/>

## LoginForm

```dart
import 'package:flutter/material.dart';

import 'package:flutter_bloc/flutter_bloc.dart';

import 'package:flutter_login/authentication/authentication.dart';
import 'package:flutter_login/login/login.dart';

class LoginForm extends StatefulWidget {
  final LoginBloc loginBloc;
  final AuthenticationBloc authenticationBloc;

  LoginForm({
    Key key,
    @required this.loginBloc,
    @required this.authenticationBloc,
  }) : super(key: key);

  @override
  State<LoginForm> createState() => _LoginFormState();
}

class _LoginFormState extends State<LoginForm> {
  final _usernameController = TextEditingController();
  final _passwordController = TextEditingController();

  LoginBloc get _loginBloc => widget.loginBloc;

  @override
  Widget build(BuildContext context) {
    return BlocBuilder<LoginEvent, LoginState>(
      bloc: _loginBloc,
      builder: (
        BuildContext context,
        LoginState state,
      ) {
        if (state is LoginFailure) {
          _onWidgetDidBuild(() {
            Scaffold.of(context).showSnackBar(
              SnackBar(
                content: Text('${state.error}'),
                backgroundColor: Colors.red,
              ),
            );
          });
        }

        return Form(
          child: Column(
            children: [
              TextFormField(
                decoration: InputDecoration(labelText: 'username'),
                controller: _usernameController,
              ),
              TextFormField(
                decoration: InputDecoration(labelText: 'password'),
                controller: _passwordController,
                obscureText: true,
              ),
              RaisedButton(
                onPressed:
                    state is! LoginLoading ? _onLoginButtonPressed : null,
                child: Text('Login'),
              ),
              Container(
                child:
                    state is LoginLoading ? CircularProgressIndicator() : null,
              ),
            ],
          ),
        );
      },
    );
  }

  void _onWidgetDidBuild(Function callback) {
    WidgetsBinding.instance.addPostFrameCallback((_) {
      callback();
    });
  }

  _onLoginButtonPressed() {
    _loginBloc.dispatch(LoginButtonPressed(
      username: _usernameController.text,
      password: _passwordController.text,
    ));
  }
}
```

**Note**: 우리의 `LoginForm`은 새로운 `LoginState`가 있을 때마다 다시 빌드 할 수 있도록 `BlocBuilder` 위젯을 사용합니다.  `BlocBuilder`는 Block과 `builder` 함수를 필요로 하는 Flutter 위젯입니다.  `BlocBuilder`는 새로운 상태에 대한 응답으로 위젯을 구현합니다. `BlocBuilder`는 `StreamBuilder`와 매우 비슷하지만 필요한 보일러플레이트 코드(boilerplate code; 관용 코드)의 양과 다양한 성능 최적화를 줄이는 더 간단한 API를 가지고 있습니다.

`LoginForm` 위젯에 그다지 많은 부분이 없으므로 로딩 인디케이터를 만드는 단계로 넘어 가겠습니다.

<p id = "loadingindicator"/>

## LoadingIndicator

```dart
import 'package:flutter/material.dart';

class LoadingIndicator extends StatelessWidget {
  @override
  Widget build(BuildContext context) => Center(
        child: CircularProgressIndicator(),
      );
}
```

이제 마침내 모든 것을 정리하고 `main.dart`에 메인 앱 위젯을 만듭니다.

<p id = "putting-it-all-together"/>

## Putting it all together

```dart
import 'package:flutter/material.dart';

import 'package:bloc/bloc.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:user_repository/user_repository.dart';

import 'package:flutter_login/authentication/authentication.dart';
import 'package:flutter_login/splash/splash.dart';
import 'package:flutter_login/login/login.dart';
import 'package:flutter_login/home/home.dart';
import 'package:flutter_login/common/common.dart';

class SimpleBlocDelegate extends BlocDelegate {
  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    print(transition);
  }
}

void main() {
  BlocSupervisor().delegate = SimpleBlocDelegate();
  runApp(App(userRepository: UserRepository()));
}

class App extends StatefulWidget {
  final UserRepository userRepository;

  App({Key key, @required this.userRepository}) : super(key: key);

  @override
  State<App> createState() => _AppState();
}

class _AppState extends State<App> {
  AuthenticationBloc authenticationBloc;
  UserRepository get userRepository => widget.userRepository;

  @override
  void initState() {
    authenticationBloc = AuthenticationBloc(userRepository: userRepository);
    authenticationBloc.dispatch(AppStarted());
    super.initState();
  }

  @override
  void dispose() {
    authenticationBloc.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return BlocProvider<AuthenticationBloc>(
      bloc: authenticationBloc,
      child: MaterialApp(
        home: BlocBuilder<AuthenticationEvent, AuthenticationState>(
          bloc: authenticationBloc,
          builder: (BuildContext context, AuthenticationState state) {
            if (state is AuthenticationUninitialized) {
              return SplashPage();
            }
            if (state is AuthenticationAuthenticated) {
              return HomePage();
            }
            if (state is AuthenticationUnauthenticated) {
              return LoginPage(userRepository: userRepository);
            }
            if (state is AuthenticationLoading) {
              return LoadingIndicator();
            }
          },
        ),
      ),
    );
  }
}
```

**Note**: 다시, 우리는 `AuthenticationState`의 변화에 반응하기 위해 `BlocBuilder`를 사용하고 있습니다. 그래서 현재의 `AuthenticationState`를 기반으로 사용자가 `SplashPage`, `LoginPage`, `HomePage` 또는`LoadingIndicator`를 보여줄 수 있습니다.

**Note**: 우리의 앱에는 `AuthenticationBloc`이 주입되어 `BlocProvider` 위젯을 사용하여 전체 위젯 하위 트리에 사용할 수 있습니다. `BlocProvider`는 `BlocProvider.of(context)`를 통해 자식에게 Bloc을 제공하는 Flutter 위젯입니다. 의존성 삽입(dependency injection; DI) 위젯으로 사용되어 bloc의 단일 인스턴스가 서브 트리 내의 여러 위젯에 제공 될 수 있습니다.

이제 여러분의 `HomePage`와 `Login Page` 위젯의 `BlocProvider.of<AuthenticationBloc>(context)`은 의미가 있습니다.

우리가 `MaterialApp`를 `BlocProvider<AuthenticationBloc>`에 래핑하였으므로  서브 트리의 어디서나 `BlocProvider.of<AuthenticationBloc>(BuildContext context)`정적 메소드를 사용하여`AuthenticationBloc`의 인스턴스에 접근 할 수 있습니다.

이 시점에서 우리는 꽤 단단한 로그인 구현을 가지고 있으며 우리는 Bloc을 사용하여 비즈니스 로직 레이어에서 프리젠 테이션 레이어를 분리했습니다.

전체 소스 코드는 [이곳](https://github.com/felangel/Bloc/tree/master/examples/flutter_login)에서 볼 수 있습니다.

---

이전: [무한 리스트 (Infinite List)](tutorials_flutter_infinite_list.md)

다음: [날씨 (Weather)](tutorials_flutter_weather.md)
