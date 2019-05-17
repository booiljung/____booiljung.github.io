[Up](index.md)

# 기초 확률론

## 확률

### 결합확률

$x$ 라는 사건과 $y$라는 사건이 함께 발생할 확률을 결합확률(joint probability)라고 합니다.
$$
P(y, x)
$$

### 조건부확률

$y$라는 사건이 발생한 상태에서 $x​$라는 사건이 발생할 확률을 조건부 확률(conditional probability)이라고 합니다.
$$
P(x|y)
$$

조건부 확률은 $|​$의 우측에 이미 알고 있는 사건의 확률을 쓰고, 좌측에 추정해야 할 사건의 확률을 씁니다.

### 곱규칙

$x$ 라는 사건과 $y$라는 사건이 함께 발생할 확률은 $y$ 라는 사건이 발생한 상태에서 $x$라는 확률이 발생할 확률에, $y$라는 사건이 발생할 확률을 곱한 것과 같습니다. 이것을 곱규칙(product rule)이라고 합니다.
$$
P(y, x) = P(x|y)P(y)
$$

### 합규칙

$$
P(x) = \sum_yP(y, x) = \sum_yP(x|y)P(y)
$$

이때 다음식
$$
P(x, y) = p(x)P(y)
$$
가 만족하면 독립(independent)이라고 말합니다.

## 베이즈 정리

일반적으로 $P(y, x)$나 $P(x, y)$ 는 같습니다. 그래서 다음 식이 성립합니다.
$$
P(y, x) = P(x|y)P(y) = P(x, y) = P(y|x)|P(x)
$$
위 식을 정리하면 다음식이 됩니다.
$$
P(y|x) = \frac{P(x|y)P(y)}{P(x)}
$$
이것을 베이즈 정리(Bayes formula)라고 합니다. 베이즈 정리는 다음 질문

> 빨강 공이 나왔다는 사실만 알고 어느 병에서 나왔는지 알 수 없는 경우, 어느 병에서 나왔는지 추정하라.

에 합리적인 답을 구할 수 있습니다. 그래서 가장 큰 조건부 확률을 선택하는 전략을 세울 수 있으며 다음 식
$$
\hat y = \arg\max_y P(y|x)
$$
으로 표현할 수 있습니다. 이 식을 베이즈정리에 대입하면
$$
\hat y = \arg\max_y P(y|x) = \arg\max_y \frac{P(x|y)P(y)}{P(x)}
$$

으로 표현할 수 있습니다.

베이즈 정리
$$
P(y|x) = \frac{P(x|y)P(y)}{P(x)}
$$
는 사전확률$P(y)$로 사후확률 $P(y|x)$를 구합니다. 여기서, $P(x|y)$ 우도(likehood)입니다. 우도는 likehood의 첫자 $\mathcal L$을 가져오고, 조건부확률의 파마미터를 교환하여, $\mathcal L(y|x)$로 표기하기도 합니다.
$$
P(x|y) = \mathcal L(y|x)
$$

### 최대 우도 추정

$$
P(\mathcal X | \Theta) = P(\mathbf x_1, \mathbf x_2, \mathbf x_3, \dots, \mathbf x_n | \Theta) = \prod_{i=1}^n P(\mathbf x_i| \Theta)
$$

그래서 최대 우도 추정은,
$$
\bar \Theta = \arg\max_\Theta P(\mathcal X|\Theta)
= \arg\max_\Theta \sum_{i=1}^n \log P(\mathbf x_i|\Theta)
$$

### 평균과 분산

평균은
$$
\mu = \frac{1}{n} \sum_{i=1}^n x_i
$$
분산은
$$
\sigma^2 = \frac{1}{n} \sum_{i=1}^n(x_i-\mu)^2
$$
평균벡터는
$$
\mathbf \mu = \frac{1}{n} \sum_{i=1}^n \mathbf x_i
$$
공분산은


$$
\mathbf \Sigma = \frac{1}{n} \sum_{i=1}^n(\mathbf x_i-\mathbf \mu)(\mathbf x_i-\mathbf \mu)^\top
$$

### 확류분포

확률변수가 하나일때
$$
\mathcal N(x;\mu,\sigma^2)
= \frac{1}{\sigma\sqrt{2\pi}}
\exp\left(
	-\frac{1}{2}
	\left(
		\frac{x-\mu}{\sigma}
	\right)^2
\right)
$$
확률변수가 벡터일때
$$
\mathcal N(\mathbf x;\mathbf \mu, \mathbf \sigma^2)
= \frac{1}{\sqrt{\left|\mathbf\Sigma\right|}\sqrt{(2\pi)^d}}
\exp\left(
	-\frac{1}{2}
		(\mathbf x-\mathbf \mu)^\top
		\mathbf \Sigma^{-1}
		(\mathbf x-\mathbf \mu)
\right)
$$

## 정보이론

자기 정보는
$$
h(e_i) = -\log_2P(e_i)
$$
또는
$$
h(e_i) = -\ln P(e_i)
$$
여기서, 밑수가 2인 로그함수를 사용하는 경우 자기 정보의 단위는 비트(bit)이며, 밑이 $e$인 자연로그를 사용하는 경우 단위는 나츠(nat)이다.

자기 정보가  특정 사건 $e_i$의 정보량을 측정한다면, 엔트로피는 확률분포의 무질서도 또는 불확실성을 측정한다.

이산확률분포의 엔트로피 $H$는
$$
H(x) = -\sum_{i=1}^k P(e_i)\log_2P(e_i)
$$
또는
$$
H(x) = -\sum_{i=1}^k P(e_i)\ln P(e_i)
$$
이며

연속확률분포의 엔트로피 $H$는
$$
H(x) = -\int_{\R} P(x)\log_2P(x)
$$
또는
$$
H(x) = -\int_{\R} P(x)\ln P(x)
$$
있습니다.

두 확률 $P$와 $Q​$간의 교차엔트로피는 
$$
\begin{aligned}
H(P, Q) &= -\sum_{x} P(x)\log_2 Q(x)\\
&= -\sum_{i=1}^k P(e_i)\log_2 Q(e_i)
\end{aligned}
$$
이며, 다음식 처럼
$$
\begin{aligned}
H(P, Q)
&= -\sum_{x} P(x)\log_2 Q(x) \\
&= -\sum_{x} P(x)\log_2 P(x) + \sum_{x} P(x)\log_2 P(x) - \sum_{x} P(x)\log_2 Q(x) \\
&= H(P) + \sum_{x} P(x) \log_2 \frac{P(x)}{Q(x)}
\end{aligned}
$$
유도할 수 있습니다. 여기서 마지막식의 두번쨰 항 $\sum_{x} P(x) \log_2 \frac{P(x)}{Q(x)}$을 KL divergence라고 하고, 다음
$$
\mathrm{KL}(P\|Q) = \sum_{x} P(x) \log_2 \frac{P(x)}{Q(x)}
$$
과 같이 정의하며 두 확률분포가 얼마나 다른지 측정하며, $P$와 $Q$가 같으면 $0$이 됩니다. 그리고,교차엔트로피와 KL 다이버전스는 다음
$$
\begin{aligned}
H(P, Q)
&= H(P) + \sum_{x} P(x) \log_2 \frac{P(x)}{Q(x)}
\end{aligned}
$$
이며, 이것은 '$P$에 대한 엔트로피 + $P$와 $Q$ 간의 $\mathrm{KL}$ 다이버전스' 라는 의미입니다.