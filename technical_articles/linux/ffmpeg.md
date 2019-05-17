[Up](./index.md)

# Ubuntu 18.04에서 FFMPEG 설치

2019년 4월 10일

ffmpeg는 손쉬운 동영상 라이브러리 입니다. 웹브라우저부터 시작하여 많은 소프트웨어들이 ffmpeg에 의존합니다. Ubuntu에서 ffmpeg를 설치할때 컴파일을 해야 한다거나 하는 문서들이 있습니다. 일반적으로 컴파일할 필요 없이 설치 할 수 있습니다.

먼저 패키지를 업데이트 합니다.

```sh
sudo apt install update
```

ffmpeg를 설치합니다.

```sh
sudo apt install ffmpeg
```

제대로 설치 되었는지 확인 합니다.

```sh
ffmpeg -version
```

설치된 인코더나 디코더는 다음 인수를 주어 확인 할 수 있습니다.

```sh
ffmpeg -encoders
ffmpeg -decoders
```

## 참조

- [How to Install and Use FFmpeg on Ubuntu 18.04](https://linuxize.com/post/how-to-install-ffmpeg-on-ubuntu-18-04/)
