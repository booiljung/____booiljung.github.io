[Up](index.md)

# 포아송분포 (Poisson distribution)

포아송분포는 단위 구간 안에 어떤 사건이 일어날 횟수에 대한 기대값을 $\lambda$ 라고 했을때 그 사건이 $n$회 일어날 확률을 계산하는데 사용할 수 있으며 기호는

$$
\mathrm{Pois}(\lambda), \mathrm{Poisson}(\lambda)
$$

이며, 매개변수는 $\lambda > 0$ 입니다.

포아송분포는 왼편이 볼록한 분포인데 기대값 $\lambda$가 커질수록 오른편으로 이동해서 좌우대칭인 분포에 근접합니다. $\lambda$는 평균발생건수이므로 fraction(소수)를 가질 수 있지만, 사건이 몇번 발생했는지 나타내는 $n$은 정수값만 가질 수 있습니다. 그래서, 포아송분포는 이산확률분포입니다.

포아송 분포는 이산확률분포이므로 PMF(Probability Mass Function)를 가집니다. 반대로 연속확률분포는 PDF(Probability Density Function)을 가집니다.


포아송분포의 PMF는 다음과 같습니다.

$$
f(n; \lambda) = \frac{\lambda^n e^{-\lambda}} {n!}
$$

여기서  $e$는 자연상수입니다.

포아송분포의 CDF(Cumulative Distribution Function)는

$$
\begin{aligned}
F(k;\lambda) &= \sum_{i=0}^{\lfloor{k}\rfloor} \frac{\lambda^i}{i!} \\
&= \frac{\Gamma(\lfloor k+1 \rfloor, \lambda)}{\lfloor k \rfloor !}
\end{aligned}
$$

이며, mode(최빈값)은

$$
\lceil \lambda \rceil - 1
$$

입니다.

어떤 단위구간을 더 짧은 단위구간으로 나눌 수 있으며, 이 더 짧은구간중 어떤 사건이 발생할 확률은 전체 척도 중에서 항상 일정해야 하며, 구간의 척도가 연속변수이므로 두개 이상의 사건이 동시에 발생할 확률은 0에 가까우며, 어떤 단위구간의 사건의 발생은 다른 단위 구간의 사건의 발생으로부터 독립적입니다.

특정 구간에서 사건 발생확률은 그 구간의 크기와 비례하며, 특이하게도 기대값과 분산 모두 $\lambda$ 입니다.

포아송분포 확률은 연속확률분포인 Exponential distribution(지수분포)로 구하는 확률과 같은데, 지수분포와 단위 구간의 변화에 따라 사건발생건수도 비례하여 변하는 것을 가정합니다. 포아송분포에서 각각의 사건발생 사이의 간격은 지수분포를 따릅니다. 

다음과 같은 조건을 충족하는 경우 변수가 포아송 분포를 따릅니다.

- 데이터는 사건 횟수입니다. 여기서 사건 횟수는 상한이 없는 음수가 아닌 정수 입니다.
- 모든 사건은 독립적입니다.
- 해당 기간 동안 평균 비율은 변경되지 않습니다.

####  참조

- [wikipedia.org](https://ko.wikipedia.org/wiki/%ED%91%B8%EC%95%84%EC%86%A1_%EB%B6%84%ED%8F%AC)

