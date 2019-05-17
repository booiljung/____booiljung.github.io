# BatchNormal in ConvNet

### Yann Lecun's ConvNet without BatchNormal

```
Conv2d
MaxPool2d
ReLU
...
Linear
ReLU
...
SoftMax
```





```
Conv2d
MaxPool2d
ReLU
BatchNormal
...
Linear
ReLU
BatchNormal
...
SoftMax
```

### BatchNormal in ConvNet

| 데이터셋 | Model           | epoch | 전처리 | Accuracy |      |
| -------- | --------------- | ----- | ------ | -------- | ---- |
| CIFAR10  | Yann Lecun      | 200   | RGB    | 74%      |      |
| CIFAR10  | Yann Lecun      | 200   | Gray   | 69%      |      |
| CIFAR10  | Yann Lecun      | 200   | BPAT   | 72%      |      |
| CIFAR10  | Yann Lecun + BN | 200   | RGB    | 76%      |      |
| CIFAR10  | Yann Lecun + BN | 200   |        |          |      |
| CIFAR10  | Yann Lecun + BN | 200   | BPAT   | 76%      |      |

