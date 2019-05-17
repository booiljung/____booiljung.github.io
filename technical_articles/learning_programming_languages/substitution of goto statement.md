# goto 문의 대용품

## 왜?

`goto`문은 코드를 복잡하게 하여 혐오한다는 개발자들이 있고, 일부 언어들은 `goto`문을 지원하지 않습니다. `goto`문을 사용할 경우는 많지 않지만 간혹 다중루프를 빠져나와야 할때 어려워집니다. 인터넷을 찾아보면 `goto`문을 사용하지 않고 어떻게 다중루프를 빠져나오는 방법들에 대한 토론들이  있습니다. 사례들을 살펴보고 새로운 다중루프 탈출 문을 제안합니다.

## goto문을 사용할 경우 다중루프 빠져나오기

```C#
void func() {
    for (int i = 0; i < 100; i++) {
        for (int j = 0; j < 100; j++) {
            for (int k = 0; k < 100; k++) {
                ...
                if (condtion)
                    goto loop_break;
            }
        }
    }
loop_break:
    ...    
}
```

C#은 `goto`문을 사용하여 다중루프를 빠져나오면 됩니다. 그렇다면 goto문을 사용하지 않고 다중 루프를 빠져나오는 방법이 있을까요?

## goto를 사용하지 않는 파이썬 예제

파이썬은 `goto`문을 지원하지 않습니다. 스택오버플로우에서 찾은 3가지 예를 보겠습니다.

### 루프를 별도의 함수로 만들고 리턴해라.

```python
def func():    
    def loop():
	    for i in range(100):
    	    for j in range(100):
	    	    for k in range(100):
    	    	    if codition:
        	    	    return
    ...
	loop()
    ...
```

### 예외를 던져라.

```python
def func():
    ...
	try:
    	for i in range(100):
    		for j in range(100):
    			for k in range(100):
    				if codition:
    					raise LoopBreak()
	except LoopBreak as e:
        ...
	...
```

### 플래그를 써라

```python
def func():
	finish = False
    for i in range(100):
        for j in range(100):
            for k in range(100):
                if codition:
                    finish = True
                    break
        	if finish:
            	break
      	if finish:
            break
```

`goto`문이 가독성을 해친다고 하여, `goto`문을 사용을 금지했더니 더 가독성이 떨어지는 느낌이 들지 않습니까?

## 제안

새로운 루프 중단 문을 제안합니다.

```python
def func():
	for:
        for i in range(100):
            for j in range(100):
                for k in range(100):
                    if codition:
                        break for
    finish:
        ...
```







