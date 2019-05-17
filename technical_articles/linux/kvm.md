# Check KVM on Ubuntu

먼저 `cpu-checker`를 설치합니다.

```sh
sudo apt install cpu-checker
```

그리고 

```sh
kvm-ok
```

를 실행하여 

```
INFO: /dev/kvm exists
KVM acceleration can be used
```

이 표시되면 `kvm`을 지원합니다.

만일

```
INFO: Your CPU does not support KVM extensions
KVM acceleration can NOT be used
```

이며 `kvm`을 지원하지 않습니다.

다음을 통해 현재 유저가 `kvm`을 사용할 수 있도록 합니다.

```sh
sudo apt install qeum-kvm
sudo adduser $USER kvm
```

## 참조

- [StackOverflow: Android Studio not recognising KVM on Ubuntu 18.04](https://askubuntu.com/questions/1062253/android-studio-not-recognising-kvm-on-ubuntu-18-04)