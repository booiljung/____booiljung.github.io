# List in Module

Pythorch의 `nn.Module` 클래스는 특별합니다. 모델 클래는 `nn.Module`을 상속하고, 옵티마이저가 멤버를 탐색하기도 합니다. 그래서 각 레이어들은 멤버변수로 두어야  합니다.

가령 컨벌루션 레이어를 만든다면 이렇게 하죠.

```python
class MyNet(nn.Module):
    
    def __init__(self):
        super(MyNet, self).__init__()
        self.conv1 = nn.Conv2d(1, 10, 5, 1)
        self.conv2 = nn.Conv2d(10, 20, 5, 1)
        self.conv3 = nn.Conv2d(20, 40, 5, 1)
        self.conv4 = nn.Conv2d(40, 100, 5, 1)

    def forward(self, x):
        x = self.conv1(x)
        x = F.relu(x)
        x = F.max_pool2d(x, 2, 2)
        x = self.conv2(x)
        x = F.relu(x)
        x = F.max_pool2d(x, 2, 2)
        x = self.conv3(x)
        x = F.relu(x)
        x = F.max_pool2d(x, 2, 2)
        x = self.conv4(x)
        x = F.relu(x)
        x = F.max_pool2d(x, 2, 2)
        return x
```

동일한 코드를 4번이나 반복하는 부당해 보이는 코드 입니다. 피쳐 갯수`[1, 10, 20, 40, 100]`을 파라미터로 받아서 네트워크를 구성하면 코드도 짧아지고 재활용도 될텐데요.

위 코드는 이렇게 바꿔보죠.

```python
class MyNet(nn.Module):    
    def __init__(self, features):
        super(MyNet, self).__init__()
        self.convs = []
        for i in range(len(features)-1):
        	self.convs.append(nn.Conv2d(features[i], [features[i+1]], 5, 1))

    def forward(self, x):
    	for i in range(len(self.conv))
        	x = self.conv[i](x)
        	x = F.relu(x)
        	x = F.max_pool2d(x, 2, 2)
        return x
```

그런데 이 코드는 `model.parameters()`리턴값이 비어서 오류가 발생합니다. 

```pyhon
model = MyNet([1, 10, 20, 40, 100])
optim.SGD(model.parameters(), lr=0.02, momentum=my_momentum)
```

파이썬의 `getattr()`함수를 활용하면 이 문제를 해결 할 수 있습니다.

```python
class MyNet(nn.Module):    
    def __init__(self, features):
        super(MyNet, self).__init__()
        self.convs = []
        for i in range(len(features)-1):
        	name = 'conv'+str(i)
        	setattr(self, name, nn.Conv2d(features[i], features[i+1], 5, 1))
        	self.convs.append(getattr(self, name))

    def forward(self, x):
    	for i in range(len(self.conv))
        	x = self.conv[i](x)
        	x = F.relu(x)
        	x = F.max_pool2d(x, 2, 2)
        return x
```

각 컨볼루션 레이어들이 `self.conv0`, `self.conv1`, `self.conv2` 라는 식별자를 가진 멤버 변수가 되고, `model.parameters()` 반환값에 포함되게 됩니다.