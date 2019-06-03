[Up](./index.md)

##  Typedefs

Dart에서 문자열과 숫자가 객체인것처럼 함수도 객체 입니다. typedef 또는 function-type alias는 함수 타입에 필드 및 반환 타입을 선언 할 때 사용할 수 있는 이름을 제공합니다. typedef는 함수 타입이 변수에 지정 될 때 타입 정보를 보유합니다.

typedef를 사용하지 않는 다음 코드를 먼저 생각해 보겠습니다.

```dart
class SortedCollection {
  Function compare;

  SortedCollection(int f(Object a, Object b)) {
    compare = f;
  }
}

// 초기화, 깨진 구현.
int sort(Object a, Object b) => 0;

void main() {
  SortedCollection coll = SortedCollection(sort);

  // 우리가 아는 전부는 compare는 함수라는 것인데,
  // 함수의 타입은 무엇일까요?
  assert(coll.compare is Function);
}
```

`compare` 에 `f`를 대입하면 타입 정보가 유실됩니다. `f`의 타입은 `(Object, Object)`→`int` (여기서 →는 반환을 의미)이지만, `compare`의 타입은 `Function`입니다. 명시적 이름을 사용하고 타입 정보를 유지하도록 코드를 변경하면 개발자와 도구 모두가 해당 정보를 사용할 수 있습니다.

```dart
typedef Compare = int Function(Object a, Object b);

class SortedCollection {
  Compare compare;

  SortedCollection(this.compare);
}

// 초기화, 깨진 구현.
int sort(Object a, Object b) => 0;

void main() {
  SortedCollection coll = SortedCollection(sort);
  assert(coll.compare is Function);
  assert(coll.compare is Compare);
}
```

> **Note:** 현재 typedef는 함수 타입으로 제한됩니다. 우리는 이것이 변하기를 기대합니다.

typedef는 단순히 별칭이기 때문에 어떤 함수의 타입을 검사 할 수 있는 방법을 제공합니다. 예:

```dart
typedef Compare<T> = int Function(T a, T b);

int sort(int a, int b) => a - b;

void main() {
  assert(sort is Compare<int>); // True!
}
```

---

이전: [격리 (Isolates)](./Isolates.md)

다음: [메타데이터 (Metadata)](./metadata.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
