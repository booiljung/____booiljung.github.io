[Up](index.md)

# Google Colab의 GPU 정보 확인.

이 글은 2018년 4월 5일 현재 유효합니다. 

CPU로 트레이닝하면 1주일이 걸리는 네트워크를 <a href="https://colab.research.google.com">Google Colab</a>을 사용하여 100분 정도로 해결 하고 있습니다. 네트워크 트레이닝을 기다리며 다른 구글 계정으로, 구글 Colab이 어떤 장비를 제공하는지 궁금하여 <a href="http://pytorch.org/docs/master/cuda.html">PyTorch</a>를 사용하여 확인 해 보았습니다.

먼저, [이 글](google_colab_for_pytorch.md)을 참고 하여 Colab을 Python 3와 GPU로 설정합니다.

다음은 Colab에 파이토치를 설치합니다.

```sh
!pip3 install http://download.pytorch.org/whl/cu80/torch-0.3.0.post4-cp36-cp36m-linux_x86_64.whl
```

다음은 PyTorch로 디바이스 정보를 얻어 표시합니다.

```python
import torch

if torch.cuda.is_available():
	device_count = torch.cuda.device_count()
	print("device_count: {}".format(device_count))
 	for device_num in range(device_count):
   		print("device {} capability {}".format(
        	device_num,
            torch.cuda.get_device_capability(device_num)))
		print("device {} name {}".format(
	        device_num, torch.cuda.get_device_name(device_num)))
else:
  print("no cuda device")
```

실행하면...

```sh
device_count: 1
device 0 capability (3, 7)
device 0 name Tesla K80
```

[NVIDIA의 Telsa K80](http://kr.nvidia.com/object/tesla-k80-kr.html) 이네요!

주피터노트북 소스코드는 <a href="https://drive.google.com/file/d/1800zlbcgVcn3HRT0n4rUob_Jd4p67BCi/view?usp=sharing">이곳에 공개</a> 하였습니다.

## 같이보기

- [Colab에서 PyTorch 사용하기](google_colab_for_pytorch.md)

## 참조

- [torch.cuda](http://pytorch.org/docs/master/cuda.html)


