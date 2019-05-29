[Up](./index.md)

### 변수 (Variables)

변수는 다음과 같이 생성하고 초기화 합니다.

```dart
var name = 'Bob';
```

변수를 문자열로 초기화 하였으므로 컴파일러는 유추하여 `String`타입으로 컴파일을 하게 됩니다.

특별한 타입을 지정하지 않고 변경 하려면 `dyanmic`을 지정합니다. `dynamic`은 컴파일러가 유추하여 지정하는 `var`와  다른 점에 주의 해야 합니다.

```dart
dynamic name = 'Bob';
name = 45;
```

아래처럼 타입을 명시적으로 지정할 수도 있으며 컴파일러나 개발자가 코드를 읽을때 보다 명확해 집니다. 

```dart
String name = 'Bob';
```

<p id="default-value"/>

#### 기본값 (Default value)

코드에서 명시적으로 초기화 되지 않은 변수는 `null`로 초기화 됩니다.

```dart
int lineCount;
assert(lineCount == null);
```

<p id="final-and-const"/>

#### final과 const

변수의 값을 변경하지 않으려면 `var` 대신 또는 타입에 `final` 또는 `const`를 사용합니다. `final` 변수는 한 번만 설정할 수 있습니다. `const` 변수는 컴파일 타임 상수입니다. (`const` 변수는 암시적으로 `final` 변수입니다.) `final` 최상위 또는 클래스 변수는 처음 사용될 때 초기화됩니다.

```sh
final name = 'Bob';
final String nickname = 'Bobby';
```

변수 값을 변경하려고 하면 오류가 발생합니다.

```dart
name = 'Alice'; // 오류!
```

`const`는 컴파일 타임(compile-time) 상수입니다. `const` 변수가 클래스 수준에 있으면 정적 `const`로 표시합니다. 변수를 선언 할 때 숫자 또는 문자열 리터럴, `const` 변수 또는 상수에 대한 산술 연산의 결과와 같은 컴파일 타임 상수로 값을 설정합니다.

```dart
const bar = 1000000; // 압력 단위 (dynes/cm2)
const double atm = 1.01325  bar; // 표준 대기압
```

`const` 키워드는 상수 변수를 선언하기 위한 것이 아닙니다. 또한 이를 사용하여 상수 값을 생성하고 상수 값을 생성하는 생성자를 선언 할 수 있습니다. 모든 변수는 상수 값을 가질 수 있습니다.

```dart
var foo = const [];
final bar = const [];
const baz = []; // Equivalent to `const []`
```

위의 `baz`처럼 `const` 선언의 초기화 표현식에서 `const`를 생략 할 수 있습니다. 자세한 내용은 `const` 중복 사용 안함을 참조하십시오.

`const`가 아닌 다른 변수의 값을 변경할 수 있습니다 :

```dart
foo = [1, 2, 3]; // Was const []
```

`const` 변수의 값을 변경할 수 없습니다.

```dart
baz = [42]; // 오류!
```

---

이전: [시작하기 (Getting started)](./getting_started.md)

다음: [내장 타입 (Built in types)](./built_in_types.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
