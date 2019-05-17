[Up](index.md)

# Gaussian distribution

## 가우시안 분포식:

변수 $x$에 대한 단변량 가우시안 분포:

$$
\mathcal N (x | \mu, \sigma^2 )
=
\frac{1}{(2 \pi \sigma ^2) ^ {\frac{1}{2}}}
\exp \left\{ - \frac{1}{2 \sigma ^2}  (x - \mu) ^2  \right\}
$$

$\mu$는 평균, $\sigma^2$ 는 표준편차.

$D$차원 벡터 $\mathbf x$에 대한 다변량 가우시안 분포:

$$
\mathcal N ( \mathbf{x} | \boldsymbol \mu, \mathbf \Sigma )
=
\frac{1}{(2 \pi) ^{\frac{D}{2}}}
\frac{1}{ | \mathbf \Sigma | ^{\frac{1}{2}}}
\exp \left\{
    - \frac{1}{2} (\mathbf x - \boldsymbol \mu) ^\mathrm T
    \mathbf \Sigma ^{-1}
    (\mathbf x - \boldsymbol \mu)
\right\}
$$

$\boldsymbol \mu$는 평균벡터, $\boldsymbol \Sigma$는 공분산행렬, $|\boldsymbol \Sigma|$는 공분산행렬의 행렬식.

