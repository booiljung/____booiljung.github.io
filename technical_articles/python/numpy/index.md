# numpy

#### Ones and zeros

##### `np.empty(shape, dtype, order)`

랜덤으로 채워진 배열 생성.

```python
>>> np.empty([2, 2])
array([[ -9.74499359e+001,   6.69583040e-309],
       [  2.13182611e-314,   3.06959433e-309]])         #random
```

##### `np.empty_like(shape, dtype, order, subok)`

지정한 배열 형상에 따라 랜덤으로 채워진 배열 생성

```python
>>> a = ([1,2,3], [4,5,6])                         # a is array-like
>>> np.empty_like(a)
array([[-1073741821, -1073741821,           3],    #random
       [          0,           0, -1073741821]])
```

```python
>>> a = np.array([[1., 2., 3.],[4.,5.,6.]])
>>> np.empty_like(a)
array([[ -2.00000715e+000,   1.48219694e-323,  -2.00000572e+000],#random
       [  4.38791518e-305,  -2.00000715e+000,   4.17269252e-309]])
```

##### `np.eye(N, M, k, dtype, order)`

단위 행렬 생성. `k`는 대각에서의 거리.

```python
>>> np.eye(2, dtype=int)
array([[1, 0],
       [0, 1]])
```

```python
>>> np.eye(3, k=1)
array([[ 0.,  1.,  0.],
       [ 0.,  0.,  1.],
       [ 0.,  0.,  0.]])
```

##### `np.identity(n, dtype)`

단위 행렬 생성.

```python
>>> np.identity(3)
array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],
       [ 0.,  0.,  1.]])
```

##### `np.ones(shape, dtype, order)`

1로 채워진 배열 생성

```python
>>> s = (2,2)
>>> np.ones(s)
array([[ 1.,  1.],
       [ 1.,  1.]])
```

##### `np.ones(a, dtype, order, subok)`

```python
>>> x = np.arange(6)
>>> x = x.reshape((2, 3))
>>> x
array([[0, 1, 2],
       [3, 4, 5]])
>>> np.ones_like(x)
array([[1, 1, 1],
       [1, 1, 1]])
```

```python
>>> y = np.arange(3, dtype=float)
>>> y
array([ 0.,  1.,  2.])
>>> np.ones_like(y)
array([ 1.,  1.,  1.])
```

##### `np.ones_like(a, dtype, order, subok)`

##### `np.zeros(shape, dtype, order)`

##### `np.zeros_like(a, dtype, order, subok)`

##### `np.full(shape, fill_value, dtype, order)`

##### `np.full_like(a, fill_value, dtype, order, subok)`

#### From existing data

##### `np.array(object, dtype, copy, subok, ndmin)`

##### `np.asarray(a, dtype, order)`

##### `np.asanyarray(a, dtype, order)`

##### `np.ascontlguousarray(a, dtype)`

##### `np.asmatrix(data, dytpe)`

##### `np.copy(a, order)`

##### `np.frombuffer(buffer, dtype, count, offset)`

##### `np.fromfile(file, dtype, count, sep)`

##### `np.fromfunction(function, shape, **kwargs)`

##### `np.fromiter(iterable, dtype, count)`

##### `np.fromstring(string, dtype, count, sep)`

##### `np.loadtxt(fname, dtype, comments, delimiter, ...)`

#### Creating record arrays (numpy.rec)

#### Creating character arrays (numpy.char)

#### Numerical ranges

##### `np.arange(start, stop, step, dtype)`

##### `np.linspace(start, stop, num, endpoint, ...)`

##### `np.logspace(start, stop, num, endpoint, base, ...)`

##### `np.geomspace(start, stop, num, endpoint, ...)`

##### `np.meshgrid(*xi, **kwargs)`

```python
>>> nx, ny = (3, 2)
>>> x = np.linspace(0, 1, nx)
>>> x
array([0. , 0.5, 1. ])
>>> y = np.linspace(0, 1, ny)
>>> y
array([0., 1.])

>>> xv, yv = np.meshgrid(x, y)
>>> xv
array([[ 0. ,  0.5,  1. ],
       [ 0. ,  0.5,  1. ]])
>>> yv
array([[ 0.,  0.,  0.],
       [ 1.,  1.,  1.]])

>>> xv, yv = np.meshgrid(x, y, sparse=True)  # make sparse output arrays

>>> xv
array([[ 0. ,  0.5,  1. ]])
>>> yv
array([[ 0.],
       [ 1.]])
```

##### `np.mgrid`

##### `np.orgid`







## 참조

- [numpy official reference manual](https://docs.scipy.org/doc/numpy/reference/routines.array-creation.html)

