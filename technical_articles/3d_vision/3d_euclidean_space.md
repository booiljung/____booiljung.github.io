# 3D 유클리드 공간

유클리드 공간:
$$
\mathbb E^3
$$
유클리드 공간의 점:
$$
p \in \mathbb E^3
$$
3차원 실수:
$$
p \in \mathbb R^3
$$
점의 좌표 $\mathbf X$:
$$
\mathbf X =
\begin{bmatrix}
X_1 \\ X_2 \\ X_3
\end{bmatrix}
$$
또는
$$
\mathbf X =
\begin{bmatrix}
X \\ Y \\ Z
\end{bmatrix}
$$
벡터 $v$는 두개의 점으로 구성:
$$
(p, q)
$$
여기서, $p \in \mathbb R^3$ 그리고 $p \in \mathbb E$, $p$는 시작 점(base point).

벡터 $v$는
$$
v = \mathbb R^3 \\
\\
v = \begin{bmatrix}
v_1 \\ v_2 \\ v_3
\end{bmatrix}
$$
$p = \mathbf X$이고 $q = \mathbf Y$ 일때
$$
v = \mathbf Y- \mathbf X
$$
Free vector:
$$
(p, q)
$$
가 있고 그리고
$$
(p', q')
$$
가 있을때
$$
\mathbf Y- \mathbf X = \mathbf Y' - \mathbf X'
$$
를 만족하면 free vector라고 한다.

#### 벡터의 선형 결합 (linear combination):

두 벡터 
$$
u, v \in \mathbb R^3
$$
가 있고 두 실수,
$$
\alpha, \beta \in \mathbb R
$$
가 있을때
$$
\alpha v + \beta u = 
\begin{bmatrix}
\alpha v_1 + \beta u_1 \\
\alpha v_2 + \beta u_2 \\
\alpha v_3 + \beta u_3
\end{bmatrix}
$$

이다.

### 벡터의 내적 (Inner product)

#### 벡터의 내적의 특성

1.
$$
\left< u, \alpha v + \beta w \right> = \alpha \left< u, v \right> + \beta \left< u, w \right>
$$

2.
$$
\left< u, v \right> = \left< v, u \right>
$$
3.
$$
\left< v, v \right> \ge 0 \text{ and } \left< v, v \right> = 0 \text{ then}, \\
v = 0
$$
유클리디안 거리:
$$
\begin{aligned}
\|v\| &= \sqrt{\left< v, v \right>} \\
&= \sqrt{v_1^2 + v_2^2 + v_3^2}
\end{aligned}
$$


내적:
$$
\begin{aligned}
\left< u, v \right> &= u^\top v \\
&= u_1 v_1 + u_2 v_2 + u_3 v_3
\end{aligned}
$$

## 벡터의 외적 (Coss product)

두개의 벡터
$$
u, v \in \mathbb R^3
$$
일때 벡터의 외적:
$$
u \times v =
\begin{bmatrix}
u_2 v_3 = u_3 v_2 \\
u_3 v_1 = u_1 v_3 \\
u_1 v_2 = u_2 v_1 \\
\end{bmatrix}
\in \mathbb R^3
$$
으로 얻는다.

두개의 벡터가 선형일때
$$
u \times (\alpha v + \beta w) = \alpha u \times \beta u \times w
$$
이고, 여기서 $\forall \alpha, \beta \in \mathbb R$이며,
$$
\left< u \times v, u \right> = \left< u \times v, v \right> = 0
$$
일때,
$$
u \times v = -v \times u
$$
따라서, 두 벡터의 외적은 각 요인에 직교하며 요인의 순서는 방향을 정의한다.

이 행렬이 다음에 의해 주어 졌다는 것을 대체하여 검증하는 것이 즉각적이다.





#### Denition 2.3

Rigid body motion or special Euclidean transformation.

1. Norm:

$$
\begin{aligned}
\| g*(v) \| = \| v \|, && \forall u \in \mathbb R^3
\end{aligned}
$$

2. Cross product:

$$
\begin{aligned}
g*(u) \times g*(v) = g*(u \times v), && \forall u, v \in \mathbb R^3
\end{aligned}
$$

?
$$
u ^\top v = \frac{1}{4} \left( \| u + v \|^2 - \| u-v \|^2 \right)
$$
?
$$
\begin{aligned}

\end{aligned}
$$






$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$





$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

$$
\begin{aligned}
\end{aligned}
$$

