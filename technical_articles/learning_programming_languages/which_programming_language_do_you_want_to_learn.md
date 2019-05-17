[Up](./index.md)

# 첫 프로그래밍 언어?

소프트웨어 개발을 하려면 프로그래밍 언어를 배워야 합니다. 누구나에게나 첫 프로그래밍 언어가 있습니다. 첫 프로그래밍 언어로 어느 언어를 선택해야 할까요?

프로그래밍 언어를 처음 배우게 되면, 개발 도구를 설치하고, 먼저 `Hello, world!`를 출력하는 코드를 작성합니다. 이 `Hello, world!`는 1978년에 브라이언 커니핸과 데니스 리치가 쓴 `The Programming Language` 교재의 첫 번째 예제였고, 이후 모든 프로그래밍 언어 학습서의 첫번째 예제로 굳어지게 되었습니다.

어느 언어의 `Hello, world!`예제가 가장 적절할까요? 다양한 언어로 `Hello, world!`를 출력하는 코드를 보겠습니다.

### C 언어

C 언어는 운영체제를 개발하기 위해 개발되었으며 운영체제, 리얼타임시스템, 고성능 응용프로그램, 과학기술계산용 프로그램 등 시스템 프로그래밍 목적으로 사용됩니다.

```c
#include <stdio.h>

int main(void)
{
    puts("Hello, world!");
    return 0;
}
```

C언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `#include`
- `<stdio.h>`
- `int`
- `void`
- `main()`, 함수
- `{}`블럭
- 들여쓰기
- `puts()`
- 문자열 리터럴
- `;`
- `return`
- `0` 정수형 리터럴
- main함수에서 `return 0`
- 컴파일, 링크, 패키징

### BASIC 언어

BASIC 언어는 교육용으로 개발 되었습니다.

```basic
10 PRINT "Hello, world!"
```

BASIC 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `10` (줄번호)
- `PRINT`
- 문자열 리터럴
- 인터프리터

### C++ 언어

C++ 언어는 시스템 프로그래밍, 과학기술계산용, 고성능 응용프로그램 개발을 목적으로 사용됩니다.

```c++
#include <iostream>

int main()
{
    std::cout << "Hello, world!" << std::endl;
    return 0;
}
```

C++ 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `#include`
- `<iostream>`
- `int`
- `main()`, 함수
- `{}`블럭
- 들여쓰기
- `std`
- `::` 멤버, 멤버 액세스 연산자
- `cout`
- `<<`
- 문자열 리터럴
- `endl`
- `;`
- `return`
- `0` 정수형 리터럴
- main함수에서 `return 0`
- 컴파일, 링크, 패키징

### Objective-C 언어

Objective-C 언어는 과학기술계산용, 시스템 프로그램, 고성능 응용 프로그램, 애플의 iOS, macos 용 응용 프로그램 개발에 사용됩니다.

```objective-c
#import <Fundation/Foundation.h>

int main() {
    @autoreleasepool {
        NSLog(@"Hello, world!");
    }
    return 0;
}
```

Objective-C 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `#import`
- `<Foundation/Foundation.h>`
- `int`
- `main()`, 함수
- `{}` 블록
- `@autoreleasepool`
- `NSLog`
- @문자열 리터럴
- `;`
- `return`
- main 함수에서 `return 0`
- 컴파일, 링크, 패키징

### C#  언어

C# 언어는 주로 업무용 응용프로그램 개발에 사용되며, 유니티에서 스크립트 언어로 사용됩니다. (스크립트 언어란 어떤 프로그램 내부에서 주어진 목적을 위해 사용되는 프로그래밍 언어를 말합니다.)

```c#
using System;

namespace helloworld {
    public class Program {
        static void Main(string[] args) {
            Console.WriteLine("Hello, world!");
        }
    }
}
```

C# 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `using`
- `System`
- `namespace`
- `helloworld`
- `{}` 블록
- 들여쓰기
- `public`
- `class`
- `Program`
- `static`
- `void`
- `Main()`, 함수
- `string`
- `[]` 배열
- `args` 파라미터
- `Console`
- `.` 멤버, 멤버 액세스 연산자
- `WriteLine()`
- 문자열 리터럴
- `;`
- 컴파일, 링크, 패키징, IL, JIT, CLR

### Java 언어

Java 언어는 주로 업무용 응용프로그램 개발에 사용됩니다.

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

Java 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `public`
- `class`
- `HelloWorld`
- `{}` 블럭
- 들여쓰기
- `static`
- `void`
- `main()` 함수
- `String`
- `[]` 배열
- `args` 파라미터
- `System`
- `.` 멤버, 멤버 액세스 연산자
- `out`
- `println()`
- 문자열 리터럴
- `;`
- 컴파일, 링크, 패키징, 자바 바이트 코드, JIT, JVM

### Pascal 언어

Pascal 언어는 교육용으로 개발 되었으며 주로 업무용 응용프로그램 개발에 사용됩니다.

```pascal
Program HelloWorld;

begin
	writeln('Hello, world!')
end.
```

Pascal 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `Program`
- `HelloWorld`
- `;`
- `begin end` 블록
- `writeln()`
- 문자열 리터럴
- 컴파일, 링크, 패키징

### Javascript 언어

Javascript 언어는 웹 브라우저의 스크립트 언어로 개발 되었으며, 주로 업무용 응용 프로그램 개발에 사용됩니다.

```javascript
console.log("Hello, world!");
```

Javascript 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `console`
- `.` 멤버, 멤버 액세스 연산자
- `log()`
- 문자열 리터럴
- JIT

### COBOL  언어

COBOL 언어는 주로 업무용 응용 프로그램 개발에 사용됩니다.

```CObol
************************
IDENTIFICATION DIVISION.
PROGRAM-ID. HELLO.

ENVIRONMENT DIVISION.

DATA DIVISION.

PROCEDURE DIVISION.
MAIN SECTION.
DISPLAY "Hello, world!"
STOP RUN.
************************
```

COBOL 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `DIVISION`
- `.`
- `IDENTIFICATION DIVISION`
- `PROGRAM-ID`
- `ENVRIONMENT DIVISION`
- `DATA DIVISION`
- `PROCDEURE DIVISION`
- `MAIN`
- `SECTION`
- `DISPLAY`
- 문자열 리터럴
- `STOP RUN`
- 컴파일, 링크, 패키징

### FORTRAN 언어

FORTRAN 언어는 주로 과학기술계산용 응용 프로그램 개발에 사용됩니다.

```fortran
program hello
	print *, "Hello, world!"
end program hello
```

FORTAN 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `program end program` 
- `hello`
- `print`
- `print *,`
- 문자열 리터럴
- 컴파일, 링크, 패키징

### Go 언어

GO 언어는 주로 시스템, 과학기술계산용, 업무용 응용 프로그램 등 범용 개발 언어 입니다.

```go
package main

import "fmt"

func main() {
    fmt.Printf("Hello, world!")
}
```

Go 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `package main`
- `import`
- `"fmt"`
- `func`
- `main()`
- `{} 블록`
- `fmt`
- `.` 멤버, 멤버 액세스 연산자
- `Printf()`
- 문자열 리터럴
- 컴파일, 링크, 패키징

### Scala 언어

Scala 언어는 어려운 Java 언어를 대체하기 위해 개발 되었으며, 주로 Spark에서 데이터 분석용으로 사용됩니다.

```scala
object HelloWorld extends App {
  println "Hello, world!"
}
```

Scala 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `object`
- `HelloWorld`
- `{}` 블록
- `extends`
- `App`
- `println()`
- 문자열 리터럴
- 컴파일, 링크, 패키징, 자바 바이트 코드, JIT, JVM

### Python 3 언어

Python 언어는 과학기술계산용, 응용 프로그램 개발에 사용됩니다.

```python
print('Hello, world!')
```

Python 3 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `print()`
- 문자열 리터럴
- 인터프리터

### Lua 언어

Lua 언어는 다른 언어의 내장 스크립트 언어로 사용됩니다.

```lua
print("Hello, world!")
```

Lua 언어 예제는 다음 항목들에 대한 이해를 필요로 합니다.

- `print()`
- 문자열 리터럴
- 인터프리터

## 결론

나쁜 언어 좋은 언어 구분은 좋지 않은 행동이며, 의미가 없는 주장이며 어느 커뮤니티에서라도 해서는 안되는 행동 입니다. 언어마다 분야에 있어 강점과 약점이 있습니다. 첫 프로그래밍 언어를 선택할때 여러 언어 중 첫 프로그래밍 언어로 어떤 언어를 선택해야 할까요? 이미 여러분께서 적절한 언어를 선택 하셨으리라 생각 합니다.

---

##### 참조

- 숙묵님 추천 링크 1: [Hello world collection](http://helloworldcollection.de/?fbclid=IwAR3-cCPA3H4kCZkZx--8LZKbs5BarBXn5-F6KuQQcDFXlO6CdXteAQGuBsw)
- 숙묵님 추천 링크 2: [Hello World Quiz](http://helloworldquiz.com/?fbclid=IwAR0N3wuLdVauYcYmvGTrtlII-rZ-zNpHqmiNLjUB4q3VUPTuA_d9vYd7NOE)