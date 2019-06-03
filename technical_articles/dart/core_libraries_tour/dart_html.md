[Up](./index.md)

## dart:html - browser-based apps

원문: [A tour of the core libraries](https://dart.dev/guides/libraries/library-tour)

[dart:html](https://api.dartlang.org/stable/dart-html/dart-html-library.html) 라이브러리를 사용하여 브라우저를 프로그래밍하고, DOM에서 객체와 요소를 조작하며 HTML5 API에 액세스 하세요. DOM은 HTML 페이지의 계층 구조를 설명하는 *Document Object Model*의 약자입니다.

dart:html의 다른 일반적인 사용법은 스타일(*CSS*)을 조작하고, HTTP 요청을 사용하여 데이터를 가져오고, [WebSockets](https://dart.dev/guides/libraries/library-tour#sending-and-receiving-real-time-data-with-websockets)를 사용하여 데이터를 교환합니다. HTML5 (및 dart:html)에는 이 섹션에서 다루지 않는 추가 API가 많이 있습니다. 명령 줄 애플리케이션이 아닌 웹 애플리케이션만 dart:html을 사용할 수 있습니다.

**Note:** 웹 애플리케이션 UI에 대한보다 높은 수준의 접근 방법을 원하면 [AngularDart](https://webdev.dartlang.org/angular)와 같은 웹 프레임워크를 사용하세요.

웹 애플리케이션에서 HTML 라이브러리를 사용하려면 dart:html를 import 하세요:

```
import 'dart:html';
```

###  DOM을 조작하기 (Manipulating the DOM)

DOM을 사용하려면 windows, documents, elements 및 nodes에 대해 알아야 합니다.

[`Window`](https://api.dartlang.org/stable/dart-html/Window-class.html) 객체는 웹 브라우저의 실제 윈도우를 나타냅니다. 각 윈도우에는 현재로드 된 문서를 가리키는 `Document` 객체가 있습니다. 또한 `Window` 객체에는 `IndexedDB` (데이터 저장 용), `requestAnimationFrame` (애니메이션 용) 등과 같은 다양한 API에 대한 접근자가 있습니다. 탭이있는 브라우저에서 각 탭에는 고유한 `Window` 객체가 있습니다.

[`Document`](https://api.dartlang.org/stable/dart-html/Document-class.html) 개체를 사용하면 해당 `Document`의 [`Element`](https://api.dartlang.org/stable/dart-html/Element-class.html)를 만들고 조작 할 수 있습니다. `Document` 자체는 `Element`이므로 조작 할 수 있습니다.

DOM은 [Nodes](https://api.dartlang.org/stable/dart-html/Node-class.html)의 트리를 모델입니다. 이러한 노드들은 종종 `Element`이지만 attributes, texts, comments, 및 기타 DOM 타입일 수 있습니다. 부모가 없는 루트 노드를 제외하고 DOM의 각 노드는 하나의 부모를 가지며, 많은 자식을 가질 수 있습니다.

#### Finding elements

element 를 조작하려면 먼저 element 를 나타내는 객체가 필요합니다. 쿼리를 사용하여이 개체를 가져올 수 있습니다.

최상위 함수 `querySelector()`와 `querySelectorAll()`을 사용하여 하나 이상의 요소를 찾으세요. id, class, tag, name 또는 이들의 조합으로 쿼리 할 수 있습니다. [CSS Selector Specification guide](http://www.w3.org/TR/css3-selectors/)는 id를 지정하기 위해 # 접두사를 사용하고 class에 마침표 (.)를 사용하는 것과 같이 selector의 형식을 정의 합니다.

`querySelector()`함수는 selector와 일치하는 첫 번째 요소를 반환하고, `querySelectorAll()`은 selector와 일치하는 요소의 집합을 반환합니다.

```dart
  // id로 요소 찾기
  Element elem1 = querySelector('#an-id');

  // class로 요소 찾기
  Element elem2 = querySelector('.a-class');

  // tag로 요소 찾기
  List<Element> elems1 = querySelectorAll('div');

  // Find all text inputs.
  List<Element> elems2 = querySelectorAll(
    'input[type="text"]',
  );

  // ID가 'id'인 요소 안에 있는 <p> 안에 CSS 클래스
  // 'class'가 있는 모든 요소를 찾습니다.
  List<Element> elems3 = querySelectorAll('#id p.class');
```

#### 요소  다루기 (Manipulating elements)

property을 사용하여 요소의 상태를 변경할 수 있습니다. node와 하위 타입(subtype) 요소는 모든 요소에 있는 property을 정의 합니다. 예를 들어, 모든 요소에는 상태를 설정하는 데 사용할 수 있는`class`, `hidden`, `id`, `style` 및 `title` 속성이 있습니다. `Element`의 서브 클래스는 [AnchorElement](https://api.dartlang.org/stable/dart-html/AnchorElement-class.html)의 `href` 속성과 같은 추가 속성을 정의합니다.

HTML에서 `AnchorElement`를 지정하는 다음 예제를 살피세요:

```html
<a id="example" href="http://example.com">link text</a>
```

이 `<a>` 태그는 `href` 속성을 가진 요소와 `"link text"` 문자열을 포함하는 text node (`text` 속성을 통해 접근 가능)를 지정합니다. 링크 가기 (link goes)의 URL을 변경하려면 `AnchorElement`의 `href` 속성을 사용할 수 있습니다:

```
var anchor = querySelector('#example') as AnchorElement;
anchor.href = 'http://dartlang.org';
```

종종 여러 요소에 property를 설정해야 합니다. 예를 들어, 다음 코드는 `"mac"`, `"win"`또는 `"linux"` 클래스를 갖는 모든 요소의 `hidden` 속성을 설정합니다. `hidden` 속성을 `true`로 설정하는 것은 CSS에 `display:none`을 추가 하는 것과 같은 효과가 있습니다.

```html
  <!-- In HTML: -->
  <p>
    <span class="linux">Words for Linux</span>
    <span class="macos">Words for Mac</span>
    <span class="windows">Words for Windows</span>
  </p>
  // In Dart:
  final osList = ['macos', 'windows', 'linux'];
  final userOs = determineUserOs();

  // For each possible OS...
  for (var os in osList) {
    // Matches user OS?
    bool shouldShow = (os == userOs);

    // Find all elements with class=os. For example, if
    // os == 'windows', call querySelectorAll('.windows')
    // to find all elements with the class "windows".
    // Note that '.$os' uses string interpolation.
    for (var elem in querySelectorAll('.$os')) {
      elem.hidden = !shouldShow; // Show or hide.
    }
  }
```

올바른 property을 사용할 수 없거나 편리하지 않으면 `Element`의`attributes` 속성을 사용할 수 있습니다. 이 property은 키가 attribute name 인 `Map<String, String>`입니다. attribute name과 그 의미의 목록을 보려면 [MDN Attributes page](https://developer.mozilla.org/en/HTML/Attributes)를 참조하세요. attribute value를 설정하는 예는 다음과 같습니다:

```dart
  elem.attributes['someAttribute'] = 'someValue';
```

#### Creating elements

새 element를 작성하고 DOM에 첨부하여 기존 HTML 페이지에 추가 할 수 있습니다. 다음은 paragraph (`<p>`) element를 만드는 예제입니다.

```dart
  var elem = ParagraphElement();
  elem.text = 'Creating is easy!';
```

HTML 텍스트를 구문 분석하여 `Element`를 만들 수도 있습니다. 모든 하위 element도 구문 분석되고 작성됩니다.

```dart
  var elem2 = Element.html(
    '<p>Creating <em>is</em> easy!</p>',
  );
```

`elem2`는 앞의 예제에서 `ParagraphElement`입니다.

요소에 부모를 지정하여 새로 만든 요소를 document에 첨부합니다. 기존 요소의 하위 요소에 요소를 추가 할 수 있습니다. 다음 예제에서 `body`는 요소이고, 그 자식 요소는 `children` property으로부터 접근 가능합니다 (`List<Element>`처럼).

```dart
  document.body.children.add(elem2);
```

#### Adding, replacing, and removing nodes

`element`는 일종의 node 일뿐입니다. `List<Node>`를 반환하는 `Node`의 `nodes` property를 사용하여 node의 모든 children을 찾을 수 있습니다 (element가 아닌 node를 생략하는 `children`과 반대). 이 list가 있으면 일반 `List` 메소드와 연산자를 사용하여 `Node`의 하위 항목을 조작 할 수 있습니다.

`node`를 부모의 마지막 자식으로 추가하려면 `List`의 `add()`메소드를 사용하세요.

```dart
  querySelector('#inputs').nodes.add(elem);
```

Node를 교체하려면 `Node`의 `replaceWith()`를 사용하세요:

```dart
  querySelector('#status').replaceWith(elem);
```

Node를 제거하려면 `Node`의 `remove()`메소드를 사용하십시오 :

```dart
  // Find a node by ID, and remove it from the DOM.
  querySelector('#expendable').remove();
```

#### Manipulating CSS styles

CSS 또는 *cascading style sheet*는 DOM 요소의 presentation style을 정의합니다. ID 및 class attribute을 요소에 연결하여 요소의 모양을 변경할 수 있습니다.

각 요소는 `class` 필드를 가지고 있습니다. 이 컬렉션에서 문자열을 추가 및 제거하기만하면 CSS class를 추가 및 제거 할 수 있습니다. 예를 들어, 다음 예제는 요소에 `warning` class를 추가합니다 :

```dart
  var elem = querySelector('#message');
  elem.classes.add('warning');
```

ID로 요소를 찾는 것이 종종 매우 효율적입니다. `id` property으로 요소 ID를 동적으로 설정할 수 있습니다 :

```dart
  var message = DivElement();
  message.id = 'message2';
  message.text = 'Please subscribe to the Dart mailing list.';
```

이 예제에서 중복된 텍스트를 줄이려면 cascades 메소드를 사용할 수 있습니다.

```javascript
  var message = DivElement()
    ..id = 'message2'
    ..text = 'Please subscribe to the Dart mailing list.';
```

요소를 style set와 연결하는데 ID와 class를 사용하는 것이 가장 좋지만 때로는 특정 style을 요소에 직접 첨부하려고 합니다.

```javascript
  message.style
    ..fontWeight = 'bold'
    ..fontSize = '3em';
```

#### 이벤트 다루기 (Handling events)

클릭, 포커스 변경 및 선택과 같은 외부 이벤트에 응답하려면 이벤트 리스너를 추가하세요. 페이지의 모든 요소에 이벤트 리스너를 추가 할 수 있습니다. 이벤트 보내기 및 전파는 복잡한 주제입니다. 웹 프로그래밍을 처음 접한다면 [research the details](http://www.w3.org/TR/DOM-Level-3-Events/#dom-event-architecture)르 보세요.

`*element*.on*Event*.listen(*function*)`을 사용하여 이벤트 핸들러를 추가하세요. `*Event*`는 이벤트 이름이고`*function*`은 이벤트 핸들러입니다.

예를 들어 버튼 클릭 수를 처리하는 방법은 다음과 같습니다.

```javascript
  // ID로 버튼을 찾고 이벤트 핸들러를 추가 합니다.
  querySelector('#submitInfo').onClick.listen((e) {
    // 버튼이 클릭되면 코드가 실행 됩니다.
    submitData();
  });
```

이벤트는 DOM 트리를 통해 위아래로 전파 될 수 있습니다. 어떤 요소가 원래 이벤트를 시작했는지 알아 내려면`e.target`을 사용하십시오 :

```javascript
  document.body.onClick.listen((e) {
    final clickedElem = e.target;
    // ...
  });
```

이벤트 리스너를 등록 할 수있는 모든 이벤트를 보려면 [Element](https://api.dartlang.org/stable/dart-html/Element-class.html)의 API 문서에서 "`onEventType`"속성을 찾습니다. ) 및 해당 하위 클래스. 일반적인 이벤트는 다음과 같습니다.

- `change`
- `blur`
- `keyDown`
- `keyUp`
- `mouseDown`
- `mouseUp`

###  HttpRequest로 HTTP 리소스 사용하기 (Using HTTP resources with HttpRequest)

이전의 `XMLHttpRequest`로 알려진 [HttpRequest](https://api.dartlang.org/stable/dart-html/HttpRequest-class.html) 클래스를 사용하면 브라우저 기반 앱에서 HTTP 리소스에 액세스 할 수 있습니다. 전통적으로 AJAX 스타일 앱은 `HttpRequest`를 많이 사용합니다. `HttpRequest`를 사용하여 웹 서버에서 JSON 데이터나 다른 리소스를 동적으로 로드하세요. 웹 서버에 동적으로 데이터를 보낼 수도 있습니다.

#### 서버에서 데이터 받기 (Getting data from the server)

`HttpRequest` 정적 메소드 `getString()` 은 웹 서버로부터 데이터를 얻는 쉬운 방법 입니다. `getString()`호출과 함께 `await`을 사용하여 실행을 계속하기 전에 데이터를 가지고 있는지 확인하세요.

```dart
  Future main() async {
    String pageHtml = [!await HttpRequest.getString(url);!]
    // pageHtml로 무언가 합니다...
  }
```

try-catch를 사용하여 에러 핸들러를 지정합니다.

```javascript
  try {
    var data = await HttpRequest.getString(jsonUri);
    // 데이터 처리...
  } catch (e) {
    // 예외 처리...
  }
```

가져 오는 텍스트 데이터뿐만 아니라 `HttpRequest`에 대한 액세스가 필요한 경우 `getString()` 대신에 정적 메소드 인 `request(`)를 사용할 수 있습니다. 다음은 XML 데이터를 읽는 예제입니다.

```dart
  Future main() async {
    HttpRequest req = await HttpRequest.request(
      url,
      method: 'HEAD',
    );
    if (req.status == 200) {
      // ULR 액세스 성공...
    }
    // ···
  }
```

전체 API를 사용하여 보다 흥미로운 사례를 처리 할 수도 있습니다. 예를 들어, 임의의 헤더를 설정할 수 있습니다.

`HttpRequest`의 전체 API를 사용하기위한 일반적인 흐름은 다음과 같습니다.

1. `HttpRequest` 객체를 만듭니다.
2. URL 을`GET` 또는 `POST`로 엽니다.
3. 이벤트 핸들러를 연결합니다.
4. 요청을 보냅니다.

예 :

```javascript
  var request = HttpRequest();
  request
    ..open('POST', url)
    ..onLoadEnd.listen((e) => requestComplete(request))
    ..send(encodedData);
```

#### 서버로 데이터 전송 (Sending data to the server)

`HttpRequest`는 HTTP 메소드 POST를 사용하여 서버에 데이터를 보낼 수 있습니다. 예를 들어, 폼(form) 핸들러에 데이터를 동적으로 제출(submit)하고자 할 수 있습니다. RESTful 웹 서비스에 JSON 데이터를 보내는 것도 또 다른 일반적인 사례 입니다.

폼 핸드러에 데이터를 제출하려면 name-value pair쌍을 URI로 인코딩 된 문자열로 제공해야 합니다. (URI 클래스에 대한 정보는 [Dart librarary tour](https://dart.dev/guides / libraries / library-tour)의 [URIs selection](https://dart.dev/guides/libraries/library-tour#uris)에 있습니다. 폼 핸들러에 데이터를 보내려면`Content-type` 헤더를 `application/x-www-form-urlencode`로 설정해야 합니다.

```dart
  String encodeMap(Map data) => data.keys
      .map((k) => '${Uri.encodeComponent(k)}=${Uri.encodeComponent(data[k])}')
      .join('&');

  Future main() async {
    var data = {'dart': 'fun', 'angular': 'productive'};

    var request = HttpRequest();
    request
      ..open('POST', url)
      ..setRequestHeader(
        'Content-type',
        'application/x-www-form-urlencoded',
      )
      ..send(encodeMap(data));

    await request.onLoadEnd.first;

    if (request.status == 200) {
      // Successful URL access...
    }
    // ···
  }
```

###  웹소켓으로 실시간으로 데이터를 보내고 받기 (Sending and receiving real-time data with WebSockets)

앱에서 웹소켓을 사용하려면, 먼저 [WebSocket](https://api.dartlang.org/stable/dart-io/WebSocket-class.html) 개체를 생성하고, 웹소캣을 통해 URL을 인자로 보내야 합니다.:

`WebSocket`을 사용하면 웹 애플리케이션이 서버와 대화식으로 데이터를 교환 할 수 있으므로 폴링이 필요하지 않습니다. 서버는 `WebSocket`을 생성하고 `ws://`로 시작하는 URL에서 요청을 수신합니다 (예: `ws://127.0.0.1:1337/ws`). `WebSocket`을 통해 전송되는 데이터는 문자열 또는 BLOB 일 수 있습니다. 종종 데이터는 JSON 형식의 문자열입니다.

웹 애플리케이션에서 `WebSocket`을 사용하려면 먼저 `WebSocket` URL을 인수로 전달하여 [`WebSocket`](https://api.dartlang.org/stable/dart-io/WebSocket-class.html) 객체를 만듭니다.

```dart
  var ws = WebSocket('ws://echo.websocket.org');
```

#### 데이터 보내기 (Sending data)

`WebSocket`에서 문자열 데이터를 보내려면, `send()`메소드를 사용하세요:

```dart
  ws.send('Hello from Dart!');
```

#### 데이터 받기 (Receiving data)

`WebSocket`에서 데이터를 수신하려면 메시지 이벤트에 대한 수신기를 등록하세요.

```dart
  ws.onMessage.listen((MessageEvent e) {
    print('Received message: ${e.data}');
  });
```

메시지 이벤트 핸들러는 [MessageEvent](https://api.dartlang.org/stable/dart-html/MessageEvent-class.html) 오브젝트를 수신합니다. 이 객체의 `data` 필드는 서버의 데이터를 가지고 있습니다.

#### 웹소켓 이벤트 다루기 (Handling WebSocket events)

앱은 다음과 같은 `WebSocket` 이벤트를 처리 할 수 있습니다: open, close, error 및 (앞에서 설명한 것처럼) message. 다음은 `WebSocket` 객체를 만들고 open, close, error 및 message 이벤트에 대한 핸들러를 등록하는 메서드의 예입니다.

```dart
  void initWebSocket([int retrySeconds = 1]) {
    var reconnectScheduled = false;

    print("Connecting to websocket");

    void scheduleReconnect() {
      if (!reconnectScheduled) {
        Timer(Duration(seconds: retrySeconds),
            () => initWebSocket(retrySeconds * 2));
      }
      reconnectScheduled = true;
    }

    ws.onOpen.listen((e) {
      print('Connected');
      ws.send('Hello from Dart!');
    });

    ws.onClose.listen((e) {
      print('Websocket closed, retrying in ' + '$retrySeconds seconds');
      scheduleReconnect();
    });

    ws.onError.listen((e) {
      print("Error connecting to ws");
      scheduleReconnect();
    });

    ws.onMessage.listen((MessageEvent e) {
      print('Received message: ${e.data}');
    });
  }
```

###  [더 많은 정보 (More information)](https://dart.dev/web/libraries)

이 섹션에서는 dart:html 라이브러리 사용의 겉을 간신히 봤습니다. 자세한 내용은 [dart:html](https://api.dartlang.org/stable/dart-html/dart-html-library.html) 문서를 참조하세요. Dart에는 보다 전문화 된 웹 API를 위한 추가 라이브러리 [web audio](https://api.dartlang.org/stable/dart-web_audio/dart-web_audio-library.html), [IndexedDB](https://api.dartlang.org/stable/dart- indexed_db / dart-indexed_db-library.html), 및 [WebGL](https://api.dartlang.org/stable/dart-web_gl/dart-web_gl-library.html)가 있습니다.

Dart 웹 라이브러리에 대한 자세한 내용은 [web library overview.](https://dart.dev/web/libraries)를 보세요.