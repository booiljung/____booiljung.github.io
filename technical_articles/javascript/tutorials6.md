# JavaScript

### 변수

#### var의 Hoisting 함정

다른 언어를 사용하는 개발자라면 JavaScript 에서 실수하기 쉬운 함정이 있습니다.

다음 새 예제를 보겠습니다.

```javascript
var n = 1;
function f() {
  	console.log(n);
  	var n = 2;
  	console.log(n);
}
f();
```

전역 변수를 지원하는 다른 언어라면 

```
1
2
```

라는 결과를 예측 할 것입니다.

그런데 JavaScript는:

```
undefined
2
```

예상치 못한 결과를 내놓습니다.

왜 이런 결과를 내놓을까요? 바로 변수 Hoisting 때문입니다. 변수 Hoisting이란 `var`를 사용하여 변수를 선언하면 해당 변수를 스코프의 최상단으로 올려버리는 현상입니다. 그리고 `var`를 사용한 변수의 스코프는 블록이 아니라 함수라는 점입니다.

위의 코드는 Hoisting 때문에  다음 코드로 해석 됩니다.

```javascript
var n = 1;
function f() {
    var n; // 초기화가 되지 않음.
  	console.log(n);
  	n = 2;
  	console.log(n);
}
f();
```

3번째 라인에서 변수가 선언 되고, 초기값이 없으므로 `undefined`가 됩니다.

JavaScript는 `var`를 사용하지 말고, `let`이나 `const`를 사용할 것을 권장 합니다.

#### for-loop에서 var의 함정

for-loop에서 `var`를 사용하면 예측하지 않은 동작을 하게 됩니다.

다음 0부터 9까지 합하는 새 예제를 보겠습니다.

```javascript
function f() {
    var sum = 0;
    for (var n = 0; n < 10; n++) {
        sum += n;
    }
    console.log('n: ': n);
    console.log('sum: ': sum);
}
f();
```

다른 언어를 사용하는 개발자라면

```
n: undefined
sum: 45
```

를 예상할 것입니다. 변수 `n`이 for-loop 안의 초기화 절에 선언되었기 때문에 for-loop 밖에서 액세스 할 수 없다고 생각하기 때문입니다. 다른 언어는 실제로 그렇습니다.

그러나 JavaScript는

```
n: 10
sum: 45
```

가 출력됩니다.

이 `var` 변수는 Hoisting 되어 다음 함수 스코프를 가진 변수가 되어 , 아래 코드 처럼 동작 하기 때문입니다:

```javascript
function f() {
    var sum;
    var n;
    sum = 0;
    for (n = 0; n < 10; n++) {
        sum += n;
    }
    console.log('n: ': n);
    console.log('sum: ': sum);
}
f();
```

변수 `sum`과 for-loop 안의  `n`은 Hoisting 되어 함수 `f()`의 최상단으로 올라가게 되고, for-loop 밖에서 액세스 할 수 있게 됩니다.

### 참 또는 거짓

`NaN`은 Not a Number라는 것으로 숫자가 아니라는 의미를 가집니다. 다음 예제를 보겠습니다.

```javascript
typeof NaN == 'number' // true 
```

`typeof NaN`으로 타입을 `number` 타이냐고 질의하면 그렇다고 합니다.

심지어 `NaN`과 `NaN` 같은가 질의하면:

```javascript
NaN === NaN
```

예상하기에 결과는 참일것 같습니다. 그러나 실제 결과는 거짓입니다. `NaN`과 `NaN`이 다르다는 것입니다. 그래서 

`NaN`과 `NaN`은 다른거구나 예상하고 다르냐고 질의하면:

```javascript
NaN !== NaN
```

결과는 true이며 다르다고 합니다.

이 문제를 해결하기 위해 JavaScript는 `isNaN()` 함수를 제공하여 판단하게 합니다.

```javascript
isNaN(0) // false
isNaN(100) // false
isNaN(NaN) // true
isNaN('Alice') // true
isNaN('0') // false
```









------

## 참조

- [Modern JavaScript Cheatsheet](https://github.com/mbeaudru/modern-js-cheatsheet)
- [Make your JavaScript code shide knockout old es5 hack](https://rainsoft.io/make-your-javascript-code-shide-knockout-old-es5-hack/)
- [Mozilla JavaScript](https://developer.mozilla.org/ko/docs/Web/JavaScript)