# 이페머럴 상태와 앱 상태; Differentiate between ephemeral state and app state

원문: [Flutter: Docs/Development/Data & backend/State management/ephemeral vs app state](https://flutter.dev/docs/development/data-and-backend/state-mgmt/ephemeral-vs-app)

## Contents

- [이페머럴 상태 (Ephemeral state)](#ephemeral-state)
- [앱 상태 (App state)](#app-state)
- [정답이 있는게 아닙니다 (There is no clear-cut rule)](there-is-no-clear-cut-rule)

*이 문서는 앱 상태(App state), 이페머럴 상태(Ephemeral state) 및 Flutter 앱에서 각 상태를 관리하는 방법을 소개합니다.*

가장 넓은 의미에서 앱 상태(App state)는 앱이 실행될 때 메모리에 존재하는 모든 것입니다. 여기에는 앱의 애셋과, UI, 애니메이션 상태, 텍스처, 글꼴 등에 대해 Flutter 프레임워크가 유지하는 모든 변수가 포함됩니다. 이 상태를 가능한 한 광범위하게 정의 할 수는 있지만, 앱을 설계하는 데는 그리 유용하지 않습니다.

첫째, 개발자는 일부 상태 (텍스처와 같은)조차 관리하지 않습니다. 이 프레임워크는 개발자를 대신합니다. 그러므로 상태에 대한보다 유용한 정의는 "어느 시점에서라도 UI를 **재구성**하기 위해 필요한 모든 데이터"입니다. 둘째로, 자신을 관리하는 상태는 이페머럴 상태와 앱 상태의 두 가지 개념의 타입으로 구분할 수 있습니다.

<p id="ephemeral-state"/>

##  이페머럴 상태 (Ephemeral state)

일시 상태 (*UI상태* 또는 *local state*라고도 함)는 단일 위젯에 깔끔하게 포함 할 수 있는 상태입니다.

이것은 의도적으로 모호한 정의이므로 여기에 몇 가지 예가 나와 있습니다.

- `PageView` 안의 현재 페이지.
- 복잡한 애니메이션의 현재 진행 상황.
- `BottomNavigationBar` 의 현재 선택된 탭.

위젯 트리의 다른 부분은 이런 종류의 상태에 액세스 할 필요가 거의 없습니다. 직렬화 할 필요가 없으며 복잡한 방식으로 변경되지 않습니다.

다른 표현으로 말하자면, 상태 관리 기법 (ScopedModel, Redux 등)을 이러한 종류의 상태에 사용할 필요가 없습니다. 필요한 것은 `StatefulWidget` 뿐입니다.

아래에서, 당신은 아래쪽 네비게이션바에서 현재 선택된 항목이 `_MyHomepageState` 클래스의`_index` 필드에 어떻게 저장되어 있는지 볼 수 있습니다. 이 예에서`_index`는 이페머럴 상태입니다.

```dart
class MyHomepage extends StatefulWidget {
  @override
  _MyHomepageState createState() => _MyHomepageState();
}

class _MyHomepageState extends State<MyHomepage> {
  int _index = 0;

  @override
  Widget build(BuildContext context) {
    return BottomNavigationBar(
      currentIndex: _index,
      onTap: (newIndex) {
        setState(() {
          _index = newIndex;
        });
      },
      // ... items ...
    );
  }
}
```

여기서 `setState()`와 `StatefulWidget`의 `State` 클래스 안에 있는 필드를 사용하는 것은 자연스럽습니다. 앱의 다른 부분은 `_index`에 접근 할 필요가 없습니다. 변수는 `MyHomepage` 위젯 내에서만 변경됩니다. 그리고 사용자가 앱을 닫고 다시 시작하면`_index`가 0으로 재설정 되더라도 상관 없습니다.

<p id="app-state"/>

##  앱 상태 (App state)

일시적이지 않은 상태 - 앱의 여러 부분에서 공유하려는 상태, 사용자 세션간에 유지하려는 상태 - 는 애플리케이션 상태 (공유 상태라고도 함)라고 합니다.

응용 프로그램 상태의 예 :

- 사용자 환경 설정
- 로그인 정보
- 소셜 네트워킹 앱의 알림
- 전자상거래 앱의 카트
- 뉴스 앱의 기사 읽기 / 읽지 않음 상태

앱 상태를 관리하려면, 옵션을 연구해야 합니다. 선택은 앱의 복잡성과 특성, 팀의 이전 경험 및 기타 여러 측면에 따라 다릅니다. 계속 읽으세요.

<p id = "there-is-no-clear-cut-rule"/>

##  정답이 있는게 아닙니다 (There is no clear-cut rule)

명확하게 말하면, `State`와 `setState()`를 사용하여 앱의 모든 상태를 관리 할 수 있습니다. 사실, Flutter 팀은 많은 간단한 앱 샘플 (`flutter create`로 생성되는 모든 시작 앱을 포함)에서 이 작업을 수행합니다.

그것은 다른 방향으로도 진행됩니다. 예를 들어 특정 앱의 컨텍스트에서 하단 탐색 바의 선택된 탭이 이페머럴 상태가 아닌지 결정할 수 있습니다. 클래스 외부에서 변경하거나 세션간에 유지하는 등의 작업이 필요할 수 있습니다. 이 경우, `_index` 변수는 앱 상태입니다.

특정 변수가 이페머럴 상태인지 앱 상태인지 구분하는 명확한 보편적 규칙은 없습니다. 때로는 하나를 다른 것으로 리팩터링해야 합니다. 예를 들어 명확한 이페머럴 상태로 시작 하겠지만 기능이 확장되면 앱 상태로 변경 해야 합니다.

그러한 이유로 많은 양의 소금으로 다음 도표를 취하세요. (For that reason, take the following diagram with a large grain of salt:) 에...?

![A flow chart. Start with 'Data'. 'Who needs it?'. Three options: 'Most widgets', 'Some widgets' and 'Single widget'. The first two options both lead to 'App state'. The 'Single widget' option leads to 'Ephemeral state'.](ephemeral_vs_app_state.assets/ephemeral-vs-app-state-3137024aa509b4df5d20ed7ed30fb8a0f7cff54ebc8ab0d6e39794bced87e27c.png)

React의 `setState` 대 Redux의 `store`에 대해 물었더니 Redux의 저자인 Dan Abramov는 다음과 같이 대답했습니다.

> “The rule of thumb is: [Do whatever is less  awkward](https://github.com/reduxjs/redux/issues/1287#issuecomment-175351978).”

요약하면 Flutter 앱에는 두 가지 개념적 상태 타입이 있습니다. 이페머럴 상태는 `State`와 `setState()`를 사용하여 구현 될 수 있으며, 종종 하나의 위젯에 국한 됩니다. 나머지는 앱 상태입니다. 두 가지 타입 모두 Flutter 앱에서 자리를 차지하며, 둘 사이의 구분은 사용자의 선호도와 앱의 복잡성에 따라 다릅니다.

- [이전: Start thinking declaratively](start_thinking_declaratively.md)

- [다음: Simple app state management](simple_app_state_management.md)

