# 번역: Mock dependencies using Mockito

때때로 단위 테스트는 실제 웹 서비스 또는 데이터베이스에서 데이터를 가져 오는 클래스에 따라 달라질 수 있습니다. 이는 다음과 같은 이유로 불편합니다:

- 라이브 서비스나 데이터베이스를 호출하면 테스트 실행 속도가 느려집니다.
- 웹 서비스나 데이터베이스가 예기치 않은 결과를 반환하면 통과 테스트가 실패 할 수 있습니다. 이것은 "벗겨지기 쉬운 테스트"라고 합니다.
- 실제 웹 서비스 또는 데이터베이스를 사용하여 성공 및 실패 시나리오를 모두 테스트하는 것은 어렵습니다.

따라서 라이브 웹 서비스나 데이터베이스에 의존하기보다는 이러한 종속성을 "조롱"할 수 있습니다. Mocks는 실제 웹 서비스나 데이터베이스를 에뮬레이션하고 상황에 따라 특정 결과를 반환 할 수 있습니다.

일반적으로 클래스의 대체 구현을 작성하여 종속성을 조롱 할 수 있습니다. 이러한 대체 구현을 직접 작성하거나 [Mockito package](https://pub.dev/packages/mockito)를 단축키로 사용하십시오.

이 방법은 다음 단계를 사용하여 Mockito 패키지로 조롱하는 기본 사항을 보여줍니다.

1. Add the package dependencies.
2. Create a function to test.
3. Create a test file with a mock `http.Client`.
4. Write a test for each condition.
5. Run the tests.

자세한 내용은 [Mockito](https://pub.dev/packages/mockito) 패키지 설명서를 참조하십시오.

##  1. Add the package dependencies

 `mockito` 패키지를 사용하려면 `dev_dependencies` 섹션에서 `flutter_test` 의존성과 함께 `pubspec.yaml` 파일에 추가하십시오.

이 예제는 또한 `http` 패키지를 사용하므로 `dependencies` 섹션에서 의존성을 정의하십시오.

```yaml
dependencies:
  http: <newest_version>
dev_dependencies:
  test: <newest_version>
  mockito: <newest_version>
```

##  2. Create a function to test

이 예제에서, [fetch data from the internet](https://flutter.dev/docs/cookbook/networking/fetch-data) 레시피에서`fetchPost` 함수를 단위 테스트합니다. 이 기능을 테스트하려면 두 가지 사항을 변경하십시오.

1. Provide an `http.Client` to the function. This allows providing the correct `http.Client` depending on the situation. For Flutter and server-side projects, provide an `http.IOClient`. For Browser apps, provide an `http.BrowserClient`. For tests, provide a mock `http.Client`.
2. Use the provided `client` to fetch data from the internet, rather than the static `http.get()` method, which is difficult to mock.

The function should now look like this:

 

```
Future<Post> fetchPost(http.Client client) async {
  final response =
      await client.get('https://jsonplaceholder.typicode.com/posts/1');

  if (response.statusCode == 200) {
    // If the call to the server was successful, parse the JSON.
    return Post.fromJson(json.decode(response.body));
  } else {
    // If that call was not successful, throw an error.
    throw Exception('Failed to load post');
  }
}
```

##  3. Create a test file with a mock `http.Client` 

Next, create a test file along with a `MockClient` class. Following the advice in the [Introduction to unit testing](https://flutter.dev/docs/cookbook/testing/unit/introduction) recipe, create a file called `fetch_post_test.dart` in the root `test` folder.

The `MockClient` class implements the `http.Client` class. This allows you to pass the `MockClient` to the `fetchPost` function, and return different http responses in each test.

  

```
// Create a MockClient using the Mock class provided by the Mockito package.
// Create new instances of this class in each test.
class MockClient extends Mock implements http.Client {}

main() {
  // Tests go here
}
```

##  4. Write a test for each condition

The `fetchPost()` function does one of two things:

1. Returns a `Post` if the http call succeeds
2. Throws an `Exception` if the http call fails

Therefore, you want to test these two conditions. Use the `MockClient` class to return an “Ok” response for the success test, and an error response for the unsuccessful test. Test these conditions using the `when()` function provided by Mockito:

  

```
// Create a MockClient using the Mock class provided by the Mockito package.
// Create new instances of this class in each test.
class MockClient extends Mock implements http.Client {}

main() {
  group('fetchPost', () {
    test('returns a Post if the http call completes successfully', () async {
      final client = MockClient();

      // Use Mockito to return a successful response when it calls the
      // provided http.Client.
      when(client.get('https://jsonplaceholder.typicode.com/posts/1'))
          .thenAnswer((_) async => http.Response('{"title": "Test"}', 200));

      expect(await fetchPost(client), isInstanceOf<Post>());
    });

    test('throws an exception if the http call completes with an error', () {
      final client = MockClient();

      // Use Mockito to return an unsuccessful response when it calls the
      // provided http.Client.
      when(client.get('https://jsonplaceholder.typicode.com/posts/1'))
          .thenAnswer((_) async => http.Response('Not Found', 404));

      expect(fetchPost(client), throwsException);
    });
  });
}
```

###  5. Run the tests

Now that you have a `fetchPost()` function with tests in place, run the tests.

  

```

 dart test/fetch_post_test.dart
```

You can also run tests inside your favorite editor by following the instructions in the [Introduction to unit testing](https://flutter.dev/docs/cookbook/testing/unit/introduction#run-tests-using-intellij-or-vscode) recipe.

###  Summary

In this example, you’ve learned how to use Mockito to test functions or classes that depend on web services or databases. This is only a short introduction to the Mockito library and the concept of mocking. For more information, see the documentation provided by the [Mockito package](https://pub.dev/packages/mockito).