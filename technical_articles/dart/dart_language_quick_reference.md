# Dart 언어 빨리 보기

이 글은 이미 다른 프로그래밍 언어를 알고 있는 개발자가 Dart를 빠르게 살펴 볼 수 있게 하기 위해 작성하였습니다. 이 글은 첫 프로그래밍 언어를 배우기에 적합하지 않습니다.

## 클래스

#### 클래스 선언

```dart
class MyClass {
}
```

#### 클래스 상속

```dart
class Employee extends Person {
}
```

#### 라이브러리 내부 클래스

```dart
class _InternalClass {
}
```

#### 인스턴스 변수

```dart
class Point2 {
	num x; // null로 초기화 됩니다.
	num y = 0; // 0으로 초기화 됩니다.
}
```

#### 생성자

```dart
class Point2 {
	num x, y;
	Point(num x, numy) {
        this.x = x;
        this.y = y;
	}
}
```

