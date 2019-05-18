# 선언적으로 생각하기; Start thinking declaratively

원문: [Flutter: Docs/Development/Data & backend/State management/Thinking declaratively](https://flutter.dev/docs/development/data-and-backend/state-mgmt/declarative)

Android SDK 또는 iOS UIKit 같은 명령형 프레임워크에서 Flutter를 사용하려는 경우 새로운 관점에서 앱 개발을 고려해야 합니다.

많은 가정은 Flutter에 적용되지 않을 수도 있습니다. 예를 들어, Flutter에서는 UI의 일부를 수정하는 대신 처음부터 수정하는 것이 좋습니다. Flutter는 필요한 경우 모든 프레임에서 조차도 이를 수행 할 수 있을 정도로 빠릅니다.

Flutter는 *선언적*입니다. 이것은 Flutter가 앱의 현재 상태를 반영하도록 사용자 인터페이스를 구축 한다는 것을 의미합니다.

![A mathematical formula of UI = f(start_thinking_declaratively.assets/ui-equals-function-of-state-54b01b000694caf9da439bd3f774ef22b00e92a62d3b2ade4f2e95c8555b8ca7.png). 'UI' is the layout on the screen. 'f' is your build methods. 'state' is the application state.](https://flutter.dev/assets/development/data-and-backend/state-mgmt/ui-equals-function-of-state-54b01b000694caf9da439bd3f774ef22b00e92a62d3b2ade4f2e95c8555b8ca7.png)

앱의 상태가 변경되면(예: 사용자가 설정 화면에서 스위치를 전환 한 경우), 상태가 변경되고, 사용자 인터페이스가 다시 그려집니다. UI 자체 (예 :`widget.setText`)를 절대 변경하지 않아도 됩니다. 상태를 변경하면 UI가 처음부터 다시 빌드됩니다.

UI 프로그래밍에 대해 [시작 안내서 참조](https://flutter.dev/docs/get-started/flutter-for/declarative)에서 선언적 접근 방법에 대해 자세히 읽어 보세요.

선언적 스타일의 UI 프로그래밍에는 많은 이점이 있습니다. 놀랍게도 UI의 모든 상태에 대한 코드 경로는 하나뿐입니다. 특정 상태에 대해 UI가 어떤 모습이어야하는지 설명합니다.

처음에는 이런 프로그래밍 스타일이 명령형만큼 직관적이지 않을 수도 있습니다. 이것이 바로 이 절이있는 이유입니다. 일어보세요.

- [이전: Introduction](introduction.md)
- [다음: Differentiate between ephemeral state and app state](ephemeral_vs_app_state.md)