# 교차 엔트로피 오차 (Cross entropy error)

교차 엔트로피의 오차의 식은 다음과 같습니다.
$$
E = -\sum t_k \ln y_k
$$
여기서 $y_k$는 신경망의 출력이며, $t_k$는 정답 레이블입니다.

교차 엔트로피를 파이썬으로 구현하면 다음과 같습니다.

```python
import numpy as np

def cross_entropy_error(y, t):
	delta = 1e-7
	return -np.sum(t * np.log(y + delta))
```

여기서 `delta`  라는 작은 값을 `y`에 더했습니다. `np.log()` 함수에 `0`을 입력하면 $-\infty$가 되기때문에 이를 방지하기 위해서입니다.



