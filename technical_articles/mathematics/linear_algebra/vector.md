[Up](index.md)

# 벡터(vector)

주의: 이 글의 공학 분야 활용 중심이며 수학 전공자가 보기에 엄밀하지 않을 수 있습니다.

벡터(vector)는 방향과 크기를 가집니다. 벡터에 대비하여 크기는 없지만 방향이 없는 것을 스칼라(scalar)라고 합니다. 속도(velocity)는 속력과 방향을 가지므로 벡터입니다. 속력(speed)는 방향이 없는 스칼라입니다.

컴퓨터 그래픽, 머신러닝, 이미지 프로세싱, 컴퓨터 비전 분야에서 벡터에 대한 지식은 필수라고 할 수 있습니다. 물리학에서 벡터는 힘의 방향과 크기를 나타냅니다. 이미지 프로세싱과 그래픽에서 벡터는 방향과 크기 뿐만 아니라 공간상의 위치, 픽셀의 색상을 표현 합니다. 머신러닝에서 벡터는 데이터의 특징을 나타냅니다.

이미지 프로세싱, 컴퓨터 비전, 머신러닝에서 벡터는 볼드체 소문자로 표기합니다.

$$
\mathbf{u}, \mathbf{v}, \mathbf{w}
$$

컴퓨터 그래픽 분야는 벡터를 대문자 $U, V, W$ 로 표기하기도 합니다. 이 블로그에서는 머신러닝 표기법으로 설명합니다.

다음은 $N$개의 항목을 가지는 $N$차원 벡터입니다.

$$
\mathbf{u} =
\begin{pmatrix}
  u_1 &
  u_2 &
  u_3 &
  \cdots &
  u_{N-1} &
  u_N
\end{pmatrix}
$$

벡터의 항목은 벡터의 기호와 동일한 소문자로 나타냅니다. $ \mathbf{u} $의 첫번째 항목을 $u_1$로 표현하였습니다. 각각의 벡터의 항목은 아래 첨자로 구분합니다.

가로로 나열된 벡터를 행벡터(raw vector)라고 합니다.

$$
\mathbf{u}
=
\begin{pmatrix}
  u_1 &
  u_2 &
  u_3 &
  \cdots &
  u_{N-1} &
  u_N
\end{pmatrix}
$$

세로로 나열된 벡터를 열벡터(column vector)라고 합니다.

$$
\mathbf{v}
=
\begin{pmatrix}
  v_1 \\
  v_2 \\
  v_3 \\
  \cdots \\
  v_{N-1} \\
  v_N \\
\end{pmatrix}
$$

다음은 행벡터 $\mathbf{u}$와 열벡터 $\mathbf{v}$입니다.

$$
\mathbf{u}
=
\begin{pmatrix}
  1 &
  2 &
  3 &
  \cdots &
  N
  \end{pmatrix}
$$

$$
\mathbf{v}
=
\begin{pmatrix}
  1 \\
  2 \\
  3 \\
  \cdots \\
  N
\end{pmatrix}
$$

벡터는 전치(transpose)할 수 있으며 $\mathbf{u}^T$로 표기합니다. 열벡터를 전치하면 행벡터가 되고, 행벡터를 전치하면 열벡터가 됩니다. 벡터 $\mathbf{u}$를 전치하면 $\mathbf{v}$ 와 같습니다.

$$
\mathbf{u}^\mathrm T = \mathbf{v}
$$

$$
\begin{pmatrix}
  1 &
  2 &
  3 &
  \cdots &
  N
\end{pmatrix}^\mathrm T
=
\begin{pmatrix}
  1 \\
  2 \\
  3 \\
  \cdots \\
  N \\
\end{pmatrix} \\
$$

열벡터를

$$
\begin{pmatrix} u_1 \\
	u_2 \\
	u_3 \\
	\cdots \\
	u_{N-1} \\
	u_N \\
\end{pmatrix}
$$

로 표기하면 문서의 공간을 차지하므로, 

$$
\begin{pmatrix}
	u_1 &
	u_2 &
	u_3 &
	\cdots &
	u_{N-1} &
	u_N
\end{pmatrix}^\mathrm T
$$

로 표기하여 문서 공간을 절약하기도 합니다.



