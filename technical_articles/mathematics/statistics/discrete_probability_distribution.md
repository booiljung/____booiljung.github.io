[Up](index.md)

# 이산 확률 분포 (Discrete probability distribution)

확률변수가 이산확률변수일 경우 확률분포는 이산확률분포라고 합니다.

## 기대값 (expected value):

$$
\mathbb E = \sum [ X \cdot P(X) ]
$$



## 분산 (variance):

$$
\begin{aligned}
\mathrm{Var}(X)
&= \mathbb E [ X - \mathbb E (X) ] ^2 \\
&= \sum [ X-\mathbb E(X) ]^2 \cdot P(X)
\end{aligned}
$$



또는

$$
\begin{aligned}
\mathrm{Var}(X)
&= \mathbb E(X^2) - [\mathbb E(X)]^2 \\
&= \sum X^2 \cdot P(X) - [\mathbb E(X)]^2
\end{aligned}
$$



## 기대값의 특성:

$$
\begin{aligned}
\mathbb E(a) &= a \\
\mathbb E(bX) &= b \mathbb E(X) \\
\mathbb E(a+bX) &= a + b \mathbb E(X) \\
\mathbb E(X +Y) &= \mathbb E(X) + \mathbb E(Y) \\
\mathbb E(aX + bY) &= a \mathbb E(X) + b \mathbb E (Y)
\end{aligned}
$$

여기서 $a$, $b$는 상수입니다.

## 분산의 특성:

$$
\begin{aligned}
\mathrm{Var}(a) &= 0 \\
\mathrm{Var}(X+a) &= \mathrm{Var}(X) \\
\mathrm{Var}(bX) &= b^2 \mathrm{Var}(X) \\
\mathrm{Var}(X+Y) &= \mathrm{Var}(X) + \mathrm{Var}(Y) + \mathrm{Cov}(X, Y)
\end{aligned}
$$

여기서 $a$, $b$는 상수이며, 공분산(covariance)는:

$$
\begin{aligned}
\mathrm{Cov}(X, Y) &= \mathbb E[X - \mathbb E(X)][Y - \mathbb E(Y)] \\
&= \mathbb E (X, Y) - \mathbb E (X) \mathbb E(Y)
\end{aligned}
$$

입니다.
