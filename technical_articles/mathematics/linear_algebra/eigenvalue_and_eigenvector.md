# 고유값과 고유벡터

정방행렬 $\mathbf A$가 있고 다음의 식을 만족하는 열벡터 $\mathbf x$(단, $\mathbf x \ne \mathbf 0$)가 존재할 때, $\lambda$를 행렬 $\mathbf A$의 고유값(eigenvalue)라고 하고 $\mathbf x$를 고유벡터(eigenvector)라고 합니다.
$$
\mathbf A \mathbf x = \lambda \mathbf E \mathbf x
$$
여기서, $\mathbf E$는 단위행렬입니다. 위 식을 변형하면,
$$
(\mathbf A - \lambda \mathbf E)\mathbf x = \mathbf 0
$$
여기서, $(\mathbf A - \lambda \mathbf E)$가 역행렬$(\mathbf A - \lambda \mathbf E)^{-1}$을 가진다면(정칙행렬, nonsigular matrix, regular matrix), 양변에 역행렬을 곱해 줍니다.
$$
(\mathbf A - \lambda \mathbf E)^{-1} (\mathbf A - \lambda \mathbf E)\mathbf x = (\mathbf A - \lambda \mathbf E)^{-1} \mathbf 0
$$
정리하면
$$
\mathbf x = (\mathbf A - \lambda \mathbf E)^{-1} \mathbf 0 = \mathbf 0
$$
여기서 $\mathbf x = \mathbf 0$인데, 처음에 정의한 $\mathbf x \ne \mathbf 0$와 모순 되므로, 이 모순이 없고 고유벡터가 존재하려면 $(\mathbf A - \lambda \mathbf E)$가 역행렬 $(\mathbf A - \lambda \mathbf E)^{-1}$를 가지지 말아야 합니다. 그래서,
$$
\text{det}(\mathbf A - \lambda \mathbf E) = \mathbf 0
$$

## 고유값 분해

- Eigenvalue decomposition

행렬 $\mathbf A$ 가 있을때 다음과 같이 분해 합니다.
$$
\mathbf A = \mathbf Q \mathbf \Lambda \mathbf Q^{-1}
$$
여기서, 행렬 $\mathbf Q$는 행렬 $\mathbf A$의 고유벡터를 열에 배치한 직교행렬이고, $\Lambda$는 고유값을 대각선에 배치한 대각행렬입니다. 보통 고유값은 크기순으로 배치합니다.

## 참조

- 인공지능을 위한 수학
- 오일석 2017

