# Ubuntu 18.04 LTS에서 NVidia 드라이버

*2019년 3월 8일 현재* 이 글은 완벽하지 않다. 일단 다음 참조를 위해 정리 하였다.

nvdia 드라이버, cuda 드라이버, Pytorch, Keras... 이들의 버전이 잘 맞지 않으면 엉망으로 돌아 간다. 공유 오브젝트를 지연 로딩하는 파이썬의 특징이기도 한다. 일단 Ubuntu 18.04에서 GPU를 통해 돌아가게 구성 하였다. 잊지 않기 위해 이를 회고하여 정리해 보자.

## nvidia-driver-415

일단 nvidia-driver-415를 설치해야 한다. 이번의 경우 여러차례 시도를 하다 보니 Software & Update에 415를 선택할 수 있게 표시 되었다.

![1552057674543](/home/booil/.config/Typora/typora-user-images/1552057674543.png)

더 최신인 418로 변경을 시도하였지만 선택되지 않았다. 다음에 다시 Ubuntu를 설치하게 되면 더 실험을 해보자.

일단 415가 설치되니 다른 도구들도 설치가 된다.

```sh
sudo apt install nvidia-cuda-toolkit
```

아나콘다를 설치하고

```sh
conda upgrade --all
```

아나콘다 환경을 생성하고

```sh
conda upgrade -n <환경이름> --all
```

```sh
source activate <환경이름>
conda install cudatoolkit
conda install -c pytorch pytorch
conda install -c pytorch torchvision
conda install -c <채널> torchtext
conda install keras-gpu
```

