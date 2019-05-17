[Up](index.md)

# 컴퓨터 비전에서 성능 평가

컴퓨터 비전(computer vision)의 성능을 개선하기 위해서는 엄밀한 성능 평가를 필요로 합니다. 보편적인 성능 평가는 정인식율(>corrected recognition rate), 기각율(rejected rate), 오류율(error rate)로 평가합니다.

$$
\begin{aligned}
\text{정인식률} &= \frac{c}{N} \\
\text{기각률} &= \frac{r}{N} \\
\text{오류율} &= \frac{e}{N} \\
\end{aligned}
$$

각각의 비율은 각각의 샘플 수로 나누면 됩니다. 기각은 기계가 분류를 확신하지 못하여 포기한 경우를 말하며 이는 모수(parameter)를 설정하여 조정할 수 있습니다.

$$
\begin{aligned}
c &= \text{정인식 샘플 수} \\
r &= \text{기각 샘플 수} \\
e &= \text{오류 샘플 수} \\
N &= c + r + e \\
\end{aligned}
$$


