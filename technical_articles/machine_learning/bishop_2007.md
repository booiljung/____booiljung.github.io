### 1.2 확률론

오차 함수 (error function):
$$
E(\mathbf w) = {1 \over 2} \sum_{n=1}^N \left\{ y (x_n, \mathbf w) - t_n \right\}^2
$$
평균 제곱 오차 (root mean square error , RMS error):
$$
E_\text{RMS} = \sqrt { 2 E (\mathbf W ^*) \over N}
$$
패널티항 추가하는 정규화 (regularization):
$$
\widetilde E ( \mathbf w ) =
$$




주변 확률 (marginal probability):
$$
p(X)
$$
합의 법칙 (sum rule):
$$
p(X) = \sum _Y p(X, Y)
$$
곱의 법칙 (production rule):
$$
p(X, Y) = p(Y|X)p(X)
$$
$x$가 $(-\infty, z)$일때 누적 분포 함수 (cumulative distribution function):
$$
P(z) = \int_{-\infty}^z p(x) \mathrm d x
$$
이산 분포의 기댓값 (expectation):
$$
\mathbb E(x) = \sum_x p(x, y) \mathrm dy
$$
연속 분포의 기댓값 (expectation):
$$
\mathbb E(x) = \int p(x)f(x) \mathrm dx
$$
함수 $f(x, y)$의 평균값을 $x$의 분포에 대해 구하라:
$$
\mathbb E_x \left[ f(x, y) \right]
$$
조건부 분포에 해당하는 조건부 기대값 (conditional expectation):
$$
\mathbb E_x \left[ f | y \right] = \sum_x p(x|y) f(x)
$$
$f(x)$의 분산 (variance):
$$
\begin{align*}
\operatorname{var}\left[f\right] &= \mathbb E \left[ (f(x) - \mathbb E\left[f(x)\right]^2  \right]
\\&= \mathbb E\left[f(x)^2\right] - E\left[f(x)\right]^2
\end{align*}
$$
변수 $$x$$ 자체에 대한 분산:
$$
\text{var} \left[ x \right] = \mathbb E \left [ x^2 \right ] - \mathbb E \left[ x \right]^2
$$
두 개의 확률 변수 $x$와 $y$에 대한 공분산 (covariance):
$$
\begin{align*}
\operatorname{cov} \left[ x,y \right] &= \mathbb E_{x,y} \left[ \left\{ x - \mathbb E \left[ x \right] \right\} \left\{ y-\mathbb E \left[ y \right] \right\} \right]
\\ & \mathbb E_{x,y} \left[ xy \right] - \mathbb E \left[ x \right] \mathbb E \left[ y \right]

\end{align*}
$$
두 개의 확률 변수가 벡터인 경우 $\mathbf x$와 $\mathbf y$에 대한 공분산:
$$
\begin{align*}
\operatorname{cov} \left[ \mathbf x, \mathbf y \right] &= \mathbb E_{\mathbf x, \mathbf y} \left[ \left\{ \mathbf x - \mathbb E \left[ \mathbf x \right] \right\} \left\{ \mathbf y ^\top-\mathbb E \left[ \mathbf y^\top \right] \right\} \right]
\\ & \mathbb E_{x,y} \left[ \mathbf x \mathbf y^\top \right] - \mathbb E \left[ \mathbf x \right] \mathbb E \left[ \mathbf y^\top \right]
\end{align*}
$$
벡터 $\mathbf x$의 구성 원소들 서로간의 공분산 표현:
$$
\operatorname{cov} \left[\mathbf x \right ] \equiv \operatorname{cov} \left[\mathbf x, \mathbf x \right ]
$$

#### 1. 2. 3 베이지언 확률

$\mathcal D$를 관찰한 후의 $\mathbf w$에 대한 불확실성을 사후 확률 $p(\mathbf w | \mathcal D)$로 표현:
$$
p(\mathbf w | \mathcal D) = { p ( \mathcal D | \mathbf w) p (\mathbf w) \over p(\mathcal D)}
$$
$\mathbf w​$에 대해 적분하면:
$$
p (\mathcal D) = \int p(\mathcal D | \mathbf w) p (\mathbf w) \mathrm d \mathbf w	
$$








