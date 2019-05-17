[Up](./index.md)

# Installation of LAPACK

LAPACK는 고수준 선형대수 라이브러리입니다. 포트란으로 작성되어 있고 다른 저수준 선형대수 라이브러리에 의존합니다. 이 글을 작성하는 이유는 인터넷을 검색하면 여러 소스가 있는데 모두 실패하였기 때문입니다.

## Ubuntun 18.04 LTS

2019년 4월 14일 현재 유효합니다.

LAPACK에서 다운로드 받습니다. github에서 [공식 릴리즈 리파지터리](https://github.com/Reference-LAPACK/lapack-release)에서도 받을 수 있습니다. github에서 받을때 [개발용 리파지터리](https://github.com/Reference-LAPACK/lapack)와 구분되어 있으니 주의해야 합니다.

```sh
git clone https://github.com/Reference-LAPACK/lapack-release.git
```

빌드 폴더를 만들고 구성을 합니다.

```sh
mkdir build.ubuntu
cd build.ubuntu
cmake ../lapack-release
```

C에서 사용하려면 `-DCBLAS=TRUE` 변수를 설정합니다.

확장을 사용하려면 `-DLAPACKE=TRUE` 변수를 설정합니다.

레거시를 사용하려면 `-DBUILD_DEPRECATED=TRUE` 변수를 설정합니다.

구성이 되면 빌드를 하고 설치 합니다.

```sh
make
sudo make install
```

