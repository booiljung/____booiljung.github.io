[Up](../index.md)

# 마크다운 에디터

2019년 3월 9일

마크다운은 간결하며, 키보드만으로 편집 할 수 있으며, 텍스트 포맷으로 머지가 가능하기 때문에 개발자들이 선호하는 문서 형식입니다.

여러가지 마크다운 편집기가 있지만 [Typora](https://typora.io)는 뛰어 납니다.

리눅스에서 Typora의 설치는 [다음](https://typora.io/#linux)과 같습니다.

먼저 패키지매니저에 리파지토리 키를 설치합니다.

```sh
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -
```

다음은 패키지 매니저에 리파지토리를 등록하고 업데이트 합니다.

```sh
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
```

Typora를 설치합니다.

```sh
sudo apt-get install typora
```

## 참조

- [Typora](https://typora.io/#linux)