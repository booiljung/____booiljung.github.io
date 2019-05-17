[Up](index.md)

# Bayes' theorem

$$
p(X = x_i, Y = y_j) = \frac{n_{ij}}{N}
$$

$$
p(X = x_i) = \frac{c_i}{N}
$$

$$
p(X = x_i) = \sum_{j=1}^L p(X = x_i, Y = y_i)
$$

$$
p(Y = y_i | X = x_i) = \frac{n_{ij}}{c_i}
$$

$$
\begin{aligned}
p(X = x_i, Y = y_j) &= \frac{n_{ij}}{N} = \frac{n_{ij}}{c_i} \cdot \frac{c_i}{N} \\
&= p(Y = y_i | X = x_i) \cdot p(X = x_i)
\end{aligned}
$$

$$
\begin{aligned}
p(X) &= \sum_Y p(X, Y) \\
&= p(X|Y)
\end{aligned}
$$

$$
\begin{aligned}
p(Y) &= \sum_Y p(Y, X) \\
&= p(Y|X)
\end{aligned}
$$

$$
\begin{aligned}
p(X, Y) &= p(X) \cdot p(Y) \\
&= p(X) \cdot p(Y|X) \\
&= p(Y|X) \cdot p(X)
\end{aligned}
$$

$$
\begin{aligned}
p(Y, X) &= p(Y) \cdot p(X) \\
&= p(Y) \cdot p(X|Y) \\
&= p(X|Y) \cdot p(Y)
\end{aligned}
$$

$$
p(X, Y) = p(Y, X)
$$

$$
p(Y|X) p(X) = p(X|Y) p(Y)
$$

$$
p(Y|X) = \frac{p(X|Y)p(Y)}{p(X)}
$$

$$
p(X) = \sum_Y p(X|Y) p(Y)
$$

