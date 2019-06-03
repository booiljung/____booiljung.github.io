[Up](./index.md)

##  제너레이터 (Generators)

값의 시퀀스를 지연하여 생성하려면 generator 함수를 사용하는 것을 고려하세요. Dart에는 두 종류의 제널이터 함수에 대한 지원이 내장되어 있습니다.

-  **동기** 제너레이터: **Iterable** 개체를 반환.
-  **비동기** 제너레이터: **Stream**개체를 반환.

동기 제너레이터 함수를 구현하기 위해, 함수 바디에 `sync*`를 표시하고, `yeild` 문을 사용하여 값을 전달하세요.

```dart
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n)
    yield k++;
}
```

**비동기** 제너레이터 함수를 구현하려면 함수 본문을 `async*`로 표시하고 `yield` 문을 사용하여 값을 전달하세요 :

```dart
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n)
    yield k++;
}
```

제너레이터가 재귀적이라면 `yield*` 를 사용하여 성능을 향상시킬 수 있습니다:

```dart
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```

---

이전: [비동기 지원 (Asynchorony support)](./asynchrony_support.md)

다음: [Callable classes](./callable_classes.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
