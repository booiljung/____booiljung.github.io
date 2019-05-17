[Up](index.md)

# 베이블분포 (Weibull distribution)

베이블분포는 연속확률분포의 하나로 스웨덴의 물리학자 W. Weibull 이 재료의 파괴강도를 설명하기 위해 1939년에 제안하였으며, 지수분포를 보다 일반화하여, 여러 다양한 확률분포 형태를 모두 나타낼 수 있도록 고안되었습니다. 특히 신뢰성 분야의 응용에서 자주 사용되는 수명분포를 나타냅니다.

베이블 분포는 2 또는 3개의 매개변수에 의해 특징이 지워지며 형상매개변수 $\beta$, 척도매개변수 $\eta$, 위치매개변수 $\gamma$가 있습니다. 형상모수 $\beta < 1$이면 감마분포, $\beta = 1$이면 지수분포, $\beta = 2$이면 레일리히 분포이며, $\beta = 3.5$이면 정규분포에 근사합니다.

형상 매개변수 $\beta$, 척도 매개변수 $\eta$로 매개변수 2개인 베이블분포의 PDF는

$$
f (x, \eta, \beta) =
\begin{cases}
\left( \frac{\beta}{\eta} \right)
\left( \frac{x}{\eta} \right) ^{\beta-1}
\exp \left[ - \left( \frac{x}{\eta} \right) ^\beta \right]
, & x \ge 0 \\
0, & x < 0
\end{cases}
$$

이며, 여기서 $\eta, \beta > 0$ 입니다.

형상 매개변수 $\beta$, 척도 매개변수 $\eta$, 위치 매개변수 $\gamma$로 매개변수 3개인 베이블분포의 PDF는

$$
f (x, \eta, \beta, \gamma) =
\begin{cases}
\left( \frac{\beta}{\eta} \right)
\left( \frac{x-\gamma}{\eta} \right) ^{\beta-1}
\exp \left[ - \left( \frac{x-\gamma}{\eta} \right) ^\beta \right]
, & x \ge 0 \\
0, & x < 0
\end{cases}
$$

이며, 여기서 $\eta, \beta, \gamma > 0$ 입니다.

형상 매개변수 $\beta$, 척도 매개변수 $\eta$로 매개변수 2개인 베이블분포의 누적분포는

$$
F (x, \eta, \beta) =
\begin{cases}
1 - \exp \left[ - \left( \frac{x}{\eta} \right) ^\beta \right]
, & x \ge 0 \\
0, & x < 0
\end{cases}
$$

이며,

형상 매개변수 $\beta$, 척도 매개변수 $\eta$, 위치 매개변수 $\gamma$로 매개변수 3개인 베이블분포의 누적분포는

$$
F (x, \eta, \beta, \gamma) =
\begin{cases}
1 - \exp \left[ - \left( \frac{x-\gamma}{\eta} \right) ^\beta \right]
, & x \ge 0 \\
0, & x < 0
\end{cases}
$$

이며, 여기서 $x \ge 0$ 그리고 $\eta, \beta, \gamma > 0$ 입니다.

형상 매개변수 $\beta$, 척도 매개변수 $\eta$로 매개변수 2개인 베이블분포의 기대값(expectation)은

$$
\mu =
\mathbb E[X] = \eta \Gamma \left( 1 +  \frac{1}{\beta} \right)
$$
이며,

형상 매개변수 $\beta$, 척도 매개변수 $\eta$, 위치 매개변수 $\gamma$로 매개변수 3개인 베이블분포의 기대값은

$$
\mu =
\mathbb E[X] = \gamma + \eta \Gamma \left( 1 +  \frac{1}{\beta} \right)
$$

입니다.

형상 매개변수 $\beta$, 척도 매개변수 $\eta$로 매개변수 2개, 또는 $\gamma$까지 매개변수가 3개인 와이블분베포의 분산(variance)은

$$
\sigma^2 =
\eta^2 \left\{
    \Gamma
    \left(
    1 + \frac{2}{\beta}    
    \right)
    -
    \left[
        \Gamma
        \left(
        1 + \frac{1}{\beta}
        \right)
    \right] ^2
\right\}
$$

으로 $\gamma$ 는 사용되지 않습니다.

베이블분포는 주로 다음과 같은 경우에 사용 됩니다.

- 부품의 수명 추정 분석
- 산업현장에서 어떤 제품의 제조와 배달에 걸리는 시간
- 날씨예보
- 신뢰성공학에서 실패분석

