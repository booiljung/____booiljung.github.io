# 오차함수 (error functions)

## RSS


$$
E(\mathbf{w})
=
\frac{1}{2}
\sum _{n=1} ^N
\{
	y(x_n, \mathbf{w}) - t_n
\}^2
\tag{1.2}
$$

## 평균제곱오차 (RMSE; root mean square error)

일반적으로 많이 사용.
$$
\mathrm{RMSE}(\mathbf X, h) =
\sqrt{\frac{1}{m} \sum_{i=1}^m
[h(\mathbf x_i)-y_i]^2}
$$
샘플의 예측값과 라벨의 차이를 제곱하고, 이들을 합하여, 샘플수로 산술평균을 구한다.

- $m$: 샘플 수
- $\mathbf X$: 훈련 데이터세트
- $h$: 는 예측 함수이며 가설(hypothesis)
- $h(\mathbf x_i)$의 결과 $\hat y_i$: 예측값.
- $\mathbf x_i$: $i$ 번째 데이터세트
- $y_i$: 는 $i$번째 라벨

#### 평균절대오차 (MAE; Mean Absolute Error) 또는 평균절대편차 (MAD; Mean Absolute Deviation)

이상치로 보이는 구역이 많은 경우 사용된다.
$$
\mathrm{MAE} (\mathbf X, h) =
\frac{1}{m} \sum_{i=1}^m \left | h(\mathbf x_i) - y_i  \right|
$$
 샘플의 예측값과 라벨의 차이를 모두 합하고, 샘플수로 산술평균을 구한다.

- $m$: 샘플 수
- $\mathbf X$: 훈련 데이터세트
- $h$: 는 예측 함수이며 가설(hypothesis)
- $h(\mathbf x_i)$의 결과 $\hat y_i$: 예측값.
- $\mathbf x_i$: $i$ 번째 데이터세트
- $y_i$: 는 $i$번째 라벨

## RMS (root-mean-square)


$$
E_\text{RMS} = \sqrt{2\frac{E(\mathbf{w}^*)}{N}}
$$

## 능선회귀 (ridge regression)

$$
\tilde{E}(\mathbf{w})
=
\frac{1}{2}
\sum_{n=1}^N
	\{
		y(x_n, \mathbf{w}) - t_n
	\}^2
	+
	\frac{\lambda}{2} \| \mathbf{w} \| ^2
	
\tag{1.4}
$$

## 참조

- bishop 2007

