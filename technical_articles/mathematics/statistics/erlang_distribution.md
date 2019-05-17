[Up](index.md)

# 얼랭분포(Erlang distribution)

얼랭분포는 [지수분포(exponential distribution)](exponential_distribution.md)를 일반화한 분포입니다. 지수분포를 사용하여, 어떤 기준점에서 시작해 **첫번째** 사건이 발생할 때까지 시간이 얼마나 소요되는 지에 대한 확률을 계산할 수 있었는데 얼랭분포를 사용하면 **n번** 성공할 때 까지의 소요시간에 대해 모델링 할 수 있습니다. 이는 이산확률분포에서 [기하분포](geometric_distributon.md)와 음이항분포의 관계와 유사합니다.

표기는

$$
\mathrm{Erlang}(n, \lambda)
$$

로 하며, 여기서 $\lambda$는 평균 발생건수이고, $n$은 성공의 횟수입니다.

얼랭분포의 확률분포함수(PDF)는 다음과 같습니다.

$$
f(x) = \begin{cases}
\frac{\lambda^n x^{n-1} e^{-\lambda x}}{(n-1)!}, & \text{where } x \ge 0, \lambda > 0 \\
0, & \text{otherwise}
\end{cases}
$$

여기서 $n = \mathbb N$ 입니다.

$n = 1$이면 지수분포와 동일하며, $n=\infty$이면 일정한 값을 갖습니다.

기대값(expectation)은

$$
\mathbb E(X) = \frac{n}{\lambda}
$$

이며, 분산(variance)은

$$
\mathrm{Var}(X) = \frac{n}{\lambda^2}
$$

로, 지수분포의 분산에서 $n=1$일 때와 같습니다.

