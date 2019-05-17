# ReLU funciton

$$
h(x) =
\begin{cases}
x &(x \gt 0)\\
0 &(x \le 0)
\end{cases}
$$

파이썬으로 구현하면 다음과 같습니다.

```python
import numpy as np

def relu(x):
	return np.maximum(0, x)
```

이것을 그래프로 플롯하면 다음과 같습니다.

![Activation rectified linear.svg](relu_function.assets/120px-Activation_rectified_linear.svg.png)

## 참조

- wikipedia.org
- [밑바닥부터 시작하는 딥러닝](http://www.hanbit.co.kr/media/community/review_view.html?hbr_idx=3595)

