# 편미분 (partial differential)

$y$가 두개의 다른 변량에 의해 결정되는 경우 이때 변량을 $u$와 $v$라고 하고, 식으로 나타내면
$$
y = f(u, v)
$$
과 같습니다.

이 함수의 간단한 예를
$$
y = f(u, v) = u \times v
$$
고려해 보겠습니다.

이 경우 독립 변수가 2개이며 어느 하나를 상수로 간주합니다. 우선 $v$를 상수로 간주하고 $u$에 대해서 미분하면,
$$
\mathrm d y _v = v\mathrm d u
$$
이 되고, $u$를 상수로 간주하여 $v$에 대해 미분하면
$$
\mathrm d y _u = v\mathrm d v
$$
이 됩니다. 여기서 $y$의 서브스크립트 $_v$나 $_u$는 미분과정에서 상수로 취급되는 변량을 표시 합니다. 그런데 이 표기 방법은 불편하므로 편미분에서는 $\mathrm d$ 문자 대신, $\partial$ 문자를 표시 합니다. 이 표시 방법으로 위 두개의 식은
$$
\frac{\partial y}{\partial u} = v \\
\frac{\partial y}{\partial v} = u \\
$$
라고 표시 합니다. 이 값들을 위의 식들의 $v$와 $u$에 각각 넣으면
$$
\mathrm d y_v =
v \mathrm d u =
\frac{\partial y}{\partial u} \mathrm d u \\
\mathrm d y_u =
u \mathrm d v =
\frac{\partial y}{\partial v} \mathrm d v \\
$$
가 되며, 이들을 편미분(partial differential)이라고 합니다.

자세히 생각해 보면, $y$의 총변량은 이 두 조건이 동시에 만족될 때 성립이 됩니다. 즉 $u$와 $v$가 동시에 변하면 실제적인 $\mathrm d y$는 
$$
\mathrm d y =
\frac{\partial y}{\partial u} \mathrm d u +
\frac{\partial y}{\partial v} \mathrm d v
$$
입니다.

## 예제 1

다음 식
$$
w = 2 a x^2 + 3bxy + 4cy^3
$$
의 편도함수를 구하여라.
$$
\begin{aligned}
\frac{\partial w}{\partial x} &= 4ax + 3by \\
\frac{\partial w}{\partial y} &= 3bx + 12cy^2 \\
\end{aligned}
$$
하나는 $y$를 상수로 하여 구하고, 하나는 $x$를 상수로 하여 구합니다.
$$
\mathrm dw = (4ax + 3by) \mathrm dx + (3bx + 12cy^2) \mathrm dy
$$

## 예제 2

다음 식
$$
z = x^y
$$
의 편도함수를 구하여라.
$$
\frac{\partial z}{\partial x} = y x ^{y-1} \\
\frac{\partial z}{\partial y} = x^y \ln x
$$

$$
\mathrm dz = y x ^{y-1} \mathrm d x + x^y \ln x \mathrm d y
$$

