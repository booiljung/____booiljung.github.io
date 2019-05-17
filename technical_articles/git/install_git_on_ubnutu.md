# Install git on Ubuntu 16.04 18.04 LTS

Ubuntu에서 CLI를 준비하고, 먼저 업데이트를 합니다.

```sh
sudo apt update
```

git을 설치 합니다.

```sh
sudo apt install git
```

git의 최초 설정을 합니다.

```sh
git config --global user.name "your name"
```

다음은 이메일을 등록합니다.

```sh
git config --global user.email "your email"
```

이제 git을 사용할 준비가 되었습니다.

원격 저장소를 사용하는 경우 원격 저장소의 계정과 비밀번호를 매회 요구하는데 불편하면, [이 글](setup_git_password_cache.md)을 참조하여 1회의 입력으로 계정 이름과 비밀번호를 캐시할 수 있습니다.

## 참조

- [Pro Git](https://git-scm.com/book/en/v2)