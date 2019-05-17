[Up](../index.md)

# 벡터의  내적 (inner product)

주의: 이 글은 공학분야 활용 중심입니다. 수학 전공자가 보기에 엄밀하지 않을 수 있습니다.

차원이 동일한 벡터의 서로 대응하는 성분끼리 곱한 다음 그것을 모두 더한 것이 내적입니다. 따라서, 벡터의 내적은 스칼라(scalar)이며, 서로 차원의 크기가 다르면 내적을 계산할 수 없습니다.

벡터의 내적(inner product)는 스칼라곱 (sclar product) 또는 점곱(dot product)로 불리기도 합니다.

벡터 $\mathbf a$와 $\mathbf b$가 있을때 벡터의 내적은 
$$
\left< \mathbf a, \mathbf b \right>
$$
또는
$$
\mathbf a \cdot \mathbf b
$$
으로 표현 합니다.

다음, 벡터
$$
\mathbf a = \begin{pmatrix} a_1 \\ a_2 \\ \vdots \\ a_n \end{pmatrix},
\\
\mathbf b = \begin{pmatrix} b_1 \\ b_2 \\ \vdots \\ b_n \end{pmatrix}
$$
가 있을때, 벡터의 내적
$$
\begin{aligned}

\left< \mathbf a, \mathbf b \right> &= a_1b_1 + a_2b_2 + \cdots + a_nb_n \\
&= \sum _{i=1} ^n a_i b_i

\end{aligned}
$$
입니다.

내적을 정의할때는 기하학적 특징으로 정의할 수도 있습니다.

벡터 $\mathbf a$와 벡터 $\mathbf b$가 있을때, 이 벡터들이 이루는 각이 $\theta$ 일 때, $\left< \mathbf a, \mathbf b \right>$ 는 다음과 같이 정의할 수 있습니다.
$$
\left< \mathbf a, \mathbf b \right> = \left\| \mathbf a \right\| \left\| \mathbf b \right\| \cos \theta
$$
여기서 $\theta$ 는 두 벡터 $\mathbf a$  와 $\mathbf b$ 의 방향의 각을 말하며, $\left\| \mathbf a \right\|$는 $\mathbf a$의 길이로 노름(norm)을 말합니다.



