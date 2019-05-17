# L2 regularization

L2 regularization를 살펴보기 위해서는 먼저 L2 norm을 살펴봐야 합니다.

## L2 norm

다음 벡터
$$
\mathbf x = \begin{pmatrix} x_1 \\ x_2 \\ x_3 \\ \vdots \\ x_n \end{pmatrix}
$$
가 있을때 L2 norm은 다음 식으로 얻을 수 있습니다.
$$
L_2 \text{norm} = ||\mathbf w||_2^2 = \sqrt{ \sum _{r=1} ^n |w_r|^2 }
$$
즉, 벡터 항목들의 제곱의 합에 제곱근을 적용하였고, 유클리드 거리라고도 합니다.

## L2 regularization

L2 regularization은 정규화의 일종으로 모델 가중치의 L2 norm에 대해 패널티를 부과 합니다. L2 regularization은 아웃라이어 가중치에 대해 0에 가깝지만 0은 아닌 값으로 만듦니다. 이는 L1 regularization과 차이점이며, 선형 모델의 일반화 능력을 개선 시킵니다.

L2 regularization은 다음 수식으로 표현할 수 있습니다.
$$
c = c_0 + \lambda \frac{1}{2n} \sum_w w^2
$$
여기서, $c_0$는 원래의 비용함수(cost function)이며, $n$은 훈련 데이터의 수, $\lambda$는 regularization coefficient, $w$는 가중치를 나타냅니다.

위 식을 보면 학습의 방향이 단순히 $c_0$가 작아지는 방향으로만 진행되는 것이 아니라, $w$ 도 최소가 되는 방향으로 진행됩니다. 이렇게 정의된 비용함수를 $w$에 대해 편미분을 하면 새로운 $w$는 다음과 같습니다.
$$
\begin{aligned}
w
&\rarr w' - \eta \frac{\part c_0}{\part w} - \frac{\eta \lambda}{n} w \\
&= (1 - \frac{\eta \lambda}{n}) w - \eta \frac{\part c_0}{\part w}
\end{aligned}
$$
여기서, $(1 - \frac{\eta \lambda}{n})w$ 은 $w$에 $(1 - \frac{\eta \lambda}{n})$를 곱했으므로 $w$가 값이 작아지는 방향으로 가게 됩니다. 이것을 가중치감소(weight decay)라고 합니다. weight decay는 특정 가중치기 비이상적으로 커지는 영향을 방지 합니다.  그래서 특이점(outlier)



