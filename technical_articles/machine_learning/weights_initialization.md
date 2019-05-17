# 가중치 초기값

Weights Initialization

## 사비에르 초기값

Xavier Initialization

사비에르 초기값은 Xavier Glorot와 Yoshua Bengio에 의해 발견되었습니다.
$$
\sigma = \frac{1}{\sqrt{n}}
$$
여기서 $n​$은 앞 레이어의 노드 갯수입니다.

각 층의 활성화값들을 광범위하게 분포시킬 목적으로 가중치의 적절한 분포를 찾고자 했고, 앞 계층의 노드가 $n$개라면 표준편차가 $\frac{1}{\sqrt{n}}$인 분포를 사용하면 된다는 결론을 이끌었습니다. 그래서 사비에르 초기값을 사용하면 앞 층의 노드가 많을 수록 대상 노드의 초기값으로 설정하는 가중치가 좁게 퍼집니다.

사비에르 초기값을 파이썬으로 표현하면 다음과 같습니다.

```python
w = np.random.randn(prev_node_num, prev_node_num) / np.sqrt(prev_node_num)
```

sigmoid나 tanh는 대칭함수이며 중앙 부근이 선형입니다. 그래서 사비에르 초기값이 적당합니다.

## He 초기값

ReLU를 사용하면 음수의 입력에 대해 0이 되므로 2배 더 넓은 분포를 필요로 합니다. 따라서 ReLU를 사용할때는 ReLU에 특화된 초기값을 사용해야 합니다. 이 초기값을 찾아낸 Kaiming He의 이름에서 가져와 He 초기값이라고 합니다.
$$
\sigma = \frac{2}{\sqrt{n}}
$$
여기서 $n$은 앞 레이어의 노드 갯수입니다.

