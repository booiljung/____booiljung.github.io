[Up](./index.md)

# 디버깅 가능한 코드 (Debuggable code)

## 조건문

다음 `while`루프가 있다. 언제 루프를 빠져 나갈까?

```c#
while (true)
{
    int a = ...;
    int	b = ...;
    int	c = ...;
    int d = ...;
    ...
    if (a == b && c != d) break;
    ...
}
```

`break`문에 브레이크 포인트를 걸 수 없어, 루프를 빠져나가는 시기를 루프를 돌며 조건절 안의 모든 변수를 반복 확인해야 할 것이다. 루프를 빠져나가면 변수 `a, b, c, d`의 스코프도 벗어나니 빠져나간 시점의 값도 확인할 수 없다.

다음 예제를 보자.

```c#
while (true)
{
    int a = ...;
    int	b = ...;
    int	c = ...;
    int d = ...;
    ...
	if (a == b && c != d)
		break;
    ...
}
```

`break`문에 브레이크 포인트를 걸 수 있고, 브레이크가 걸리면 루프를 빠져 나가는 시점의 변수 값들을 확인 할 수 있다. **빠르게** 디버깅이 가능하다.

두줄로 늘여쓰면 코드가 느려지지 않느냐고? 코드를 한줄에 쓴다고 하여 성능이 빨라지는 것은 아니다.

## 리턴값을 인자로 전달 하기

다음 `Dog`, `Cat`, `Ox` 클래스가 있다.

```c#
class Dog;
class Cat;
class Ox;
```

이 클래스들을 찾아내서 반환하고, 처리하는 함수들이 있다.

```C#
Dog FindDog() { ... }
Dog WashDog(Dog dog) { ... }
Cat FindCat() { ... }
Cat FeedCat(Cat cat) { ... }
Ox FindOx() { ... }
Ox TrainOx(Ox ox) { ... }
```

그리고 이 세가지 클래스를 파라미터로 받는 함수 `Foo()`가 있다.

```C#
void foo(Dog dog, Cat cat, Ox ox)
{
    ...
}
```

우리는 댕댕이, 야옹이, 소를 찾아내고, 씻기고, 먹이고, 훈련시켜서 `Foo()`에 전달해야 한다.

```c#
void Main()
{
    ...
	foo(WashDog(FindDog()), FeedCat(FindCat()), TrainOx(FindOx()));
    ...
}
```

실행했더니 `NullReferenceException`이 발생하였다.  어느 곳에서 `NullReferenceException`이 발생 하였을까? 브레이크 포인트를 걸어도 값의 변화를 알 수 없다. 스택을 뒤져야 한다.

```C#
void Main()
{
    ...
	Dog dog = FindDog();
	dog = WashDog(dog);
	Cat cat = FindCat();
	cat = FeedCat(cat);
	Ox ox = FindOx();
	ox = TrainOx(ox);    
	foo(dog, cat, ox);
    ...
}
```

브레이크 포인트를 걸고 로컬 변수를 확인하며 **빠르게** 디버깅이 가능하다.

길게 쓰면 컴파일러가 긴 바이너리 코드를 생성하여 느려지지 않느냐고? 정상적인 컴파일러라면 컴파일러는 동일한 바이너리 코드를 생성 한다.

개발자들의 평균 근속 기간은 3년이 안된다. 언젠가 이 코드에서 손을 떼고 다른 사람이 맡을 것이고, 코드를 읽을때 디버거를 사용하여 읽을 수도 있다. 디버깅 가능하게 코딩 하자.

모 반도체 회사의 레거시 소스 코드를 읽다가 생각나서 적어 본다. 디버그를 위해 코드를 변경하는 상황은 나쁜 상황이다. 새로 버그가 들어갈지 우연히 수정이 될지 불확실성이 더 높아지기 때문이다.

좋은 개발자는 디버깅이 가능하도록 코딩을 한다. 코드로 의도를 나타내고, 로그를 남기며, 디버깅 프레임워크를 활용하며, 디버깅 가능한 코드를 쓴다.

