# L1 regularization

L1 regularization을 살펴보기 위해서는 먼저 L1 norm을 살펴봐야 합니다.

## L1 norm

다음 벡터
$$
\mathbf x = \begin{pmatrix} x_1 \\ x_2 \\ x_3 \\ \vdots \\ x_n \end{pmatrix}
$$
가 있을때  L1 norm은 다음 식으로 얻을 수 있습니다.
$$
L_1 \text{norm} = |\mathbf w|_1 = \sum _{r=1} ^n |w_r|
$$
즉, L1 norm은 벡터 항목들의 절대값의 합이며 맨하탄거리라고도 합니다.

## L1 regularization

sparse vector는 종종 고차원 벡터이기도 합니다. 특히 특성교차를 생성하면 더욱 고차원 벡터가 됩니다. 이런 고차원 특성벡터는 모델 파라미터를 크게 하며 많은 컴퓨팅 자원을 소비하게 됩니다. 할 수 있다면, 희소 벡터에서는 가중치를 $0$으로 떨어지게 하여 그 특성을 모델에서 제거하여 컴퓨팅 자원을 절약 할 수 있습니다.

L1 regularization은 정규화의 일종으로 모델 가중치의 L1 norm에 대해 패널티를 부과 합니다. 대부분의 요소값이 0인  sparse feature에 의존한 모델에서 L1 정규화는 불필요한 피처에 대응하는 가중치들을 정확히 0으로 만들어 해당 피처를  모델이 무시하도록 만들어 feature selection 효과가 있습니다.

L1 regularization은 다음 수식으로 표현할 수 있습니다.
$$
c = c_0 + \lambda \frac{1}{n} \sum _w |w|
$$
여기서, $c_0$는 원래의 비용함수(cost function)이며, $n$은 훈련 데이터의 수, $\lambda$는 regularization coefficient, $w​$는 가중치를 나타냅니다.

$w$에 대해 편미분을 하면 새로운 $w$는 다음과 같습니다.
$$
\begin{aligned}
w
&\rarr w' = w - \eta \frac{\lambda}{n} \text{sgn}(w) - \eta \frac{\part c_0}{\part w}
\end{aligned}
$$
그래서 $w$를 줄이는 것이 아니라 $w$에서 부호 반대값을 더하게 됩니다. 

L1 regularization은 $w​$에서 일정한 값을 빼기 때문에 작은 가중치들은 0으로 수렴하게 되어 결국 몇개의 큰 가중치들만 남게 됩니다. 여러개의 값에서 의미가 있는 값만 남기고 싶은 경우 L1 regularization이 효과적이기 때문에 sparse 모델에 적합합니다. 또 미분이 불가능하기 때문에 gradient based learning 에 적용할때 주의가 필요합니다.



