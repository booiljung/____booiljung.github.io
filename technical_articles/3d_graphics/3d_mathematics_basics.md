# 공개 금지 (제대로 정리해야 함)

# 3D 수학 기초

## Vectors

### 벡터의 합 (Vector Addition)

두 벡터의 위치나 방향을 합합니다.
$$
\begin{aligned}
\mathbf p + \mathbf q 
= \begin{bmatrix}
p_x\\ p_y\\ p_z
\end{bmatrix}
+ \begin{bmatrix}
q_x\\ q_y\\ q_z
\end{bmatrix}
= \begin{bmatrix}
p_x + q_x\\ p_y + q_y\\ p_z + q_z
\end{bmatrix}
\end{aligned}
$$

### 벡터의 반전

두 벡터의 방향을 반대로 하거나 위치를 원점의 반대 방향으로 옮깁니다.
$$
\begin{aligned}
- \mathbf p
= - \begin{bmatrix}
p_x\\ p_y\\ p_z
\end{bmatrix}
= \begin{bmatrix}
- p_x \\ - p_y \\ -p_z
\end{bmatrix}
\end{aligned}
$$

### 벡터의 내적 (Dot product)

두개의 n차원 벡터 $\mathbf p$와 $\mathbf q​$의 내적(dot product)은 다음 식
$$
\begin{aligned}
\mathbf p \cdot \mathbf q
&= \sum_{k=1}^n p_iq_i
\end{aligned}
$$
에 의해 스칼라로 정의되며, 다음 공식
$$
\begin{aligned}
\mathbf p \cdot \mathbf q
&= \| \mathbf p \| \| \mathbf q \| \cos \alpha
\end{aligned}
$$
에 의해 두벡터 $\mathbf p$와 $\mathbf q$의 각도 $\alpha$와 관계됩니다.

### 벡터의 투영 (Projection)

벡터 $\mathbf p$의 벡터 $\mathbf q$ 위에 대한 투영(projection)은 다음 식
$$
\begin{aligned}
\text{proj}_\mathbf q \mathbf p
&= \frac{\mathbf p \cdot \mathbf q}{\| \mathbf q \|^2} \mathbf q
\end{aligned}
$$
이며, $\mathbf q$에 수직(perpendicular)인 $\mathbf p​$의 성분은 다음 식
$$
\begin{aligned}
\text{perp}_\mathbf q
&= \mathbf p - \text{proj}_\mathbf q \mathbf p \\
&= \mathbf p - \frac{\mathbf p \cdot \mathbf q}{\| \mathbf q \|^2} \mathbf q
\end{aligned}
$$
에 의해 주어집니다.

### 벡터의 외적 (Cross product)

3차원상의 두 벡터 $\mathbf p$와 $\mathbf q$의 외적은 3차원 벡터이며 다음 식
$$
\begin{aligned}
\mathbf p \times \mathbf q
= \left<
p_y q_z - p_z q_y, \space
p_z q_x - p_x q_z, \space
p_x q_y - p_y q_x
\right>
\end{aligned}
$$
에 의해 정의되며, 다음
$$
\begin{aligned}
\mathbf p \times \mathbf q
= \begin{bmatrix}
0 & -p_z & p_y\\
p_z & 0 & -p_x\\
-p_y & p_x & 0
\end{bmatrix}
\begin{bmatrix}
q_x\\
q_y\\
q_z
\end{bmatrix}
\end{aligned}
$$
행렬과 벡터의 곱(product)로 할 수 있습니다. 외적의 크기는 벡터 $\mathbf p$와 $\mathbf q$ 사이의 각도 $\alpha$와 다음 공식
$$
\begin{aligned}
\| \mathbf p \times \mathbf q \|
= \| \mathbf p \| \| \mathbf q \| \sin \alpha
\end{aligned}
$$
에 의해 관련됩니다.

### Gram-Schmidt Orthogonalization

n차원 벡터 공간에 대한 기저 $\mathcal B = {\mathbf e_1, \mathbf e_2, \dots, \mathbf e_n}$는 다음 공식 
$$
\begin{aligned}
\mathbf e'_i
= \mathbf e_i - \sum_{k=1}^{i-1} \frac{\mathbf e_i \cdot \mathbf e'_k}{\mathbf e'^2_k}
\end{aligned}
$$
을 사용하여 새로운 벡터 $\mathcal B' = {\mathbf e'_1, \mathbf e'_2, \dots, \mathbf e'_n}$를 구성함으로써 직교화(orthogonalize) 될 수 있습니다.

## Matrices

### 행렬의 곱 (Matrix product)

$\mathbf F$가 $n\times m$ 행렬이고 $\mathbf G$가 $m \times p$ 행렬이면, $\mathbf F \mathbf G$의 $(i, j)$요소는 다음 식
$$
\begin{aligned}
(\mathbf F \mathbf G)_{ij}
= \sum_{k=1}^m F_{ik} G_{kj}
\end{aligned}
$$
에 주어지는  $n \times p​$ 행렬이 됩니다.

### 행렬식 (Determinants)

$n\times n$ 행렬 $\mathbf M$의 행렬식은 다음 공식
$$
\begin{aligned}
\det \mathbf M = \sum _{i=1}^n M_{ik} C_{ik} (\mathbf M)
\end{aligned}
$$
와
$$
\begin{aligned}
\det \mathbf M = \sum _{j=1}^n M_{kj} C_{kj} (\mathbf M)
\end{aligned}
$$
에 의해 주어집니다. 여기서 $C_{ij}(\mathbf M)$은 $M_{ij}$의 보조인자이며, $C_{ij}(\mathbf M) = (-1)^{i+j} \text{det} \mathbf M ^{i, j}$에 의해 정의 됩니다.

$2\times2$ 행렬의 행렬식은 다음 식
$$
\begin{aligned}
\begin{vmatrix}
a&b\\
c&d
\end{vmatrix}
= ad -bc
\end{aligned}
$$
에 의해 주어지며, $3\times3$ 행렬의 행렬식은 다음 식
$$
\begin{aligned}
\begin{vmatrix}
a_{11} & a_{12} & a_{13}\\
a_{21} & a_{22} & a_{23}\\
a_{31} & a_{32} & a_{33}
\end{vmatrix}
= a_{11}(a_{22}a_{33} - a_{23}a_{32}) 
- a_{12}(a_{21}a_{33} - a_{23}a_{31}) 
+ a_{13}(a_{21}a_{32} - a_{22}a_{31})
\end{aligned}
$$
에 의해 주어집니다.

### 역행렬 (Inverse)

$n \times n$의 행렬 $\mathbf M$은 선형적으로 독립적인 집합을 형성하는 경우에만 역행렬을 얻을 수 있습니다. 동등하게 $\mathbf M$은 $\det \mathbf M \ne 0$인 경우에만 역행렬을 얻을 수 있습니다. $n \times n$ 행렬 $\mathbf F$의 역행렬 $\mathbf G$의 항목은 명시적으로 다음 공식 
$$
\begin{aligned}
\mathbf G_{ij}
= \frac{C_{ji}(\mathbf F)}{\det \mathbf F}
\end{aligned}
$$
을 사용하여 계산할 수 있습니다.

이 공식을 사용하여 $2 \times 2$ 행렬 $\mathbf A$의 역행렬은 다음
$$
\begin{aligned}
\mathbf A^{-1} 
= \frac{1}{\det \mathbf A}
\begin{bmatrix}
A_{22} & -A_{12}\\
-A_{21} & A_{11}
\end{bmatrix}
\end{aligned}
$$
에 의해 주어지며, $3 \times 3$ 행렬 $\mathbf B$의 역행렬은 다음
$$
\begin{aligned}
\mathbf B^{-1} 
= \frac{1}{\det \mathbf A}
\begin{bmatrix}
B_{22}B_{33} - B_{23}B_{32} & B_{13}B_{32}-B_{12}B_{33} & B_{12}B_{23}-B_{13}B_{22} \\
B_{23}B_{31} - B_{21}B_{33} & B_{11}B_{33}-B_{13}B_{31} & B_{13}B_{21}-B_{11}B_{23} \\
B_{21}B_{32} - B_{22}B_{31} & B_{12}B_{31}-B_{11}B_{32} & B_{11}B_{22}-B_{12}B_{21} \\
\end{bmatrix}
\end{aligned}
$$
에의해 주어집니다.

### 고유값(Eigenvalue)와 고유벡터(Eigenvector)

$n \times n$ 행렬 $\mathbf M$의 고유값은 다음 식
$$
\begin{aligned}
\det(\mathbf M - \mathcal \lambda \mathbf I)
\end{aligned}
$$
에 주어진 특성 다항식(characteristic polynomial)의 근(root)과 같으며, 행렬 $\mathbf M$의 고유값 $\lambda$와 연관된 고유벡터 $\mathbf v$는 동차선형계(homogeneous linear system)
$$
\begin{aligned}
(\mathbf M - \lambda \mathbf I) \mathbf v
= \mathbf 0
\end{aligned}
$$
에 대한 해에 의해 주어 집니다. 실제 대칭행렬의 고유값은 실수이고, 실제 대칭행렬의 고유한 고유값에 해당하는 고유벡터는 직교합니다.

### 대각화 (Diagonalization)

$\mathbf v_1, \mathbf v_2, \dots, \mathbf v_n$이 $n \times n$ 행렬 $\mathbf M$의 선형 독립적인 고유벡터라면, 행렬 $\mathbf A$는 다음
$$
\begin{aligned}
\mathbf A
=
\begin{bmatrix}
\mathbf v_1 & \mathbf v_2 & \cdots & \mathbf v_n
\end{bmatrix}
\end{aligned}
$$
에 의해 주어지며, 대각화(diagonalize)는 다음
$$
\begin{aligned}
\mathbf A^{-1} \mathbf M \mathbf A
=
\begin{bmatrix}
\lambda_1 & 0 & \cdots & 0 \\
0 & \lambda_2 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \lambda_n
\end{bmatrix}
\end{aligned}
$$
을 의미하며, 여기서, $\lambda_1, \lambda_2, \dots, \lambda_n$는 행렬 $\mathbf M$의 교유값입니다.

## 변환 (Transforms)

### 직교행렬 (Orthogonal Matrices)

$n \times n$ 가역행렬 $\mathbf M$ 이 $\mathbf M^{-1} = \mathbf M^\top$을 만족하면 직교행렬(orthogonal matrix)이라고 합니다. 벡터의 직교 정규 집합(orthonormal set)을 형성하는 행이 직각인 행렬입니다. 직교행렬은 길이와 각도를 유지하므로 회전과 반사만 수행합니다.

##3 축척변환

다음 변환행렬
$$
\begin{aligned}
\begin{bmatrix}
a & 0 & 0 \\
0 & b & 0 \\
0 & 0 & c \\
\end{bmatrix}
\end{aligned}
$$
을 사용하여 3차원 축척연산이 수행됩니다. 여기서 $a = b = c$이면, 이 행렬은 스칼라 곱셈을 사용하여 수행 할 수있는 균일한 스케일을 나타냅니다.

### 회전변환 (Rotation Transforms)

$x$, $y$ 및 $z$축에 대한 각도 $\theta$를 통한 회전은 다음 변환행렬
$$
\begin{aligned}
\mathbf R_x(\theta)
= \begin{bmatrix}
1 & 0 & 0 \\
0 & \cos \theta & -\sin \theta \\
0 & \sin \theta & \cos \theta \\
\end{bmatrix}
\end{aligned}
$$

$$
\begin{aligned}
\mathbf R_y(\theta)
= \begin{bmatrix}
\cos \theta & 0 & \sin \theta \\
0 & 1 & 0 \\
-\sin \theta & 0 & \cos \theta \\
\end{bmatrix}
\end{aligned}
$$

$$
\begin{aligned}
\mathbf R_z(\theta)
= \begin{bmatrix}
\cos \theta & -\sin \theta & 0 \\
\sin \theta & \cos \theta & 0 \\
0 & 0 & 1 \\
\end{bmatrix}
\end{aligned}
$$

을 사용하여 수행됩니다.

임의의 축 $a$에 대한 각도 $\theta$의 회전은 변환행렬 
$$
\begin{aligned}
\mathbf R_a(\theta)
= \begin{bmatrix}
c+(1-c)a_x^2 & (1-c)a_x a_y - sa_z & (1-c)a_x a_z + sa_y \\
(1-c) a_x a_y + sa_z & c+(1-c)a_y^2 & (1-c)a_y a_z - sa_x \\
(1-c) a_x a_z - sa_y & (1-c) a_y a_z + sa_x & c+(1-c)a_z^2 &  \\
\end{bmatrix}
\end{aligned}
$$
를 사용하여 수행 됩니다. 여기서 $c=\cos \theta$ 이고 $s = \sin \theta$ 입니다.

### 동차좌표계 (homogeneous coordinates)

3차원 점을 나타내는 벡터 $\mathbf p$에 $w$ 좌표를 $1$로 설정하여 4차원의 동차 좌표계로 확장 합니다. 3차원 방향을 나타내는 벡터 $\mathbf d$는 $w$ 좌표를 $0$으로 설정하여 동차좌표계로 확장할 수 있습니다.

$3 \times 3$ 변환행렬 $\mathbf M$ 과 3차원 평행이동 벡터 $\mathbf t$는 $4 \times 4$ 변환행렬을 사용하여 결합할 수 있습니다.
$$
\begin{aligned}
\mathbf F
= \begin{bmatrix}
\begin{array}{ccc|c}
M_{11} & M_{12} & M_{13} & t_{x} \\
M_{21} & M_{22} & M_{23} & t_{y} \\
M_{31} & M_{32} & M_{33} & t_{z} \\
\hline
0 & 0 & 0 & 1 \\
\end{array}
\end{bmatrix}
\end{aligned}
$$
법선벡터(normal vector)는 점을 변환(transform point)하는데 사용 된 행렬의 역 전치(inverse transpose)를 사용하여 변환(transform)되어야 합니다.

### 사원수 (Quaternion)

단위 축 $a$에 대한 각도 $\theta$를 통한 회전에 해당하는 단위 사원수는 다음 식
$$
\begin{aligned}
\mathbf q
= \cos \frac{\theta}{2} + a \sin \frac{\theta}{2}
\end{aligned}
$$
에 의해 주어집니다.

사원수 $\mathbf q$는 동차모델(homophism) $\mathbf p' = \mathbf q \mathbf p \mathbf q^{-1}$를 사용하여 점 $\mathbf p$에 회전변환을 적용합니다.



사원수 $\mathbf q$에 의해 수행되는 변환은 다음 $3\times 3$ 행렬
$$
\begin{aligned}
\mathbf R_\mathbf q
= \begin{bmatrix}
1 - 2y^2 - 2z^2 & 2xy - 2wz & 2xz + 2wy \\
2xy + 2wz & 1-2x^2 - 2z^2 & 2yz-2wx \\
2xz - 2wy & 2yz + 2wx & 1 - 2x^2 - 2y^2 \\
\end{bmatrix}
\end{aligned}
$$
에 의해 수행되는 변환과 동일 합니다.

### 구형 선형 보간 (Spherical Linear Interpolation)

두개의 사원수 $\mathbf q_1$과 $\mathbf q_2$는 다음 공식에 의해
$$
\begin{aligned}
\mathbf q(t)
= \frac{\sin \theta (1-t)}{\sin \theta} \mathbf q_1
+ \frac{\sin \theta t}{\sin \theta} \mathbf q_2
\end{aligned}
$$
구형으로 보간 되며, 여기서 $0 \le t \le 1$.

## Geometry for 3D engine

### 선 (Lines)

점 $\mathbf p_0$를 지나며 방향이 $\mathbf v$에 평행인 선은 다음 식
$$
\begin{aligned}
\mathbf p(t) 
= \mathbf p_0 + t \mathbf v
\end{aligned}
$$
으로 나타냅니다.

점 $\mathbf q$에서 선 $\mathbf p(t)$까지의 거리는 다음
$$
\begin{aligned}
d
= \sqrt{
	(\mathbf q - \mathbf p_0)^2
	- \frac{[(\mathbf q - \mathbf p_0) \cdot \mathbf v]^2}
		{v^2}
}
\end{aligned}
$$
에 의해 주어집니다.

### 평면 (Planes)

점 $\mathbf p_0$를 포함하고 법선 방향이 $\mathbf n$인 평면은 다음 식
$$
\mathbf n \cdot \mathbf p + d = 0
$$
에 의해 표현 되며, 여기서
$$
d = -\mathbf n \cdot \mathbf p_0
$$
이며, 이것은 
$$
\mathbf l \cdot \mathbf p = 0
$$
으로 표현 할 수 있으며, 여기서 $\mathbf l$은 4차원 벡터 $\left< \mathbf n, d \right>$이며 $\mathbf p$는 $w$좌표가 $1$인 동차점 (homogeneous point) 입니다. 점 $\mathbf q$로부터 평면 $\mathbf l$까지의 거리는 
$$
\begin{aligned}
\mathbf l \cdot \mathbf q
\end{aligned}
$$
로 표현됩니다.

평면은 점을 변환하는데 사용되는 행렬의 역전치(inverse tranpose)를 사용하여 변형(transform)되어야 합니다.

### 선과 평면의 교차점 (Intersection of a Line and a Plane)

매개변수가 $t$인 선 $\mathbf p(t) = \mathbf q + t \mathbf v$과 평면 $\mathbf l$의 교차점은 다음 식
$$
\begin{aligned}
t = \frac{\mathbf l \cdot \mathbf q}{\mathbf l \cdot \mathbf v}
\end{aligned}
$$

### 시야 절두체(View Frustum)

수평 시야각 $\alpha$을 갖는 시야 절두체의 초점 거리 $e$는 다음 식
$$
\begin{aligned}
e
= \frac{1}{\tan\frac{\alpha}{2}}
\end{aligned}
$$
으로 주어 집니다.

종횡비가 $a$ 인 디스플레이의 경우, 카메라에서 거리가 $n$ 만큼 떨어진 가까운 평면에서 새겨진 사각형은 $x = \frac{n}{e}$ 과 $y = \pm a\frac{n}{e}$로 제한 됩니다.

### 원근-보정 보간 (Perspective-Correct Interpolation)

원근투영에서, 깊이 값이 $z_1$및 $z_2$ 는 그들 상호간에 선형적으로 보간하여 올바르게 보간됩니다:
$$
\frac{1}{z_3}
=
\frac{1}{z_1}
(1-t) +
\frac{1}{z_2}
t
$$
원근-보정 정점 속성 보간(vertex attribute interpolation)은 다음 유사한 공식
$$
\frac{1}{z_3}
=
\left[
\frac{b_1}{z_1} (1-t)
\frac{b_2}{z_2} t
\right]
$$
을 사용하며, 여기서 $b_1$와 $b_2$는 정점 속성값(vertex attribute values)입니다.

### 원근 투영 (Perspective Projection)

카메라 공간의 한 점을 절두공간(clip space) 변환하는 원근투영 행렬 $\mathbf M_\text{frustum}$은 다음 식
$$
\mathbf M_\text{frustum}
= \begin{bmatrix}
\frac{2n}{r-l} & 0 & \frac{r+l}{r-l} & 0 \\
0 & \frac{2n}{t-b} & \frac{t+b}{t-b} & 0 \\
0 & 0 & \frac{f+n}{f-n} & \frac{2nf}{f-n} \\
0 & 0 & -1 & 0 \\
\end{bmatrix}
$$
에 의해 주어지며,  여기서 $n$과  $f$는 카메라로 near plane과 far plane이며, $l$, $r$, $b$ 및 $t$는 near plane으로 부터 시야 사각형의 left, right, bottom 및 top edge를 새깁니다.

An infinite view frustum can be constructed by allowing the far plane distance $f$ to tend to infinity. The corresponding projection matrix infinite $\mathbf M_\text{infinite}$ is given by
$$
\mathbf M_\text{infinite}
= \lim_{f \rarr \infty} \mathbf M_\text{infinite}
= \begin{bmatrix}
\frac{2n}{r-l} & 0 & \frac{r+1}{r-1} & 0 \\
0 & \frac{2n}{t-b} & \frac{t+b}{t-b} & 0 \\
0 & 0 & -1 & -2n \\
0 & 0 & -1 & 0 \\
\end{bmatrix}
$$

### Oblique Near-Plane Clipping 

A  perspective  projection  matrix  $\mathbf M$ can  be  modified  so  that  the  near  plane  is  replaced by any camera-space clipping plane $\mathbf c$, with $c_w < 0$, by constructing a new projection matrix′ $\mathbf M'$ as follows,
$$
\mathbf M'
= \begin{bmatrix}
\mathbf M_1 \\
\mathbf M_2 \\
\frac{-2q_z}{\mathbf c \cdot \mathbf q} + \left< 0, 0, 1, 0 \right> \\
\mathbf M_4 \\
\end{bmatrix}
$$
where $\mathbf q = \mathbf M^{-1} \mathbf q'$ 와 $\mathbf q'$ is given by 
$$
\mathbf q' = \left< \text{sgn} (c'_x) \text{sgn}(c'_y), 1, 1 \right>
$$

## Ray Tracing

### Analytic Root Finding

Solution to the quadratic equation $at^2 + bt + c = 0​$ are given by quadratic formular:
$$
t = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$
Cubic and quartic equations can also be solved analytically.

### Numberical Root Finding

Root of a function $f(x)​$ can be found numerically using Newton's method, whitch refines an approximate solution $x_n​$ using the formula
$$
x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}
$$
The refinement formula for the reciprocal $x_n$ of a number $r$ is
$$
x_{n+1} = x_n (2-rx_n)
$$
and the refinement formula for the reciporocal square root $x_n$ of number $r$ is
$$
x_{n+1} = \frac{1}{2} x_n (3 - rx_n^2)
$$

### Intersection of a Ray and a Sphere

The points where a ray $\mathbf p (t) = \mathbf s + t \mathbf v​$ intersect a spere of radius $r​$ are given by the solutions of the quadratic equation
$$
v^2 t^2 + 2(\mathbf s \cdot \mathbf v) t + s^2 - r^2 = 0
$$

### Normal Vector Calculation

The normal vector at a point $\left< x, y, z \right>​$ on a surface defined by the function $f(x, y, z) = 0​$ is given by $\mathbf n = \Delta f (x, y, z)​$.

### Reflection Vector Calculation

The reflection $\mathbf r​$ of a vector $\mathbf l​$ across the normal vector $\mathbf n​$ is given by
$$
\mathbf r = 2 (\mathbf n \cdot \mathbf l) \mathbf n - \mathbf l
$$

### Transmission Vector Calculation

The direction $\mathbf t$ in which light is transmitted when leaving a medium having index of refraction $\mu _\mathbf L$ and entering a medium having index of refraction $\eta _\mathbf t​$ is given by
$$
\mathbf t = \left(
	\frac{\eta_\mathbf l}{\eta_\mathbf t} \mathbf n \cdot \mathbf l
	-
	\sqrt{1 - \frac{\eta_\mathbf l^2}{\eta_\mathbf t^2}
		[
			1-(\mathbf n \cdot \mathbf l)^2
		]
	}
\right)
\mathbf n - \frac{\eta_\mathbf l}{\eta _\mathbf t} \mathbf l
$$
where $\mathbf l$ is the direction pointing toward the incident light, and $\mathbf n$ is the surface normal.

## Lighting and Shading

### Point Light Source Attenuation

The intensity $\mathcal c$ of a point light source at a distance $d$ from its position is given by
$$
\mathcal c = \frac{1}{k_c + k_l d + k_q d^2} \mathcal c_0
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

## 참조

- Mathematics for 3D Game Programming and Computer Graphics - Eric Lengyel.