# Installation of Dart

## On Unbuntu 18.04 LTS

먼저 apt 저장소 키를 등록합니다.

```sh
sudo sh -c 'curl https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -'
sudo sh -c 'curl https://storage.googleapis.com/download.dartlang.org/linux/debian/dart_stable.list > /etc/apt/sources.list.d/dart_stable.list'
```

설치를 합니다.

```sh
sudo apt-get update
sudo apt-get install dart
```

### 참조

- [Installing Dart on Linux](https://v1-dartlang-org.firebaseapp.com/install/linux)

