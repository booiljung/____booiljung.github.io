# Installation of SDL on Ubuntu

OpenGL은 표준을 지향하기 위해 탄생하였지만, OpenGL의 첫 윈도우와 프레임 버퍼를 띄우고, 소리를 발생하고 것 등은 운영체제마다 다르며, 이것은 개발자들을 힘들게 합니다. SDL은 한가지 방법으로 많은 운영체제들이 호환 될 수 있도록 돕습니다. OpenGL은 callback 함수를 통해 사용자 애플리케이션을 실행하는데, 메인 루프를 OpenGL이 가져가는 것은 코드 디자인에 제한을 줍니다. SDL은 응용 프로그램이 메인루프를 처리 할 수 있게 합니다.

## 설치

개발을 위한 SDL 설치 방법은 아래와 같습니다.

```sh
sudo apt-get install libsdl2-dev
```



