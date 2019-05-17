[Up](index.md)

# 벡터의 합

주의: 이 글은 공학 분야 활용 중심이며 수학 전공자가 보기에 엄밀하지 않을 수 있습니다.

벡터에 대비하여 방향은 없고 크기만 있는 것을 스칼라(scalar)라고 합니다. 속도(velocity)는 속력과 방향을 가지므로 벡터입니다. 속력(speed)는 방향이 없으므로 스칼라입니다. 스칼라는 소문자로 표기합니다.

차원(demension)이 동일한 행벡터(row vector)끼리 더할 수 있습니다.

$$
\begin{aligned}
\mathbf{u}
&=
\begin{pmatrix}
  u_1 &
  u_2 &
  u_3
\end{pmatrix}
,
\mathbf{v}
=
\begin{pmatrix}
  v_1 &
  v_2 &
  v_3
\end{pmatrix}
\\
\\
\mathbf{w}
&=
\mathbf{u} + \mathbf{v}
\\
\\
&=
\begin{pmatrix}
  u_1 &
  u_2 &
  u_3
\end{pmatrix}
+
\begin{pmatrix}
  v_1 &
  v_2 &
  v_3
\end{pmatrix}
\\
\\
&=
\begin{pmatrix}
  u_1 + v_1 &
  u_2 + v_2 &
  u_3 + v_3
\end{pmatrix}
\end{aligned}
$$

차원이 동일한 열벡터(column vector)끼리도 더할 수 있습니다.
$$
\begin{aligned}
\mathbf{u}
&=
\begin{pmatrix}
  u_1 \\
  u_2 \\
  u_3
\end{pmatrix}
,
\mathbf{v}
=
\begin{pmatrix}
  v_1 \\
  v_2 \\
  v_3
\end{pmatrix}
\\
\\
\mathbf{w}
&=
\mathbf{u} + \mathbf{v}
\\
\\
&=
\begin{pmatrix}
  u_1 \\
  u_2 \\
  u_3
\end{pmatrix}
+
\begin{pmatrix}
  v_1 \\
  v_2 \\
  v_3
\end{pmatrix}
\\
\\
&=
\begin{pmatrix}
  u_1 + v_1 \\
  u_2 + v_2 \\
  u_3 + v_3
\end{pmatrix}
\end{aligned}
$$

벡터끼리의 차도 연산 할 수 있습니다.

$$
\mathbf{w} = \mathbf{u} - \mathbf{v}
$$

수학적으로 엄밀하게 벡터와 스칼라는 더할 수 없습니다. 공학수학(engineering mathematics)은 스칼라를 벡터의 덧셈 표기를 사용하며 이때 스칼라는 스칼라를 항목으로 가지는 동일 차원의 벡터로 변환됩니다.

다음은 열벡터와 스칼라의 합입니다.
$$
\begin{aligned}
\mathbf{u}
&=
\begin{pmatrix}
  u_1 &
  u_2 &
  u_3
\end{pmatrix}
,
s
\\
\\
\mathbf{w}
&=
\mathbf{u} + s
\\
\\
&=
\begin{pmatrix}
  u_1 &
  u_2 &
  u_3
\end{pmatrix}
+
\begin{pmatrix}
  s &
  s &
  s
\end{pmatrix}
\\
\\
&=
\begin{pmatrix}
  u_1 + s &
  u_2 + s &
  u_3 + s
\end{pmatrix}
\end{aligned}
$$

공학수학에서 열벡터와 스칼라도 더할 수 있습니다.

$$
\mathbf{u}
=
\begin{pmatrix}
  u_1 \\
  u_2 \\
  u_3
\end{pmatrix}
,
s
\\
\\
\\
\mathbf{w}
=
\mathbf{u} + s
\\
\\
\\
=
\begin{pmatrix}
  u_1 \\
  u_2 \\
  u_3
\end{pmatrix}
+
\begin{pmatrix}
  s \\
  s \\
  s
\end{pmatrix}
\\
\\
\\
=
\begin{pmatrix}
  u_1 + s \\
  u_2 + s \\
  u_3 + s
\end{pmatrix}
$$

벡터와 스칼라의 뺄셈도 할 수 있습니다.

$$
\mathbf{w} = \mathbf{u} - s
$$

벡터끼리의 덧셈과 뺄셈은 교환법칙이 성립합니다.

$$
\mathbf{u} + \mathbf{v} = \mathbf{v} + \mathbf{u}
\\
\mathbf{u} - \mathbf{v} = \mathbf{u} + (-\mathbf{v}) = (-\mathbf{v}) + \mathbf{u}
$$

공학수학에서 벡터와 스칼라의 덧셈과 뺄셈도 교환법칙이 성립합니다.

$$
\mathbf{u} + s = s + \mathbf{u}
\\
\mathbf{u} - s = \mathbf{u} + (-s) = (-s) + \mathbf{u}
$$


