# Objective functions

목적함수는 오차함수(error fuction), 손실함수(loss function), 비용함수(cost function)이라고도 한다.

최소값을 찾는 경우라면 비용함수라고 한다.
$$
x' = \arg\max_x f(x)
$$
최대값을 찾은 경우라면 함수라고 한다.
$$
x' = \arg\min_x f(x)
$$
목적함수는 $\mathbb J$로 표기하며, 비용함수는 $\mathbb C$, 손실함수는 $\mathbb L$, 오차함수는 $\mathbb E$로 표기하기도 한다.

#### L1

Pytorch: `L1Loss`

#### SmoothL1Loss

Pytorch: `SmoothL1Loss`

#### 평균제곱오차 (Mean Squared Error: MSE)

$$
\mathbb E = \frac{1}{n} \sum_k^n (y_k - t_k)^2
$$

Pytorch: `MSELoss`

#### 교차엔트로피오차 (Cross Entropy Error: CEE)

$$
\mathbb E = -\sum_k t_k \log y_k
$$

Pytorch: `CrossEntropyLoss`

#### NLL

Pytorch: `NLLLoss`

#### Poisson NLL

Pytorch: `NLLLoss`

#### KLDiv

Pytorch: `KLDivLoss`

#### CTC

Pytorch: `CTCLoss`

#### BCE

Pytorch: `BCELoss`

#### BCE with Logits

Pytorch: `BCEWithLogitsLoss`

#### MarginRanking

Pytorch: `MarginRankingLoss`

#### HingeEmbedding

Pytorch: `HingeEnbeddingLoss`

#### MultiLabelMarginLoss

Pytorch: `MultiLabelMarginLoss`

#### SoftMargin

Pytorch: `SoftMargin`

#### MultiLabelSoftMargin

Pytorch: `MultiLabelSoftMarginLoss`

#### CosineEmbedding

Pytorch: `CosineEmbeddingLoss`

#### MultiMargin

Pytorch: `MultiMarginLoss`

#### TripletMargin

Pytorch: `TripletMarginLoss`


