

[Up](activation_functions.md)

# 시그모이드 함수 (Sigmoid function)

$$
\begin{aligned}
h(x) &= \frac{1}{1 + e^{-x}} \\
&= \frac{e^x}{e^x+1}
\end{aligned}
$$

여기서 $e$ 네이피어수 입니다.

시그모이드 함수를 파이썬으로 구현하면

```python
import numpy as np

def sigmoid(x):
    return 1 / (1 + np.exp(-x))
```

입니다. 이것을 플롯하면 아래 그래프를 얻습니다. (출처: wikipedia.org)

![img](sigmoid_function.assets/320px-Logistic-curve.svg.png)

## 참조

- wikipedia.org
- [밑바닥부터 시작하는 딥러닝](http://www.hanbit.co.kr/media/community/review_view.html?hbr_idx=3595)

