# Installation of Android Studio on Ubuntu

*우분투에 안드로이드 스튜디오 설치.*

안드로이드 공식 문서에 따라 우분투에 설치하면 잘 되지 않습니다. [쉽게 설치하는 방법](https://webnautes.tistory.com/1134)을 발견하였는데 빠진 부분도 있어 다시 정리하였습니다.

## 의존 패키지 설치

먼저 다음 [안내](../linux/kvm.md)에 따라 `qemu`을 설치 합니다.

64비트 우분트라면 다음 패키지들을 설치 합니다.

```sh
$ sudo apt install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386
```

## 안드로이드 스튜디오 설치

다음 PPA 저장소를 우분투를 위한 안드로이드 패키지를 제공하고 있습니다. PPA를 추가 합니다.

```sh
$ sudo add-apt-repository ppa:maarten-fonville/android-studio
```

저장소의 패키지 리스트들을 받아 옵니다.

```sh
 $ sudo apt update
```

두가지 패키중에 하나를 선택할 수 있습니다.

```sh
$ apt-cache search android-studio
```

stable 패키지를 설치합니다.

```sh
$ sudo apt install android-studio
```

##### 실행

터미널에서 안드로이드 스튜디오를 실행합니다.

```sh
$ /opt/android-studio/bin/studio.sh
```

##### Complete Installation

'Complete Installation' 대화상자가 표시됩니다. 처음 설치라면 'Do not import settings' 를 선택합니다.

- Complete Installation
  - Custom location. Config floder or installation home of previous version.
  - Do not import settings.

##### UI 테마

계속 'Next'를 눌러 진행하여 UI 테마를 선택합니다.

##### SDK Components Setup

다음 SDK들이 선택되어 있습니다.

- Android SDK
- Android SDK Platform
  - API XX: Android X.X

선택되어 있지 않은 

- Android Virtual Device

를 선택합니다.

Next 버튼을 클리갛면 설치될 SDK 목록이 표시되고, Finish 버튼을 누르면 설치가 시작 됩니다.

SDK가 설치되면 Finish 버튼을 누릅니다.

## 안드로이드 스튜디오

이제 Welcome to Android Studio 창이 표시 됩니다. 

- 새 안드로이드 프로젝트를 시작 하거나 기존 프로젝트를 열 수 있습니다.
- SDK 매니저를 실행할 수 있습니다.
- 플러그인 매니저를 설치할 수 있습니다.

## CLI에서 사용하기

##### 가상 디바이스 목록

```sh
$ ~/Android/Sdk/tools/emulator -list-avds
```

##### 가상 디바이스 실행

```
$ ~/Android/Sdk/tools/emulator -adv 디바이스이름
```

## 참조

- [Install Android Studio](https://developer.android.com/studio/install)

- [우분투에 안드로이드 개발환경 준비하기](https://webnautes.tistory.com/1134)