[Up](index.md)

# 정보이론

*이 정보이론은 Christopher M. Bishop 의 저서를 기초로 번역하며 어려운 부분을 해설하였습니다.*

---

정보이론은 최대한 많은 데이터를 매체에 저장하거나 채널을 통해 전송하기 위해 데이터를 정량화하는 응용수학의 한 분야다. 단위는 엔트로피이며 보통 저장 또는 통신에 사용되는 비트 (binary digit; bit)또는 나트(nats)로 표현 된다.

정보량(정보엔트로피)는 열역학에서 사용되는 엔트로피를 가져온 개념으로, 정보엔트로피가 높을수록 불확실성이 커진다. 반대로 계에서 정보량은 불확실성의 정도이므로 불확실성이 적은 계의 정보엔트로피는 감소 할 수 있다.

---

이산확률변수 $x$를 고려해보자. 이 변수에 대한 특정 값을 관찰 할 때 얼마나 많은 정보량이 수신되는지 묻는다. 정보량은 $x$의 값을 학습 할 때 '놀라움의 정도'로 볼 수 있다.

거의 발생할 가능성이 없는 사건이 발생 했다는 말을 들을 경우, 발생할 가능성이 높다고 하는 다른 사건이 발생 했을 경우보다 더 많은 정보를 얻게 된다. 예를 들어, 겨울에 눈이 내리면 일상적인 사건이므로 정보량이 낮다. 반대로, 여름에 눈이 내렸다면 거의 발생할 가능성이 없는 사건으로 정보량이 높다.

발생 가능성이 낮은 사건은 발생 확률이 낮은 사건이며, 발생 가능성이 높은 사건은 발생 확률이 높은 사건이다.

---

정보 내용 측정은 확률분포 $p(x)$에 의존하므로 확률 $p(x)$의 **단조 함수(monotonic function)**이며, 정보 내용을 표현하는 양 $h(x)$를 찾아야 한다. 여기서 $h$는 정보의 양을 나타내며, $p$는 확률을 나타낸다.

서로 관계가 없는 두 가지 사건 $x$와 $y$가 있는 경우를 보자. 두 사건 모두를 관찰함으로써 얻은 정보가 각각의 정보로부터 얻은 정보의 합계(sum of information)가 되어야 하므로  $h(x, y) = h(x) + h(y)$ 다. 관계가 없는 두 개의 사건은 통계적으로 독립적(statistically independent)이므로 결합확률(joint probability)이며 $p(x, y) = p(x)p(y)$다.

이 두 관계로부터, $h(x)$는 $p(x)$의 대수(logarithm)로 주어져야 한다는 것을 알 수 있으므로:



$$
h(x) = −\log_2 p(x)
\tag{1}
$$



으로 나타낼 수 있다. 통계치를 정보량으로의 변환은 음의 로그다. 여기서 음의 부호는 확률이 (0-1]이므로 정보량이 0 또는 양수로, 낮은 확률의 사건 $x$는 높은 정보 내용으로 변환한다. 발생할 확률이 낮은 사건은 높은 정보 내용에 해당한다.

로그의 선택은 임의적다. 섀넌의 정보이론에서 보편적인 관례로 사용하는 $2$를 밑수(basis)로 하는 로그 $\log_2$를 먼저 볼 계획이다. $2$를 밑수로 하는 로그의 경우 $h(x)$의 단위는 비트(bits;'binary digits')이다.

---

이제 송신자가 무작위 변수의 값을 수신자에 전송하려고 한다고 가정 해보자. 이 절차에서 전송하는 평균 정보량은 분포 $p(x)$에 대한 (1)의 기대치를 취함으로써 얻어지며:



$$
\mathrm H [x] = - \sum_x p(x) \log_2 p(x)
\tag{2}
$$


에 의해 주어 진다. 이 중요도에 대한 양을 무작위 변수 $x$의 **엔트로피**라고 한다. 정보 엔트로피는 $\mathrm H$로 나타낸다. $\lim_{p \rightarrow 0} p \ln p = 0$이므로  $p(x) = 0$와 같은 $x$에 대한 값을 만날 때마다 $p(x) \ln p(x) = 0$를 취해야 한다는데 유의해야 한다.

---

8개의 가능한 상태를 갖는 임의의 변수 $x$를 고려해 보자. 각각의 상태는 동일하게 가능하다. 수신자에게 $x$의 값을 전달하기 위해서는 길이가 3 비트인 메시지를 전송해야 한다.

이 변수의 엔트로피는 다음과 같이 주어 진다.



$$
\mathrm H [x] = -8 \times \frac{1}{8} \log_2 \frac{1}{8} = 3 \text{bits}
$$
---

다음은 8 개의 가능한 상태 $\{a, b, c, d, e, f, g, h\}$를 가지는 변수의 예(Cover and Thomas, 1991)를 고려해 보겠습니다. 이 경우 각 상태의 발생 확률은 $(\frac{1}{2}, \frac{1}{4}, \frac{1}{8}, \frac{1}{16}, \frac{1}{64}, \frac{1}{64}, \frac{1}{64}, \frac{1}{64},)$이며, 이 경우의 엔트로피는



$$
\begin{aligned}
\mathrm H [x]
&=
-\frac{1}{2} \log_2 \frac{1}{2}
-\frac{1}{4} \log_2 \frac{1}{4}
-\frac{1}{8} \log_2 \frac{1}{8}
-\frac{1}{16} \log_2 \frac{1}{16}
-\frac{4}{64} \log_2 \frac{1}{64}
\\&=
2 \text{bits}
\end{aligned}
$$



이다.

균일분포 예제는 3bits이고, 비균일분포 예제는 2bits라는 것을 확인하였다. 비균일분포는 균일한분포보다 엔트로피가 더 작다는 것을 알 수 있다. 이제, 엔트로피를 무질서의 척도로 논의할때 이에 대한 통찰력을 얻게 될 것이다.

변수 상태의 신원(identity)을 수신자에게 전송하는 방법을 고려해 보자. 이전과 마찬가지로 3 비트 숫자를 사용하여 이 작업을 수행 할 수 있다. 그러나 평균 코드 길이가 짧아지기를 기대하면서 발생 가능성이 낮은 이벤트에 대해 더 긴 코드를 할당하면, 보다 발생 가능성이 높은 사건에 대해 더 짧은 코드를 사용하는 비균일분포(uniform distribution)를 이용할 수 있다.

예를 들어, 다음 코드 문자열 세트인 $0$, $10$, $110$, $1110$, $111100$, $111101$, $111110$, $111111$를 사용하여 상태 $\{a, b, c, d, e, f, g, h\}$를 나타내어 수행해 보자.

전송되어야 하는 평균 코드 길이(average coding length)는



$$
\text{평균코드길이} =
\frac{1}{2} \times 1
+ \frac{1}{4} \times 2
+ \frac{1}{8} \times 3
+ \frac{1}{16} \times 4
+ 4
+ \frac{1}{64} \times 6
=2 \text{bits}
$$



이며, 이것은 앞서 무작위 변수의 엔트로피와 동일하다.

이러한 문자열을 해당 구성 요소 부분으로 연결을 명확하게 할 수 있어야 하므로 더 짧은 코드 문자열을 사용할 수 없다. 예를 들어, $11001110$은 상태 시퀀스(state sequence) $c, a, d$로 고유하게 디코딩 한다.

---

엔트로피와 가장 짧은 코딩 길이 간의 이러한 관계는 일반적이다.

섀넌(Shannon, 1948)의 **무잡음 코딩 정리(noiseless coding theorem)는** 엔트로피가 무작위 변수의 상태를 전송하는 데 필요한 비트 수에 대한 하한선이라고 했다. 이제 자연로그를 엔트로피 정의에 사용하여 다른 개념들과 더 편리하게 연결 되도록 하자. 이 경우 엔트로피는 비트가 아니라 내츠(nats) 단위이며 $\log_2$ 대신에 $\ln$을 사용 한다.

---

우리는 무작위 변수의 상태를 지정하는데 필요한 평균 정보량 측면에서 엔트로피의 개념을 도입했다. 사실, 엔트로피의 개념은 열역학적 평형의 맥락에서 소개되었으며, 나중에는 통계역학의 발달을 통한 무질서의 척도로서 더 깊은 해석이 주어지는 물리학에서 기원을 가지고 있다.

---

$i$번째 칸(bin)에 $n_i$ 개체가 있도록 칸(bins) 집합 사이에 나눌 $N$ 개의 동일한 개체 집합을 고려하여 엔트로피를 이해해 보자. 객체를 칸에 할당하는 다양한 경우의 수를 고려할 수 있다.

첫 번째 객체를 선택하는 $N$ 가지 방식이 있고, 두 번째 객체를 선택하는 $N-1$ 방식이 있으며, 계속하여, 모든 $N$개의 객체를 각각의 칸에 할당하는 방법은 총 $N!$ 가지라는 방법을 유도할 수 있다. (여기서 $N!$은 '팩토리얼 N')

그러나 각 칸 내의 객체를 재배치하는 것을 구분하지 않을 것이므로 $i$번째 칸에는 객체를 재정렬하는 $n_i!$ 가지 방식이 있으므로 $N$ 개의 객체를 칸에 할당하는 방법의 총 수는



$$
W =
\frac{N!}{\prod_i n_i!}
\tag{3}
$$



이다. 이것은 다중성(multiplicity)이라고 불린다. 엔트로피는 적절한 상수로 스케일 된 다중성의 로그



$$
\begin{aligned}
\mathrm H
&= \frac{1}{N} \ln W
\\&= \frac{1}{N} \ln N! - \frac{1}{N} \sum_i \ln n_i!
\end{aligned}
\tag{4}
$$



로 정의된다. 이제 분수 $\frac{n_i}{N}$이 고정되어 있는 극한 $N \rightarrow \infty$를 고려하고 스털링의 근사값을 한다.



$$
\ln N! \simeq N \ln N − N
\tag{5}
$$


이것은



$$
\begin{aligned}
\mathrm H
&=
\lim_{N \rightarrow \infty}
\sum_i
\left (
	\frac{n_i}{N}
\right)
\ln
\left (
	\frac{n_i}{N}
\right)
\\&=
-\sum_i p_i \ln p_i
\end{aligned}
\tag{6}
$$



를 준다. 여기서 $\sum_i n_i = N$을 사용했으며,  $p_i = \lim _{N \rightarrow \infty} \left (\frac {n_i} {N} \right)$는 객체가 $i$번째 칸에 할당 될 확률이다.

물리학 용어에서, 칸에 있는 물체의 특정 배열(arrangement)을 마이크로상태라(microstate)고 부르며, 비율 $\frac {n_i} {N}$을 통해 표현 되는 점유 수(occupation numbers)의 전체 분포를 매크로상태(macrostate)라고 부른다. 다중도 $W$는 매크로 상태의 가중치라고도 한다.

---

우리는 칸을 이산확률변수 $X$의 상태 $x_i$로 해석 할 수 있습니다. 여기서 $p (X = x_i) = p_i$다. 무작위 변수 $X$의 엔트로피는



$$
\mathrm H [p] =
- \sum_i p(x_i) \ln p(x_i)
\tag{7}
$$



다.

좁은 범위의 값을 중심으로 뾰쪽한 정상에 분포하는 분포 $p(x_i)$는 상대적으로 낮은 엔트로피를 가지지만, 넓은 범위의 값에 걸쳐 보다 균등한 분포는 더 높은 엔트로피를 갖게 된다. 즉, 분포가 편평할 수록 높은 엔트로피를 가지게 된다.

왜냐하면 $0 \le p_i \le 1$이므로 엔트로피는 음이 아니며, $p_i = 1$이고 $p_{j \ne i} = 0$ 일 때 최소값 $0$과 같기 때문이다. ($\ln 1 = 0$)

최대 엔트로피 구성(maximum entropy configuration)은 라그랑주 승수(Lagrange multipiler)를 사용하여 확률에 대한 정규화 제약(normalization constraint)을 적용하여 $\mathrm H$를 최대화함으로써 발견 될 수 있다. 따라서



$$
\tilde {\mathrm H} =
- \sum _i p(x_i) \ln p(x_i) + \lambda \left( \sum_i p(x_i) - 1 \right)
\tag{8}
$$



을 극대화 한다. 여기서 우리는 모든 $p(x_i)$가 같고 $p(x_i) = \frac{1}{M}$에 의해 주어진다는 것을 알 수 있다. 여기서 $M$은 $x_i$의 총 상태 수 이며, 엔트로피의 해당 값은 $\mathrm H = \ln M$이다. 이 결과는 곧 논의 될 예정인 Jensen의 부등식(inequality)에서도 파생 될 수 있다.

정지점(stationary point)이 실제로 최대값인지 확인하기 위해 엔트로피의 2차 미분을 계산할 수 있다.



$$
\frac{\part \tilde{\mathrm H}}
{\part p (x_i) \part p(x_j)}
= -I_{ij} \frac{1}{p_i}
\tag{9}
$$



여기서 $I_ {ij}$는 단위 행렬의 원소들이다.

---

다음과 같이 연속 변수 $x$에 대한 분포 $p(x)$를 포함하도록 엔트로피의 정의를 확장 할 수 있다. 먼저 $x$를 넓이(width) $\Delta$의 칸으로 나눈다. 그런 다음, $p(x)$가 연속적이라고 가정 할 때, 평균값 정리 (mean value theorem; VMT; Weisstein, 1999)는 각각의 칸에 대해 $x_i$가 존재해야 함을 나타낸다.

제1 적분 평균값 정리를 도입하여



$$
\int_{i\Delta}^{(i+1)\Delta}
p(x) \mathrm d x
= p(x_i) \Delta
\tag{10}
$$



가 $i$ 번째 저장소에 있을 때마다 값 $x_i$에 임의의 값 $x$를 할당하여 연속 변수 $x$를 양자화 할 수 있다. 값 $x_i$를 관측 할 확률은 $p(x_i) \Delta$이다. 이것은 엔트로피가



$$
\begin{aligned}
\mathrm H _\Delta
&= - \sum _i p(x_i) \Delta \ln (p(x_i) \Delta) \\
&= - \sum _i p(x_i) \Delta \ln p(x_i) - \ln \Delta
\end{aligned}
\tag{11}
$$



이와 같은 형식을 취하는 이산분포를 제공한다. 여기서 $\sum _i p (x_i) \Delta = 1$을 사용했다.

이제 우리는 (11)의 우변에 있는 두 번째 항, 즉 $\ln \Delta$를 생략하고, 극한(limit) $\Delta \rightarrow 0$을 고려한다. (11)의 오른쪽에 있는 첫 번째 항은 이 극한에서 $p (x) \ln p (x)$의 적분에 접근 할 것이므로:



$$
\lim_{\Delta \rightarrow 0}
\left \{
    \sum_i p(x_i) \Delta \ln p (x_i)    
\right \}
=
- \int p(x) \ln p(x) \mathrm d x
\tag{12}
$$



이며, 여기서 우변의 양을 **미분 엔트로피(differential entropy)**라고 부른다. 우리는 엔트로피의 불연속적이고 연속적인 형태가 $\ln \Delta$만큼 차이가 있음 알 수 있다. 이것은 극한(limit) $\Delta \rightarrow 0$ 에서 발산한다. 이는 연속변수를 매우 정확하게 지정하기 위해서는 많은 수의 비트가 필요하다는 사실을 반영한다.

벡터 $\mathbf x$로 표시된 다중 연속 변수에 대해 정의 된 밀도의 경우 미분 엔트로피는 다음과 같이 표시된다.



$$
\mathrm H [\mathbf x]
= - \int p(\mathbf x) \ln p(\mathbf x) \mathrm d \mathbf x
\tag{13}
$$

---

이산 분포의 경우, 최대 엔트로피 구성이 변수의 가능한 상태 전반에 걸쳐 확률 분포에 일치한다는 것을 확인하였다. 이제 연속 변수에 대한 최대 엔트로피 구성(maximum entropy configuration)을 고려해 보자. 이 최대 값을 잘 정의하려면 $p(x)$의 첫 번째 및 두 번째 모멘트를 제한하고 정규화 제약(normalization constraint)을 유지해야 한다.

따라서 우리는 세 가지 제약 조건을 가지고 미분 엔트로피를 극대화 한다.




$$
\int _{-\infty} ^\infty p(x) \mathrm d x = 1
\tag{14}
$$

$$
\int _{-\infty} ^\infty xp(x) \mathrm d x = \mu
\tag{15}
$$

$$
\int _{-\infty} ^\infty (x - \mu)^2 p(x) \mathrm d x = \sigma ^2
\tag{1.16}
$$



제약된 최대화(constrained maximization)는 라그랑주 승수법을 사용하여 수행 할 수 있으므로 $p(x)$에 대해 다음 함수



$$
\begin{aligned}
&-\int _{-\infty} ^\infty p(x) \ln p(x) \mathrm d x \\
&+ \lambda_1 \left ( \int _{-\infty} ^\infty p(x) \mathrm d x - 1  \right ) \\
&+ \lambda_2 \left ( \int _{-\infty} ^\infty xp(x) \mathrm d x - \mu  \right) \\
&+ \lambda_3 \left ( \int _{-\infty} ^\infty (x-\mu)^2 p(x) \mathrm d x - \sigma^2 \right )
\end{aligned}
$$



를 최대화 한다.

변분법(calculus of variations)을 사용하여 이 함수의 미분을 $0$으로 설정한다.



$$
p(x) = \exp \left\{ -1 + \lambda_1 + \lambda_2 x + \lambda_3 (x - \mu)^2  \right\}
\tag{17}
$$



라그랑주 승수는 이 결과를 세 개의 제약 방정식(constraint equation)으로 역 대체(back substitution)하여 최종적으로 다음 결과를 이끌어 냄으로써 발견 할 수 있다.



$$
p(x)
= \frac{1}
{(2 \pi \sigma) ^\frac{1}{2}}
\exp \left\{
- \frac{(x-\mu)^2}
{2 \sigma ^2}
\right\}
$$



그래서 미분 엔트로피를 최대화하는 분포는 가우시안이다. 엔트로피를 최대화 할 때 비음수가 되도록 분포를 제한하지 않았다. 그러나 결과적으로 분포가 실제로 음수가 아니기 때문에 이러한 제약이 필요하지 않다는 것을 추후에 알 수 있다.

---

가우시안 미분 엔트로피를 계산하면



$$
\mathrm H[ x ] =
\frac{1}
{2}
\left\{
1 + \ln (2 \pi \sigma^2)  
\right\}
\tag{18}
$$



이다. 따라서, 분포가 넓어지면, 즉, $\sigma^2$가 증가하면, 엔트로피가 증가한다는 것을 다시 알 수 있다. 이 결과는 이산 엔트로피와 달리 미분 엔트로피가 음수일 수 있음을 보여 준다. 왜냐하면 $\sigma^2 < \frac{1}{(2 \pi e)}$에 대한 (18)의 $\mathrm H(x) < 0$ 때문 이다.

---

$\mathbf x$와 $\mathbf y$ 값의 쌍을 그려내는 결합분포 $p(\mathbf x, \mathbf y)$가 있다고 가정 하자. 이미 $\mathbf x$ 값을 알려져 있다면, $\mathbf y$의 해당 값을 지정하는 데 필요한 추가 정보가 $− \ln p(\mathbf y | \mathbf x)$에 의해 제공 된다. 따라서 $\mathbf y$를 지정하는 데 필요한 평균 추가 정보는 다음과 같이 쓸 수 있다.



$$
\mathrm H [ \mathbf y | \mathbf x ] =
− \int \int p(\mathbf y, \mathbf x) \ln p( \mathbf y | \mathbf x ) \mathrm d \mathbf y \mathrm d \mathbf x
\tag{19}
$$



이것은 $\mathbf x$가 주어진 $\mathbf y$의 **조건부 엔트로피(conditional entropy)**라고 불린다. 결합 법칙을 사용하면 조건부 엔트로피가 관계를 만족 한다는 것을 쉽게 알 수 있다



$$
\mathrm H[ \mathbf x, \mathbf y] = \mathrm  H [ \mathbf y | \mathbf x ] + \mathrm  H[ \mathbf x ] 
\tag{20}
$$



여기서 $\mathrm H[\mathbf x, \mathbf y]$는 $p(\mathbf x, \mathbf y)$의 미분 엔트로피이고 $\mathrm H[\mathbf x]$는 주변 분포 $p(\mathbf x)$의 미분 엔트로피다.  따라서 $\mathbf x$와 $\mathbf y$를 설명하는 데 필요한 정보는 $\mathbf x$만을 나타내는 데 필요한 정보와 $\mathbf x$가 주어진 $\mathbf y$의 정보의 합으로 표시 된다.

---

지금까지 엔트로피의 핵심 개념을 포함하여 정보 이론으로부터 많은 개념을 도입했다. 이제 이 아이디어를 패턴 인식에 연결 하겠다.

알려지지 않은 어떤 분포 $p (\mathbf x)$가 있다고 생각하고, 이에 근사한 분포 $q (\mathbf x)$를 사용하여 모델링 했다고 가정하자. $q(\mathbf x)$를 사용하여 $\mathbf x$의 값을 수신자로 전송할 목적으로 코드 체계(code scheme)를 구성하면, $\mathbf x$의 값을 지정하는데 (효율적인 코딩 체계를 선택한다고 가정 할 때) 필요한 평균 추가 정보 (nats 단위)는 실제 분포 $p(\mathbf x)$ 대신 $q(\mathbf x)$를 사용한 결과는 다음과 같다.



$$
\begin{aligned}
\mathrm {KL}(p \| q)
&= 
- \int p(\mathbf x) \ln q(\mathbf x) \mathrm d \mathbf x
- \left( - \int p(\mathbf x) \ln p(\mathbf x) \mathrm d \mathbf x \right)
\\
&=
- \int p(\mathbf x) \ln
\left\{
\frac{q(\mathbf x)}
{p(\mathbf x)}
\mathrm d \mathbf x
\right\}
\end{aligned}
\tag{21}
$$



이는 상대 엔트로피 또는 Kullback-Leibler divergence 또는 분포 $p(\mathbf x)$와 $q(\mathbf x)$ 사이의 KL divergence (Kullback and Leibler, 1951)로 알려져 있다. 그것이 $ \mathrm{KL}(p\|q) \not\equiv \mathrm{KL}(q\|p) $와 같이 대칭적인 양이 아님을 주목 하자.

---

Kullback-Leibler divergence가 $p(\mathbf x) = q(\mathbf x)$ 인 경우에만 $ \mathrm{KL}(p\|q) \ge 0 $를 동등(equality)하게 만족한다는 것을 보여 준다. 이를 위해 먼저 convex 함수의 개념을 알아 보자. 함수 $f(x)$는 그림 1.31에서와 같이 모든 코드가 함수 위 또는 위에 놓이는 성질을 가진다면 convex하다고 한다.

$x = a$에서 $x = b$까지의 간격에 있는 $x$ 값은 $\lambda a + (1 − \lambda)b$ 형식으로 작성할 수 있다. 여기서 $0 \le \lambda \le 1$ 이다. 현의 해당 지점은 $\lambda f (a) + (1 − \lambda)f (b)$로 주어지며 함수의 해당 값은 $f (\lambda a + (1 − \lambda)b)$ 이다.

Convexity는 다음을 의미 한다.



$$
f (\lambda a + (1 − \lambda)b)  \lambda f (a) + (1 − \lambda)f (b)
\tag{22}
$$



이것은 함수의 2 차 미분 값이 어디에서나 양의 값을 가져야 한다는 요구 사항과 동일 하다. convex 함수의 예는 ($x > 0$일때) $x \ln x$과 $x^2$ 이다. 등식이 $ \lambda = 0$ 및 $ \lambda = 1 $에 대해서만 만족되면 함수는 엄격하게 convex라고 부른다.

함수가 반대 성질을 갖는 경우, 즉 모든 현(chord)이 함수의 위 또는 아래에 놓이는 경우, 이는 오목한 것으로 불리며, 엄격하게 오목한 것에 대한 해당 정의가 있다. 함수 $f (x)$가 convex이면, $-f (x)$는 오목 한다.

---

귀납에 의한 증명 기법을 사용하면 (22)로부터 볼록 함수 $f (x)$가



$$
f \left(
\sum _{i=1} ^M
\lambda_i x_i
\right)
\le
\sum _{i=1}^M
\lambda_i f(x_i)
\tag{23}
$$



여기서 $\lambda_i \ge 0$와 $\sum_i \lambda _i = 1$은 점 $\{x_i\}$의 모든 집합을 나타 낸다. 결과 (1.115)는 Jensen의 부등식이라고 한다. 우리가 $\lambda_i$를 이산변수 $x$에 대한 확률분포로 값 $\{x_i\}$로 해석하면, (23)은



$$
f(\mathbb E[x]) \le  \mathbb E[f (x)]
\tag{24}
$$



형태로 쓸 수 있으며, 여기서 $\mathbb E[\cdot]$는 기대값을 나타 낸다.

연속 변수의 경우, 옌센부등식은



$$
f \left(
\int \mathbf x p (\mathbf x) \mathrm d \mathbf x
\right)
\le 
\int f(\mathbf x) p(\mathbf x) \mathrm d \mathbf x
\tag{25}
$$



과 같은 형식을 취한다.

---

옌센부등식 (25)를 Kullback-Leibler divergence (21)에 적용하여



$$
\mathrm{KL} (p \| q)
= - \int p(\mathbf x) \ln
\left\{
        \frac{q(\mathbf x)}
        {p(\mathbf x)}
\right\}
\mathrm d \mathbf x
\ge
- \ln \int q(\mathbf x) \mathrm d \mathbf x
= 0
\tag{26}
$$



이며, 여기서 우리는 $− \ln x$가 정규화 조건 $\int q(\mathbf x) \mathrm d \mathbf x = 1$과 함께 convex function라는 사실을 사용했다. 사실, $− \ln x$는 엄격하게 convex function이므로 모든 $\mathbf x$에 대해 $q(\mathbf x) = p(\mathbf x)$가 있는 경우에만 등식이 유지 된다. 따라서 Kullback-Leibler divergence를 두 분포 $p(\mathbf x)$와 $q(\mathbf x)$의 차이(dissimilarity)의 척도로 해석 할 수 있다.

---

실제 압축을 알면 가장 효율적인 압축이 이루어지기 때문에 데이터 압축과 밀도 추정 (즉, 알려지지 않은 확률 분포를 모델링하는 문제) 사이에 긴밀한 관계가 있다.

참분포(true distribution)와 다른 분포를 사용하면, 필연적으로 비효율적인 코드체계를 가질 수 밖에 없으며, 평균적으로 전송되어야 하는 추가 정보는 적어도 두 분포 사이의 Kullback-Leibler 발산과 같다.

---

모델링하고자 하는 알려지지 않은 분포 $p (\mathbf x)$로부터 데이터가 생성된다고 가정해 보자. 모수적 분포 $q (\mathbf x | \mathbf \theta)$를 사용하여 이 분포를 근사화하려고 시도 할 수 있다. 예를 들어 다변량 가우시안과 같은 조정 가능한 모수 $\mathbf \theta$로 제어 된다.

$\theta$를 결정하는 한 가지 방법은 $\mathbf \theta$에 대해 $p(\mathbf x)$와 $q (\mathbf x | \mathbf \theta)$ 사이의 Kullback-Leibler 발산을 최소화하는 것이다. 우리는 $p (\mathbf x)$를 모르기 때문에 이것을 직접 수행 할 수 없다.

그러나 $p (\mathbf x)$으로부터 그려진 $n = 1, \dots, N$에 대한 일련의 훈련 점(training points) $\mathbf x_n$의 유한한 집합을 관찰했다고 가정해 보자. 그러면 $p(\mathbf x)$에 대한 기대치는



$$
\begin{aligned}
p_y (y) &= p_x (x)  \cdot \left|  \frac{\mathrm dx}{\mathrm dy}  \right| \\
&= p_x(g(y)) \cdot \left| g'(y) \right|
\end{aligned}
$$



를 사용하여 이 점들에 대한 유한 합으로 근사 할 수 있다.



$$
\mathrm {KL} (p \| q)
\simeq
\sum _{n=1} ^N
\left\{
	- \ln q(\mathbf x_n | \mathbf \theta) + \ln p(\mathbf x_n)
\right\}
\tag{27}
$$



(27)의 우변에 있는 두 번째 항 $\ln p(\mathbf x_n)$은 $\mathbf \theta$와는 무관하며, 첫 번째 항 $\ln q(\mathbf x | \theta)$은 훈련 집합을 사용하여 계산된 분포 $q(\mathbf x | \mathbf \theta)$하에서 $\mathbf \theta$에 대한 음의 로그 우도 함수(negative log likelihood function) 이다. 따라서 이 Kullback-Leibler 발산을 최소화 하는 것은 우도 함수를 최대화하는 것과 같다.

---

이제 $p (\mathbf {x}, \mathbf {y})$에 의해 주어진 변수 $\mathbf{x}$와 $\mathbf{y}$의 두 집합 사이의 결합분포를 보자. 변수들의 집합이 독립적이라면, 그들의 결합분포는 그들의 주변의 곱으로 분해



$$
p(\mathbf x, \mathbf y) = p(\mathbf x)p(\mathbf y)
$$



된다.

변수가 독립적이지 않다면 우리는 Kullback-Leibler의 결합분포(joint distribution)와 주변의 곱(product of marginals) 사이의 차이를 고려하여, 이들이 '독립($\mathrm I$ndependent)'에 '근접'하는지 몇 가지 아이디어를



$$
\begin{aligned}
\mathrm I [\mathbf x, \mathbf y]
&\equiv \mathrm {KL} (p (\mathbf x, \mathbf y) \| p(\mathbf x)p(\mathbf y))
\\&= -\int \int p(\mathbf x, \mathbf y)
\ln \left(
	\frac{p(\mathbf x) p(\mathbf y)}
	{p(\mathbf x, \mathbf y)}
\right)
\mathrm d \mathbf x \mathrm d \mathbf y
\end{aligned}
\tag{28}
$$



에 의해 얻을 수 있다.

변수 $\mathbf x$와 $\mathbf y$사이의 상호정보(mutual information)라고 한다.

Kullback-Leibler divergence의 성질로 $\mathbf x$와 $\mathbf y$가 독립적인 경우에만 $\mathrm I(\mathbf x, \mathbf y) \ge 0$가 같음을 알 수 있다. 확률의 합 및 곱 법칙(sum rule and product rule)을 사용하여, 상호정보(mutual information)가 다음을 통한 조건부 엔트로피(conditional entropy)와 관련됨을 알 수 있다.



$$
\mathrm I[\mathbf x, \mathbf y]
= \mathrm  H[\mathbf x] − \mathrm  H[\mathbf x | \mathbf  y] = \mathrm  H[\mathbf y] − \mathrm  H[\mathbf y | \mathbf x]
\tag{29}
$$



따라서 상호정보는 $\mathbf y$의 값을 전달 받음으로써 $\mathbf x$에 대한 불확실성을 감소시키는 것으로 볼 수 있다.

베이지안 관점에서,  $p (\mathbf x)$를 $\mathbf x$의 사전 분포로 그리고 새로운 데이터 $\mathbf y$를 관찰하고 나서 $p (\mathbf x | \mathbf y)$를 사후 분포로 볼 수 있습니다. 따라서 상호정보는 새로운 관측의 결과로서 $\mathbf x$에 대한 불확실성의 감소를 나타 낸다.

#### 참조

- wikipedia.org

- bishop 2007




