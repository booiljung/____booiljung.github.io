# 미분

## 상미분 (ordinary derivative)

변수가 하나인 함수를 미분 하는 것을 상미분이라고 합니다.
$$
\begin{aligned}
\frac{\mathrm df(a)}{\mathrm dx}
&= \lim_{\Delta x \rarr 0} \frac{\Delta f(a)}{\Delta x} \\
&= \lim_{\Delta h \rarr 0} \frac{f(a + h) - f(a)}{(a+h)-a} \\
&= \lim_{\Delta h \rarr 0} \frac{f(a+h)-f(a)}{h}
\end{aligned}
$$

### 공식

$$
\begin{aligned}
\frac{\mathrm d}{\mathrm dx}
\left\{
x^r
\right\}
= rx^{r-1}
\end{aligned}
\tag{1}
$$

$$
\begin{aligned}
\frac{\mathrm d}{\mathrm dx}
\left\{
f(x) + g(x)
\right\}
= \frac{\mathrm d f(x)}{\mathrm d x} + \frac{\mathrm d g(x)}{\mathrm d x}
\end{aligned}
\tag{2}
$$

$$
\begin{aligned}
\frac{\mathrm d}{\mathrm dx}
\left\{
kf(x)
\right\}
= k\frac{\mathrm d f(x)}{\mathrm d x}
\end{aligned}
\tag{3}
$$

## 편미분 (partial derivative)

