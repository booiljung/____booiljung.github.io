# aptitude로 Ubuntu 18.04 LTS에 CUDA 설치

2019년 3월 10일

Ubuntu에 nvidia 드라이버나 cuda를 설치하는 것은 복잡하고 어렵습니다. 드라이버가 사용중이라며 제거되지도 않기도 합니다. 여기저기 인터넷 검색하며 해결하는데 많은 시간을 낭비 하기도 합니다. 정말 리눅서가 아니면 Ubuntu에 cuda를 설치하기란 매우 어렵습니다.

그런데 이것을 발견했습니다. 아마도 누군가 cuda를 설치하다가 빡쳐서 만든것 같은데요. `apitude`를 통해서 하면 쉽게 해결 됩니다.

```sh
sudo apt install aptitude
sudo aptitude install nvidia-driver-396
sudo aptitude install cuda
```

드라이버들을 내리고 올리면서 설치합니다. 설치하는데 시간이 꽤 걸립니다.

## 참조

