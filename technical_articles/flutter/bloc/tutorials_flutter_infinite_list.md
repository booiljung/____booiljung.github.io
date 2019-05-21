[Up](./index.md)

# Flutter 무한 리스트 튜터리얼 (Flutter Infinite List Tutorial)

원문: [Bloc / Tutorials / Flutter / Infinite List](https://felangel.github.io/bloc/#/flutterinfinitelisttutorial)

![intermediate](tutorial_flutter_inifinite_list.assets/level-intermediate-orange.svg)

> 이 튜토리얼에서는 Flutter와 Bloc 라이브러리를 사용하여 사용자가 스크롤 할 때 네트워크를 통해 데이터를 가져와 로드하는 앱을 구현할 것입니다.

![demo](tutorial_flutter_inifinite_list.assets/flutter_infinite_list.gif)

<p id = "setup"/>

## 준비 (Setup)

우리는 새 이름으로 Flutter 프로젝트를 만드는 것으로 시작합니다.

```bash
flutter create flutter_infinite_list
```

그러면 `pubspec.yaml`의 내용을 다음과 같이 바꿀 수 있습니다.

```yaml
name: flutter_infinite_list
description: A new Flutter project.

version: 1.0.0+1

environment:
  sdk: ">=2.0.0-dev.68.0 <3.0.0"

dependencies:
  flutter:
    sdk: flutter
  flutter_bloc: ^0.13.0
  http: ^0.12.0
  equatable: ^0.2.0

dev_dependencies:
  flutter_test:
    sdk: flutter

flutter:
  uses-material-design: true
```

그리고, 모든 의존 패키지를 설치합니다.

```bash
flutter packages get
```

<p id="reset-api"/>

## REST API

이 데모 애플리케이션에서는 [jsonplaceholder](http://jsonplaceholder.typicode.com)를 데이터 소스로 사용합니다.

jsonplaceholder는 가짜 데이터를 제공하는 온라인 REST API입니다. 프로토 타입 제작에 매우 유용합니다.

브라우저에서 새 탭을 열고 <https://jsonplaceholder.typicode.com/posts?_start=0&_limit=2>를 방문하여 API가 반환하는 내용을 확인하세요.

```json
[
  {
    "userId": 1,
    "id": 1,
    "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
    "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
  },
  {
    "userId": 1,
    "id": 2,
    "title": "qui est esse",
    "body": "est rerum tempore vitae\nsequi sint nihil reprehenderit dolor beatae ea dolores neque\nfugiat blanditiis voluptate porro vel nihil molestiae ut reiciendis\nqui aperiam non debitis possimus qui neque nisi nulla"
  }
]
```

**Note:** 우리 url에서 GET 요청에 대한 질의 매개 변수(query parameter)로 `_start`와 `_limit`을 지정했습니다.

이제 데이터가 어떻게 생겼는지 알았으므로 모델을 만들어 보겠습니다.

<p id="data-model"/>

## 데이터 모델 (Data Model)

`post.dart`를 만들고 `Post` 객체의 모델을 만들어 보겠습니다.

```dart
import 'package:equatable/equatable.dart';

class Post extends Equatable {
  final int id;
  final String title;
  final String body;

  Post({this.id, this.title, this.body}) : super([id, title, body]);

  @override
  String toString() => 'Post { id: $id }';
}
```

`Post`는 단지 `id`, `title`, `body`를 가진 클래스입니다.

우리는 `toString` 함수를 재정의하여 나중에 `Post`를 사용자 정의 문자열로 표현합니다.

우리는 [`Equatable`](https://pub.dartlang.org/packages/equatable)을 확장하여 `Post`를 비교할 수 있습니다; 항등 연산자(equality operator)는 기본적으로 `this`와 `other`가 같은 인스턴스 인 경우에만 `true`를 반환합니다.

이제 우리는 `Post` 객체 모델을 가지므로, Business Logic Component (bloc)에 대한 작업을 시작 합니다.

<p id="post-event"/>

## PostEvent

구현에 들어가기 전에, 우리는 `PostBloc`이 무엇을 할 것인지를 정의 할 필요가 있습니다.

상위 레벨에서는 사용자 입력 (스크롤링)에 응답하고 더 많은 `Post`을 가져 와서 프리젠테이션 레이어가 표시하도록합니다. 우리의 `Event`를 만들어 보겠습니다.

우리의 `PostBloc`은 표시할 `Post`가 더 필요할 때마다 프리젠테이션 레이어에 의해 보낼  `Fetch` 한 이벤트에만 응답 할 것입니다. 우리의 `Fetch` 이벤트는 `PostEvent`의 한 타입이기 때문에 `bloc/post_event.dart`를 만들고 이벤트를 구현할 수 있습니다.

```dart
import 'package:equatable/equatable.dart';

abstract class PostEvent extends Equatable {}

class Fetch extends PostEvent {
  @override
  String toString() => 'Fetch';
}
```

다시 말하지만, 우리는 우리 이벤트의 문자열 표현을 읽기 쉽게 하기 위해 `toString`을 재정의하고 있습니다. 다시, 인스턴스를 비교할 수 있도록 [`Equatable`](https://pub.dartlang.org/packages/equatable)을 확장합니다.

요약하자면, 우리의 `PostBloc`은 `PostEvents`를 받고 `PostStates`로 변환 할 것입니다. 우리는 `PostEvents` (Fetch)를 모두 정의 했으므로 다음으로 `PostState`를 정의 하겠습니다.

<p id="poststate"/>

## PostState (Post States)

우리의 프리젠테이션 레이어는 스스로를 적절히 배치하기 위해 여러 가지 정보가 필요합니다.

- `PostUninitialized`-  게시물의 초기 배치가 로드 되는 동안 로딩 인디케이터를 렌더링할 수 있도록 프리젠테이션 레이어에 알림.
- `PostLoaded`- 프리젠테이션 레이어에 렌더링 할 내용이 있음을 알림
  - `posts`- 표시 될 `List<Post>`
  - `hasReachedMax`- 프리젠테이션 레이어에 최대 개수의 `Post`에 도달했는지 여부를 알림
- `PostError`- `Post`를 가져 오는 동안 오류가 발생했다고 프리젠테이션 레이어에 알림

이제 우리는 `bloc/post_state.dart` 파일을 만들고 아래와 같이 구현할 수 있습니다.

```dart
import 'package:equatable/equatable.dart';

import 'package:flutter_infinite_list/post.dart';

abstract class PostState extends Equatable {
  PostState([List props = const []]) : super(props);
}

class PostUninitialized extends PostState {
  @override
  String toString() => 'PostUninitialized';
}

class PostError extends PostState {
  @override
  String toString() => 'PostError';
}

class PostLoaded extends PostState {
  final List<Post> posts;
  final bool hasReachedMax;

  PostLoaded({
    this.posts,
    this.hasReachedMax,
  }) : super([posts, hasReachedMax]);

  PostLoaded copyWith({
    List<Post> posts,
    bool hasReachedMax,
  }) {
    return PostLoaded(
      posts: posts ?? this.posts,
      hasReachedMax: hasReachedMax ?? this.hasReachedMax,
    );
  }

  @override
  String toString() =>
      'PostLoaded { posts: ${posts.length}, hasReachedMax: $hasReachedMax }';
}
```

우리는 `copyWith`를 구현하여 `PostLoaded`의 인스턴스를 복사하고 0개 이상의 속성을 편리하게 업데이트 할 수 있습니다 (나중에 유용 할 것입니다).

이제 `Events`와 `States`가 구현되었으므로 `PostBloc`을 만들 수 있습니다.

하나의 import로 상태와 이벤트를 import 할 수있게 하기 위해서 우리는 배럴 파일 `bloc/bloc.dart`를 생성 할 수 있습니다 (다음 절에서 `post_bloc.dart` 를 추가 할 것입니다).

```dart
export './post_event.dart';
export './post_state.dart';
```

<p id="postbloc"/>

## PostBloc

편의를 위해 `PostBloc`은 `http.Cliet`에 직접 의존합니다. 그러나 프로덕션 애플리케이션에서 대신 api 클라이언트를 삽입하고 저장소 패턴을 사용할 수 있습니다 [아키텍쳐](./introduction_architecture.md).

`post_bloc.dart`를 만들고 빈 `PostBloc`을 만듭니다.

```dart
import 'package:bloc/bloc.dart';
import 'package:meta/meta.dart';
import 'package:http/http.dart' as http;

import 'package:flutter_infinite_list/bloc/bloc.dart';
import 'package:flutter_infinite_list/post.dart';

class PostBloc extends Bloc<PostEvent, PostState> {
  final http.Client httpClient;

  PostBloc({@required this.httpClient});

  @override
  // TODO: implement initialState
  PostState get initialState => null;

  @override
  Stream<PostState> mapEventToState(PostEvent event) async* {
    // TODO: implement mapEventToState
    yield null;
  }
}
```

**Note:** 클래스 선언에서 `PostBloc`이 `PostEvent`를 입력으로 사용하고 `PostState`를 출력한다는 것을 알 수 있습니다.

이벤트가 전달되기 전에 `PostBloc`의 상태가 될 `initialState`를 구현함으로써 시작할 수 있습니다.

```dart
@override
get initialState => PostUninitialized();
```

다음으로 `PostEvent`가 전달 될 때마다 실행될 `mapEventToState`를 구현해야 합니다.

```dart
@override
Stream<PostState> mapEventToState(PostEvent event) async* {
  if (event is Fetch && !_hasReachedMax(currentState)) {
    try {
      if (currentState is PostUninitialized) {
        final posts = await _fetchPosts(0, 20);
        yield PostLoaded(posts: posts, hasReachedMax: false);
        return;
      }
      if (currentState is PostLoaded) {
        final posts =
            await _fetchPosts((currentState as PostLoaded).posts.length, 20);
        yield posts.isEmpty
            ? (currentState as PostLoaded).copyWith(hasReachedMax: true)
            : PostLoaded(
                posts: (currentState as PostLoaded).posts + posts,
                hasReachedMax: false,
              );
      }
    } catch (_) {
      yield PostError();
    }
  }
}

bool _hasReachedMax(PostState state) =>
    state is PostLoaded && state.hasReachedMax;

Future<List<Post>> _fetchPosts(int startIndex, int limit) async {
  final response = await httpClient.get(
      'https://jsonplaceholder.typicode.com/posts?_start=$startIndex&_limit=$limit');
  if (response.statusCode == 200) {
    final data = json.decode(response.body) as List;
    return data.map((rawPost) {
      return Post(
        id: rawPost['id'],
        title: rawPost['title'],
        body: rawPost['body'],
      );
    }).toList();
  } else {
    throw Exception('error fetching posts');
  }
}
```

우리의 `PostBloc`은 `Stream<PostState>`를 반환하기 때문에 새로운 상태가 될 때마다 `yield` 됩니다. `Streams`과 다른 핵심 개념에 대한 더 자세한 정보는 [핵심 개념](introduction_core_concepts.md?id=streams)을 확인 하세요.

이제 `PostEvent`가 전달 될 때마다, `Fetch` 이벤트이고 현재 상태가 max에 도달하지 않았다면, 우리의 `PostBloc`은 다음 20 개의 `Post`을 가져올 것입니다.

우리가 최대 `Post`(100)를 넘어서 페치하려고 하면 API는 빈 배열을 반환 할 것이므로 빈 배열을 되돌려 받는다면, 우리의 bloc은 `currentReate`를`yield` 할 것이고 `hasReachedMax`를 `true`로 설정할 것입니다.

`Post`를 검색 할 수 없으면 예외가 발생하고 `PostError()`가 `yield` 됩니다.

`Post`를 검색 할 수 있다면 `PostLoaded()`를 반환하며, `Post`의 전체 목록을 가져옵니다.

우리가 만들 수 있는 최적화 중 하나는 API를 불필요하게 스패밍하지 못하도록 하기 위해 `Events`를 `debounce`하는 것입니다. 우리는 `PostBloc`에서 `transform` 메소드를 재정의함으로써 이것을 할 수 있습니다.

**Note:** `transform`을 재정의하여 `mapEventToState`가 호출되기 전에 `Stream`을 `transform` 할 수 있습니다. `distinct()`, `debounceTime()` 등의 operations을 적용 할 수 있습니다.

```dart
@override
Stream<PostState> transform(
  Stream<PostEvent> events,
  Stream<PostState> Function(PostEvent event) next,
) {
  return super.transform(
    (events as Observable<PostEvent>).debounceTime(
      Duration(milliseconds: 500),
    ),
    next,
  );
}
```

완성 된 `PostBloc`은 이제 다음과 같을 것입니다 :

```dart
import 'dart:convert';

import 'package:meta/meta.dart';
import 'package:rxdart/rxdart.dart';
import 'package:http/http.dart' as http;
import 'package:bloc/bloc.dart';

import 'package:flutter_infinite_list/post.dart';
import 'package:flutter_infinite_list/bloc/bloc.dart';

class PostBloc extends Bloc<PostEvent, Post게시물State> {
  final http.Client httpClient;

  PostBloc({@required this.httpClient});

  @override
  Stream<PostState> transform(
    Stream<PostEvent> events,
    Stream<PostState> Function(PostEvent event) next,
  ) {
    return super.transform(
      (events as Observable<PostEvent>).debounceTime(
        Duration(milliseconds: 500),
      ),
      next,
    );
  }

  @override
  get initialState => PostUninitialized();

  @override
  Stream<PostState> mapEventToState(event) async* {
    if (event is Fetch && !_hasReachedMax(currentState)) {
      try {
        if (currentState is PostUninitialized) {
          final posts = await _fetchPosts(0, 20);
          yield PostLoaded(posts: posts, hasReachedMax: false);
        }
        if (currentState is PostLoaded) {
          final posts = await _fetchPosts(currentState.posts.length, 20);
          yield posts.isEmpty
              ? currentState.copyWith(hasReachedMax: true)
              : PostLoaded(
                  posts: currentState.posts + posts, hasReachedMax: false);
        }
      } catch (_) {
        yield PostError();
      }
    }
  }

  bool _hasReachedMax(PostState state) =>
      state is PostLoaded && state.hasReachedMax;

  Future<List<Post>> _fetchPosts(int startIndex, int limit) async {
    final response = await httpClient.get(
        'https://jsonplaceholder.typicode.com/posts?_start=$startIndex&_limit=$limit');
    if (response.statusCode == 200) {
      final data = json.decode(response.body) as List;
      return data.map((rawPost) {
        return Post(
          id: rawPost['id'],
          title: rawPost['title'],
          body: rawPost['body'],
        );
      }).toList();
    } else {
      throw Exception('error fetching posts');
    }
  }
}
```

우리의 `PostBloc`을 포함하기 위해 `bloc/bloc.dart`를 업데이트하는 것을 잊지 마세요!

```dart
export './post_bloc.dart';
export './post_event.dart';
export './post_state.dart';
```

훌륭해요! 이제는 비즈니스 로직 구현을 마쳤으므로 프레젠테이션 레이어를 구현하는 것만 남았습니다.

## 프리젠테이션 레이어 (Presentation Layer)

`main.dart`에서 우리는 `main` 함수를 구현하고 루트 위젯을 렌더링하기 위해 `runApp`를 호출함으로써 시작할 수 있습니다.

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(App());
}

class App extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Infinite Scroll',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Posts'),
        ),
        body: HomePage(),
      ),
    );
  }
}
```

다음으로, 우리는 `PostBloc`에 연결하는 우리의 게시물을 보여줄 `HomePage` 위젯을 구현할 필요가 있습니다.

```dart
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  final _scrollController = ScrollController();
  final PostBloc _postBloc = PostBloc(httpClient: http.Client());
  final _scrollThreshold = 200.0;

  _HomePageState() {
    _scrollController.addListener(_onScroll);
    _postBloc.dispatch(Fetch());
  }

  @override
  Widget build(BuildContext context) {
    return BlocBuilder(
      bloc: _postBloc,
      builder: (BuildContext context, PostState state) {
        if (state is PostUninitialized) {
          return Center(
            child: CircularProgressIndicator(),
          );
        }
        if (state is PostError) {
          return Center(
            child: Text('failed to fetch posts'),
          );
        }
        if (state is PostLoaded) {
          if (state.posts.isEmpty) {
            return Center(
              child: Text('no posts'),
            );
          }
          return ListView.builder(
            itemBuilder: (BuildContext context, int index) {
              return index >= state.posts.length
                  ? BottomLoader()
                  : PostWidget(post: state.posts[index]);
            },
            itemCount: state.hasReachedMax
                ? state.posts.length
                : state.posts.length + 1,
            controller: _scrollController,
          );
        }
      },
    );
  }

  @override
  void dispose() {
    _postBloc.dispose();
    super.dispose();
  }

  void _onScroll() {
    final maxScroll = _scrollController.position.maxScrollExtent;
    final currentScroll = _scrollController.position.pixels;
    if (maxScroll - currentScroll <= _scrollThreshold) {
      _postBloc.dispatch(Fetch());
    }
  }
}
```

`HomePage`는 `PostBloc`뿐만 아니라 `ScrollController`를 유지해야하기 때문에 `StatefulWidget`입니다. `HomePageState`에서 `ScrollController`와 `PostBloc` 인스턴스를 생성합니다. 생성자에서 스크롤 이벤트에 응답 할 수 있도록 `ScrollController`에 리스너를 추가합니다. 또한 생성자에서 앱이 로드 될 때 `Post`의 첫 번째 배치(Batch)를 요청할 수 있도록 `Fetch`이벤트를 전달해야 합니다.

계속해서, 우리의 `build` 메소드는 `BlocBuilder`를 리턴합니다. `BlocBuilder`는 [flutter_bloc package](https://pub.dartlang.org/packages/flutter_bloc)의 Flutter 위젯으로 새로운 bloc 상태에 대한 응답으로 위젯 빌드를 처리합니다. 우리의 `PostBloc` 상태가 변경 될 때마다 우리의 `builder` 함수는 새로운 `PostState`와 함께 호출 될 것입니다.

`StatefulWidget`이 폐기 될 때 우리는 스스로를 정리하고 우리의 bloc을 처분해야 한다는 것을 기억해야 합니다.

사용자가 스크롤 할 때마다 사용자의 페이지 하단까지의 거리를 계산합니다. 거리가 우리의`_scrollThreshold`보다 작거나 같으면, 더 많은 게시물을 로드하기 위해 'Fetch' 이벤트를 전달합니다.

다음으로 우리는 더 많은 게시물을 로드하고 있음을 사용자에게 알려주는 `BottomLoader` 위젯을 구현해야합니다.

```dart
class BottomLoader extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      alignment: Alignment.center,
      child: Center(
        child: SizedBox(
          width: 33,
          height: 33,
          child: CircularProgressIndicator(
            strokeWidth: 1.5,
          ),
        ),
      ),
    );
  }
}
```

마지막으로 우리는 개별 `Post`를 렌더링 할 `PostWidget`을 구현해야 합니다.

```dart
class PostWidget extends StatelessWidget {
  final Post post;

  const PostWidget({Key key, @required this.post}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ListTile(
      leading: Text(
        '${post.id}',
        style: TextStyle(fontSize: 10.0),
      ),
      title: Text(post.title),
      isThreeLine: true,
      subtitle: Text(post.body),
      dense: true,
    );
  }
}
```

이 시점에서 우리는 앱을 실행할 수 있어야 하며 모든 것이 작동해야합니다. 그러나 우리가 할 수 있는 한 가지 더 있습니다.

Bloc 라이브러리를 사용하는 데 하나의 추가 보너스는 우리가 한 곳에서 모든 'Transitions'에 액세스 할 수 있다는 것입니다.

> 한 상태에서 다른 상태로의 변경을 'Transitions'이라고 합니다.

`Transition`은 현재 상태, 이벤트 및 다음 상태로 구성됩니다.

이 애플리케이션에는 하나의 Bloc만 있지만 많은 애플리케이션에서 많은 Bloc이 애플리케이션 상태의 여러 부분을 관리하는 것이 일반적입니다.

우리가 모든 `Transitions`에 대응하여 무언가를 할 수 있기를 원한다면 우리는 단순히 우리 자신의 `BlocDelegate`를 만들 수 있습니다.

```dart
import 'package:bloc/bloc.dart';

class SimpleBlocDelegate extends BlocDelegate {
  @override
  void onTransition(Bloc bloc, Transition transition) {
    super.onTransition(bloc, transition);
    print(transition);
  }
}
```

우리가 할 일은 `BlocDelegate`를 확장하고 `onTransition` 메소드를 재정의 하는 것뿐입니다.

Bloc에 우리의 `SimpleBlocDelegate`를 사용하라고 지시하기 위해서, 우리는 단지 우리의 `main` 함수를 조정할 필요가 있습니다.

```dart
void main() {
  BlocSupervisor().delegate = SimpleBlocDelegate();
  runApp(MyApp());
}
```

이제 우리의 애플리케이션을 실행할 때 Bloc `Transition`이 발생할 때마다 전환이 콘솔에 출력 된 것을 볼 수 있습니다.

실제적으로, 당신은 다른 `BlocDelegates`를 만들 수 있으며, 모든 상태 변경이 기록되기 때문에, 우리는 매우 쉽게 우리의 애플리케이션을 측정하고 모든 사용자 상호 작용과 상태 변화를 한 곳에서 추적 할 수 있습니다!

그게 전부입니다! 이제 bloc(https://pub.dartlang.org/packages/bloc) 및 [flutter_bloc](https://pub.dartlang.org/packages/flutter_bloc)을 사용하여 무한 모록을 성공적으로 구현했습니다. 우리는 프레젠테이션 계층과 비즈니스 로직을 성공적으로 분리했습니다.

우리의 `HomePage`는 `Posts`가 어디서 왔는지, 어떻게 검색되는지 알지 못합니다. 반대로, 우리의 `PostBloc`은`State`가 어떻게 렌더링되고 있는지 전혀 알지 못하며 단순히 이벤트를 상태로 변환합니다.

이 예제에 대한 전체 소스코드는 [이곳](https://github.com/felangel/Bloc/tree/master/examples/flutter_infinite_list)에서 볼 수 있습니다.

---

이전: [카운터 (Counter)](tutorials_flutter_counter.md)

다음: [로그인 (Login)](tutorials_flutter_login.md)