원문: https://aqueduct.io/docs/http/websockets/

# Using Websockets in Aqueduct

표준 HTTP 요청은 웹 서버에서 HTTP 응답을 생성합니다. 서버가 클라이언트에 데이터를 보내려면 클라이언트가 해당 데이터에 대한 요청을 보내야합니다. *websocket*은 열려 있는 상태로 유지되는 특수한 유형의 HTTP 요청이며 서버와 클라이언트는 언제든지 서로에게 데이터를 보낼 수 있습니다.

예를 들어 채팅 응용 프로그램은 웹 소켓을 사용하여 대화방의 모든 사람에게 메시지를 보낼 수 있습니다. 이 시나리오에서 채팅 클라이언트 응용 프로그램은 서버 응용 프로그램에 대한 웹 소켓 연결을 엽니다. 사용자가 메시지를 입력하면 채팅 클라이언트가 websocket에서 해당 메시지를 보냅니다. 페이로드는 다음과 같은 JSON 데이터 일 수 있습니다.

```json
{
  "action": "send_message",
  "room": "general",
  "text": "Hi everyone"
}
```

서버는 이 데이터를 수신 한 다음 차례에 변경된 버전을 모든 websocket 연결에 보냅니다. 그 데이터는 다음과 같습니다.

```json
{
  "action": "receive_message",
  "room": "general",
  "from": "Bob",
  "text": "Hi everyone"
}
```

연결된 모든 사용자는이 데이터를 수신하고 `Bob: Hi everyone`라는 메시지를 화면에 표시합니다.

JSON 데이터를 사용해야 한다는 웹 소켓에는 아무 것도 없습니다. 원하는 데이터 형식을 사용할 수 있습니다.

## Upgrading an HTTP Request to a WebSocket

Aqueduct에서 웹 소켓은 Dart의 표준 라이브러리 `WebSocket` 유형에 의해 처리됩니다. 다음은 그 예입니다.

```dart
router
  .route("/connect")
  .linkFunction((request) async {
    var socket = await WebSocketTransformer.upgrade(request.raw);
    socket.listen(listener);

    return null;
  });
```

websocket으로 업그레이드 된 요청은 컨트롤러에서 null을 반환하여 채널에서 제거하는 것이 중요합니다. (자세한 내용은 `Aqueduct and dart:io`  [in this guide](https://aqueduct.io/docs/application/structure/)  섹션을 참조하십시오.)

클라이언트 응용 프로그램은 `ws://localhost:8888/connect` URL에 연결할 수 있습니다. Dart 응용 프로그램은 다음과 같이 연결합니다.

```dart
var socket = await WebSocket.connect("ws://localhost:8888/connect");
socket.listen(...);
```

## Bi-directional Communication

위의 간단한 예제에서 서버는 클라이언트의 데이터만 수신합니다. 데이터가 클라이언트에 보내지려면 데이터를 추가 할 수 있도록 참조가 `WebSocket`에 보관되어야 합니다. Aqueduct 응용 프로그램이 웹 소켓 연결을 관리하는 방법은 응용 프로그램의 동작, 응용 프로그램이 실행되는 격리의 수 및 시스템 전체의 인프라에 크게 의존합니다.

간단한 응용 프로그램은 `Map`에서 websocket 연결을 추적 할 수 있습니다. 여기서 key는 요청 권한으로부터 얻은 사용자 식별자입니다 :

```dart
router
  .route("/connect")
  .link(() => new Authorizer(authServer));
  .linkFunction((request) async {
    var userID = request.authorization.ownerID;
    var socket = await WebSocketTransformer.upgrade(request.raw);
    socket.listen((event) => handleEvent(event, fromUserID: userID));

    connections[userID] = socket;

    return null;
  });
```

'채팅 응용 프로그램'예제를 계속 사용하면 `handleEvent` 코드는 다음과 같을 수 있습니다.

```dart
void handleEvent(dynamic event, {int fromUserID}) {
  var incoming = json.decode(UTF8.decode(event));
  var outgoing = utf8.encode(json.encode({
    "text": incoming["text"],
    ...
  }));

  connections.keys
    .where((userID) => userID != fromUserID)
    .forEach((userID) {
      var connection = connections[userID];
      connection.add(outgoing);
    });
}
```

이 간단한 구현은 동일한 사용자 또는 다중 격리 응용 프로그램의 다중 연결을 설명하지 않습니다.

## Considerations for Multi-Isolate and Multi-Instance Applications

기본적으로 Aqueduct 응용 프로그램은 여러 격리에서 실행됩니다. 각각의 격리에는 자체 힙이 있기 때문에 하나의 격리에서 생성 된 웹 소켓에는 다른 분격리가 직접 접근 할 수 없습니다. 위의 예에서 각 격리는 고유 한 연결 맵을 가집니다. 따라서 채팅 메시지가 시작된 동일한 격리에서 열린 연결에만 메시지가 전송됩니다.

간단한 해결책은 하나의 격리 된 시스템에서만 응용 프로그램을 실행하여 모든 웹 소켓이 단일 격리되어 있고 서로 액세스 할 수 있도록하는 것입니다.

```
aqueduct serve -n 1
```

많은 어플리케이션에서, 이것은 훌륭한 해결책입니다. 다른 사람들에게는 그렇지 않을 수도 있습니다.

Aqueduct의 다중 격리 아키텍처의 이점 중 하나는 단일 인스턴스에서 테스트 된 코드가 로드 밸런서 뒤에 여러 인스턴스로 확장된다는 점입니다. Aqueduct 응용 프로그램이 단일, 다중 분리 인스턴스에서 올바르게 실행되면 다중 인스턴스에서 올바르게 실행됩니다. 이 (다소) 강제적인 구조는 단일 격리에서 웹 소켓 연결을 순진하게 추적하지 못하게 하여 다중 인스턴스 시스템으로 확장 할 때 문제를 일으킬 수 있습니다.

응용 프로그램이 매우 인기있는 상황에서 요청을 효율적으로 처리하기 위해 여러 대의 서버가 필요하다면 적절한 솔루션을 설계하는 방법에 대해 잘 알고 있을 것입니다 (또는 필요한 사람을 고용 할 돈이 있을 것입니다) . 대부분의 경우 REST API와 websocket 서버는 별개의 인스턴스입니다. 즉, 라이프 사이클과 배포 동작이 다릅니다. 단일 격리에서 websocket 서버를 실행하는 것이 합리적 일 수 있습니다. CPU 바운드 대신 IO 바인딩 가능성이 높기 때문입니다.

여전히 웹 소켓이있는 다중 격리 서버를 선호한다면 `ApplicationMessageHub`가 도움이 될 것입니다. 응용 프로그램에서 연결된 웹 소켓으로 메시지를 브로드 캐스팅 할 때 먼저 메시지를 생성 한 격리 지점에 연결된 각 웹 소켓으로 데이터를 보냅니다. 그런 다음 메시지가 `ApplicationMessageHub`에 추가됩니다.

```dart
void onChatMessage(String message) {
  connectedSockets.forEach((socket) {
    socket.add(message);
  });

  ApplicationChannel.messageHub.add({"event": "websocket_broadcast", "message": message});
}
```

`messageHub`에 추가 된 것은 다른 모든 메시지 허브에 대한 청취자에게 전달됩니다. 즉 모든 다른 격리가 이 데이터를 수신합니다. 그런 다음 다른 격리가 메시지를 연결된 각 웹 소켓으로 보냅니다.

```dart
class ChatChannel extends ApplicationChannel {
  @override
  Future prepare() async {
    messageHub.listen((event) {
      if (event is Map && event["event"] == "websocket_broadcast") {
        connectedSockets.forEach((socket) {
          socket.add(event["message"]);
        });
      }
    });
  }
}
```