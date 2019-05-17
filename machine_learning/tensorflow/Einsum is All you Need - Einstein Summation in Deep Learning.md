# [Einsum is All you Need - Einstein Summation in Deep Learning](https://rockt.github.io/2018/04/30/einsum)

When talking to colleagues I realized that not everyone knows about *einsum*, my favorite function for developing deep learning models. This post is trying to change that once and for all! :) Einstein summation (einsum) is implemented in [numpy](https://docs.scipy.org/doc/numpy/reference/generated/numpy.einsum.html), as well as deep learning libraries such as [TensorFlow](https://www.tensorflow.org/api_docs/python/tf/einsum) and, thanks to [Thomas Viehmann](https://github.com/pytorch/pytorch/pull/6307), recently also [PyTorch](http://pytorch.org/docs/master/torch.html?highlight=torch%20max#torch.einsum). For background reading on einsum, I recommend the excellent blog posts by [Olexa Bilaniuk](https://obilaniu6266h16.wordpress.com/2016/02/04/einstein-summation-in-numpy/) and [Alex Riley](http://ajcr.net/Basic-guide-to-einsum/). While their posts discuss einsum in the context of numpy, I am going to illustrate how einsum is extremely useful for writing elegant PyTorch/TensorFlow models.[1](https://rockt.github.io/2018/04/30/einsum#fn.1)

# 1 Einsum Notation


If you are anything like me, you find it difficult to remember the names and signatures of all the different functions in PyTorch/TensorFlow for calculating dot products, outer products, transposes and matrix-vector or matrix-matrix multiplications. Einsum notation is an elegant way to express all of these, as well as complex operations on tensors, using essentially a domain-specific language. This has benefits beyond not having to memorize or regularly looking up specific library functions. Once you understand and make use of einsum, you will be able to write more concise and efficient code more quickly. When not using einsum it is easy to introduce unnecessary reshaping and transposing of tensors, as well as intermediate tensors that could be omitted. Furthermore, domain-specific languages like einsum can sometimes be compiled to high-performing code, and einsum is in fact the basis for the recently introduced Tensor Comprehensions2 in PyTorch which automatically generate GPU code and auto-tune that code for specific input sizes. 

Let's say we want to multiply two matrices A∈ℝI×K and B∈ℝK×J, resulting in a matrix C∈ℝI×J. Using Einstein summation notation, we can write this matrix-matrix multiplication as

cij=∑kaikbkj

which specifies how all individual elements cij in C are calculated from multiplying values in the column vectors ai: and row vectors b:j and summing them up. So far so good, but we can also express more basic operations using  einsum. For instance, calculating the dot product of two vectors a,b∈ℝI can be written as

c=∑iaibi.

A problem that I encounter often in deep learning is applying a transformation to vectors in a higher-order tensor. For example, I might have a tensor that contains J-long sequences of K-dimensional word vectors for B training examples in a batch and I want to project the word vectors to a different dimension Q. Let ∈ℝB×J×K be an order-3 tensor where the first dimension corresponds to the batch, the second dimension to the sequence length, and the last dimension to the word vectors. In addition, let W∈ℝK×Q be a projection matrix. The desired computation can be expressed using einsum

cijq=∑ktijkwkq.

As a final example, say you are given an order-4 tensor ∈ℝB×J×K×M and you are supposed to project vectors in the 3rd dimension to Q using the projection matrix from before. However, let's say I also ask you to sum over the 2nd dimension and transpose the result, yielding a matrix C∈ℝM×B. Einsum to the rescue!

cmi=∑j∑ktijkmwkq.

Do you see a pattern emerging?

# 2 All you Need: Einsum in numpy, PyTorch, and TensorFlow


Einsum is implemented in numpy via np.einsum, in PyTorch via torch.einsum, and in TensorFlow via tf.einsum. All three einsum functions share the same signature einsum(equation,operands) where equation is a string representing the Einstein summation and operands is a sequence of tensors.3 The examples above can all be written using an equation string.  For instance, the matrix-matrix multiplication cij=∑kaikbkj can be written as the equation string "ik,kj -> ij". Note that this string expresses the summation over k implicitly as k does not appear on the right-hand side. Moreover, the naming of the indices is arbitrary but it needs to be used consistently. 

What's great about having einsum not only in numpy but also in PyTorch and TensorFlow is that it can be used in arbitrary computation graphs for neural network architectures and that we can backpropagate through it. A typical call to einsum has the following form

result=einsum("◻◻,◻◻◻,◻◻->◻◻",arg1,arg2,arg3)

where ◻ is a placeholder for a character identifying a tensor dimension. From this equation string we can infer that arg1 and arg3 are matrices, arg2 is an order-3 tensor, and that the result of this einsum operation is a matrix. Note that einsum works with a variable number of inputs. In the example above, einsum specifies an operation on three arguments, but it can also be used for operations involving one, two or more than three arguments. Einsum is best learned by studying examples, so let's go through some examples for einsum in PyTorch that correspond to library functions which are used in many deep learning models.

## 2.1 Transpose


cji=aij

```python
import torch
a = torch.arange(6).reshape(2, 3)
torch.einsum('ij->ji', [a])
```

```xml
tensor([[ 0.,  3.],
        [ 1.,  4.],
        [ 2.,  5.]])
```

##2.2 Sum


c=∑i∑jaij

```python
a = torch.arange(6).reshape(2, 3)
torch.einsum('ij->', [a])
```

```xml
tensor(15.)
```

##2.3 Column Sum


cj=∑iaij

```python
a = torch.arange(6).reshape(2, 3)
torch.einsum('ij->j', [a])
```

```xml
tensor([ 3.,  5.,  7.])
```

## 2.4 Row Sum


ci=∑jaij

```python
a = torch.arange(6).reshape(2, 3)
torch.einsum('ij->i', [a])
```

```xml
tensor([  3.,  12.])
```

##2.5 Matrix-Matrix Multiplication


Our running example from before.
cij=∑kaikbkj

```python
a = torch.arange(6).reshape(2, 3)
b = torch.arange(15).reshape(3, 5)
torch.einsum('ik,kj->ij', [a, b])
```

```xml
tensor([[  25.,   28.,   31.,   34.,   37.],
        [  70.,   82.,   94.,  106.,  118.]])
```

##2.6 Matrix-Vector Multiplication


ci=∑kaikbk

```python
a = torch.arange(6).reshape(2, 3)
b = torch.arange(3)
torch.einsum('ik,k->i', [a, b])
```

```xml
tensor([  5.,  14.])
```

##2.7 Dot Product

Vector:

c=∑iaibi

```python
a = torch.arange(3)
b = torch.arange(3,6)  # -- a vector of length 3 containing [3, 4, 5]
torch.einsum('i,i->', [a, b])
```

```xml
tensor(14.)
```

Matrix:

c=∑i∑jaijbij

```python
a = torch.arange(6).reshape(2, 3)
b = torch.arange(6,12).reshape(2, 3)
torch.einsum('ij,ij->', [a, b])
```

```xml
tensor(145.)
```

##2.8 Hadamard Product


cij=aijbij

```python
a = torch.arange(6).reshape(2, 3)
b = torch.arange(6,12).reshape(2, 3)
torch.einsum('ij,ij->ij', [a, b])
```

```xml
tensor([[  0.,   7.,  16.],
        [ 27.,  40.,  55.]])
```

##2.9 Outer Product


cij=aibj


a = torch.arange(3)
b = torch.arange(3,7)  # -- a vector of length 4 containing [3, 4, 5, 6]
torch.einsum('i,j->ij', [a, b])

tensor([[  0.,   0.,   0.,   0.],
        [  3.,   4.,   5.,   6.],
        [  6.,   8.,  10.,  12.]])

##2.10 Batch Matrix Multiplication


cij=∑kaijkbikl


a = torch.randn(3,2,5)
b = torch.randn(3,5,3)
torch.einsum('ijk,ikl->ijl', [a, b])

tensor([[[ 1.6788, -0.9068,  1.5409],
         [-0.8146, -0.9746, -1.4256]],

```xml
    [[ 2.3736,  0.3846, -1.3845],
     [-1.0196, -0.4007,  0.8802]],

    [[-0.9265,  4.1591, -2.9643],
     [-1.0113,  2.0883,  3.0959]]])
```

## 2.11 Tensor Multiplication


Batch matrix multiplication is a special case of a tensor multiplication. Let's say we have two tensors, an order-n tensor ∈ℝI1×⋯×In and an order-m tensor ∈ℝJ1×⋯×Im.
As an example, take n=4, m=5 and assume that I2=J3 and I3=J5. We can multiply the two tensors in these two dimensions (2 and 3 for  and 3 and 5 for ) resulting in a new tensor ∈ℝI1×I4×J1×J2×J4 as follows
cpstuv=∑q∑rapqrsbtuqvr


a = torch.randn(2,3,5,7)
b = torch.randn(11,13,3,17,5)
torch.einsum('pqrs,tuqvr->pstuv', [a, b]).shape

torch.Size([2, 7, 11, 13, 17])

##2.12 Bilinear Transformation

As mentioned earlier, einsum can operate on more than two tensors. One example where this is used is bilinear transformation.

dij=∑k∑laikbjklcil

a = torch.randn(2,3)
b = torch.randn(5,3,7)
c = torch.randn(2,7)
torch.einsum('ik,jkl,il->ij', [a, b, c])

tensor([[-1.7542, -2.5391, -1.9625, -1.4395, -2.1652],
        [-0.8850,  0.1964,  0.1527, -0.9615,  0.0425]])

#3 Case Studies

##3.1 TreeQN

An example where I used einsum in the past is implementing equation 6 in 4. Given a low-dimensional state representation zl at layer l and a transition function Wa per action a, we want to calculate all next-state representations zal+1 using a residual connection.5

zal+1=zl+tanh(Wazl)

In practice, we want to do this efficiently for a batch B of K-dimensional state representations Z∈ℝB×K and for all transition functions (i.e. for all actions A) at the same time. We can arrange these transition functions in a tensor ∈ℝA×K×K and calculate the next-state representations efficiently using einsum.


import torch.nn.functional as F

def random_tensors(shape, num=1, requires_grad=False):
  tensors = [torch.randn(shape, requires_grad=requires_grad) for i in range(0, num)]
  return tensors[0] if num == 1 else tensors

# Parameters

# -- [num_actions x hidden_dimension]
b = random_tensors([5, 3], requires_grad=True)
# -- [num_actions x hidden_dimension x hidden_dimension]
W = random_tensors([5, 3, 3], requires_grad=True)

def transition(zl):
  # -- [batch_size x num_actions x hidden_dimension]
  return zl.unsqueeze(1) + F.tanh(torch.einsum("bk,aki->bai", [zl, W]) + b)

# Sampled dummy inputs
# -- [batch_size x hidden_dimension]
zl = random_tensors([2, 3])

transition(zl)



tensor([[[-1.7242,  0.3292,  0.1905],
         [-0.1919,  1.5831,  0.3056],
         [-1.6587,  0.0405,  1.4893],
         [ 0.1270,  0.4966,  0.4039],
         [-1.6734, -0.0062,  1.1650]],

        [[ 0.3329,  0.8956, -1.5150],
         [-0.7523,  1.5297, -1.5845],
         [ 1.1469,  2.8664, -0.8737],
         [ 1.1680,  2.7033, -0.4808],
         [-0.7719,  2.8522,  0.0185]]])



##3.2 Attention

Another real-world example for using einsum is the word-by-word attention mechanism defined in equations 11 to 13 in 6.

Mtαtrt=tanh(WyY+(Whht+Wrrt−1)⊗eL)=softmax(wTMt)=YαTt+tanh(Wtrt−1)Mtαtrt∈ℝk×L∈ℝL∈ℝk

This is not trivial to implement, particularly if we care about a batched implementation. Einsum to the rescue!

# Parameters

# -- [hidden_dimension]
bM, br, w = random_tensors([7], num=3, requires_grad=True)
# -- [hidden_dimension x hidden_dimension]
WY, Wh, Wr, Wt = random_tensors([7, 7], num=4, requires_grad=True)

# Single application of attention mechanism 
def attention(Y, ht, rt1):
  # -- [batch_size x hidden_dimension] 
  tmp = torch.einsum("ik,kl->il", [ht, Wh]) + torch.einsum("ik,kl->il", [rt1, Wr])
  Mt = F.tanh(torch.einsum("ijk,kl->ijl", [Y, WY]) + tmp.unsqueeze(1).expand_as(Y) + bM)
  # -- [batch_size x sequence_length]
  at = F.softmax(torch.einsum("ijk,k->ij", [Mt, w])) 
# -- [batch_size x hidden_dimension]

  rt = torch.einsum("ijk,ij->ik", [Y, at]) + F.tanh(torch.einsum("ij,jk->ik", [rt1, Wt]) + br)
  # -- [batch_size x hidden_dimension], [batch_size x sequence_dimension]
  return rt, at

# Sampled dummy inputs
# -- [batch_size x sequence_length x hidden_dimension]
Y = random_tensors([3, 5, 7])
# -- [batch_size x hidden_dimension]
ht, rt1 = random_tensors([3, 7], num=2)

rt, at = attention(Y, ht, rt1)
at  # -- print attention weights



tensor([[ 0.3651,  0.1988,  0.2401,  0.0556,  0.1405],
        [ 0.4192,  0.0079,  0.4985,  0.0367,  0.0377],
        [ 0.2961,  0.2288,  0.1679,  0.1807,  0.1265]])

#4 Summary


Einsum is one function to rule them all. It's your Swiss army knife for all kinds of tensor manipulations. 
That said, "einsum is all you need" is obviously an overstatement. If you look at the case studies above, I still had to add biases, apply non-linearities and construct extra dimensions (unsqueeze). Similarly, for splitting, concatenating or indexing of tensors you still have to employ other library functions. Moreover, einsum in PyTorch currently does not support diagonal elements, so the following throws an error: torch.einsum('ii->i', [torch.randn(3, 3)]). 

One thing that can become annoying when working with einsum is that you have to instantiate parameters manually, take care of their initialization, and registering them with modules. Still, I strongly encourage you to look out for situations where you can  use einsum.

#Footnotes: 


1 My examples use PyTorch, but translating them to TensorFlow is trivial.

2 Vasilache, Zinenko, Theodoridis, Goyal, DeVito, Moses, Verdoolaege, Adams and Cohen. Tensor Comprehensions: Framework-Agnostic High-Performance Machine Learning Abstractions. arXiv preprint arXiv:1802.04730. 2018

3 In numpy and TensorFlow, operands can be a variable-length argument list whereas in PyTorch it needs to be a list.

4 Farquhar, Rocktäschel, Igl and Whiteson. TreeQN and ATreeC: Differentiable Tree-Structured Models for Deep Reinforcement Learning. in: International Conference on Learning Representations (ICLR). 2018

5 He, Zhang, Ren and Sun. Deep Residual Learning for Image Recognition. in: 2016 IEEE Conference on Computer Vision and Pattern Recognition, CVPR. 2016

6 Rocktäschel, Grefenstette, Hermann, Kocisky and Blunsom. Reasoning about Entailment with Neural Attention. in: International Conference on Learning Representations (ICLR). 2016

