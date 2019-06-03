[Up](./index.md)

# 핵심 개념 (Core Concepts)

Bloc을 사용하기 전에 다음 섹션을 주의 깊게 읽고 이해 해야 합니다.

Bloc을 사용하는 방법을 이해하는 데 중요한 몇 가지 핵심 개념이 있습니다.

다음 섹션에서는 각 애플리케이션에 대해 자세히 논의하고 실제 애플리케이션에 어떻게 적용 할 것인가에 대해 설명 할 것입니다: 카운터 앱.

<p id="events"/>

##  이벤트 (Events)

> 이벤트는 Bloc으로 입력입니다. 버튼 누름이나 페이지 로드와 같은 라이프 사이클 이벤트와 같은 사용자 상호 작용(user interactions)에 응답하여 공통적으로(commonly) 전달(dispatch)됩니다.

앱을 디자인 할 때 우리는 뒤로 한발 물러나서 사용자가 어떻게 상호 작용하는지 정의해야 합니다. 우리의 카운터 앱의 콘텍스트에서 우리는 카운터를 증가 및 감소시키는 두 개의 버튼을 갖게 될 것입니다.

사용자가 이 버튼 중 하나를 탭하면, 사용자의 입력에 응답 할 수 있도록 앱의 "두뇌"에 알려야(notify) 합니다. 이것은 이벤트가 실행되는 곳입니다.

우리는 애플리케이션의 "두뇌"에 증분과 감소 둘을 알릴 수 있어야 하므로 이 이벤트들을 정의해야합니다.

```dart
enum CounterEvent { increment, decrement }
```

이 경우, 우리는 `enum`을 사용하여 이벤트를 나타낼 수 있지만, 더 복잡한 경우에는 `class`를 필요로 할 수도 있습니다.

이 시점에서 우리는 첫 번째 이벤트를 정의했습니다! 우리는 지금까지 어떤 식으로든 Bloc을 사용하지 않았으며, 아무런 마술도 일어나지 않았습니다. 그것은 평범한 Dart 코드입니다.

<p id="states"/>

## 상태 (States)

> 상태는 Bloc의 출력이며 앱의 상태 일부를 나타냅니다. UI 구성 요소에 상태를 알리고 현재 상태(current state)를 기반으로 그들 (UI) 자체의 부분을 다시 그릴 수 있습니다.

지금까지 우리의 앱이 응답하게 될 두 가지 이벤트, 즉 `CounterEvent.increment`와 `CounterEvent.decrement`를 정의했습니다.

이제 애플리케이션의 상태를 나타내는 방법을 정의해야합니다.

우리는 카운터를 만들고 있기 때문에 상태는 매우 간단합니다. 카운터의 현재 값을 나타내는 정수입니다.

나중에 더 복잡한 상태 예제를 보게 될 것이지만, 이 경우 기본 타입(primitive type)은 상태 표현(state representation)으로 완벽하게 적합합니다.

<p id="transitions"/>

## 전환 (Transitions)

> 한 상태에서 다른 상태로의 변화를 트랜지션이라고 합니다. 트랜지션은 현재 상태, 이벤트 및 다음 상태로 구성됩니다.

사용자가 카운터 앱과 상호 작용할 때 카운터의 상태를 업데이트하는 `Increment` 및 `Decrement` 이벤트를 트리거합니다. 이러한 모든 상태 변화는 일련의 전환으로 설명 할 수 있습니다.

예를 들어 사용자가 앱을 열고, 증분 버튼을 한번 탭하면, 우리는 다음과 같은 전환을 보게 됩니다.

```json
{
  "currentState": 0,
  "event": "CounterEvent.increment",
  "nextState": 1
}
```

모든 상태 변경이 기록되기 때문에, 우리는 앱을 매우 쉽게 측정하고, 모든 사용자 상호 작용 및 상태 변경을 한 곳에서 추적 할 수 있습니다. 또한, 이는 시간-여행 디버깅(time-travel debugging)과 같은 것을 가능하게 합니다.

<p id="stream"/>

## 스트림 (Streams)

`Streams`에 대한 더 자세한 정보는 공식 [Dart Documentation](https://www.dartlang.org/tutorials/language/streams)에서 확인 합니다.

> 스트림은 비동기 데이터 시퀀스입니다.

Bloc은 [RxDart](https://pub.dartlang.org/packages/rxdart) 위에 구축되었습니다. 그러나, 그것은 모든 `RxDart` 특정 구현 세부 사항을 추상화 합니다.

Bloc을 사용하려면 `Streams`와 그 작동 원리를 확실하게 이해하는 것이 중요합니다.

> `Streams`에 익숙하지 않다면 물이 흐르는 파이프를 생각해 보십시오. 파이프는 `Stream`이고 물은 비동기 데이터입니다.

`async*` 함수를 작성하여 Dart에 `Stream`을 생성 할 수 있습니다.

```dart
Stream<int> countStream(int max) async* {
    for (int i = 0; i < max; i++) {
        yield i;
    }
}
```

함수를 `async*`로 표시함으로써 `yield` 키워드를 사용하고`Stream` 데이터를 반환 할 수 있습니다. 위의 예에서 정수의 `Stream`을 `max` 정수 매개 변수로 반환합니다.

`async*` 함수에서 `yield` 할 때마다 우리는 `Stream`을 통해 그 데이터 조각을 밀어 냅니다.

우리는 위의 `Stream`을 여러 가지 방법(way)으로 소비(consume) 할 수 있습니다. 우리가 정수의 `Stream`의 합을 반환하는 함수를 작성하고자 한다면 다음과 같이 보일 수 있습니다:

```dart
Future<int> sumStream(Stream<int> stream) async {
    int sum = 0;
    await for (int value in stream) {
        sum += value;
    }
    return sum;
}
```

위의 함수를 `async`로 표시함으로써 `await` 키워드를 사용하고 정수의 `Future`를 반환 할 수 있습니다. 이 예제에서는, 스트림의 각 값을 기다리고 스트림의 모든 정수 합을 반환합니다.

다음과 같이 모두 함께 넣을 수 있습니다.

```dart
void main() async {
    /// 정수의 스트림을 0에서 9까지 초기화 합니다.
    Stream<int> stream = countStream(10);
    /// 정수의 합계를 계산합니다.
    int sum = await sumStream(stream);
    /// 합계를 인쇄 합니다.
    print(sum); // 45
}
```

<p id="blocks"/>

## Blocs

> Bloc (Business Logic Component)은 들어오는 이벤트의 `Stream`을 나가는 상태의 `Stream`으로 변환 하는 컴포넌트 입니다. Bloc을 위에서 설명한 "두뇌"라고 생각하십시오.

> 모든 Bloc은 core bloc package의 일부인 기본 'bloc' 클래스를 확장해야 합니다.

```dart
import 'package:bloc/bloc.dart';

class CounterBloc extends Bloc<CounterEvent, int> {

}
```

위의 코드 조각에서, 우리는 `CounterBloc`이 `CounterEvents`를 `int`로 변환하는 Bloc으로 선언하고 있습니다.

> 모든 Bloc은 이벤트가 수신되기 전의 상태인 초기 상태(initial state)를 정의해야 합니다.

이 경우 카운터는 '0'부터 시작합니다.

```dart
@override
int get initialState => 0;
```

모든 Bloc은 `mapEventToState`라는 함수를 구현해야 합니다. 이 함수는 들어오는 `event`를 인수로 취해서 프리젠테이션 레이어가 소비하는 새로운 상태의 `Stream`을 반환해야 합니다. 우리는 `currentState` 속성을 사용하여 언제든지 현재 Bloc 상태에 접근 할 수 있습니다.

```dart
@override
Stream<int> mapEventToState(CounterEvent event) async* {
    switch (event) {
      case CounterEvent.decrement:
        yield currentState - 1;
        break;
      case CounterEvent.increment:
        yield currentState + 1;
        break;
    }
}
```

이 시점에서, 우리는 완전히 동작하는 `CounterBloc`을 가지고 있습니다.

```dart
import 'package:bloc/bloc.dart';

enum CounterEvent { increment, decrement }

class CounterBloc extends Bloc<CounterEvent, int> {
  @override
  int get initialState => 0;

  @override
  Stream<int> mapEventToState(CounterEvent event) async* {
    switch (event) {
      case CounterEvent.decrement:
        yield currentState - 1;
        break;
      case CounterEvent.increment:
        yield currentState + 1;
        break;
    }
  }
}
```

Bloc은 중복된 상태를 무시합니다. Bloc이 `currentState == state` 인 `state`를 산출하면, 전환은 일어나지 않고 `Stream<State>`에 대한 변경은 일어나지 않을 것입니다.

이 시점에서 궁금한 점은 *"*How do I notify a Bloc of an event?"

> 모든 Bloc에는 `dispatch` 메소드가 있습니다. `dispatch`는 `event`를 취해 `mapEventToState`를 트리거 합니다. `dispatch`는 프리젠테이션 계층에서 호출되거나, Bloc 내에서 호출 될 수 있으며 새로운 `event`가 Bloc에 통지 됩니다.

우리는 0에서 3까지 세는 간단한 애플리케이션을 생성 할 수 있습니다.

```dart
void main() {
    CounterBloc bloc = CounterBloc();

    for (int i = 0; i < 3; i++) {
        bloc.dispatch(CounterEvent.increment);
    }
}
```

위 코드 조각에서 `Transitions`은

```json
{
    "currentState": 0,
    "event": "CounterEvent.increment",
    "nextState": 1
}
{
    "currentState": 1,
    "event": "CounterEvent.increment",
    "nextState": 2
}
{
    "currentState": 2,
    "event": "CounterEvent.increment",
    "nextState": 3
}
```

입니다.

불행하게도, 현재 상태에서 우리는 `onTransition`을 재정의(override)하지 않는 한 이러한 트랜지션을 볼 수 없을 것입니다.

> `onTransition`은 모든 로컬 Bloc `Transition`을 핸들링 하기 위해 재정의 될 수 있는 메소드 입니다. `onTransition`은 Bloc의 `state`가 업데이트 되기 전에 호출 됩니다.

**Tip**: `onTransition`은 bloc-specific한 기록과 분석을 추가하기에 좋은 위치입니다.

```dart
@override
void onTransition(Transition<CounterEvent, int> transition) {
    print(transition);
}
```

이제 `onTransition`을 재정의 했으므로 어떤 `Transition`이 발생하더라도 무엇이든 할 수 있습니다.

`bloc` 레벨에서 `Transition`을 처리 할 수 있는 것처럼 `Exception`도 처리 할 수 있습니다.

> `onError`는 모든 로컬 Bloc `Exception`을 처리하기 위해 재정의 될 수 있는 메소드 입니다. 기본적으로 모든 예외는 무시되고 ` `Bloc`기능은 영향을 받지 않습니다.

**Note**: 상태 스트림이 `StackTrace` 없이 에러를 수신하면  stacktrace 인수는 `null`이 될 수 있습니다.

**Tip**: `onError` 은 bloc 전용 에러 핸들링을 넣을 수 있는 좋은 위치 입니다.

```dart
@override
void onError(Object error, StackTrace stackTrace) {
  print('$error, $stackTrace');
}
```

이제 `onError`를 재정의 했으므로 `Exception`이 발생할 때마다 원하는 대로 할 수 있습니다.

<p id="blocdelegate"/>

## BlocDelegate

Bloc을 사용하는데 또 하나의 이익은 우리가 한 곳에서 모든 `Transitions`에 액세스 할 수 있다는 것입니다. 이 애플리케이션에는 하나의 Bloc만 있지만 많은 응용 프로그램에서는 많은 Bloc이 애플리케이션 상태의 다른 부분을 관리하는 것이 공통적입니다.

우리가 모든 `Transition`에 대응하여 무언가를 할 수 있기를 원한다면 우리는 단순히 우리 자신의 `BlocDelegate`을 만들 수 있습니다.

```dart
class SimpleBlocDelegate extends BlocDelegate {
  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    print(transition);
  }
}
```

**Note**: 우리가 할 일은 `BlocDelegate`를 확장하고 `onTransition` 메소드를 재정의하는 것 뿐입니다.

Bloc에 우리의 `SimpleBlocDelegate`를 사용하라고 지시하기 위해서 `main` 함수를 조정할 필요가 있습니다.

```dart
void main() {
  BlocSupervisor().delegate = SimpleBlocDelegate();
  CounterBloc bloc = CounterBloc();

  for (int i = 0; i < 3; i++) {
    bloc.dispatch(CounterEvent.increment);
  }
}
```

우리가 보낸 모든 `Events`에 응답하여 무언가를 할 수 있기를 원한다면, 우리는 `SimpleBlocDelegate`에서`onEvent` 메소드를 재정의 할 수 있습니다.

```dart
class SimpleBlocDelegate extends BlocDelegate {
  @override
  void onEvent(Bloc bloc, Object event) {
    super.onEvent(bloc, event);
    print(event);
  }

  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    print(transition);
  }
}
```

Bloc에 던져진 모든 `Exceptions`에 대한 응답으로 무언가를 할 수 있기를 원하면 `SimpleBlocDelegate`의 `onError` 메소드를 재정의 할 수 있습니다.

```dart
class SimpleBlocDelegate extends BlocDelegate {
  @override
  void onEvent(Bloc bloc, Object event) {
    super.onEvent(bloc, event);
    print(event);
  }

  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    print(transition);
  }

  @override
  void onError(Bloc bloc, Object error, StackTrace stacktrace) {
    super.onError(bloc, error, stacktrace);
    print('$error, $stacktrace');
  }
}
```

**Note**: `BlocSupervisor`는 모든 `Bloc`들을 감독하고 `BlocDelegate`에 책임을 위임하는 싱글톤입니다.

---

이전: [왜 Bloc인가? (Why Bloc?)](introduction_why_bloc.md)

다음: [아키텍쳐 (Architecture)](introduction_architecture.md)