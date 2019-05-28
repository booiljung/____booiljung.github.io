# JavaScript

### 타입

#### 객체의 프로퍼티

개체의 프로퍼티는 기본적으로 key-vaue pair 입니다. 다음 `person`개체가 있습니다.

```javascript
const person = {
	firstName: 'Alice';
    lastName: 'Washington';
    age: 67;
    sex: 'F';
}
console.log(person.firstNname); // Alice
console.log(person.lastName); // Washington
console.log(person.age); // 67
console.log(person.sex); // F
```

#### 변수를 객체의 프로퍼티로

변수를 객체 프로퍼티로 할 수 있습니다.

```javascript
const x = 'Alice';
const y = 'Bob';
const o = {
	x: x,
    y: y
}
console.log(o.x); // Alice
console.log(o.y); // Bob
```

단축하여 변수의 식별자와 값을 그대로 객체를 구성할 수 있습니다.

```javascript
const x = 'Alice';
const y = 'Bob'
const o = { x, y }
console.log(o.x); // Alice
console.log(o.y); // Bob
```

#### 배열 destruction

다음 배열이 있습니다.

```javascript
const s = ['a', 'b', 'c'];
```

계속하여 destruction하지 않은채...

```javascript
const i = s[0];
const j = s[1];
console.log(i); // a
console.log(j); // b
```

계속하여 배열을 destruction 시키면...

```javascript
const [x, y] = s;
console.log(x); // a
console.log(y); // b
```

### 관계 연산자

`>`

좌측이 크면 참입니다.

```javascript
console.log(2>1); // true
console.log(1>2); // false
```

`<`

좌측이 작으면 참입니다.

```javascript
console.log(2<1); // false
console.log(1<2); // true
```

`>=`

좌측이 크거나 같으면 참입니다.

```javascript
console.log(2>=1); // true
console.log(2>=2); // true
console.log(1>=2); // false
```

`<=`

좌측이 작거나 같으면 참입니다.

```javascript
console.log(2<=1); // false
console.log(2<=2); // true
console.log(1<=2); // true
```

`==`

양변이 같으면 참입니다.

```javascript
console.log(1==1); // true
console.log(2==1); // false
console.log(1==2); // false
```

`!=`

양변이 다르면 참입니다.

```javascript
console.log(1!=1); // false
console.log(2!=1); // true
console.log(1!=2); // true
```

### 배열

#### 배열 메소드

배열의 길이

배열의 길이는 `length`로 얻습니다.

```javascript
a = [60, 70, 80, 90, 100];
console.log(a.length); // 5
```

#### 함수형 패러다임

`map()`, `filter()`, `reduce()` 은 함수형 프로그래밍 패러다임을 제공합니다. 다음 배열 `a`가 있습니다.

```javascript
const a = [0, 1, 2, 3, 4, 5];
```

`map()`은 함수를 매개변수로 받아서 각각의 원소에 연산하고 그 결과를 새로운 배열로 구성하여 반환 합니다.

다음 화살표 함수 `bf`는 매개변수에 2를 곱하여 반환 합니다.

```javascript
const bf = p => p * 2;
```

위 화살표 함수를 배열 `a`에 대해 `a.map()`하여 인자로 전달 할 수 있습니다.

```javascript
const b = a.map(p => p * 2);
```

그 결과를 `b`로 받았고, 계속하여 

```javascript
console.log(b);
```

는 

```
[0, 2, 4, 6, 8, 10]
```

입니다.

`filter()` 메소드는 매개변수로 받아서 그 조건에 맞는 원소로 새로운 배열을 구성하여 반환 합니다.

계속하여, 다음 화살표 함수 `cf`가 있습니다.

```javascript
cf = p => 3 < p;
```

이 화살표 함수 `cf`는 `p`가 3보다 크면 `true`를 반환하고 거짓이면  `false`를 반환합니다.

계속하여 이 화살표 함수들을 호출합니다.

```javascript
cf(0); // 0은 3보다 작으므로 false
cf(1); // 1은 3보다 작으므로 false
cf(2); // 2는 3보다 작으므로 false
cf(3); // 3은 3과 같으므로 false
cf(4); // 4는 3보다 크므로 true
cf(5); // 5는 3보다 크므로 true
```

입니다. `filter()`메소드는 배열의 원소에 대해 이 처리를 합니다. 계속하여

```javascript
const c = a.filter(p => 3 < p); // c = [4, 5]
```

입니다.

`reduce()`메소드는 연쇄 연산을 합니다. 다음 초기값이 0인 `a`에 1, 2, 3, 4, 5를 차례대로 더해 봅니다.

```javascript
a = 0; // a의 초기값은 0
a = a + 1; // a에 1을 더하여 a에 대입하여 a는 1
a = a + 2; // a에 2을 더하여 a에 대입하여 a는 3
a = a + 3; // a에 3을 더하여 a에 대입하여 a는 6
a = a + 4; // a에 4을 더하여 a에 대입하여 a는 10
a = a + 5; // a에 5을 더하여 a에 대입하여 a는 15
```

`reduce()` 메소드는 배열에 대해 연속으로 이 작업을 하고 결과를 내놓습니다. `reduce()`에는 두개의 인수를 필요로 합니다.

```javascript
array.reduce(함수, 초기값);
```

계속하여 배열 `a`에 대해 `reduce()`를 해보겠습니다.

```javascript
const d = a.reduce((p, q) => p + q, 0);
```

초기값은 `0`입니다. `(p, q) => p + q`에서 `p`는 이전에 연산된 결과 입니다. `q`는 배열 `a`의 원소를 차례대로 받습니다. 이 `(p, q) => p + q`가 연속으로 호출되는 매개변수는 다음과 같습니다.

```javascript
(0, 1) => 0 + 1; // p는 초기값 0, q는 배열의 0번째 원소 1, 그리고 0 + 1을 반환.
(1, 2) => 1 + 2; // p는 이전 반환값 1, q는 배열의 1번째 원소 2, 그리고 1 + 2를 반환.
(3, 3) => 3 + 3; // p는 이전 반환값 3, q는 배열의 1번째 원소 3, 그리고 3 + 3를 반환.
(6, 4) => 6 + 4; // p는 이전 반환값 6, q는 배열의 1번째 원소 4, 그리고 6 + 4를 반환.
(10, 5) => 10 + 5; // p는 이전 반환값 10, q는 배열의 1번째 원소 5, 그리고 10 + 5를 반환.
```

최종 결과는 15 입니다.

##### 응용: 다섯 사람의 나이의 합.

`name`과 `age`를 프로퍼티로 가지는 개체의 배열이 있습니다.

```javascript
const s = [
    { name = 'Alice', age = 12 },
    { name = 'Bob', age = 23 },
    { name = 'Charlie', age = 34 },
    { name = 'Dave', age = 45 },
    { name = 'Eve', age = 56 },
];
```

계속하여 30세 이상인 개체의 나이를 합해보겠습니다.

```javascript
const a = s
	.map(p => p.age) // [12, 23, 34, 45, 56]
	.filter(p => 30 <= p) // [34, 45, 56]
	.reduce((p, q) => p + q, 0) // 135
console.log(a); // 135
```

간단하죠?

### 객체

#### window

`window`객체는 브라우저를 처음 시작할때 생성되는 객체입니다. 이 `window`가 먼저 생성되고, 이 `winodw`안에 다른 `window`나 `document`가 있습니다.

#### document

`document`개체는 브라우저에서 로드한 콘텐츠를 말합니다. `document`는 `window`에 로드 됩니다.

### 연산자

#### 스프레드 연산자

스프레드 연산자는 항목을 풀어서 삽입합니다.

```javascript
const a = [ 1, 2, 3 ];
const b = [ ...a, 4, 5, 6]; // b = [1, 2, 3, 4, 5, 6]
```

함수의 파라미터의 경우 다수의 인자를 배열로 받습니다.

```javascript
function f(p, q, ...s) {
	console.log(p);
	console.log(q);
	console.log(s);
}
f(1, 2, 10, 11, 12);
```

출력은 아래와 같습니다.

```
1
2
[10, 11, 12]
```

개체의 경우

```javascript
const { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4, c: 5 };
console.log(x); // 1
console.log(y); // 2
console.log(z); // { a: 3, b: 4, c: 5 }
```

#### 함수의 rest 매개변수

함수는 매개변수에 대해 `arguments`라는 객체를 가집니다.

```javascript
function f() { // 매개변수가 없습니다.
    console.log(arguments);
}
f(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

이름과 여러개의 점수를 입력 받아 개체를 반환하는 함수의 예를 보겠습니다.

```javascript
function f(name, ...scores) {
	const avg = scores.reduce((acc, p) => acc + p, 0) / score.length; // 평균 계산
	return {
        name: name,
        scores: scores,
        average: avg
    }
}

const a = f('Alice', 70, 80, 90, 100);
console.log(a);
```

계속하여 결과는

```
{
	name: 'Alice',
	scores: 70, 80, 90, 100,
	average: 85,
}
```

입니다.

#### 개체의 프로퍼티를 spread 하기.

개체 `o`가 있습니다.

```javascript
const o = { x: 1, y: 2, a: 3, b: 4, c: 5 };
```

계속하여 소멸하고 출력합니다.

```javascript
const { x, y, ...z } = o;
console.log(x); // 1
console.log(y); // 2
console.log(z); // [3, 4, 5]
```

계속하여

```javascript
const m = { x, y, ...z };
console.log(m); // { x:1, y:2, a:3, b: 4, c: 5 }
```

#### Promises

Promises는 비동기 함수를 동기적으로 결과를 얻는 방법으로 콜백지옥을 회피하도록 돕습니다.

```javascript
const prom = new Promise((fulfilled, rejected) => {
	$.get("/posts")
		.done(posts => fulfilled(posts))
		.fail(err => rejected(err));
});
prom
	.then(posts => console.log(posts))
	.catch(err => console.log(err));               
```

Ajax 요청을 할 때 응답 시간이 오래 걸리는 리소스를 원하기 때문에 응답이 동기가 아닙니다. 요청한 리소스가 어떤 이유로 든 사용할 수 없는 경우도 있습니다.

이러한 상황을 처리하기 위해 ES2015는 Promise을 제공 했습니다. Promise에는 세 가지 상태가 있습니다.

- 대기중 (pending)
- 완성됨 (fulfilled)
- 거부됨 (rejected)

리소스 `res`를 가져 오기 위해 Ajax 요청을 처리하기로 약속을 사용하고자 한다고 가정해  보겠습니다.

먼저 Promise를 생성합니다.

```javascript
const prom = new Promise(
	function(fulfilled, rejected) {
        $.get('res')
        	.done(function(res) {
            	fulfilled(res);
        	})
        	.fail(function(err) {
            	rejected(err);
        	});
    }
)
```

위의 예제에서 볼 수 있듯이 `Promise` 객체는 두 매개 변수를 `fulfilled`와 `rejected`  두개의 실행자를 받습습니다. 이러한 매개변수는 호출 될 때 Promise의 pending 상태를 각각 fulfilled 및 rejected 상태로 이동시키는 함수입니다.

Promise는 인스턴스 작성 후 보류 상태에 있고, 실행 프로그램 기능은 즉시 실행됩니다. 실행자 함수에서 fulfilled 또는 rejected 함수 중 하나가 호출되면 promise는 연관된 핸들러를 호출합니다.

Promise의 결과를 얻으려면 다음을 수행하여 핸들러를 연결해야 합니다.

```javascript
fetcherPromise
	.then(function(res) {
    	console.log(res);
    })
	.catch(function(err) {
    	console.log(err);
	})
```

### 템플리트 리터럴

템플리트 리터럴은 싱글 및 멀티 라인 문자열에 대한 표현 interpolation입니다.

즉JavaScript 문법 (예 : 변수)을 편리하게 사용할 수있는 새로운 문자열 구문 입니다.

```javascript
const a = 'Alice';
const b = 12;
const c = 'Hello ${a}, Do you have ${b} apples?';
console.log(c); // Hello Alice, Do you have 12 apples?
```

#### 태그가 지정된 템플릿 리터럴

템플릿 태그는 템플릿 리터럴에 접두사를 붙일 수있는 함수입니다. 함수가 이런 방식으로 호출되면 첫 번째 매개변수는 템플릿의 interpolation 된 변수 사이에 나타나는 문자열의 배열이며 후속 매개 변수는 interpolation 된 값입니다. 스프레드 연산자를 사용하여 모두 캡처하세요.

아래는 그들이 어떻게 작동하는지에 대한 장난감 예제입니다.

```javascript
function f(strings, ...values) {
	const interpolation = strings.reduce((prev, current) => {
    	return prev + current + (values.length ? "<mark>" + values.shift() + "</mark>" : "");
		}, "");

  	return interpolation;
}

const a = "jam";
const b = "toast";

f`I like ${a} on ${b}.`; // "I like <mark>jam</mark> on <mark>toast</mark>."
```

예제 하나를 더 볼까요?

```javascript
function f(strings, ...values) {
	return strings.reduce((p, q) => {
    	let v = values.shift() || [];
    	v = v.join(", ");
    	return p + q + value;
  	}, "");
}
const a = ['apples', 'bananas', 'cherries'];
comma`I like ${a} to snack on.`; // "I like apples, bananas, cherries to snack on."
```

### Imports / Exports

ES6 모듈은 가져온 모듈에서 명시적으로 export하는 모듈의 변수 또는 함수에 액세스 하는데 사용됩니다.

##### 명명된 exports

명명된 exports는 모듈에서 여러 값을 내보내는 데 사용됩니다.

`math.js` 파일에서

```javascript
// mathConstants.js
export const pi = 3.14;
export const exp = 2.7;
export const alpha = 0.35;
```

`main.js`에서

```javascript
import { pi, exp } from './math.js'; // 명명된 import -- destructuring-like syntax
console.log(pi) // 3.14
console.log(exp) // 2.7

```

`other.js`에서

```javascript
import * as constants from './math.js'; // 모든 변수 주입
console.log(constants.pi) // 3.14
console.log(constants.exp) // 2.7
```

명명된 import는 구조가 잘못된 것처럼 보이지만 구문이 다르며 같지 않습니다. 그들은 기본값이나 deep destructuring를 지원하지 않습니다.

게다가 alias을 사용할 수는 있지만 구문은 구조화에 사용 된 구문과 다릅니다.

```javascript
import { foo as bar } from 'myFile.js';
```

`foo`가 import 되었고 새로운 변수 이름 `bar`로 주입되었습니다.

##### 기본 import/export

기본 import와 관련하여 모듈 당 하나의 기본 export만 있습니다. 기본 export는 함수, 클래스, 객체 또는 다른 것일 수 있습니다. 이 값은 가져 오기가 가장 쉬울 것이므로 "기본" export 값으로 간주됩니다.

다음 `num.js`:

```javascript
const ultimateNumber = 42;
export default ultimateNumber;
```

다음 `main.js`:

```javascript
import number from './num.js';
// 기본 export는 이름에 대해 독립적이며 자동으로 주입 됩니다.
console.log(number) // 42
```

함수 예제를 볼까요?

`sum.js`:

```javascript
export default function sum(x, y) {
  return x + y;
}
```

`main.js`:

```javascript
import sum from './sum.js';
const r = sum(1, 2);
console.log(r) // 3
```

---

## 참조

- [Modern JavaScript Cheatsheet](https://github.com/mbeaudru/modern-js-cheatsheet)

- [Make your JavaScript code shide knockout old es5 hack](https://rainsoft.io/make-your-javascript-code-shide-knockout-old-es5-hack/)

- [Mozilla JavaScript](https://developer.mozilla.org/ko/docs/Web/JavaScript)