[Up](index.md)

# 이미지 기하 변환(Image Geometry Transformation)

주의: 이 글은 공학 분야 활용 중심이며 수학 전공자가 보기에 엄밀하지 않을 수 있습니다.

디지털 이미지 프로세싱(digital image processing0에서 2차원 이미지(2 dimensional image)는 행렬(matrix)에 표현되며, 행렬의 항목(element) 하나는 하나의 픽셀(picture element; pixel)을 표현합니다. 픽셀이 행렬에 표현되므로 $y, x$ 순서로 나타냅니다.

동차 좌표(homogeneous coordinates) 벡터:

$$
\begin{pmatrix}
y \\
x \\
1
\end{pmatrix}
\\
$$

일반적으로 2차원 이미지의 픽셀들의 좌표는 2차원 벡터로 표현 할 수 있습니다. 이미지의 좌표를 동차 행렬을 통한 기하 변환(geometry transformation)은 항목이 3개인 동차 좌표를 사용합니다.

2차원 동차 좌표를 변환하기 위한 동차 행렬(homogeneous matrix)은 $3 \times 3$ 크기의 행렬을 사용합니다. 행렬 기호는 굵은 대문자로 표현합니다.

동차 행렬:

$$
\mathbf{A} = \begin{pmatrix} 
a_{1,1} & a_{1,2} & a_{1,3} \\
a_{2,1} & a_{2,2} & a_{2,3} \\
a_{3,1} & a_{3,2} & a_{3,3} \\
\end{pmatrix}
\\
$$

이동(translation) 동차 행렬:

$$
T(t_y, t_x)
=
\begin{pmatrix} 
1 & 0 & 0 \\
0 & 1 & 0 \\
t_y & t_x & 1 \\
\end{pmatrix}
$$
$$
\begin{pmatrix}
y' \\
x' \\
1
\end{pmatrix}
=
T(t_y, t_x)
\cdot
\begin{pmatrix}
y \\
x \\
1
\end{pmatrix}
$$

회전(rotation) 동차 행렬:

$$
R(\theta)
=
\begin{pmatrix}
\cos{\theta} & -\sin{\theta} & 0 \\
\sin{\theta} & \cos{\theta} & 0 \\
0 & 0 & 1 \\
\end{pmatrix}
$$
$$
\begin{pmatrix}
y' \\
x' \\
1 \\
\end{pmatrix}
=
R(\theta)
\cdot
\begin{pmatrix}
y \\
x \\
1 \\
\end{pmatrix}
$$

확대(scale) 동차 행렬:

$$
S(s_y, s_x)
=
\begin{pmatrix} 
s_y & 0 & 0 \\
0 & s_x & 0 \\
0 & 0 & 1 \\
\end{pmatrix}
$$
$$
\begin{pmatrix}
y' \\
x' \\
1
\end{pmatrix}
=
S(s_y, s_x)
\cdot
\begin{pmatrix}
y \\
x \\
1 \\
\end{pmatrix}
\\
$$

$y$축 기울이기 동차 행렬:

$$
H(h_y)
=
\begin{pmatrix} 
1 & 0 & 0 \\
h_y & 1 & 0 \\
0 & 0 & 1 \\
\end{pmatrix}
$$
$$
\begin{pmatrix}
y' \\
x' \\
1
\end{pmatrix}
=
H(h_y)
\cdot
\begin{pmatrix}
y \\
x \\
1
\end{pmatrix}
\\
$$

$x$축 기울이기 동차 행렬:

$$
H(h_x)
=
\begin{pmatrix} 
1 & h_x & 0 \\
0 & 1 & 0 \\
0 & 0 & 1 \\
\end{pmatrix}
$$
$$
\begin{pmatrix}
y' \\
x' \\
1
\end{pmatrix}
=
H(h_x)
\cdot
\begin{pmatrix}
y \\
x \\
1
\end{pmatrix}
$$

기하 변환에 행렬을 사용하면 계산량이 많은 것처럼 보입니다. 왜 기하 변환에 행렬을 사용해야 할까요? 만일 이동, 회전, 확대를 모두 적용하는 좌표를 연산한다면 3번의 행렬 연산을 하게 됩니다.

$$
\mathbf{a}' = T(t_y, t_x) \cdot \mathbf{a} \\
\\
$$

$$
\mathbf{a}'' = R(\theta) \cdot \mathbf{a}' \\
\\
$$

$$
\mathbf{a}''' = S(s_y, s_x) \cdot \mathbf{a}'' \\
\\
$$

1개의 좌표만 변환한다면 3번의 행렬 연산을 하게 됩니다. 그런데, 만일 $100\times100 \times 1$의 크기를 가지는 이미지라면 30,000번의 행렬 연산을 하게 됩니다.

기하 변환에 동차 행렬을 적용하면 미리 행렬을 연산해 둘 수 있어 행렬 연산을 줄일 수 있습니다.

$$
\mathbf{A} = T(t_y, t_x) \cdot R(\theta) \cdot S(s_y, s_x) \\
\\
$$

$$
\mathbf{a}''' = \mathbf{A} \cdot \mathbf{a} \\
\\
$$

$100 \times 100 \times 1$의 크기를 가지는 이미지라면 10,003번의 행렬 연산으로 줄어들게 됩니다. $3\times3$ 동차 행렬과 3차원 동차 좌표는 9번의 곱셈과 6번의 덧셈으로 계산하므로 계산량이 많다고 생각되지만, SIMD나 GPU를 통해 병렬처리하여 빠르게 처리할 수 있습니다.

## 참조

- wikipedia.org