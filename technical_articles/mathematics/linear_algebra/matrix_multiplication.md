# 행렬의 곱셈 (matrix multiplication)

주의: 이 글은 공학분야 활용 중심이며, 수학전공자가 보기에 엄밀하지 않을 수 있습니다.

행렬의 곱셈은 행렬에 대한 이항연산(binomial operation)입니다.

$m \times n$ 크기의 행렬 $\mathbf A$이 있고, $n \times p$ 크기의 행렬 $\mathbf B$ 있습니다.
$$
\mathbf A = \begin{pmatrix}
a_{11} & a_{12} & \dots & a_{1n} \\
a_{21} & a_{22} & \dots & a_{2n} \\
\vdots & \vdots & \ddots & \vdots \\
a_{m1} & a_{m2} & \dots & a_{mn} \\
\end{pmatrix}
$$

$$
\mathbf B = \begin{pmatrix}
b_{11} & b_{12} & \dots & b_{1p} \\
b_{21} & b_{22} & \dots & b_{2p} \\
\vdots & \vdots & \ddots & \vdots \\
b_{n1} & b_{n2} & \dots & b_{np} \\
\end{pmatrix}
$$

이 행렬 $\mathbf A$와 행렬 $\mathbf B$를 곱하면, 새로운 행렬 $\mathbf A \cdot \mathbf B$를 얻을 수 있고, 새로운 행렬 $\mathbf A \cdot \mathbf B$의 크기는 $m \times p$ 가 됩니다.
$$
\begin{aligned}
(\mathbf A \cdot \mathbf B)_{ij}
&=
a_{i1}\cdot b_{1j} +
a_{i2}\cdot b_{2j} + \cdots +
a_{in}\cdot b_{nj}
\\
&= \sum_{k=1}^{n} a_{ik} \cdot b_{kj}
\end{aligned}
$$

$$
\mathbf A \times \mathbf B = \begin{pmatrix}
\sum_{k=1}^n a_{1k}b_{k1} & \sum_{k=1}^n a_{1k}b_{k2} & \dots & \sum_{k=1}^n a_{1k}b_{kp} \\
\sum_{k=1}^n a_{2k}b_{k1} & \sum_{k=1}^n a_{2k}b_{k2} & \dots & \sum_{k=1}^n a_{2k}b_{kp}\\
\vdots & \vdots & \ddots & \vdots \\
\sum_{k=1}^n a_{mk}b_{k1} & \sum_{k=1}^n a_{mk}b_{k2} & \dots & \sum_{k=1}^n a_{mk}b_{kp} \\
\end{pmatrix}
$$
행렬 $\mathbf A$ 의 열의 수와 행렬 $\mathbf B$ 의 행의 수가 동일하지 않으면, 두 행렬의 곱은 정의되지 않습니다.

## 예제

다음 행렬 $\mathbf A$, $\mathbf B$가 있을때, 이 두 행렬의 곱은 다음과 같습니다.
$$
\begin{aligned}

\mathbf A &= \begin{pmatrix} a \\ b \end{pmatrix}, \\

\mathbf B &= \begin{pmatrix} c & d \end{pmatrix}, \\


\mathbf A \times \mathbf B &=
\begin{pmatrix} a \\ b \end{pmatrix} \times \begin{pmatrix} c & d \end{pmatrix}
\\
&=
\begin{pmatrix} ac & ad \\ bc & bd \end{pmatrix}
\end{aligned}
$$
다음 행렬 $\mathbf A$, $\mathbf B$가 있을때, 이 두 행렬의 곱은 다음과 같습니다.
$$
\begin{aligned}

\mathbf A &= \begin{pmatrix} a & b \end{pmatrix}, \\
\mathbf B &= \begin{pmatrix} c \\ d \end{pmatrix}, \\

\mathbf A \times \mathbf B &=
\begin{pmatrix} a & b \end{pmatrix} \times \begin{pmatrix} c \\ d \end{pmatrix}
\\
&=
ac + bd
\end{aligned}
$$
다음 행렬 $\mathbf A$, $\mathbf B$가 있을때, 이 두 행렬의 곱은 다음과 같습니다.
$$
\begin{aligned}

\mathbf A &= \begin{pmatrix} a & b \\ c & d \end{pmatrix}, \\

\mathbf B &= \begin{pmatrix} e & f \\ g & h \end{pmatrix}, \\


\mathbf A \times \mathbf B &=
\begin{pmatrix} a & b \\ c & d \end{pmatrix} \times \begin{pmatrix} e & f \\ g & h \end{pmatrix}
\\
&=
\begin{pmatrix} ae+bg & af+bh \\ ce+dg & cf+dh \end{pmatrix}
\end{aligned}
$$

## 행렬 곱셈의 결합법칙

행렬의 곱셈은 결합법칙이 성립합니다.
$$
(\mathbf A \cdot \mathbf B) \cdot \mathbf C =
\mathbf A \cdot (\mathbf B \cdot \mathbf C)
$$

## 행렬 곱셈의 교환법칙

행렬의 곱셈은 교한법칙이 성립하지 않습니다.
$$
\mathbf A \cdot \mathbf B \neq \mathbf B \cdot \mathbf C
$$
특수한 경우 교환법칙이 성립할 수 있습니다. 크기가 동일한 두개의 정사각형 행렬이 다음 조건을 충족할때 교환법칙이 성립합니다.
$$
\mathbf A + \mathbf B = \mathbf A \cdot \mathbf B \\
\mathbf A \cdot \mathbf B = \mathbf B \cdot \mathbf A
$$

## 참조

- wikipedia.org

