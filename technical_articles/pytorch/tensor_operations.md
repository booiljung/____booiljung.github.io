# PyTorch: Tensor operations

| methods                   | discriptions                                                 | 한국어                       |
| ------------------------- | ------------------------------------------------------------ | ---------------------------- |
| `is_tensor`               | Returns `True` if obj is a pytorch tensor.                   |                              |
| `is_storage`              | Returns `True` if obj is a pytorch storage object.           |                              |
| `set_default_tensor_type` | Set default `Tensor` type                                    |                              |
| `numel`                   | Returns the total number of elements in the `input` tensor.  |                              |
| `set_printoptions`        | Set options for printing. Items shamelessly taken from Numpy. |                              |
| `eye`                     | Returns a 2-D tensor with ones on the diagonal and zeros elsewhere. | 단위텐서                     |
| `from_numpy`              | The returned tensor and ndarray share the same memory. Modifications to the tensor will be reflected in the ndarray and vice versa. The returned tensor is not resizable. | numpy.ndarray에서 가져오기   |
| `linspace`                | Returns a one-dimensional tensor of `steps` equally spaced points between `start` and `end`. |                              |
| `logspace`                | Returns a one-dimensional tensor of `steps` points logarithmically spaced between $10^\mathrm{start}$ and $10^\mathrm{end}$ |                              |
| `ones`                    | Returns a tensor filled with the scalar value 1, with the shape defined by the varargs `sizes`. |                              |
| `one_like`                | Returns a tensor filled with the scalar value 1, with the same size as `input`. |                              |
| `arange`                  | Returns a 1-D tensor of size $\lfloor\frac{\mathrm{end}-\mathrm{start}}{\mathrm{step}} \rfloor$ with values from the interval `[start, end)` taken with step `step` starting from start. |                              |
| `range`                   | Returns a 1-D tensor of size $\lfloor\frac{\mathrm{end}-\mathrm{start}}{\mathrm{step}} \rfloor$ with values from `start` to `end` with step `step`. Step is the gap between two values in the tensor. $x_{i+1} = x_i + \mathrm{step}$. |                              |
| `zeros`                   | Returns a tensor filled with the scalar value 0, with the shape defined by the varargs `sizes`. | 영텐서                       |
| `zero_like`               | Returns a tensor filled with the scalar value 0, with the same size as `input`. |                              |
| `cat`                     | Concatenates the given sequence of `seq` tensors in the given dimension. All tensors must either have the same shape (except in the cat dimension) or be empty. | 텐서를 잇기.                 |
| `chunk`                   | Splits a tensor into a specific number of chunks.            | 텐서를 분리하기.             |
| `gather`                  | Gathers values along an axis specified by `dim`.             |                              |
| `index_select`            | Returns a new tensor which indexes the `input` tensor along dimension `dim` using the entries in `index` which is a `LongTensor`. |                              |
| `masked_select`           | Returns a new 1-D tensor which indexes the `input` tensor according to the binary mask `mask` which is a `ByteTensor`. |                              |
| `nonzero`                 | Returns a tensor containing the **indices** of all non-zero elements of `input`.  Each row in the result contains the indices of a non-zero element in `input`. | 0이 아닌 원소의 인덱스 추출. |
| `split`                   | Splits the tensor into chunks all of size `split_size` (if possible). |                              |
| `sqeeze`                  | Returns a tensor with all the dimensions of `input` of size 1 removed. | 차원이 1인 차원 제거.        |
| `stack`                   | Concatenates sequence of tensors along a new dimension.      |                              |
| `t`                       | Expects `input` to be a **matrix** (2-D tensor) and **transposes** dimensions 0 and 1. | 행렬의 전치                  |
| `take`                    | Returns a **new tensor** with the elements of `input` at the given **indices**. The input tensor is treated as if it were viewed as a **1-D** tensor. The result takes the same shape as the indices. | 지정한 인덱스를 추출.        |
| `transpose`               | Returns a tensor that is a transposed version of `input`. The given dimensions `dim0` and `dim1` are swapped. | 텐서의 전치                  |
| `unbind`                  | Removes a tensor dimension.                                  |                              |
| `unsqueeze`               | Returns a new tensor with a dimension of size one inserted at the specified position. | 차원이 1인 차원 삽입.        |

## Random sampling

| methods             | descriptions                                                 | 한국어                       |
| ------------------- | ------------------------------------------------------------ | ---------------------------- |
| manual_seed         | Sets the seed for generating random numbers. And returns a `torch._C.Generator` object. | 난수초기값지정               |
| `initial_seed`      | Returns the initial seed for generating random numbers as a python `long`. |                              |
| `get_rng_state`     | Returns the random number generator state as a `ByteTensor`. |                              |
| `set_rng_state`     | Sets the random number generator state.                      |                              |
| `default_generator` | -                                                            |                              |
| `bernoulli`         | Draws binary random numbers (0 or 1) from a Bernoulli distribution. | 베르누이 분포에 따르는 난수. |
| `multinomial`       | Returns a tensor where each row contains `num_samples` indices sampled from the multinomial probability distribution located in the corresponding row of tensor `input`. | 다항분포에 따르는 난수.      |
| `normal`            | Returns a tensor of random numbers drawn from separate normal distributions who’s mean and standard deviation are given. | 정규분포를 따르는  난수.     |
| `rand`              | Returns a tensor filled with random numbers from a uniform distribution on the interval $[0,1)$ | 0이상 1미만의 난수.          |
| `randn`             | Returns a tensor filled with random numbers from a normal distribution with zero mean and variance of one. | 정규분포를 따르는  난수.     |
| `randperm`          | Returns a random permutation of integers from `0` to `n - 1`. | 0 이상 n 미만의 정수형 난수. |

## Serialization

| methods | descriptions                                                 | 한국어         |
| ------- | ------------------------------------------------------------ | -------------- |
| `save`  | Saves an object to a disk file.                              | 파일로 저장.   |
| `load`  | Loads an object saved with [`torch.save()`](https://pytorch.org/docs/0.3.1/torch.html#torch.save) from a file. | 파일에서 읽기. |

## Parallelism

## Math operations

| methods      | 한국어                             | descriptions                                                 |
| ------------ | ---------------------------------- | ------------------------------------------------------------ |
| `abs`        | 원소별로 절대값                    | Computes the element-wise absolute value of the given `input` tensor. |
| `acos`       | 아크코사인                         | Returns a new tensor with the arccosine  of the elements of `input`. |
| `add`        |                                    | Adds the scalar `value` to each element of the input `input` and returns a new resulting tensor.<br>$$ \mathrm{out} = \mathrm{input} + \mathrm{value} $$ |
| `add`        |                                    | Each element of the tensor `other` is multiplied by the scalar `value` and added to each element of the tensor `input`. The resulting tensor is returned. The shapes of `input` and `other` must be [broadcastable](https://pytorch.org/docs/0.3.1/notes/broadcasting.html#broadcasting-semantics). <br>$$\mathrm{out} = \mathrm{input} + \mathrm{value} \times \mathrm{other}$$ |
| `addcdiv`    |                                    | Performs the element-wise division of `tensor1` by `tensor2`, multiply the result by the scalar `value` and add it to `tensor`.<br> $$\mathrm{out}_i = \mathrm{tensor}_i + \mathrm{value} \times \frac{\mathrm{tensor1}_i}{\mathrm{tensor2_i}}$$ |
| `addcmul`    |                                    | Performs the element-wise multiplication of `tensor1` by `tensor2`, multiply the result by the scalar `value` and add it to `tensor`.<br> $$\mathrm{out}_i = \mathrm{tensor}_i + \mathrm{value} \times \mathrm{tensor1}_i \times \mathrm{tensor2}_i$$ |
| `asin`       | 아크사인                           | Returns a new tensor with the arcsine  of the elements of `input`. |
| `atan`       | 아크탄젠트                         | Returns a new tensor with the arctangent  of the elements of `input`. |
| `atan2`      | 아크탄젠트                         | Returns a new tensor with the arctangent of the elements of `input1` and `input2`. |
| `ceil`       |                                    | Returns a new tensor with the ceil of the elements of `input`, the smallest integer greater than or equal to each element. |
| `clamp`      | 범위 안으로 반복                   | Clamp all elements in `input` into the range [min, max] and return a resulting tensor |
| `cos`        |                                    | Returns a new tensor with the cosine  of the elements of `input`. |
| `cosh`       |                                    | Returns a new tensor with the hyperbolic cosine  of the elements of input`. |
| `div`        |                                    | Divides each element of the input `input` with the scalar `value` and returns a new resulting tensor. |
| `erf`        |                                    | Computes the error function of each element.                 |
| `erfinv`     |                                    | Computes the inverse error function of each element.         |
| `exp`        |                                    | Computes the exponential of each element.                    |
| `floor`      |                                    | Returns a new tensor with the floor of the elements of `input`, the largest integer less than or equal to each element. |
| `fmod`       |                                    | Computes the element-wise **remainder of division**.         |
| `frac`       | 소수점 아래 남김                   | Computes the **fractional portion** of each element in `tensor`. |
| `lerp`       | 선형보간                           | Does a **linear interpolation** of two tensors `start` and `end` based on a scalar `weight` and returns the resulting `out` tensor. |
| `log`        | 자연로그                           | Returns a new tensor with the **natural logarithm** of the elements of `input`. |
| `log1p`      | 1을 더한 자연로그                  | Returns a new tensor with the **natural logarithm** of (1 + `input`). |
| `mul`        | 곱                                 | **Multiplies** each element of the input `input` with the scalar `value` and returns a new resulting tensor. |
| `neg`        | 음수                               | Returns a new tensor with the **negative** of the elements of `input`. |
| `pow`        | 지수                               | Takes the **power** of each element in `input` with `exponent` and returns a tensor with the result. |
| `reciprocal` | 역수                               | Returns a new tensor with the **reciprocal** of the elements of `input` |
| `remainder`  | 나머지                             | Computes the element-wise **remainder of division**. The divisor and dividend may contain both for integer and floating point numbers. The remainder has the **same sign as the divisor.** |
| `round`      | 근사값                             | Returns a new tensor with each of the elements of `input` **rounded** to the closest integer. |
| `rsqrt`      | 2의 역수                           | Returns a new tensor with the **reciprocal of the square-root** of each of the elements of `input`. |
| `sigmoid`    |                                    | Returns a new tensor with the **sigmoid** of the elements of `input`. |
| `sign`       |                                    | Returns a new tensor with the **sign** of the elements of `input`. |
| `sin`        |                                    | Returns a new tensor with the sine of the elements of `input`. |
| `sinh`       |                                    | Returns a new tensor with the hyperbolic sine of the elements of input. |
| `sqrt`       |                                    | Returns a new tensor with the **square-root** of the elements of `input`. |
| `tan`        |                                    | Returns a new tensor with the tangent of the elements of `input`. |
| `tanh`       |                                    | Returns a new tensor with the hyperbolic tangent of the elements of `input`. |
| `trunc`      |                                    | Returns a new tensor with the **truncated integer** values of the elements of `input`. |
| `cumprod`    |                                    | Returns the **cumulative product** of elements of `input` in the dimension `dim`. |
| `cumsum`     |                                    | Returns the **cumulative sum** of elements of `input` in the dimension `dim`. |
| `dist`       |                                    | Returns the **p-norm** of (`input` - `other`).               |
| `mean`       |                                    | Returns the **mean** value of all elements in the `input` tensor. |
| `median`     |                                    | Returns the **median** value of all elements in the `input` tensor. |
| `mode`       | row에서 가장 작은 값의 인덱        | Returns the **mode value of each row** of the `input` tensor in the given dimension `dim`. Also returns the index location of the mode value as a `LongTensor`. |
| `norm`       |                                    | Returns the **p-norm** of the `input` tensor.                |
| `prod`       |                                    | Returns the **product** of all elements in the `input` tensor. |
| `std`        |                                    | Returns the **standard-deviation** of all elements in the `input` tensor. |
| `sum`        |                                    | Returns the sum of all elements in the `input` tensor.       |
| `var`        |                                    | Returns the variance of all elements in the `input` tensor.  |
| `eq`         |                                    | Computes element-wise **equality**.                          |
| `equal`      | 원소가 동일한가?                   | `True` if two tensors have the same size and elements, `False` otherwise. |
| `ge`         | 원소가 크건 같은가?                | Computes input >= other element-wise.                        |
| `gt`         | 원소가 큰가?                       | Computes input > other element-wise.                         |
| `kthvalue`   | 주어진 차원에서 `k`번째로 작은 값. | Returns the `k` th smallest element of the given `input` tensor along a given dimension. |
| `le`         | 작거나 같은가?                     | Computes input <= other element-wise.                        |
| `lt`         | 작은가?                            | Computes input < other element-wise.                         |
| `max`        | 큰 원소 값.                        | Returns the maximum value of all elements in the `input` tensor. |
| `min`        | 작은 원소 값.                      | Returns the minimum value of all elements in the `input` tensor. |
| `ne`         | 원소가 다른가?                     | Computes input != other element-wise.                        |
| `sort`       |                                    | Sorts the elements of the `input` tensor along a given dimension in ascending order by value. |
| `topk`       | `k` 번째로 큰값.                   | Returns the `k` largest elements of the given `input` tensor along a given dimension. |
| `cross`      |                                    | Returns the **cross product** of vectors in dimension `dim` of `input` and `other`. |
| `diag`       |                                    | - If `input` is a vector (1-D tensor), then returns a 2-D square tensor with the elements of `input` as the diagonal.<br> - If `input` is a matrix (2-D tensor), then returns a 1-D tensor with the diagonal elements of `input`. |
| `histc`      |                                    | Computes the **histogram** of a tensor.                      |
| `renorm`     |                                    | Returns a tensor where each sub-tensor of `input` along dimension `dim` is normalized such that the p-norm of the sub-tensor is lower than the value `maxnorm` |
| `trace`      |                                    | Returns the **sum of the elements of the diagonal** of the input 2-D matrix. |
| `tril`       |                                    | Returns the **lower triangular part** of the matrix (2-D tensor) `input`, the other elements of the result tensor `out` are set to 0. |
| `triu`       |                                    | Returns the **upper triangular part** of the matrix (2-D tensor) `input`, the other elements of the result tensor `out` are set to 0. |

