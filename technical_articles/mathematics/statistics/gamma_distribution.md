[Up](index.md)

# 감마분포 (gamma distribution)

감마분포는 지수분포(exponential distribution)와 얼랭분포(Erlang distribution), 그리고 카이제곱분포(chi-square distribution)의 일반적인 경우입니다. 일반적이기 때문에 매개변수의 변화에 따라 분포의 모양이 비교적 크게 변합니다.

감마분포는 $\alpha$번째 사건이 일어날 때까지 걸리는 시간에 대한 연속확률분포를 말합니다.  감마분포는

$$
X \sim \mathrm{Gam}(\alpha, \beta)
$$

로 표기합니다.

확률변수 $x$가 감마분포를 따를 때 PDF는 다음과 같습니다.

$$
f(x) = 
\begin{cases}
\frac{1}{\beta^\alpha \Gamma(\alpha)} 
x^{\alpha-1}
e^{-\frac{1}{\beta}}
,& \text{where } x, \alpha, \beta > 0 \\
0
,& \text{where } x \le 0
\end{cases}
$$

여기서, $\alpha \in \mathbb N$  경우 감마분포는 얼랭분포가 되며, $\alpha = 1$, $\beta = \frac{1}{\lambda}$이면 지수분포가 되며, $\alpha = \frac{\nu}{2}$, $\beta = 2$이면 즉, $\Gamma(\frac{\nu}{2},2)$이면 카이제곱분포가 됩니다. 이때 자유도는 $\nu$입니다.

기대값은

$$
\mathbb E[X] = \frac{\alpha}{\beta}
$$

이며,

분산은

$$
\mathrm{Var}[X] = \frac{\alpha}{\beta^2}
$$

입니다.

다른 분포와 비교하여, 지수분포는 인접 사건 간의 시간 간격을 나타냅니다. 즉 사건이 1회 발생하였을때까지의 시간 분포를입니다.

감마분포는 어떤 사건과 $k$번째 후의 사건과의 시간간격을 나타냅니다. 즉, 사건이 $k$회 일어날때까지의 시간 분포를 말하며, 지수분포를 일반화한 것이며, 지수분포와 달리 기억성을 가집니다.

카이제곱분포는 감마분포에서 $\alpha = \frac{\nu}{2}, \beta = 2$인 특별한 경우입니다.

감마분포가 푸아송분포, 지수분포, 평균이 주어진 정규분포, 파레토분포, 모양매개변수가 주어진 감마분포와 역감마분포 등의 분포와 켤레사전확률분포를 구성합니다.


