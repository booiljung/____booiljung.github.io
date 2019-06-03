# JavaScript

## Type Conversion

JavaScript 타입 컨버전은 다른 언어와 다르니 주의해야 합니다.

```javascript
10 + " objects" // "10 objects"
"7" * "4"		// 28
1 - "x"			// NaN: 숫자에서 문자열을 빼면 NaN이 됩니다.
```

```javascript
n = NaN
n + " objects"	// NaN: NaN + 문자열은 NaN이 됩니다. 숫자 + 문자열이 문자열인 것과 다릅니다.
```

| Value          | To String         | To Number                     | To Boolean | To Object           |
| -------------- | ----------------- | ----------------------------- | ---------- | ------------------- |
| `undefined`    | `"undefined"`     | `NaN`                         | `false`    | `throw TypeError`   |
| `null`         | `"null"`          | `0`                           | `false`    | `throw TypeError`   |
| `true`         | `"true"`          | `1`                           |            | `Boolean(true)`     |
| `false`        | `"false"`         | `0`                           |            | `Boolean(false)`    |
| `""`           |                   | `0`                           | `false`    | `String("")`        |
| `"1.2"`        |                   | `1.2`                         | `true`     | `String("1.2")`     |
| `"one"`        |                   | `NaN`                         | `true`     | `String("one")`     |
| `1`            | `"1"`             |                               | `true`     | `Number(1)`         |
| `0`            | `"0"`             |                               | `false`    | `Number(0)`         |
| `-0`           | `"0"`             |                               | `false`    | `Number(-0)`        |
| `NaN`          | `"NaN"`           |                               | `false`    | `Number(NaN)`       |
| `Infinity`     | `"Infinity"`      |                               | `true`     | `Number(Infinity)`  |
| `-Infinity`    | `"-Infinity"`     |                               | `true`     | `Number(-Infinity)` |
| `{}`           | `toString()`      | `toString()` 또는 `valueOf()` | `true`     |                     |
| `[]`           | `""`              | `0`                           | `true`     |                     |
| `[9]`          | `"9"`             | `9`                           | `true`     |                     |
| `['a']`        | `join()`          | `NaN`                         | `true`     |                     |
| `function(){}` | `function source` | `NaN`                         | `true`     |                     |

