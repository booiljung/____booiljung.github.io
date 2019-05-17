[Up](index.md)

## t-SNE

t-Distributed Stochastic Neightbor Enbedding (t-SNE)은 데이터의 차원 축소나 차원 축소를 통한 시각화에 사용되는 기계학습 알고리즘 중 하나로, 제프리 힌튼에 의해 개발 되었습니다.

t-SNE는 비선형 차원 축소 기법으로, 고차원 데이터를 특히 2, 3차원 등으로 줄여서 가시화하는데 사용됩니다. t-SNE는 유사도가 높은 데이터는 근접한 2, 3차원 지점으로, 유사도가 낮은 데이터는 멀리 떨어진 지점으로 맵핑합니다.

t-SNE 알고리즘은 두 단계에 걸쳐서 진행됩니다. 첫번째로, 각 데이터 쌍에 대해서 결합분포를 만들고, 이 분포는 유사도가 높은 데이터가 선택될 확률은 높지만, 유사도가 낮은 데이터는 선택될 확률이 낮도록 설계 됩니다.

t-SNE는 워드 벡터와 같이 고차원 데이터를 시각화 하는데 유용한 알고리즘 이기도 합니다.

## Stochastic Neighbor Embedding

SNE (Stochastic Neighbor Embedding)이란 고차원의 원공간에 존재하는 데이터 $\mathbf x_i$의 이웃간의 거리를 최대한 보존하는 저차원의 $\mathbf y_i$를 학습하는 방법론입니다. 이름에서 유추할 수 있듯이 거리 정보를 확률적으로 나타냅니다.

먼저, $\mathbf x_i$에 대한  $\mathbf x_j$의 유사도를 다음 식의 조건부 확률로 측정합니다.
$$
p_{i \backslash j} =
\frac{\exp(\frac{- \| \mathbf x_i - \mathbf x_j \| _2^2}{2 \sigma_i^2})}
{\sum_{k \ne i} \exp (\frac{ -\| \mathbf x_i - \mathbf x_k \| }{2 \sigma _i^2}) }
\tag{1}
$$
이 분포는 가우시안 분포를 닮았습니다. $\mathbf x_i$와 가까운 $\mathbf x_j$에 높은 확률을 부여하고, 먼 $\mathbf x_j$에 대해 0에 가까운 확률을 부여 합니다. $\sigma_i$는 $\mathbf x_i$ 주변의 데이터 분포에 따라 결정되며, 데이터가 밀집될수록 작은 값을 가집니다. 이 식은 $p_{ji} \ne p_{ij}$ 이므로 대칭이 아닙니다. 대칭성을 만족 하려면 다음 식을 정의 합니다.
$$
p_{ij} = p_{ji} = \frac{p_{j \backslash i} + p_{i \backslash j}}{2n}
\tag{2}
$$
$p$는 고차원 원공간에 존재하는 $i$번째 개체 $\mathbf x_i$가 주어졌을 때 $j$번째 이웃인 $\mathbf x_j$가 선택될 확률을 의미합니다. 

변환된 공간에서 유사도는 가우시안 분포대신 Student t-distribution를 사용합니다. 변환 공간에서 점을 $\mathbf y$라고 표기하면 변환 공간에서 $\mathbf y_i$와 $\mathbf y_j$의 유사도는 다음으로 정의 됩니다.
$$
q_{ij} =
\frac{(1 + \| \mathbf y_i - \mathbf y_j \| _2 ^2) ^{-1}}
{\sum_{k \ne i} (1 + \| \mathbf y_i - \mathbf y_k \| _2^2)}
\tag{3}
$$
$q$는 저차원에 임베딩된 $i$번째 개체 $\mathbf y_i$가 주어졌을때 $j$ 번째 이웃인 $\mathbf y_j$가 선택될 확률을 나타냅니다.

원래 공간에서의 데이터 구조가 변환된 공간에서 충실하게 유지되어야 하므로, 원래 공간에서의 $P$ 확률 분포와 변환된 공간에서 $Q$ 확률 분포가 가까울 수록 좋습니다. 따라서, 두 분포 사이의 KL 다이버전스를 목적 함수로 사용합니다. 변환된 샘플의 집합을 $\mathbb X' = \{ \mathbf y_1, \dots, \mathbf y_n \}$이라고 하면 목적함수를 다음처럼 쓸 수 있습니다.
$$
\begin{aligned}
J(\mathbb X') &= \mathrm {KL}(P \| Q)
\\ &= \sum_{i=1}^n \sum_{j=1}^n p_{ij} \log \frac{p_{ij}}{q_{ij}}
\end{aligned}
\tag{4}
$$
SNE 연구진은 계산 속도를 높이기 위해 몇가지 학습 트릭을 도입하였습니다. $\sigma _i$는 각 개체마다 데이터의 밀도가 달라서 이웃으로 뽑힐 확률이 왜곡되는 현상을 방지하기 위한 값입니다. 반복 실험 결과 $p$를 계산할 때 쓰는 $\sigma_i$는 고정된 값을 써도 성능에 큰 차이를 보이지 않으며 $\sigma _i$ 계산은 생략해도 됩니다.

게다가 $i$번째 개체가 주어졌을때 $j$번째 개체가 이웃으로 뽑힐 확률과 $j$번째 개체가 주어졌을 때 $i$번째 개체가 선택될 확률이 동일 하다고 가정하고 풀어도 성능이 나쁘지 않았다고 하며, 그래서 $p$와 $q$를 다음처럼 다시 쓸 수 있습니다.
$$
\begin{aligned}
p_{ij} &= \frac{p_{j \backslash i} + p_{i \backslash j}}{2}, \\
q_{ij} &= \frac{q_{j \backslash i} + q_{i \backslash j}}{2}
\end{aligned}
\tag{5}
$$
이를 최소로 하는 $\mathbb X'$를 다시 찾으면 됩니다. 최소점은 경사하강법으로 찾는데, 목적함수 $\mathbf y_i$로 미분하여 그레디언트를 다음과 같이 얻을 수 있습니다.
$$
\begin{aligned}
J(\mathbb X') &= \mathrm {KL}(P \| Q)
\\ &= \sum_{i=1}^n \sum_{j=1}^n p_{ij} \log \frac{p_{ij}}{q_{ij}}
\end{aligned}
\tag{6}
$$

$$
\frac{\partial J}{\partial \mathbf y_i}
= 4 \sum_{j=1}^n 
(p_{ij} - q_{ij}) (\mathbf y_i - \mathbf y_j) \left (1+ \| \mathbf y_i - \mathbf y_j \|_2^2 \right)^{-1}
$$

#### 참조

- [wikipedia.org](https://ko.wikipedia.org/wiki/T-%EB%B6%84%ED%8F%AC_%ED%99%95%EB%A5%A0%EC%A0%81_%EC%9E%84%EB%B2%A0%EB%94%A9)
- Maaten [블로그](https://lvdmaaten.github.io/tsne/)
- [Visualizing Data using t-SNE](http://www.jmlr.org/papers/v9/vandermaaten08a.html) Laurens van der Maaten, Geoffrey Hinton, Journal of Machine Learning Research 9 (2008) 2579-2605. 수식 유도, 품질 개선을 위한 요령, 다른 기법과의 비교, $n$이 무척 큰 상황에서의 처리.
- 기계학습 오일석 2017.
- Student t-distribution는 원점에서 멀어질수록 가우시안보다 0에 덜 가까운 두꺼운 꼬리라는 성질을 가진 분포입니다.