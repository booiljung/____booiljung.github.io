[Up](index.md)

# Conv2dClassifier

2019년 3월 12일

하이퍼파라미터를 변경하며 모델을 반복해서 만드는 일은 단순하지만, 귀찮은 일입니다. 파라미터를 전달하여 파라미터 구성을 달리 할 수 있는 다음은 ConvNet 모델입니다.

```python
class Conv2dClassifier(nn.Module):
    
    def __init__(self, features, fcdims, conv_kernel_size=3, conv_stride=1, pool_kernel_size=2, pool_stride=2):
        super(Conv2dClassifier, self).__init__()

        self.pool_kernel_size = pool_kernel_size
        self.pool_stride = pool_stride

        self.convs = []
        for i in range(len(features)-1):
            name = 'conv'+str(i+1)
            setattr(self, name, nn.Conv2d(features[i], features[i+1], conv_kernel_size, conv_stride))
            self.convs.extend([
                getattr(self, name)
            ])
        
        self.fcdim0 = fcdims[0]

        self.fcs = []
        for i in range(len(fcdims)-1):
            name = 'fc'+str(i+1)
            setattr(self, name, nn.Linear(fcdims[i], fcdims[i+1]))
            self.fcs.extend([
                getattr(self, name)
            ])

    def forward(self, x):
        for i in range(len(self.convs)):
            x = self.convs[i](x)
            x = F.relu(x)
            x = F.max_pool2d(x, self.pool_kernel_size, self.pool_stride)
        x = x.view(-1, self.fcdim0)
        for i in range(len(self.fcs)-1):
            x = self.fcs[i](x)
            x = F.relu(x)
        x = self.fcs[-1](x)
        return F.log_softmax(x, dim=1)


    def train_model(self, device, train_loader, optimizer, step_func):
        self.train()
        for batch_idx, (data, target) in enumerate(train_loader):
            data, target = data.to(device), target.to(device)
            optimizer.zero_grad()
            output = self(data)
            loss = F.nll_loss(output, target)
            loss.backward()
            optimizer.step()
            step_func(batch_idx, data, loss)


    def test_model(self, device, test_loader, step_func):
        self.eval()
        test_loss = 0
        correct = 0
        with torch.no_grad():
            for data, target in test_loader:
                data, target = data.to(device), target.to(device)
                output = self(data)
                test_loss += F.nll_loss(output, target, reduction='sum').item() # sum up batch loss
                pred = output.argmax(dim=1, keepdim=True) # get the index of the max log-probability
                correct += pred.eq(target.view_as(pred)).sum().item()
        test_loss /= len(test_loader.dataset)
        step_func(test_loss, correct)

```

MNIST를 사용하면 다음과 같이 호출하여 모델을 구성합니다.

```python
model = tmodel.Conv2dClassifier(features=[1, 20, 50], fcdims=[4*4*50, 500, 10], kernel_size=5, stride=1).to(device)
...
def train_step(batch_idx, data, loss):
    ...
def test_step(test_loss, correct):
    ...
...
for epoch in range(1, opt.epochs + 1):
	model.train_model(device, train_loader, optimizer, train_step)
	model.test_model(device, test_loader, test_step)
```





