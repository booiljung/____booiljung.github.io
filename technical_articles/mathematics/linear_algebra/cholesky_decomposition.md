[Up](index.md)

# 숄레스키 분해(Cholesky decomposition)

주의: 이 글은 공학 분야 활용 중심이며 수학 전공자가 보기에 엄밀하지 않을 수 있습니다.

숄레스키 분해(Cholesky decomposition)란, 행렬을 하삼각행렬(lower triangular matrix)과 그 전치행렬의 곱으로 표현하는 것입니다.

이때 행렬은 [대칭행렬(symmetric matrix)](symmetric_matrix.md)이면서 [정칙행렬(nonsingular matrix)](nonsingular_matrix.md)인 행렬을 말합니다. 즉, 역행렬이 존재하면서 전치행렬도 동일할때 숄레스키 분해가 가능합니다.

콜레스키 행렬은 하삼각행렬 $\mathbf L$과 상삼각행렬 $\mathbf U$로 분해합니다. 이때 $\mathbf A$가 대칭행렬이므로 $\mathbf L$과 $\mathbf U$는 서로 전치이기도 합니다.

$$
\mathbf{A} = \mathbf{L} \mathbf{U}
$$

$\mathbf L$과 $\mathbf U$는 전치관계이므로

$$
\mathbf{A} = \mathbf{L} \mathbf{L}^\mathrm T
$$

또는

$$
\mathbf{A} = \mathbf{U}^\mathrm T \mathbf{U}
$$

로 표현할 수 있으며, 행렬의 항목을 자세히 보면

$$
\begin{aligned}
\begin{pmatrix}
a_{1,1} & a_{1,2} & a_{1,3} \\
a_{2,1} & a_{2,2} & a_{2,3} \\
a_{3,1} & a_{3,2} & a_{3,3} \\
\end{pmatrix}
&=
\begin{pmatrix}
l_{1,1} &       0 &       0 \\
l_{2,1} & l_{2,2} &       0 \\
l_{3,1} & l_{3,2} & l_{3,3} \\
\end{pmatrix}
\begin{pmatrix}
u_{1,1} & u_{1,2} & u_{1,3} \\
      0 & u_{2,2} & u_{2,3} \\
      0 &       0 & u_{3,3} \\
\end{pmatrix}
\\ \\
&=
\begin{pmatrix}
l_{1,1} &       0 &       0 \\
l_{2,1} & l_{2,2} &       0 \\
l_{3,1} & l_{3,2} & l_{3,3} \\
\end{pmatrix}
\begin{pmatrix}
l_{1,1} & l_{2,1} & l_{3,1} \\
      0 & l_{2,2} & l_{3,2} \\
      0 &       0 & l_{3,3} \\
\end{pmatrix}
\\ \\
&=
\begin{pmatrix}
u_{1,1} &       0 &       0 \\
u_{1,2} & u_{2,2} &       0 \\
u_{1,3} & u_{2,3} & u_{3,3} \\
\end{pmatrix}
\begin{pmatrix}
u_{1,1} & u_{1,2} & u_{1,3} \\
      0 & u_{2,2} & u_{2,3} \\
      0 &       0 & u_{3,3} \\
\end{pmatrix}
\end{aligned}
$$

과 같습니다. 숄레스키 분해는 최적화 문제의 해를 수치해석을 사용하여 구할때 가중최소제곱법을 반복하여 구할 수 있는데, 이때 가중최소제곱의 답을 숄레스키 분해로 구할 수 있습니다.

## PyTorch

PyTorch에서 숄레스키 분해는 [``torch.potrf()`` 함수](http://pytorch.org/docs/master/torch.html)를 사용하여 할 수 있습니다.

```python
>>> a = torch.randn(3, 3)
>>> a = torch.mm(a, a.t()) # make symmetric positive definite
>>> u = torch.potrf(a)
>>> a

 2.3563  3.2318 -0.9406
 3.2318  4.9557 -2.1618
-0.9406 -2.1618  2.2443
[torch.FloatTensor of size (3,3)]

>>> u

1.5350  2.1054 -0.6127
0.0000  0.7233 -1.2053
0.0000  0.0000  0.6451
[torch.FloatTensor of size (3,3)]

>>> torch.mm(u.t(), u)

 2.3563  3.2318 -0.9406
 3.2318  4.9557 -2.1618
-0.9406 -2.1618  2.2443
[torch.FloatTensor of size (3,3)]

```

## 참조

[pytorch.org](http://pytorch.org)
