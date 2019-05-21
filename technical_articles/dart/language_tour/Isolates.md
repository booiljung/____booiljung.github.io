[Up](./index.md)

##  격리 (Isolates)

대부분의 컴퓨터는 모바일 플랫폼에서도 멀티 코어 CPU를 사용합니다. 모든 코어를 활용하기 위해 개발자는 전통적으로 동시에 실행되는 공유 메모리 스레드를 사용합니다. 그러나 공유 상태 동시성은 오류가 발생하기 쉽고 복잡한 코드로 이어질 수 있습니다.

스레드 대신 모든 Dart 코드가 isolates 내부에서 실행됩니다. 각 격리 저장소에는 자체 격리 메모리 힙이 있으므로 격리 저장소의 상태가 다른 격리 저장소에서 액세스 할 수 없도록 합니다.

더 많은 정보는 [dart:isolate library documentation](https://api.dartlang.org/stable/dart-isolate)에서 볼 수 있습니다.

---

이전: [Callable classes](./callable_classes.md)

다음: [타입 정의 (Typedefs)](./typedefs.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
