[Up](index.md)

# 베타분포 (beta distribution)

베타분포는 다른 확률분포와는 달리 자연계에 존재하는 데이터의 분포를 묘사하기 보다는 베이지안 추정(Bayesian estimation)의 결과를 묘사하기 위한 목적으로 주로 사용됩니다. 베이지안 추정은 추정하고자 하는 매개변수의 값을 하나의 숫자로 나타내는 것이 아니라 하나의 분포로 나타냅니다.

베타분포는 연속확률분포이며

$$
\mathrm{Beta}(x; \alpha, \beta)
$$

이며 여기서 매개변수 $\alpha$와 $\beta$는 $[0, 1]$ 입니다.

베타분포의 확률밀도함수는

$$
\begin{aligned}
f(x; \alpha, \beta)
&=
\frac{x^{\alpha-1}(1-x)^{\beta-1}}
{\int_0^1 u^{\alpha-1}(1-u)^{\beta-1} \mathrm d u}
\\&=
\frac{\Gamma(\alpha+\beta)}
{\Gamma(\alpha) \Gamma{(\beta)}}
x^{\alpha-1} (1-x)^{\beta-1}
\\&=
\frac{1}
{\mathrm {B}(\alpha, \beta)}
x^{\alpha-1} (1-x)^{\beta-1}
\end{aligned}
$$

여기서 베타함수 $\mathrm{B}(\alpha, \beta)$는 함수의 적분값이 1이 되도록 하기 위해 사용되었으며, $\Gamma$는 감마함수로

$$
\Gamma(\alpha) = \int_0^\infty x^{\alpha-1} e^{-x} \mathrm d x
$$

입니다. 

베타분포의 기대값(expectation)은

$$
\mathbb E[x] = \frac{\alpha}{\alpha + \beta}
$$

이며,

최빈값(mode)는

$$
\frac{\alpha-1}{\alpha + \beta - 2}
$$

이며,

분산(variance)는

$$
\mathrm{Var}[x] = \frac{\alpha \beta}{(\alpha +\beta)^2 (\alpha + \beta + 1)}
$$

입니다.

베타분포는 다운링크 빔포밍등에 사용됩니다.
