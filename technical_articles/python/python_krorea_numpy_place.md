# Python Korea : numpy.place

파이썬 코리아 그룹에 다음 질문이 올라 왔다.

> 넘파이 질문입니다.
>  a 와 b 2개의 배열이있습니다 .
>  a배열과 b배열크기는 동일하지만 내용값은 동일하지 않습니다.
>  b배열은 nan밸류를 가지고있으며 np.where로 위치는 확인할수있는데 a배열에서 동일한 위치를.nan처리를 하거나 그 위치의 값을 수정할려면 어떤방법이 있을까요?
>
> 배열은 1차원일수도있고 2차원일수도있습니다

`np.place()`로 가능해 보였다. 해결해 보기로 했다.

```python
import numpy as np
import math

a = np.array([[1, 2, 3, 4],[5, np.nan, 7, 8]], np.float)
print("a", a)
b = np.array([[11, 12, np.nan, 14], [15, 16, np.nan, 17]], np.float)
print("b", b)
c = np.array([np.nan])
print("c", c)

c = np.array([np.nan])
np.place(a, np.isnan(b), c)
print("a", a)
```

`np.place()`는 두번째 파라미터의 조건에 맞는 값을, 주어진 세번째 파라미터 `c`의 항목을 차례대로 가져와, 첫번째 파라미터 `array`의 항목을 덮어 쓴다.

결과:

```
a	[[ 1.  2.  3.  4.]
 [ 5. nan  7.  8.]]
b	[[11. 12. nan 14.]
 [15. 16. nan 17.]]
c	[nan]
a	[[ 1.  2. nan  4.]
 [ 5. nan nan  8.]]
```

둘중 하나라도 `np.nan`이면 `np.nan`이 되었다.

소스코드는 [Google Colab](https://colab.research.google.com/drive/1k90LnLSZXvPxbL52Ly91o9pGnhP-Tvts)에 있다.

