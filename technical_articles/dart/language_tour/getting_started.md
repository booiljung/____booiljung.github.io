[Up](./index.md)

## 시작 (Getting started)

정수 42를 콘솔에 인쇄하는 짧은 Dart 코드를 작성해 보겠습니다.

```dart
// 함수를 선언 합니다.
printInteger(int aNumber) {
	print('The number is $aNumber.');
}

// 앱의 실행 시작점 입니다.
main() {
	var number = 42;
    printInteger(number);
}
```

다음은 이 프로그램이 사용하는 모든 (또는 거의 모든) Dart 앱에 적용되는 내용입니다.

`// 이것은 주석입니다. `

한 줄 주석입니다. Dart 는 또한 여러 줄 및 문서 주석을 지원합니다. 자세한 내용은 [주석](./comments.md)을 보세요.

`int`

타입입니다. 다른 [builtin types](built_in_types.md)은 `String`, `List`,  `bool` 등이 있습니다.

`42`

숫자 리터럴 입니다. 숫자 리터럴은 컴파일 타임 상수 입니다.

`print()`

디스플레이에 출력하기 위한 수단입니다.

`'...'` (또는 `"..."`)

문자열 리터럴입니다.

`$variableName` (또는 `${expression}`)

문자열 interpolation: 문자열 리터럴 내에서 변수 또는 표현식의 문자열을 포함합니다. 자세한 내용은  [Strings](built_in_types.md#strings)를 보세요.

`main()`

특별한 필수 최상위 함수로 앱 실행시 시작점입니다. 더 자세한 내용은 [main() 함수](functions.md#main) 을 보세요.

`var`

타입을 지정하지 않고 변수를 선언하는 방법 입니다.

<p id="important-concepts"/>

### 주요 컨셉 (Important concepts)

- 변수에 넣을 수 있는 모든 것은 객체이며 모든 객체는 클래스의 인스턴스입니다. 숫자, 함수 및 `null`은 객체입니다. 모든 객체는 `Object` 클래스에서 상속 받습니다.
-  Dart가 강력하게 타입화 되어 있지만, Dart는 타입을 유추 할 수 있으므로, 타입 주석(annotation)은 선택 사항입니다. 위의 코드에서 `number`는 `int` 타입으로 유추됩니다. 만일 타입이 없다고 명시적으로 지시하려면 특수한 타입인 `dynamic`을 사용하세요.
- Dart는 `List<int>` (정수 리스트) 또는 `List<dynamic>` (동적 타입의 개체 리스트)와 같은 제네릭 타입을 지원합니다.
- Dart는 최상위 함수 (top level function; 예를 들어: `main()`)와 클래스 또는 객체 (정적 메소드 및 인스턴스 메소드)에 연결된 함수를 지원합니다. 함수내에서 함수(중첩; nested 또는 로컬 함수)를 만들 수도 있습니다.
- 마찬가지로 Dart는 클래스 또는 객체 (정적 변수 및 인스턴스 변수)에 연결된 변수뿐만 아니라 최상위 변수(top level variable)도 지원합니다. 인스턴스 변수는 필드(field) 또는 속성(property)이라고도합니다.
-  Java와 달리 Dart에는 public, protected 및 private 키워드가 없습니다. 식별자가 밑줄 (`_`)로 시작하면 해당 라이브러리에 대해 비공개입니다. 자세한 내용은 [라이브러리 및 가시성](./libraries_and_visibility.md)을 참조하세요.
- 식별자는 문자 또는 밑줄 (`_`)로 시작하고 그 뒤에 문자와 숫자의 조합이 올 수 있습니다.
-  Dart에는 실행 값이 있는 표현식과 실행되지 않는 명령문이 있습니다. 예를 들어, 조건식 `condition ? 표현식1 : 표현식2`는 `표현식1` 또는 `표현식2` 값을 가집니다. 이를 값이 없는 if-else 문과 비교하면 명령문에는 하나 이상의 표현식이 포함되는 경우가 많지만 표현식에 명령문을 직접 포함 할 수는 없습니다.
-  Dart 도구는 경고 및 오류의 두 가지 종류의 문제를 보고 할 수 있습니다. 경고는 코드가 작동하지 않을 수도 있지만 프로그램 실행을 방해하지는 않습니다. 오류는 컴파일 타임 또는 런타임 중 하나 일 수 있습니다. 컴파일 타임 오류로 인해 코드가 전혀 실행되지 않습니다. 런타임 오류로 인해 코드가 실행되는 동안 예외가 발생합니다.

<p id="keywords"/>

### 예약어 (Keywords)

예약어는 다음과 같습니다.

```dart
abstract 		dynamic	 		implements  	show
as				else		 	import			static
assert 			enum 			in 				super
async 	 		export  		interface  		switch
await  			extends 		is 				sync
break 			external  		library  		this
case 			factory  		mixin  			throw
catch 			false 			new 			true
class 			final 			null 			try
const 			finally 		on  			typedef 
continue 		for 			operator  		var
covariant 	 	Function 	 	part 		 	void
default 		get 		 	rethrow 		while
deferred 	 	hide 		 	return 			with
do 				if 				set 		 	yield
```

---

다음: [변수 (Variables)](./variables.md)

## 문서 변경 이력

2019년 5월 15일: 첫 작성.
