# 평균 제곱 오차 (mean squared function)

많이 사용되는 손실 함수이며 식은 다음과 같습니다.
$$
E = \frac{1}{2} \sum _k (y_k - t_k)^2
$$
여기서 $y_k$는 신경망의 출력 값, $t_k$는 정답 레이블, $k$는 차원을 나타냅니다.

평균 제곱 오차를 파이썬으로 구현하면 다음과 같습니다.

```python
import numpy as np

def mean_squared_error(y, t):
	return 0.5 * np.sum((y-t)**2)
```

여기서 `y`와 `t`는 `numpy`의 `ndarray` 입니다.

## 참조

- wikipedia.org
- [밑바닥부터 시작하는 딥러닝](http://www.hanbit.co.kr/store/books/look.php?p_code=B8475831198)

