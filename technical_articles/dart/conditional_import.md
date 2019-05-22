[Up](./index.md)

# 번역: Dart 2 조건부 임포트의 업데이트

원문: [Data 2 conditional import update](https://medium.com/@dvargahali/dart-2-conditional-imports-update-16147a776aa8)

Dart 2가 잠시 출시되면 Dart 1과 마찬가지로 조건부 임포트가 여전히 작동하는지 확인해야합니다. 이 글의 내용을 확인할 수 있습니다.

<https://medium.com/@dvargahali/dart-vm-tests-with-dart-html-yes-you-can-c15439e49cc1>

#### Dart 1의 방법

나는 다음처럼 import/export 합니다:

```dart
import "something.dart" if (condition) "other.dart";
```

그리고 Dart VM에 대한 간단한`-D`  명령 행 인자를 사용하여 코드를 실행합니다.

```
dart -Dcondition=true myapp.dart
```

#### Dart 2의 방법

이 이슈  https://github.com/dart-lang/sdk/issues/34262 에 따라 조건부 임포트는 이유로 문서화 되지 않은 기능이 었습니다. 그들은 다른 기능을 제거하면서 그것의 일부분을 계속 유지했습니다.

당신이 할 수 있는 일은 Dart VM에 의해 정의된 조건에 기반한 기능을 사용하는 것입니다. 이것들은 라이브러리의 존재를 확인하는데 제약이 있습니다.

```dart
import "something.dart" if (dart.library.io) "other.dart"
```

You can check [this example](https://github.com/sass/dart-sass/blob/3b6730369bdff9bb7859411c82e4b21f194d2514/lib/src/io.dart) of dart-sass library for a production use-case.

What you **no longer can do** is use custom conditions. Most of what I wrote in my previous article no longer applies. `-D`  is no longer taken into consideration. This was confirmed by Dart Team  members and support for user defined conditions is unlikely to land in  Dart.



프로덕션 유즈 케이스의 경우 dart:sass 라이브러리의 [이 예제](https://github.com/sass/dart-sass/blob/3b6730369bdff9bb7859411c82e4b21f194d2514/lib/src/io.dart)를 확인할 수 있습니다.

이상 할 수 없는 것은 사용자 정의 조건을 사용하는 것입니다. 이전 글에서 내가 썼던 대부분은 더 이상 적용되지 않습니다. `-D`는 더 이상 고려되지 않습니다. 이는 Dart 팀 구성원이 확인했으며 사용자 정의 조건에 대한 지원은 Dart에 안착하기 어렵습니다.

#### 업그레이드 팁

필자의 경우에는 배포 대상을 확인하는 것이 필요했습니다. 브라우저 또는 VM. 이것은 `dart.library.io` 또는`dart.library.js`가 정의되어 있는지 확인함으로써 여전히 할 수 있습니다.

내 다른 유스 케이스는 구현 클래스를 팩토리에 래핑하고 일부 환경 변수와 연결하여 해결할 수 있습니다.

```dart
import "dart:io";

void main() {
  print(new SomeFactory().message);
}

abstract class SomeFactory {  
  String get message;
  
  factory SomeFactory() {
    if(Platform.environment['SOME_VAR'] != null)
      return new _SomeImplementation();
    return new _SomeOtherImplementation();
  }  
}

class _SomeImplementation implements SomeFactory {  
  @override
  String get message => 'SomeImplementation';  
}

class _SomeOtherImplementation implements SomeFactory {  
  @override
  String get message => "OtherImplementation";  
}
```

---

## 참조

- [Dart 2 conditional imports update](https://medium.com/@dvargahali/dart-2-conditional-imports-update-16147a776aa8)