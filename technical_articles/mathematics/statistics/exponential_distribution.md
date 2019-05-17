[Up](index.md)

# 지수분포 (exponential distribution)

지수분포는 연속확률분포(continuous probability distribution)의 일종으로, 사건(event)이 서로 독립적일때, 일정 시간 동안 발생하는 사건의 횟수가 [푸아송분포(Poisson distribution)](poisson_distribution.md)를 따른다면, 다음 사건이 일어날때까지 대기 시간은 지수분포를 따릅니다. 이는 기하분포와 유사한 측면이 있습니다.

지수분포는

$$
\mathrm{Exponential} (\lambda)
$$

또는

$$
\mathrm{Exp}(\lambda)
$$

로 나타냅니다.

매개변수는 $\lambda$로 빈도를 나타내며, $\lambda > 0$ 입니다.

지수분포의 PDF(probability density function; 확률밀도함수)는

$$
f(x; \lambda) = \begin{cases}
\lambda e ^{-\lambda x}, & \text{where } x \ge 0 \\
0, & \text{where } x < 0
\end{cases}
$$

로 정의 됩니다. 이를 단위계단함수를 사용하여 정의하면,

$$
f(x;\lambda) = \lambda e ^{\lambda x} H(x)
$$

가 되며, 여기서 $\lambda$는 빈도를 나타내는 매개변수이며, 확률변수 $x$는 구간 $[0, \infty)$에 정의됩니다.

CDF(cumulative distribution function; 누적분포함수)는

$$
F(x; \lambda) = \begin{cases}
1 - e^{\lambda x}, & \text{where } x \ge 0 \\
0, & \text{where } x < 0
\end{cases}
$$

혹은

$$
F(x; \lambda) = (1-e^{-\lambda x}) H(x)
$$

입니다.

확률변수 $X$가 빈도 $\lambda$를 모수로 갖는 지수분포를 따른다면, 기대값(expectation)은

$$
\mathbb E (X) = \frac{1}{\lambda}
$$

으로, 단위 시간당 사건이 $\lambda$회 발생한다면, 사건 사이에 평적으로 $\frac{1}{\lambda}$시간 만틈 기다릴 것이라는 것을 의미합니다. 기대값은 평균에만 의존하므로 이산확률분포인 기하분포와 같이 memoryless property를 가집니다. 따라서 생존모델(duration model)에서 hazard rate가 시간에 관계 없이 constant(일정)한 경우에는 지수분포를 사용하여 모델링 할 수 있습니다.

지수분포의 평균은 평균에만 의존하는데

$$
\text{Var}(X) =\frac{1}{\lambda ^2}
$$

입니다.

최빈값(mode)는 $0$이며, 비대칭도는 $2$이며, 첨도는 $6$ , 중앙값(median)은 $\frac{\ln2}{\lambda}$, 엔트로피는 $1 - \ln \lambda$입니다.

#### 참조

- wikipedia.org