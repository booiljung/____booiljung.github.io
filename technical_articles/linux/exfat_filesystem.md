[Up](./index.md)

# exFAT on Ubuntu

먼저 패키지 매니저에 `universe`리파지토리를 등록합니다.

```sh
sudo add-apt-repository universe
```

패키지 메니저를 업데이트합니다.

```sh
sudo apt update
```

exFAT 패키지들을 설치 합니다.

```sh
sudo apt install exfat-fuse exfat-utils
```

## 참조

- [How to Mount and Use and exFAT Drive on Ubuntu Linux](https://itsfoss.com/mount-exfat/)