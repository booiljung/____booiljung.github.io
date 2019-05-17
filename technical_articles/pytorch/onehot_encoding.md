[Up](index.md)

# Pytorch: Onehot encoding

2019년 3월 30일

Pytorch does not provide a onehot encoding function. The following code is a Pytorch Python code that can do onehot encoding.

```pyhhon
indices=[2, 2, 2, 1, 1, 1, 0, 0, 8, 9, 0]
eye=torch.eye(10, dtype=torch.float32)
onehot=self.eye[indices]
print(onehot)
```

The results are as follows.

```
tensor([[0., 0., 1., 0., 0., 0., 0., 0., 0., 0.],
        [0., 0., 1., 0., 0., 0., 0., 0., 0., 0.],
        [0., 0., 1., 0., 0., 0., 0., 0., 0., 0.],
        [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
        [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
        [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
        [1., 0., 0., 0., 0., 0., 0., 0., 0., 0.],
        [1., 0., 0., 0., 0., 0., 0., 0., 0., 0.],
        [0., 0., 0., 0., 0., 0., 0., 0., 1., 0.],
        [0., 0., 0., 0., 0., 0., 0., 0., 0., 1.],
        [1., 0., 0., 0., 0., 0., 0., 0., 0., 0.]])
```

To be used in a model, the tensor must be a `Parameter` and the class must inherit `nn.Module`.

```Python
import numpy as np

import torch
import torch.nn as nn
from torch.nn.parameter import Parameter


__all__ = ['Onehot', 'OnehotFloat', 'OnehotDouble', 'OnehotFloat32', 'OnehotFloat64']

class Onehot(nn.Module):
    def __init__(self, n_classes, dtype=torch.float32):
        super(Onehot, self).__init__()
        self.n_classes = n_classes
        self.weight = Parameter(torch.eye(self.n_classes, dtype=dtype))

    def forward(self, indices):
        labels=self.weight[indices]
        return labels


class OnehotFloat(Onehot):
    def __init__(self, n_classes):
        super(OnehotFloat, self).__init__(n_classes, dtype=torch.float32)

OnehotFloat32 = OnehotFloat


class OnehotDouble(Onehot):
    def __init__(self, n_classes):
        super(OnehotDouble, self).__init__(n_classes, dtype=torch.float64)

OnehotFloat64 = OnehotDouble

```

Here's how to use this Onehot classes:

````python
indices = torch.tensor([2, 2, 2, 1, 1, 1, 0, 0, 8, 9, 0])
onehot = OnehotFloat(10)    
r = onehot(indices)
print(r)
````

The results are as follows.

```
tensor([[0., 0., 1., 0., 0., 0., 0., 0., 0., 0.],
        [0., 0., 1., 0., 0., 0., 0., 0., 0., 0.],
        [0., 0., 1., 0., 0., 0., 0., 0., 0., 0.],
        [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
        [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
        [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
        [1., 0., 0., 0., 0., 0., 0., 0., 0., 0.],
        [1., 0., 0., 0., 0., 0., 0., 0., 0., 0.],
        [0., 0., 0., 0., 0., 0., 0., 0., 1., 0.],
        [0., 0., 0., 0., 0., 0., 0., 0., 0., 1.],
        [1., 0., 0., 0., 0., 0., 0., 0., 0., 0.]], grad_fn=<TakeBackward>)
```





