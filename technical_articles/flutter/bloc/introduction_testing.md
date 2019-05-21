[Up](./index.md)

# 테스트 (Testing)

원문: [Bloc / Introduction / Testing](https://felangel.github.io/bloc/#/testing)

> Bloc은 매우 쉽게 테스트 할 수 있도록 설계되었습니다.

간단히 하기 위해 [핵심 개념](introduction_core_concepts.md)에서 작성한 `CounterBloc`에 대한 테스트를 작성해 보겠습니다.

요약하자면, `CounterBloc` 구현은 다음과 같이 보입니다.

```dart
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

우리가 테스트를 작성하기 전에 우리는 의존성에 테스팅 프레임워크를 추가해야 합니다.

우리는 `pubspec.yaml`에 [test](https://pub.dartlang.org/packages/test)를 추가해야 합니다.

```yaml
dev_dependencies:
  test: ">=1.3.0 <2.0.0"
```

우리의 `CounterBloc` 테스트를 위해 `counter_bloc_test.dart` 파일을 만들고 `test` 패키지를 임포트하여 시작하겠습니다.

```dart
import 'package:test/test.dart';
```

다음으로, 우리는 우리의 테스트 그룹과 마찬가지로 `main`을 생성해야합니다.

```dart
void main() {
    group('CounterBloc', () {

    });
}
```

**Note**: `group`은 개별 테스트를 조직 할 뿐만 아니라, 모든 개별 테스트에 공통 `setUp` 및 `tearDown`을 공유 할 수 있는 컨텍스트를 만드는 데 사용됩니다.

우리의 모든 테스트에 걸쳐 사용 될 `CounterBloc` 인스턴스를 만들어 보겠습니다.

```dart
group('CounterBloc', () {
    CounterBloc counterBloc;

    setUp(() {
        counterBloc = CounterBloc();
    });
});
```

이제 개별 테스트를 작성할 수 있습니다.

```dart
group('CounterBloc', () {
    CounterBloc counterBloc;

    setUp(() {
        counterBloc = CounterBloc();
    });

    test('initial state is 0', () {
        expect(counterBloc.initialState, 0);
    });
});
```

**Note**: 우리는`pub run test` 명령으로 모든 테스트를 실행할 수 있습니다.

이 시점에서 첫 번째 합격 테스트가 있어야 합니다! 이제, 보다 복잡한 테스트를 작성해 보겠습니다.

```dart
test('single Increment event updates state to 1', () {
    final List<int> expected = [0, 1];

    expectLater(
        counterBloc.state,
        emitsInOrder(expected),
    );

    counterBloc.dispatch(CounterEvent.increment);
});

test('single Decrement event updates state to -1', () {
    final List<int> expected = [0, -1];

    expectLater(
        counterBloc.state,
        emitsInOrder(expected),
    );

    counterBloc.dispatch(CounterEvent.decrement);
});
```

우리는 테스트를 실행하고 모두가 통과하고 있음을 볼 수 있어야 합니다.

그게 다가 왔습니다. 테스트는 산들 바람에 빠져야 합니다. 우리는 코드를 수정하고 코드를 리팩터링 할 때 자신감을 가져야 합니다. (That's all there is to it, testing should be a breeze and we should feel confident when making changes and refactoring our code.)

---

이전: [아키텍쳐 (Architecture)](introduction_architecture.md)

다음: [카운터 (Counter)](tutorials_flutter_counter.md)
