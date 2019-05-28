# JavaScript

### 참 또는 거짓

JavaScript에서 참 또는 거짓 값은 부울 상황에서 평가 될 때 부울로 캐스팅되는 값입니다.

##### if 문

부울 컨텍스트의 예는 여러가지가 있지만 대표적으로 `if ` 문에서 조건의 평가 일 것입니다 

```javascript
if (a) {}
```

`a`는 모든 일등시민 (변수, 함수, 부울) 일 수 있지만 부울 컨텍스트에서 평가되므로 부울로 캐스팅됩니다.

조건절의 표현식이 다음 값과 같지 않으면 모든 값이 `true`로 캐스팅됩니다.

- `false`, `0`, `""` (빈문자열), `null`, `undefined`, `NaN`

##### `!` 연산자 다음

이 연산자는 단일 피연산자가 `true`로 변환 될 수 있으면 `false`를 반환합니다. 그렇지 않은 경우는 `true`를 돌려줍니다.

예제를 보겠습니다.

```javascript
!0
```

`0`이 falsy이므로 `!0` 표현식의 결과는 truthy입니다.

```javas
!!0
```

위 표현식의 연산 우선 순위는 아래와 같습니다.

```javascript
!(!(0))
```

`0`에서부터 시작하여 멀어지는 방향으로 연산됩니다.

1. `0`은 falsy입니다.

2. `!0`은 not falsy으로 결과는 truthy입니다.

3. `!!0`은 not not falsy이며, not not falsy는 not truthy이며, not truthy는 falsy입니다.

##### 부울 개체의 생성자

부울개체의 생성은 `Boolean` 을 통해서 합니다.

```javascript
new Boolean(0); // false
new Boolean(1); // true
```

##### 삼항식

삼항식은 많은 언어에서도 유일한 삼항연산자입니다.

```javascript
<condition> ? <true value> : <false value>
```

`<condition>`이 truthy이면 `<true value>`가 선태되고, `<condition>`이 falsy이면 `<false value>`가 선택됩니다.

다음 예제 삼항식에서 `a`는 부울 콘텍스트로 평가되며

```javascript
var b = a ? 'truthy' : 'falsy';
```

`a` 가 참이면 `b`는 `truthy`이고, `a`가 거짓이면 `b`는 거짓입니다.

##### 주의

2 개의 값을 비교할 때 주의 해야 합니다. 객체 값(true로 캐스트되어야 함)은 부울로 형변환 되지 않지만 `ToPrimitives`을 사용하여 원시 값 1로 변환해야 합니다. 내부적으로 객체가 `[] == true`와 같은 부울 값과 비교 될 때 `[].toString () == true`이므로 ...

```javascript
let a = [] == true;
```

`a`는 거짓입니다. `[].toString()`은 `""`이고, `""`은 거짓이기 때문입니다.

다음 표현식:

```javascript
let b = [1] == true;
```

 `b`는 참입니다. `[1].toString()`은 `"1"`이고, 빈문자열이 아니면 참이기 때문입니다.

다음 표현식:

```javascript
let c = [-10] == true;
```

`c`도 참입니다. `[-10].toString()`은 `"-10"`이고, 빈문자열이 아니면 참이기 때문입니다.

### Generators

#### Anamorphisms and Catamorphisms

##### Anamorphisms

Anamorphisms는 어떤 객체에서 객체의 타입을 포함하는 보다 복잡한 구조로 매핑하는 함수입니다. 간단한 구조를보다 복잡한 구조로 전개하는 과정입니다.

다음 정수를 정수 목록으로 전개하는 예제를 보겠습니다.

```javascript
function downToOne(n) {
	const list = [];
    for (let i = n; i > 0; --i) {
		list.push(i);
	}
	return list;
}
downToOne(5) // [ 5, 4, 3, 2, 1 ]
```

 `downToOne()`은 정수를 매개변수로 받아서, `list` 이름으로 배열을 선언하고, 해당 갯수만큼의 원소를 가진 정수 리스트를 반환합니다.

##### Catamorphisms

Catamorphisms는 Anamorphisms과 반대되는데, 그들은 더 복잡한 구조의 대상을 취하고 더 단순한 구조로 접을 수 있습니다. 다음 예제를 보면 `product`는 정수 리스트를 받아서 하나의 정수를 반환 합니다.

```javascript
function product(list) {
	let product = 1;
	for (const n of list) {
    	product = product * n;
  	}
  	return product;
}
product(downToOne(5)) // 120
```

#### Generators

위의 예제 `downToOne`을 다시 보겠습니다.

```javascript
function downToOne(n) {
	const list = [];
    for (let i = n; i > 0; --i) {
		list.push(i);
	}
	return list;
}
```

`downToOne`은 배열이 있고, 배열에 `push`도 하며 복잡한 코드 입니다.

`Generator`를 사용하여 더 간단한 구조로 동일한 결과를 낼 수 있습니다. 함수나 메소드가 Generator가 되려면  `function*`선언을 사용해야 합니다.

```javascript
function* downToOne(n) {
	...
}
```

이제 `downToOne()`은 반복가능한 개체(반복자; iterator)를 생성하는 Generator입니다. Generator 는 함수나 메소드안에서 `yield`구문으로 반복자를 생성합니다. 위 `downToOne()`은 반복자를 반환하도록 아래와 같이 다시 작성할 수 있습니다.

```javascript
function* downToOne(n) {
	for (let i = n; i > 0; --i) {
    	yield i;
  	}
}
```

list 선언과 push가 제거 되었습니다.

반환된 반복자는 스프레드하여 다시 배열에 넣을 수 있습니다.

```javascript
[...downToOne(5)] // [ 5, 4, 3, 2, 1 ]
```

위 코드는 반복자를 명시하여 작성될 수 있습니다.

```javascript
iterator = downToOne(5); // [ 5, 4, 3, 2, 1 ]
[...iterator]
```

Generator가 반환한 반복자는 `next()` 메소드가 있으며 이 메소드가 호출되면 차례대로 원소들을 이동하고, `value`프로퍼티로 값을 얻을 수 있습니다. 모두 반환하면 `undefined`를 반환합니다.

다음은 `idMaker()`는 2회 반복하는 예제입니다.

```javascript
function* idMaker(count) {
    int index = 0;
  	while (index < count) {
    	yield index;
    	index = index + 1;
  	}
}
```

`function* idMaker()`는 반복자를 반환니다. 계속하여

```javascript
var gen = idMaker(2);
```

`iterator`은 `idMaker(2)`가 생성한 반복자로 2개의 원소를 반환할 수 있습니다.

계속하여  `iterator`의 메소드 `next().value`를 호출하여 반환값을 얻습니다.

```javascript
iterator.next().value; // 0
iterator.next().value; // 1
iterator.next().value; // undefined
```

반복자가 3회 호출되면 `undefined`를 반환 하였습니다.

##### Generator 겹침

`yield*` 표현식을 사용하면 제너레이터는 반복 중에 다른 제너레이터는 함수를 호출 할 수 있습니다. 새 예제를 보겠습니다:

```javascript
function* genBob(i) {
  yield i + 1;
  yield i + 2;
  yield i + 3;
  yield i + 4;
}

```

`genBob`는 4회 반환하는 반복자를 생성합니다. 계속하여 다음:

```javascript
function * genAlice(i) {
  yield i;
  yield* genBob(i);
  yield i + 10;
}
```

`genAlice`는 먼저 `i`를 반환하고, `genBob(i)`를 호출하며, 마지막에 `i+10`를 반환합니다. `genAlice()`내부에서 `genBob()`를 호출하는 구조 입니다. `genAlice()`를 호출하여 반복자를 생성한 후 

```javascript
var iterator = genAliace(10); // genAlice()의 매개변수 i에 10을 인자로 전달 하였습니다. 
iterator.next().value; // genAlice(10)의 yield i로 10을 반환합니다.
iterator.next().value; // genBob(10)의 yield i+1로 11을 반환합니다.
iterator.next().value; // genBob(10)의 yield i+2로 12를 반환합니다.
iterator.next().value; // genBob(10)의 yield i+3으로 13을 반환합니다.
iterator.next().value; // genBob(10)의 yield i+4으로 14을 반환합니다.
iterator.next().value; // genAlice(10)의 yield i+10으로 20을 반환합니다.
```

제너레이터에서 반복을 중단하기 위해서는 `return`을 합니다. 다음 새 예제를 보겠습니다.

```javascript
// Generator Return Example
function* yieldAndReturn() {
  yield "Alice";
  return "Bob";
  yield "Charlie";
}
```

`return` 문이 사용된 것을 볼 수 있습니다. 이 제너레이터에서 반복자를 얻고 결과를 보겠습니다. 계속하여:

```javascript
var iterator = yieldAndReturn()
iterator.next().value; // Alcie
iterator.next().value; // Bob
iterator.next().value; // undefined로 더이상 원소가 없습니다. Charlie는 반환되지 않습니다.
```

반복자는 `value` 프로퍼티를 통해 값을 얻고, `done`프로퍼티를 통해 끝을 얻습니다. 계속하여:

```javascript
var iterator2 = yieldAndReturn()
gen.next(); // { value: 'Alice', done: false }
gen.next(); // { value: 'Bob', done: true }
gen.next(); // { value: undefined, done: true }
```

반복자의 반환값의 프로퍼티 `done`의 값이 true이면 반복자의 끝에 도달했음을 알 수 있습니다.

### Static Methods정적 메소드

#### 간단한 설명

static 키워드는 클래스에서 정적 메소드를 선언하는 데 사용됩니다. 정적 메소드는 클래스 객체에 속하며 해당 클래스의 인스턴스의 멤버를 참조 할 수 없습니다. 다음 새 예제를 보겠습니다.

```javascript
class Employee {
	static getName() {
		return "Empolyee1";
    }
}
```

`Empolyee`클래스는 정적 메소드 `getMethod()`를 가지고 있습니다. 계속하여

```javascript
console.log(Employee.getName()); // Empolyee1
```

`클래스이름.메소드이름()`으로 정적 메소드를 호출 할 수 있습니다.

인스턴스를 생성하고 인스턴스 멤버로 호출하면:

```javascript
let e = new Employee();
console.log(e.getName()) // TypeError
```

메소드가 아니므로 `TypeError`이 발생합니다.

### 정적 메소드의 this

정적 메소드는 `this` 키워드를 사용하여 다른 정적 메소드 내에서 호출 할 수 있습니다. 다음 새 예제는 정적 메소드에서 다른 정적 메소드를 호출합니다.

```javascript
class Customer {
    static work1() {
    	return "customer1";
  	}
  	static work2() {
    	return this.work1() + ' customer2'; // this로 다른 정적 메소드 work1을 호출합니다.
  	}
}
console.log(Customer.work2()); // customer1 customer2
```

정적 메소드에서 `this` 키워드를 사용하여 비정적 메소드를 호출할 수 없습니다. 다음 새 예제는 정적 메소드에서 비정적 메소드를 호출합니다.

```javascript
class Animal {
    eat1() {
        return "animal1";
    }
    static eat2() {
        return this.eat1() + " animal2"; // 오류! 정적 메소드에서 this로 다른 비정적 메소드 eat1을 호출합니다.
    }
}
console.log(Animal.eat2());
```

비정적 메소드에서 `this`키워드를 사용하여 정적 메소드를 호출 할 수도 없습니다. 다음 새 예제는 비정적 메소드에서 정적 메소드를 호출합니다.

```javascript
class Fish {
    static swim1() {
        return "fish1";
    }
    swim2() {
        return this.swim1() + " fish2"; // 오류! 비정적 메소드에서 this로 다른 정적 메소드를 호출 할 수 없습니다.
    }
}
console.log(Fish.swim2());
```

비정적 메소드는 `this` 키워드를 사용하여 정적 메소드에 직접 액세스 할 수 없습니다. 비정적 메소드에서 

#### 클래스 메소드로 호출

비정적 메소드에서 정적 메소드를 호출하려면 클래스 메소드를 호출하는 구문으로 호출해야 합니다.

```javascript
class Rabbit {
  static jump1() {
    return "rabbit1"
  }
  jump2(){
    return Rabbit.jump1() + ' rabbit2'
  }
}
```

계속하여 `Rabbit` 인스턴스를 만들고 비정적 메소드 `jump2()`를 호출합니다.

```javascript
let r = new Rabbit()
console.log(r.jump2()); // rabbit1 rabbit2
```

#### 생성자로 호출

정적 메서드는 `constructor`의 프로퍼티로 호출 할 수 있습니다.

```javascript
class Horse {
	static gallop1() {
    	return "horse1"
  	}
  	gallop2(){
    	return this.constructor.gallop1() + ' horse2';
  	}
}
let h = new Horse()
console.log(h.gallop2()) // horse1 horse2
```

생성자를 통해서 호출하면 클래스 이름을 명시하지 않으므로 리팩토링시 유리합니다.











#### 화살표 함수

- 주변의 `this`가 포착됩니다.

화살표 함수에서 `this` 값은 둘러싼 실행 컨텍스트의 `this` 값과 같습니다. 기본적으로 화살표 함수를 사용하면 "this = this" 트릭을 수행 할 필요가 없습니다.

```javascript
f = function() {
    this.a = 1;
    setTimeout(() => {
        this.a++;
        console.log(this.a); // 2
    }, 0)
}
f();
```





this reference
To understand this subtlety introduced with arrow functions, you must know how this
behaves in JavaScript.
In an arrow function, this is equal to the this value of the enclosing execution context. What it
means is that an arrow function doesn’t create a new this, it grabs it from its surrounding
instead.
Without arrow function, if you wanted to access a variable from this in a function inside a
function, you had to use the that = this or self = this trick.
For instance, using setTimeout function inside myFunc:
function myFunc() {
this.myVar = 0;
var that = this; // that = this trick
setTimeout(
function() { // A new *this* is created in this function scope
that.myVar++;
console.log(that.myVar) // 1
console.log(this.myVar) // undefined -- see function declaration above
},
0
);
}
But with arrow function, this is taken from its surrounding:
function myFunc() {
this.myVar = 0;
setTimeout(() => { // this taken from surrounding, meaning myFunc here
this.myVar++;
console.log(this.myVar) // 1
},
0
);
}







`person`은 `address`  항목이 없습니다. 계속합니다.

```javascript
const first = person.firstName; // Alice
const age = person.age; // 67
const city = person.city || 'Seoul';
console.log(city); // Seoul
```

계속하여 소멸을 하면 정의되지 않는 변수는 `undefined`가 됩니다. 계속합니다.

```javascript
const {
    firstName: first,
    age, 
    city = 'Seoul'
} = person;
console.log(age); // 35
console.log(first); // Alice
console.log(firstName); // undefined. person은 멤버이므로 존재하지 않습니다.
console.log(city); // Seoul
```

계속하여 소멸을 하지 않은채...

```javascript
function f(p) {
	const firstName = p.firstName;
	const lastName = p.lastName;
	return firstName + ' ' lastName;
}
console.log(f(person)); // Alice Washington
```

계속하여 아래처럼 단순하게 할 수 있습니다.

```javascript
function f({firstName, lastName}) {
	return firstName + ' ' + lastName;
}
console.log(f(person)); // Alice Washington
```

계속하여 화살표 함수로 표현할 수 있습니다.

```javascript
const f = ({ firstName, lastName }) => firstName + ' ' + lastName;
console.log(f(person)); // Alice Washington
```



---

## 참조

- [Modern JavaScript Cheatsheet](https://github.com/mbeaudru/modern-js-cheatsheet)

- [Make your JavaScript code shide knockout old es5 hack](https://rainsoft.io/make-your-javascript-code-shide-knockout-old-es5-hack/)

- [Mozilla JavaScript](https://developer.mozilla.org/ko/docs/Web/JavaScript)