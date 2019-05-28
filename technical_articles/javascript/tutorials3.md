# JavaScript

### strict 모드 

strict 모드는 엄격한 모드로 번역되며 ECMAScript 5.1에서 소개 되었습니다. ECMAScrip 5.1 전에는 non-strict 모드 또는 sloppy 모드였으며 느슨한 모드로 번역됩니다.

strict 모드는 non-strict 모드와 비교하여 차이점이 있습니다.

- 기존에 무시되던 오류들을 발생시킵니다.
- 더 빠르게 동작하도록 합니다.

더 자세한 내용은 러닝 커브에 따라 소개하도록 하겠습니다.

#### 전체에 strict 모드 적용하기

전체 스크립트에 적용하려면 `'use strict';`를 다른 구문보다 먼저 삽입합니다.

```javascript
'use strict'; // 문자열 리터럴 구문에 따라 "use strict"와 동일합니다.
```

#### 함수에 strict 모드 적용하기

지정한 함수에만 적용하려면 `'use strict'`를 함수 내부에 다른 구문보다 먼저 삽입합니다.

```javascript
function f() {
	'use strict'; // strict 모드 지정.
  	function nested() { // 이 nested 함수도 strict 모드입니다.
   		...
    }
    ... // 이 곳에 위치할 구문도 strict 모드입니다.
}
```

#### 모듈에 strict 모드 적용하기 <<< 러닝 커브에 맞지 않음. JavaScript모듈을 아직 모름.

ECMAScript 2015에서 모듈이 속개 되었습니다. JavaScript 모듈은 기본적으로 strict 모드로 지정됩니다. 강제로 strict 모드이므로`'use strict'`를 지정할 필요가 없습니다.

```javascript
function strict() {
    // 모듈이기때문에 기본적으로 strict 모드 입니다.
}
export default strict;
```









## this

이 챕터는 레벨이 2 입니다. 중요하기 때문입니다.

다른 언어에서 `this`는 클래스로부터 생성되는 인스턴스의 현재 객체를 의미합니다. `this`는 인스턴스 메소드 내부에서만 참조할 수 있으며, 외부에서는 참조 할 수 없습니다. 그래서 다른 언어에서 `this`는 혼란스러울 요소가 없습니다.

JavaScript의 `this` 연산자는 다른 언어와 다르게 동작하며 대부분의 경우 함수를 어떻게 호출하느냐에 따라 결정됩니다. 이 개념은 많은 미묘한 점을 가지고 있으며 매우 어려워 보입니다.

JavaScript에서 `this`는 현재 실행의 콘테스트 입니다. JavaScript에는 4가지 실행 타입이 있습니다.

##### 함수 실행

함수는 전역입니다.

 ```javascript
alert('Hello world')
 ```

##### 메소드 실행

메소드는 함수와 다르다는 점에 주의하세요.

```javascript
console.log('Hello world')
```

##### 생성자 실행

```javascript
new RegExp('\d')
```

##### 간접 실행

```javascript
alert.call(undefined, 'Hello world')
```

각각의 실행 타입은 다른 콘텍스트를 가집니다. 이 호출시 콘텍스트에 따라 다르며, 이것은 다른 언어와 차이점입니다.

#### 함수 실행시 this

다음 함수 `f()`가 있습니다.

```javascript
function f() {
  ...
}
```

계속하여 `f()`에 대한 다음 예제

```javascript
f.call('Alice', 'Bob');
```

에서, ` 'Alice'`는 `.call()`의 첫 인자입니다. 여기서 `'Alice'`가  `this`로 주입됩니다.

계속하여 `f()`에 대한 다음 호출 예제

```javascript
f('Alice');
```

는 non-strict모드와 strict모드냐에 따라 다릅니다.

non-strict모드라면

```javascript
f('Alice');
f.call(window, 'Alice'); // 1번 라인은 1번라인의 설탕구문
```

이며, `this`는 `winodw`개체를 참조 합니다.

strict 모드라면

```javascript
f('Alice');
f.call(undefined, 'Alice'); // 1번 라인은 1번라인의 설탕구문
```

이며,  `this`는 `undefined`로 정의되지 않습니다.

다음 예제에서 `f()` 는 `person` 개체의 메소드 입니다.

```javascript
var person = {
  f: function() { ... }
}
```

다음 메소드 `person.f()`에 대한 다음 호출 예제

```javascript
person.f.call(person, 'Alice');
```

는 `person`객체가 `this`로 첫 인수로 주입됩니다.

다음 메소스 `person.f()`에 대한 다음 호출 예제

```javascript
person.f('Alice');
person.f.call(person, 'Alice'); // 1번 라인은 2번라인의 설탕구문입니다.
```

입니다.

#### 네스티드 함수에서 실수

다음 객체 `person` 이 있습니다.

```javascript
const person = {
	name: 'Alice';
    age: 65;
    work: function() {
        console.log(this == person);
        function cook() {
            console.log(this == person);
            console.log(this.name);
            return this.age * 2;
        }
        console.log(cook());
    }
};
person.work();
```

다른 언어였다면 문제가 없는 코드입니다. JavaScript에서 위 예제는 non-strict모드냐 strict 모드냐에 따라 다르며 둘 모두 오류가 있습니다.

먼저 `person` 개체를 보겠습니다.

```javascript
const person = {
	name: 'Alice';
    age: 65;
    work: function() {
		...
    }
};
person.work();
```

일단 이 코드는 non-strict 모드나 strict모드에 아무 문제가 없습니다. `this`를 참조하지 않기 때문입니다.

다음 코드

```javascript
...
	work: function() {
        console.log(this == person); // this는 person이므로 true를 표시합니다.
        function cook() {
            ...
        }
        ...
    }
...
```

이 코드도 역시 문제가 없습니다. 3번 라인 `console.log(this == person);`에서 `this`가 `person`이므로 `true`입니다.

문제는 `cook()` 함수 내부입니다.

```javascript
...
work: function() {
    ...
    function cook() {
        ...
    }
    console.log(cook());
}
...
```
`work()` 는 개체의 메소드 입니다. 그래서 `this`가 동작합니다. 하지만 `work()` 안의 `cook()`는 메소드가 아닙니다. `cook()`는 함수입니다!

7번째 라인에서 `cook()`호출은 non-strict모드냐 strict  모드냐에 따라 다릅니다! non-strict모드라면 `window`가 `this`로 주입되고, strict 모드라면 `undefined` 개체가 `this`로 주입됩니다.

non-strict 모드에서 `coom()`함수의 `this`는 `window`입니다.

```javascript
...
		function cook() {
            console.log(window == person);
            console.log(window.name);
            return window.age * 2;
        }
...
```

non-stric 모드는 `person.age`에 액세스 할 수 없고 `NaN`을 반환 합니다.

strict 모드에서 `this`는 `undefined` 입니다. 따라서 `cook()`함수는 아래와 같습니다.

```javascript
...
		function cook() {
            console.log(undefined == person);
            console.log(undefined.name);
            return undefined.age * 2;
        }
...
```

strict 모드에서 `cook()` 호출은 `TypeError`를 발생시킵니다.

이 오류를 바로 잡으려면 `cook()` 호출은 `cook.call()`로 호출되어야 하고, 명시적으로 `this`를 주입해야 합니다. 다음은 오류를 해결한 코드입니다.

```javascript
const person = {
	name: 'Alice';
    age: 65;
    work: function() {
        console.log(this == person);
        function cook() {
            console.log(this == person);
            console.log(this.name);
            return this.age * 2;
        }
        console.log(cook.call(this)); // 네스티드 함수는 cook.call(this)로 호출
    }
};
person.work();
```

#### 생성자 실행

새 예제를 시작합니다.

표현식 앞에 `new`라는 키워드가 지시되면, 생성자로 수행됩니다. 생성자로 수행되면 생성되는 객체가 `this`로 주입됩니다. 다음 `Person()` 함수가 있습니다.

```javascript
function Person(name, age) {
	this.name = name;
    this.age = age;
}
Contry.prototype.work = function() {
	console.log(this.name);
	console.log(this.age);
}
```

계속하여 `Person()` 함수를 호출시 `new`키워드를 지시하여 호출합니다.

```javascript
var person = new Person('Alice', 90);
```

함수 호출시 `new`를 지정하면 생성자 실행입니다. 

만일 `new`를 빠뜨리고 호출하면

```javascript
var person = Person('Alice', 90);
```

non-strict 모드에서 `Person()`은 

```javascript
function Person(name, age) {
	window.name = name;
    window.age = age;
}
```

로 실행될 것이고,

strict모드에서는 

```javascript
function Person(name, age) {
	undefined.name = name;
    undefined.age = age;
}
```

로 실행되게 되어 `TypeError`가 발생하게 됩니다.

#### 객체에서 메소드를 분리할때 실수

객체의 메소드는 별도의 변수로 분리할 수 있습니다.

새 예제를 시작합니다.

다음 예제는 `Person` 생성자로 `person`개체를 생성합니다.

```javascript
function Person(name, age) {
	this.name = name;
    this.age = age;
    this.work = function() {
    	console.log(this.name);
    	console.log(this.age);
    }
}
var person = new Person('Alice', 61);
```

 계속하여 

```javascript
person.work();
```

를 호출하면 문제 없이 `Alice`와 `61`이 출력 됩니다.

계속하여 다음은 `g()`는 함수를 파라미터로 받아서 호출합니다.

```javascript
function g(p) {
    p();
}
```

계속하여 이 `g()`함수에 `person.work()`를 전달합니다.

```javascript
g(person.work); // non-strict모드에서 window가 this로 주입되고,
				// strict 모드에서 undefined가 this로 주입됩니다.
```

메소드를 함수의 인수로 전달하면 함수에서는 전달 받은 변수가 함수인지 메소드인지 알지 못합니다. 그래서 함수로 간주되고 `person.work()`는 non-strict 모드냐 strict 모드냐에 따라 다른 오류를 발생하게 됩니다.

메소드를 함수에 인자로 전달할때 발생하는 오류를 해결 하려면 `.bind()` 메소드를 사용하여 콘텍스트를 강제로 지정해야 합니다.

```javascript
g(person.work.bind(person));
```

강제로 `person`객체를 함수에 바인딩하여 전달하였고 `person.work()`는 콘텍스트에 맞는 `this`를 주입할 수 있게 됩니다. 이 바인드는 다음으로 대체 할 수도 있습니다.

```javascript
g(()=>{person.work();})
```

### 클래스

자바 스크립트는 프로토타입 기반 언어입니다 (예를 들어, Java는 클래스 기반 언어입니다). ES6에서는 새로운 클래스 기반 상속 모델이 아닌 프로토 타입 기반 상속을 위한 구문적 설탕으로 의도된 JavaScript 클래스를 도입했습니다.

다른 언어의 `class`에 익숙하다면 `class`라는 단어는 실제로 오류가 발생하기 쉽습니다. 그렇다면 JavaScript 클래스가 이 기초 단계에서는 어떻게 작동하는지 가정하지 말고 완전히 다른 개념으로 간주해야 합니다.

ES6 전에는 프로타입 구문은 아래와 같습니다.

```javascript
var Person = function(name, age) {
	this.name = name;
    this.age = age;
}
Person.prototype.stringSentence = function() {
	return "Hello, my name is ${this.name} and I'm ${this.age}";
}
```

ES6의 클래스 구문은 다음과 같습니다.

```javascript
class Person {
	constructor(name, age) {
    	this.name = name;
        this.age = age;
    }
    stringSentence() {
    	return "Hello, my name is ${this.name} and I'm ${this.age}";
    }
}
const me = new Person('Alice', 12);
console.log(me.name); // Alice
console.log(me.age); // 12
console.log(me.stringSentence()); // Hello, my name is Alice, I'm 12
```

### `extends` 와 `super` 예약어

`extends` 키워드는 클래스 선언이나 클래스 표현식에 사용되어 지정한 클래스의 파생(derrived) 클래스를 만듭니다. 파생 클래스는 수퍼 클래스의 모든 속성을 상속하며 추가적으로 새 속성을 추가하거나 상속 된 속성을 수정할 수 있습니다. `super` 키워드는 생성자를 포함하여 객체의 수퍼 클래스에 대한 기능을 액세스하는데 사용됩니다.

- 생성자에서 `super` 예약어는 `this` 예약어 보다 먼저 사용되어야 합니다.
-  `super()`  호출은 수퍼 클래스의 생성자를 호출합니다. 수퍼 클래스의 생성자에 인자를 전달하려면  `super(arguments)` 처럼 호출 할 수 있습니다.
- 수퍼 클래스가 `f`라는 메소드를 가지고 있다면, `super.f()`를 사용하여 파생 클래스에서 호출 할 수 있습니다.

다음 `Person`클래스를 선언합니다.

```javascript
class Person {
  	constructor(name, age) {
    	this.name = name;
    	this.age = age;
  	}
  	getHelloPhrase() {
    	return 'Hi, I am a ${this.name} and ${this.age} years old';
  	}
}
```

계속하여 다음 파생 클래스 `Student`은 `extends`를 사용하여 `Polygon`클래스의 속성을 물려 받습니다.

```javascript
class Student extends Person { // extends 다음에 수퍼 클래스 식별자가 따라 옵니다.
  	constructor(name, age, math, physics) { // Student 클래스의 생성자입니다.
    	super(name, age); // 수퍼 클래스의 생성자를 호출합니다. 
    	this.math = math;
        this.physics = physics;
  	}
  	getCustomHelloPhrase() {
    	const personPhrase = super.getHelloPhrase(); // 수퍼클래스 메소드 호출
    	return '${personPhrase}, my math grade is ${this.math} and physics is ${this.physics}';
  	}
  	get average() { // 파생클래스의 메소드입니다.
    	return this.math * this.physics / 2;
  	}
}
```

다시 비교하여 살펴 보겠습니다. `Person` 의 생성자는 `name`, `age` 2개의 매개 변수를 받고,

```javascript
class Person {
  	constructor(name, age) {
	...
```

 `Student` 의 생성자는 `name`, `age`, `math`, `physics` 4개의 매개변수를 받습니다.

```javascript
class Student extends Person {
  	constructor(name, age, math, physics) {
    ...
```

`Student`생성자에서 `super(...)`를 통해 수퍼클래스 생성자를 호출하여 `Person`클래의 생성자에 `name`, `age` 2개의 매개변수를 전달 합니다.

```javascript
class Student extends Person {
  	constructor(name, age, math, physics) {
    	super(name, age);
        ...
```

계속하여 `Student` 개체를 생성하고 실행합니다.

```javascript
const sa = new Student('Alice', 58, 90, 80); // Student
console.log(sa.name) // Alice
console.log(sa.getHelloPhrase()) // 'Hi, I am a Alice and 58 years old'
console.log(sa.getCustomHelloPhrase()) // 'Hi, I am a Alice and 58 years old, my math grade is 90 and physics is 80'
console.log(sa.average) // 85
```

`sa`는 `Student`의 인스턴스입니다. `Student`는 `Person`을 상속했습니다.

2번째 라인을 보면 `sa`는 수퍼 클래스의 프로프터 `name` 을 액세스 하고 있습니다. 파생 클래스가 수퍼 클래스의 특징을 상속하기 떄문입니다. 

3번째 라인은 수퍼 클래스의 메소드를 호출합니다.

4번째 라인은 파생 클래스의 메소드를 호출하고 있습니다.

5번쨰 라인도 파생 클래스의 메소드를 호출하고 있습니다.

---

## 참조

- [Modern JavaScript Cheatsheet](https://github.com/mbeaudru/modern-js-cheatsheet)

- [Make your JavaScript code shide knockout old es5 hack](https://rainsoft.io/make-your-javascript-code-shide-knockout-old-es5-hack/)

- [Mozilla JavaScript](https://developer.mozilla.org/ko/docs/Web/JavaScript)