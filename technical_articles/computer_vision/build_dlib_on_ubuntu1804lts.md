[Up](index.md)

# Build DLib on Ubuntu 18.04 LTS

2019년 4월 13일

## Install OS libraries

```sh
sudo apt-get install build-essential cmake pkg-config
sudo apt-get install libx11-dev libatlas-base-dev
sudo apt-get install libgtk-3-dev libboost-python-dev
```

## Install DLib

### Download DLib

DLib 소스코드를 다운로드하여 tar를 풉니다. 여기서 `??`는 DLib 버전에 따라 다르며 [dlib.net](dlib.net)에서 버전을 확인 합니다.

```sh
wget http://dlib.net/files/dlib-??.??.tar.bz2
tar xvf dlib-??.??.tar.bz2
```

`dlib-??.??` 폴더가 생성되면 빌드를 위한 적당한 폴더로 옮깁니다.

```sh
mv dlib-??.?? ~/workspace/dlib
cd ~/workspace/dlib
```

빌드를 위한 별도의 폴더를 만듭니다.

```sh
mkdir dilb-??.??.build.ubuntu
cd dilb-??.??.build.ubuntu
```

구성을 하고 빌드를 합니다.

```sh
cmake ../dlib-??.??
cmake --build . --config Release
sudo make install
sudo ldconfig
```

## 참조

- [Install Dlib on Ubuntu](https://www.learnopencv.com/install-dlib-on-ubuntu/)