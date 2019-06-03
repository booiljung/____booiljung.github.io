# Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift

Sergey Ioffe, Google Inc., sioffe@google.com

Christian Szegedy, Google Inc., szegedy@google.com

# Abstract

Training Deep Neural Networks is complicated by the fact

that the distribution of each layer’s inputs changes during

training, as the parameters of the previous layers change.

This slows down the training by requiring lower learning

rates and careful parameter initialization, and makes it no

\-

toriously hard to train models with saturating nonlineari-

ties.  We refer to this phenomenon as

internal covariate

shift

,  and  address the  problem by  normalizing layer in-

puts. Our method draws its strength from making normal-

ization a part of the model architecture and performing the

normalization

for each training mini-batch

.  Batch Nor-

malization allows us to use much higher learning rates and

be less careful about initialization.  It also acts as a regu-

larizer, in some cases eliminating the need for Dropout.

Applied to a state-of-the-art image classification model,

Batch Normalization achieves the same accuracy with 14

times fewer training steps,  and beats the original model

by  a  significant  margin.   Using  an  ensemble  of  batch-

normalized networks, we improve upon the best published

result  on  ImageNet  classification:   reaching  4.9%  top-5

validation error (and 4.8% test error), exceeding the ac-

curacy of human raters.

# 1    Introduction

Deep learning has dramatically advanced the state of the

art  in  vision,  speech,  and  many  other  areas.   Stochas-

tic  gradient  descent  (SGD)  has  proved  to  be  an  effec-

tive  way  of  training  deep  networks,  and  SGD  variants

such as momentum (Sutskever et al., 2013) and Adagrad

(Duchi et al., 2011) have been used to achieve state of the

art performance. SGD optimizes the parameters

Θ

of the

network, so as to minimize the loss

Θ = arg min

Θ

1

N

N

∑

i

=1

ℓ

(x

i

,

Θ)

where

x

1

...N

is the training data set. With SGD, the train-

ing proceeds in steps, and at each step we consider a

mini-

batch

x

1

...m

of size

m

. The mini-batch is used to approx-

imate the gradient of the loss function with respect to the

parameters, by computing

1

m

∂ℓ

(x

i

,

Θ)

∂

Θ

Using mini-batches of examples, as opposed to one exam-

ple at a time, is helpful in several ways. First, the gradient

of the loss over a mini-batch is an estimate of the gradient

over the training set, whose quality improves as the batch

size increases.  Second, computation over a batch can be

much more efficient than

m

computations for individual

examples, due to the parallelism afforded by the modern

computing platforms.

While  stochastic  gradient  is  simple  and  effective,  it

requires  careful  tuning  of  the  model  hyper-parameters,

specifically the learning rate used in optimization, as well

as the initial values for the model parameters.  The train-

ing is complicated by the fact that the inputs to each layer

are affected by the parameters of all preceding layers – so

that small changes to the network parameters amplify as

the network becomes deeper.

The   change   in   the   distributions   of   layers’   inputs

presents a  problem  because  the  layers  need  to  continu-

ously adapt to the new distribution.  When the input dis-

tribution to a learning system changes, it is said to experi-

ence

covariate shift

(Shimodaira, 2000). This is typically

handled via domain adaptation (Jiang, 2008).  However,

the notion of covariate shift can be extended beyond the

learning system as a whole, to apply to its parts, such as a

sub-network or a layer. Consider a network computing

ℓ

=

F

2

(

F

1

(u

,

Θ

1

)

,

Θ

2

)



where

F

1

and

F

2

are  arbitrary transformations,  and  the

parameters

Θ

1

,

Θ

2

are  to  be  learned  so  as  to  minimize

the loss

ℓ

.   Learning

Θ

2

can be viewed as if  the inputs

x =

F

1

(u

,

Θ

1

)

are fed into the sub-network

ℓ

=

F

2

(x

,

Θ

2

)

.

For example, a gradient descent step

Θ

2

←

Θ

2

−

α

m

m

∑

i

=1

∂F

2

(x

i

,

Θ

2

)

∂

Θ

2

(for batch size

m

and learning rate

α

) is exactly equivalent

to that for a stand-alone network

F

2

with input

x

.  There-

fore, the input distribution properties that make training

more efficient – such as having the same distribution be-

tween  the  training  and  test  data  –  apply  to  training  the

sub-network as well.  As such it is advantageous for the

distribution of

x

to remain fixed over time. Then,

Θ

2

does

not have to readjust to compensate for the change in the

distribution of

x

Fixed  distribution  of  inputs  to  a  sub-network  would

have positive consequences for the layers

outside

the sub-

network, as well. Consider a layer with a sigmoid activa-

tion function

z =

g

(

W

u + b)

where

u

is the layer input,

the weight matrix

W

and bias vector

b

are the layer pa-

rameters to be learned,  and

g

(

x

) =

1

1+exp(

−

x

)

.   As

|

x

|

increases,

g

′

(

x

)

tends to zero.  This means that for all di-

mensions of

x =

W

u+b

except those with small absolute

values, the gradient flowing down to

u

will vanish and the

model will train slowly.  However, since

x

is affected by

W,

b

and the parameters of all the layers below, changes

to those parameters during training will likely move many

dimensions of

x

into the saturated regime of the nonlin-

earity  and  slow  down  the  convergence.   This  effect  is

amplified  as  the  network  depth  increases.   In  practice,

the saturation problem and the resulting vanishing gradi-

ents are usually addressed by using Rectified Linear Units

(Nair & Hinton,  2010)

ReLU

(

x

) = max(

x,

0)

,  careful

initialization (Bengio & Glorot, 2010; Saxe et al., 2013),

and  small learning rates.   If,  however,  we  could ensure

that the distribution of nonlinearity inputs remains more

stable as the network trains, then the optimizer would be

less  likely  to  get stuck in  the  saturated  regime,  and the

training would accelerate.

We refer to the change in the distributions of internal

nodes of a deep network, in the course of training, as

In-

ternal Covariate Shift

.  Eliminating it offers a promise of

faster training.  We propose a new mechanism, which we

call

Batch  Normalization

,  that  takes  a  step  towards  re-

ducing internal covariate shift, and in doing so dramati-

cally accelerates the training of deep neural nets.  It ac-

complishes  this  via  a  normalization  step  that  fixes  the

means and variances of layer inputs. Batch Normalization

also has a beneficial effect on the gradient flow through

the  network,  by  reducing  the  dependence  of  gradients

on the scale  of the parameters or of their initial values.

This  allows  us  to  use  much  higher  learning  rates  with-

out the  risk  of  divergence.   Furthermore, batch  normal-

ization  regularizes  the  model  and  reduces  the  need  for

Dropout (Srivastava et al., 2014).  Finally, Batch Normal-

ization makes it possible to use saturating nonlinearities

by preventing the network from getting stuck in the satu-

rated modes.

In Sec. 4.2, we apply Batch Normalization to the best-

performing  ImageNet  classification  network,  and  show

that we can match its performance using only 7% of the

training steps,  and can  further exceed its accuracy by a

substantial margin.  Using an ensemble of such networks

trained with Batch Normalization, we achieve the top-5

error rate that improves upon the best known results on

ImageNet classification.

# 2    Towards

Reducing

Internal

Covariate Shift

We  define

Internal Covariate Shift

as the change in  the

distribution of network activations due to the  change in

network parameters during training. To improve the train-

ing,  we  seek  to  reduce  the  internal  covariate  shift.   By

fixing the distribution of the layer inputs

x

as the training

progresses, we expect to improve the training speed. It has

been  long  known  (LeCun et al.,  1998b;  Wiesler & Ney,

2011) that the network training converges faster if its in-

puts are whitened – i.e., linearly transformed to have zero

means and unit variances, and decorrelated. As each layer

observes the inputs produced by the layers below, it would

be advantageous to achieve the same whitening of the in-

puts of each layer. By whitening the inputs to each layer,

we would take a step towards achieving the fixed distri-

butions of inputs that would remove the ill effects of the

internal covariate shift.

We could consider whitening activations at every train-

ing  step  or  at  some  interval,  either  by  modifying  the

network  directly  or  by  changing  the  parameters  of  the

optimization  algorithm  to  depend  on  the  network  ac-

tivation  values  (Wiesler et al.,  2014;  Raiko et al.,  2012;

Povey et al.,  2014;  Desjardins & Kavukcuoglu).

How-

ever, if these modifications are interspersed with the op-

timization steps,  then the  gradient descent step  may at-

tempt  to  update  the  parameters  in  a  way  that  requires

the  normalization  to  be  updated,  which  reduces  the  ef-

fect of the gradient step.  For example, consider a layer

with the input

u

that adds the learned bias

b

, and normal-

izes the result by subtracting the mean of the activation

computed over the training data:

̂

x

=

x

−

E

[

x

]

where

x

=

u

+

b

,

X

=

{

x

1

...N

}

is the set of values of

x

over

the  training set,  and  E

[

x

] =

1

N

∑

N

i

=1

x

i

.   If  a  gradient

descent step ignores the dependence of E

[

x

]

on

b

, then it

will update

b

←

b

\+ ∆

b

, where

∆

b

∝ −

∂ℓ/∂

̂

x

.  Then

u

\+ (

b

\+ ∆

b

)

−

E

[

u

\+ (

b

\+ ∆

b

)] =

u

+

b

−

E

[

u

+

b

]

.

Thus, the combination of the update to

b

and subsequent

change in  normalization led  to  no  change in  the  output

of the layer nor, consequently, the loss.  As the training

continues,

b

will grow indefinitely while the loss remains

fixed. This problem can get worse if the normalization not

only centers but also scales the activations.  We have ob-

served this empirically in initial experiments, where the

model blows up when the normalization parameters are

computed outside the gradient descent step.

The issue with the above approach is that the gradient

descent optimization does not take into account the fact

that the normalization takes place.  To address this issue,

we would like to ensure that,  for any parameter values,

the network

always

produces activations with the desired

distribution.   Doing  so  would  allow  the  gradient of  the

loss with respect to the model parameters to account for

the normalization, and for its dependence on the model

parameters

Θ

.  Let again

x

be a layer input, treated as a

vector, and

X

be the set of these inputs over the training

data set. The normalization can then be written as a trans-

formation

̂

x =

Norm

(x

,

X

)

which depends not only on the given training example

x

but on all examples

X

– each of which depends on

Θ

if

x

is generated by another layer. For backpropagation, we

would need to compute the Jacobians

∂

Norm

(x

,

X

)

∂

x

and

∂

Norm

(x

,

X

)

∂

X

;

ignoring the latter term would lead to the explosion de-

scribed above. Within this framework, whitening the layer

inputs is expensive, as it requires computing the covari-

ance  matrix  Cov

[x] =

E

x

∈X

[xx

T

]

−

E

[x]

E

[x]

T

and  its

inverse square root, to produce the whitened activations

Cov

[x]

−

1

/

2

(x

−

E

[x])

, as well as the derivatives of these

transforms for backpropagation. This motivates us to seek

an alternative that performs input normalization in a way

that is differentiable and does not require the analysis of

the entire training set after every parameter update.

Some

of

the

previous

approaches

(e.g.

(Lyu & Simoncelli,    2008))    use    statistics    computed

over a single training example, or,  in the case of image

networks, over different feature maps at a given location.

However,  this  changes  the  representation  ability  of  a

network by  discarding the  absolute scale  of activations.

We want to a preserve the information in the network, by

normalizing the activations in a training example relative

to the statistics of the entire training data.

# 3    Normalization

via

Mini-Batch

Statistics

Since the full whitening of each layer’s inputs is costly

and not everywhere differentiable, we  make  two neces-

sary simplifications. The first is that instead of whitening

the  features in  layer  inputs and  outputs jointly,  we  will

normalize each scalar feature independently, by making it

have the mean of zero and the variance of 1.  For a layer

with

d

-dimensional input

x = (

x

(1)

. . . x

(

d

)

)

, we will nor-

malize each dimension

̂

x

(

k

)

=

x

(

k

)

−

E

[

x

(

k

)

]

√

Var

[

x

(

k

)

]

where the expectation and variance are computed over the

training data set. As shown in (LeCun et al., 1998b), such

normalization speeds up convergence, even when the fea-

tures are not decorrelated.

Note that simply normalizing each input of a layer may

change what the layer can represent.  For instance,  nor-

malizing the inputs of a sigmoid would constrain them to

the linear regime of the nonlinearity.  To address this, we

make sure that

the transformation inserted in the network

can represent the identity transform

.  To accomplish this,



