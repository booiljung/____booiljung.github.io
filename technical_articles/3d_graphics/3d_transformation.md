# 3D transformation

이 글은 빠른 룩업을 위해 정리하였습니다. 따라서 수학적 근거등은 설명하지 않습니다.

오른손 좌표계만을 표시합니다. 왼손좌표게는 오른손 좌표계를 위한 행렬를 전치(transpose)하면 얻을 수 있습니다.

## 3D Object

### 이동 (translation):

오른손 좌표계:
$$
\begin{aligned}
\begin{bmatrix}
x' \\
y' \\
z' \\
1 \\
\end{bmatrix}
&=
\mathbf T
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\\
&=
\begin{bmatrix}
1 & 0 & 0 & t_x \\
0 & 1 & 0 & t_y \\
0 & 0 & 1 & t_z \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\end{aligned}
$$
### 축적 (scaling):

Right handed & Right:
$$
\begin{aligned}
\begin{bmatrix}
x' \\
y' \\
z' \\
1 \\
\end{bmatrix}
&=
\mathbf S
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\\
&=
\begin{bmatrix}
s_x & 0 & 0 & 0 \\
0 & s_y & 0 & 0 \\
0 & 0 & s_z & 0 \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}

\end{aligned}
$$

### 회전 (rotation):

#### x축에 대한 회전:

오른손 좌표계:
$$
\begin{aligned}
\begin{bmatrix}
x' \\
y' \\
z' \\
1 \\
\end{bmatrix}
&=
\mathbf R_x
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\\
&=
\begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & \cos\alpha & -\sin\alpha & 0 \\
0 & \sin\alpha & \cos\alpha & 0 \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\end{aligned}
$$
#### y축에 대한 회전:

오른손 좌표계:
$$
\begin{aligned}

\begin{bmatrix}
x' \\
y' \\
z' \\
1 \\
\end{bmatrix}
&=
\mathbf R_y
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\\
&=
\begin{bmatrix}
\cos\beta & 0 & \sin\beta & 0 \\
0 & 1 & 0 & 0 \\
-\sin\beta & 0 & \cos\beta & 0 \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}

\end{aligned}
$$
#### z축에 대한 회전:

오른손 좌표계:
$$
\begin{aligned}
\begin{bmatrix}
x' \\
y' \\
z' \\
1 \\
\end{bmatrix}
&=
\mathbf R_z
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\\
&=
\begin{bmatrix}
\cos\gamma & -\sin\gamma & 0 & 0 \\
\sin\gamma & \cos\gamma & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\end{aligned}
$$
### 이동 $\times$ 축적 $\times$ 회전

이동, 이동, 회전을 모두 적용한 행렬은 다음과 같습니다.
$$
\begin{aligned}
\mathbf T \cdot \mathbf R_x \cdot \mathbf R_y \cdot \mathbf R_z \cdot \mathbf S
\begin{bmatrix}
s_x \cos\gamma \cos\beta &
-s_y \sin\gamma \cos\beta &
s_z \sin\beta &
t_x \\
s_x \cos\gamma \sin\beta \sin\alpha + s_x \sin\gamma \cos\alpha &
s_y \cos\gamma \cos\alpha - s_y \sin\gamma \sin\beta \sin\alpha &
-s_z \cos\beta \sin\alpha &
t_y \\
s_x \sin\gamma \sin\alpha - s_x \cos\gamma \sin\beta \cos\alpha &
s_y \sin\gamma \sin\beta \cos\alpha + s_y \sin\alpha \cos\gamma &
s_z \cos\beta \cos\alpha &
t_z \\
0 &
0 &
0 &
1 \\
\end{bmatrix}
\end{aligned}
$$

### 기울이기 (Shear):

오른손 좌표계:
$$
\begin{aligned}
\begin{bmatrix}
x' \\
y' \\
z' \\
1 \\
\end{bmatrix}
&=
\mathbf H
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}
\\
&=
\begin{bmatrix}
1 & sh_x^y & sh_x^z & 0 \\
sh_y^x & 1 & sh_y^z & 0 \\
sh_z^x & sh_z^y & 1 & 0 \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z \\
1 \\
\end{bmatrix}

\end{aligned}
$$
### 반사 (reflection)

#### x축에 대한 반사.

오른손 좌표계:
$$
\begin{aligned}
x' &= x\\
y' &= -y\\
z' &= -z
\end{aligned}
\\
\begin{bmatrix}
1 & 0 & 0 & 0\\
0 & -1 & 0 & 0\\
0 & 0 & -1 & 0\\
0 & 0 & 0 & 1\\
\end{bmatrix}
$$
#### y축에 대한 반사

오른손 좌표계:
$$
\begin{aligned}
x' &= y\\
y' &= -x\\
z' &= -z
\end{aligned}
\\
\begin{bmatrix}
-1 & 0 & 0 & 0\\
0 & 1 & 0 & 0\\
0 & 0 & -1 & 0\\
0 & 0 & 0 & 1\\
\end{bmatrix}
$$
#### z축에 대한 반사

오른손 좌표계:
$$
\begin{aligned}
x' &= -x\\
y' &= -y\\
z' &= z
\end{aligned}
\\
\begin{bmatrix}
-1 & 0 & 0 & 0\\
0 & -1 & 0 & 0\\
0 & 0 & 1 & 0\\
0 & 0 & 0 & 1\\
\end{bmatrix}
$$
