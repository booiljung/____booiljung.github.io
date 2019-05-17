# 소프트맥스 함수 (softmax function)

소프트맥스 함수는 분류(classification)에서 사용되며 식은 아래와 같습니다.
$$
y_k = \frac{e^{a_k}}{\sum_{i=1}^n e^{a_k}}
$$
여기서 $e$는 네이피어수, $n$은 출력층의 뉴런수, $y_k$는 그중 $k$번째 출력을 나타냅니다.

소프트맥스를 파이썬으로 구현하면 다음과 같습니다.

```python
import numpy as np

def softmax(a):
	exp_a = np.exp(a)
	sum_exp_a = np.sum(exp_a)
	y = exp_a / sum_exp_a
	return y
```

그런데, 이 식은  IEEE 754 표현 범위를 넘어서 언더플로우나 오버플로우로 값을 잃을 수 있습니다. 이런 경우 로그함수(logarithm)를 사용하여 개선할 수 있습니다.
$$
\begin{aligned}
y_k &= \frac{e^{a_k}}{\sum_{i=1}^n e^{a_k}} \\
&= \frac{Ce^{a_k}}{C\sum_{i=1}^n e^{a_k}} \\
&=\frac{e^{a_k+\log C}}{\sum_{i=1}^n e^{a_k + \log C}} \\
&=\frac{e^{a_k+C'}}{\sum_{i=1}^n e^{a_k + C'}} \\
\end{aligned}
$$
로그를 적용한 소프트맥스 함수를 파이썬으로 구현하면 다음과 같습니다.

```python
import numpy as np

def softmax(a):
	c = np.max(a)
	exp_a = np.exp(a - c)
	sum_exp_a = np.sum(exp_a)
	y = exp_a / sum_exp_a
	return y
```

소프트맥스의 각각의 출력은 $[0.0, 1.0]$ 이며 출력의 총합은 $1$로 중요한 성질이며, 이 출력을 '확률'로 이해할 수 이있습니다. 만일 $k$가 $2$의 출력이 가장 크고 출력 값이 $0.92$ 이면, $92 \%$의 정확도로 2로 분류 할 수 있다는 것을 의미합니다.

## 참조

- [밑바닥부터 시작하는 딥러닝](http://www.hanbit.co.kr/media/community/review_view.html?hbr_idx=3595)

