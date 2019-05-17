[Up](index.md)

# 연속균등분포 (continuous uniform distribution)

연속균등분포는 연속확률분포로 분포가 특정 구간 내에서 균등하게 있을 경우를 나타낼 수 있다. 이 분포는 매개변수 $a$, $b$를 받으며 $[a, b]$ 구간에서 균등한 확률을 가진다. 이때, $\infty < a < b < \infty$이다.

보통 기호로

$$
\mathcal U(a, b)
$$

로 나타내며, $\mathcal U(0, 1)$인 경우를 표준연속균등분포라고 부른다.

지지집합은 

$$
x \in [a, b]
$$

이다.

확률밀도 PDF(Probability Density)는

$$
\begin{cases}
    \frac{1}{b-a}, & \text{where } a \le x \le b\\
    0, & \text{otherwise}
\end{cases}
$$

입니다.

CDF(누적분포함수)는

$$
\begin{cases}
\frac{x-a}{b-a}, & \text{where } a \le x \le b \\
0, & \text{where } x < a \\
1, & \text{where } x \ge b
\end{cases}
$$

입니다.

기대값(expectation)과 중앙값(median)은

$$
\frac{1}{2}(a + b)
$$

입니다.

분포가 균등하므로

$$
x \in [a, b]
$$

로 모두 범위가  최빈값(mode)에 해당합니다.

분산은

$$
\frac{1}{12}(b-a)^2
$$

이며, 엔트로피는

$$
\ln (b-a)
$$

입니다.

