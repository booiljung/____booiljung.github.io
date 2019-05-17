# CIFAR10 이미지 덤프

CIFAR는 인기 있는 3채널 컬러 이미지로 $32 \times 32$ 크기에 10개의 클래스를 제공 합니다. 모델이 이미지를 제대로 생성하나 확인을 위해 덤프하였습니다.

## 1. planes

![](./cifar10.assets/0_4.png)

![](./cifar10.assets/0_16.png)

![](./cifar10.assets/0_27.png)

![](./cifar10.assets/0_38.png)

![](./cifar10.assets/0_45.png)

![](./cifar10.assets/0_54.png)

## 2. cars

![](./cifar10.assets/1_8.png)

![](./cifar10.assets/1_17.png)

![](./cifar10.assets/1_21.png)

![](./cifar10.assets/1_35.png)

![](./cifar10.assets/1_49.png)

![](./cifar10.assets/1_58.png)

## 3. birds

![](./cifar10.assets/2_0.png)

![](./cifar10.assets/2_10.png)

![](./cifar10.assets/2_24.png)

![](./cifar10.assets/2_30.png)

![](./cifar10.assets/2_46.png)

![](./cifar10.assets/2_53.png)

## 4. kitties

![](./cifar10.assets/3_3.png)

![](./cifar10.assets/3_14.png)

![](./cifar10.assets/3_23.png)

![](./cifar10.assets/3_37.png)

![](./cifar10.assets/3_40.png)

![](./cifar10.assets/3_56.png)

## 5. Deers

![](./cifar10.assets/4_6.png)

![](./cifar10.assets/4_15.png)

![](./cifar10.assets/4_25.png)

![](./cifar10.assets/4_33.png)

![](./cifar10.assets/4_44.png)

![](./cifar10.assets/4_52.png)

## 6. Puppies

![](./cifar10.assets/5_9.png)

![](./cifar10.assets/5_19.png)

![](./cifar10.assets/5_29.png)

![](./cifar10.assets/5_39.png)

![](./cifar10.assets/5_43.png)

![](./cifar10.assets/5_57.png)

## 7. Frogs

![](./cifar10.assets/6_2.png)

![](./cifar10.assets/6_11.png)

![](./cifar10.assets/6_22.png)

![](./cifar10.assets/6_32.png)

![](./cifar10.assets/6_47.png)

![](./cifar10.assets/6_50.png)

## 8. Horses

![](./cifar10.assets/7_5.png)

![](./cifar10.assets/7_12.png)

![](./cifar10.assets/7_20.png)

![](./cifar10.assets/7_31.png)

![](./cifar10.assets/7_42.png)

![](./cifar10.assets/7_59.png)

## 9. Ships

![](./cifar10.assets/8_1.png)

![](./cifar10.assets/8_13.png)

![](./cifar10.assets/8_28.png)

![](./cifar10.assets/8_34.png)

![](./cifar10.assets/8_41.png)

![](./cifar10.assets/8_55.png)

## 10. Trucks

![](./cifar10.assets/9_7.png)

![](./cifar10.assets/9_18.png)

![](./cifar10.assets/9_26.png)

![](./cifar10.assets/9_36.png)

![](./cifar10.assets/9_48.png)

![](./cifar10.assets/9_51.png)

## Pytroch

CIFAR10 이미지를 덤프하는 Pytorch 소스코드는 다음과 같습니다.

```python
import os
import numpy as np
import math

import torch

from torchvision import datasets
from torch.utils.data import DataLoader
from torchvision.utils import save_image


__all__ = ['saveimages']


def savefile(label, data, shape, number, out_dir, n_row=30):
    save = np.array([], np.float)
    save = np.reshape(save, (0, shape[0], shape[1], shape[2]))
    save = np.insert(save, 0, data, 0)
    print("save label ", label, save.shape)
    save = torch.from_numpy(save)
    fname = '%s/%d_%d.png' % (out_dir, label, number)
    save_image(save, fname, nrow=n_row, normalize=True)


def saveimages(dataloaders, n_classes, out_dir, file_qty, n_row=30):

    os.makedirs(out_dir, exist_ok=True)

    datas = [[] for _ in range(n_classes)]

    number = 0
    for dataloader in dataloaders:
        for image, label in dataloader:
            image = image.numpy()
            image = np.reshape(image, (image.shape[1], image.shape[2], image.shape[3]))
            data = datas[label]
            data.append(image)
            if file_qty <= len(data):
                savefile(label, data, image.shape, number, out_dir, n_row)
                number += 1
                data.clear()

    for label, data in enumerate(datas):
        if 1 <= len(data):
            savefile(label, data, image.shape, number, out_dir, n_row)
            number += 1
            data.clear()

```

```python
import argparse
import datetime as dt
import os
import numpy as np
import math

import torch
import torch.nn as nn
import torch.nn.functional as F
from torch.autograd import Variable

import torchvision.transforms as transforms
from torchvision import datasets
from torch.utils.data import DataLoader

from deepmodel.commonpy import filename as cmfn
from deepmodel.commonpy import imageio as imio
import deepmodel.torchmodel as tmodel


def parseargs(datetime):
    parser = argparse.ArgumentParser()
    parser.add_argument('--image_size', type=int, default=32, help='Size of each image dimension')
    parser.add_argument('--channels', type=int, default=3, help='Number of image channels')
    parser.add_argument('--n_classes', type=int, default=10, help='Number of classes for dataset')
    parser.add_argument('--image-quantity', type=int, default=10, help='Number of image per file')
    parser.add_argument('--data-dir', type=str, default=cmfn.datadir(__file__), help='Data cache directory')
    parser.add_argument('--out-dir', type=str, default=cmfn.outputdir(__file__, datetime), help='Out directory for image samples')
    parser.add_argument('--n_row', type=int, default=50, help='Number of rows of a image')
    parser.add_argument('--file_qty', type=int, default=1000, help='Number of images per a file')
    return parser.parse_args()


def main():
    now = dt.datetime.now()
    opt = parseargs(now)
    devtype = tmodel.DeviceType('cpu')

    trainloader = tmodel.cifar10_train_loader(devtype, opt.data_dir, opt.image_size, 1, (0.5, 0.5, 0.5), (0.5, 0.5, 0.5), shuffle=False)
    testloader = tmodel.cifar10_test_loader(devtype, opt.data_dir, opt.image_size, 1, (0.5, 0.5, 0.5), (0.5, 0.5, 0.5), shuffle=False)

    imio.saveimages([trainloader, testloader], opt.n_classes, opt.out_dir, opt.file_qty, n_row=opt.n_row)


if __name__ == "__main__":
    main()

```

