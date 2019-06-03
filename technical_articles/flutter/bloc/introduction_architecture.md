[Up](./index.md)

# 아키텍쳐 (Architecture)

원문: [Bloc / Introduction / Architecture](https://felangel.github.io/bloc/#/architecture)

![Bloc Architecture](introduction_architecture.assets/bloc_architecture.png)

Bloc을 사용하면 애플리케이션을 세 개의 레이어로 분리 할 수 있습니다:

- 데이터 (Data)
  - 데이터 제공자 (Data Provider)
  - 저장소 (Repository)
- 비지니스 로직 (Business Logic)
- 프리젠테이션 (Presentation)

우리는 가장 낮은 레벨의 (사용자 인터페이스에서 가장 멀리 떨어져 있는) 레이어에서 시작하여 프리젠테이션 레이어까지 진행할 것입니다.

<p id="data-layer"/>

## 데이터 레이어 (Data Layer)

> 데이터 레이어의 책임은 하나 이상의 소스에서 데이터를 얻거나 조작하는 것입니다.

데이터 레이어는 두 부분으로 나눌 수 있습니다.

- 저장소 (Repository)
- 데이터 제공자 (Data Provider)

이 레이어는 애플리케이션의 가장 낮은 레벨이며 데이터베이스, 네트워크 요청 및 기타 비동기 데이터 소스들과 상호 작용합니다.

<p id="data-provider"/>

### 데이터 제공자 (Data Provider)

> 데이터 제공자의 책임은 원시 데이터를 제공하는 것입니다. 데이터 제공자는 포괄적이고 다재다능 해야 합니다.

데이터 제공 업체는 일반적으로 간단한 API를 제공하여 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 작업을 수행합니다. 우리는 데이터 레이어의 일부로 `createData`, `readData`, `updateData` 및 `deleteData` 메소드를 가질 수 있습니다.

```dart
class DataProvider {
    Future<RawData> readData() async {
        // DB에서 읽거나 네트워크 요청 등 ...
    }
}
```

<p id="repository"/>

### 저장소 (Repository)

> 저장소 레이어는 Bloc 레이어가 통신하는 하나 이상의 데이터 제공자(data provider)를 감싸는 랩퍼입니다.

```dart
class Repository {
    final DataProviderA dataProviderA;
    final DataProviderB dataProviderB;

    Future<Data> getAllDataThatMeetsRequirements() async {
        final RawDataA dataSetA = await dataProviderA.readData();
        final RawDataB dataSetB = await dataProviderB.readData();

        final Data filteredData = _filterData(dataSetA, dataSetB);
        return filteredData;
    }
}
```

보시다시피, 저장소 계층은 여러 데이터 공급자와 상호 작용하고 결과를 비즈니스 로직 계층에 전달하기 전에 데이터에 대한 변환(transformations)을 수행 할 수 있습니다.

<p id="bloc-layer"/>

## Bloc (Business Logic) Layer

> Bloc 레이어의 책임은 프리젠테이션 레이어의 이벤트에 새로운 상태로 응답하는 것입니다. Bloc 레이어는 하나 이상의 저장소에 의존하여 애플리케이션 상태를 구축하는 데 필요한 데이터를 검색 할 수 있습니다.

Bloc 레이어를 사용자 인터페이스(프리젠테이션 레이어)와 데이터 레이어 사이의 다리라고 생각하세요. Bloc 레이어는 사용자 입력에 의해 생성된 이벤트를 취한 다음 저장소와 통신하여 프리젠테이션 레이어가 소비할 새로운 상태를 구축합니다.

```dart
class BusinessLogicComponent extends Bloc {
    final Repository repository;

    Stream mapEventToState(event) async* {
        if (event is AppStarted) {
            yield await repository.getAllDataThatMeetsRequirements();
        }
    }
}
```

### Bloc에서 Bloc으로 커뮤니케이션 (Bloc-to-Bloc Communication)

> 모든 Bloc은 Bloc 내에 있는 변화에 대응하기 위해 다른 Bloc이 구독(subscribe) 할 수 있는 상태 스트림(state stream)을 가지고 있습니다.

Bloc은 자신의 상태 변화에 반응하기 위해 다른 Bloc에 의존 할 수 있습니다. 다음 예제에서 `MyBloc`은 `OtherBloc`에 의존성을 가지며 `OtherBloc`의 상태 변화에 대한 응답으로 이벤트를 `dispatch` 할 수 있습니다. `StreamSubscription`은 메모리 누수를 피하기 위해 `MyBloc`의 `dispose` 오버라이드에서 닫혀 있습니다.

```dart
class MyBloc extends Bloc {
  final OtherBloc otherBloc;
  StreamSubscription otherBlocSubscription;

  MyBloc(this.otherBloc) {
    otherBlocSubscription = otherBloc.state.listen((state) {
        // 여기에 변경 사항을 반영하십시오.
        // MyBloc에서 변경 사항을 트리거하려면 여기에 이벤트를 보내십시오.
    });
  }

  @override
  void dispose() {
    otherBlocSubscription.cancel();
    super.dispose();
  }
}
```

<p id="presentation-layer"/>

## 프리젠테이션 레이어 (Presentation Layer)

> 프리젠테이션 레이어의 책임은 하나 이상의 bloc 상태를 기반으로 렌더링하는 방법을 파악하는 것입니다. 또한 사용자 입력 및 애플리케이션 수명주기 이벤트를 처리해야 합니다.

대부분의 애플리케이션 흐름은 애플리케이션이 사용자에게 표시 할 데이터를 가져 오도록 트리거하는 `AppStart` 이벤트로 시작됩니다.

이 시나리오에서, 프리젠테이션 레이어는 `AppStart` 이벤트를 디스패치 할 것입니다.

또한 프리젠테이션 레이어는 bloc 레이어의 상태를 기반으로 화면에 렌더링 할 내용을 파악해야합니다.

```dart
class PresentationComponent {
    final Bloc bloc;

    PresentationComponent() {
        bloc.dispatch(AppStarted());
    }

    build() {
        // bloc 상태를 기반으로 UI 렌더링
    }
}
```

지금까지는 코드 조각(snippet)을 몇 개 다루었지만, 모두 상당히 높은 수준이었습니다. 튜토리얼 섹션에서는 여러 가지 예제 애플리케이션을 빌드 할 때 이 모든 것을 함께 모으도록 하겠습니다.

---

이전: [핵심 개념 (Core Concepts)](introduction_core_concepts.md)

다음: [테스트 (Testing)](introduction_testing.md)

