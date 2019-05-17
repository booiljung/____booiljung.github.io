[Up](./index.md)

# Installation and Management of Anaconda

## Installation

2018년 3월 12일

운영체제를 설치할때마다 반복 설치하는 귀찮음으로 문서를 정리하였습니다.

---

먼저 [공식 사이트](https://www.anaconda.com/distribution/#linux)에서 설치 스크립트 `Anaconda3-YYYY.MM-OS-CPUARCHTECTURE.sh`를 다운로드 받습니다. 설치 스크립트 파일 이름은 버전과 운영체제 및 CPU 아키테쳐에 따라 다릅니다. 2019.3 버전은 654MB 크기입니다.

`Download` 폴더에서 설치를 합니다.

```sh
sh Anconda3-YYYY.MM-OS-CPUARCHTECTURE.sh
```

몇번의 `ENTER`를 입력하고 `yes`를 누르고, 설치 경로를 확인하면 설치가 시작됩니다.

설치 경로는 기본으로 `~/anaconda3`로 되며 `.bashrc`에 경로까지 등록을 해 줍니다.

설치가 완료되면

```sh
source ~/.bashrc
```

를 실행하여 환경을 다시 설정해 주면 Anaconda를 사용할 수 있습니다.

```sh
conda list
```

를 실행하여 정상적으로 사용할 수 있는지 확인 합니다.

## 업데이트

### 아나콘다 자체를 업데이트

```sh
conda update conda
```

### 아나콘다의 모든 패키지를 업데이트

```sh
conda update --all
```

### 머신러닝 연구용 설치 패키지 (ml)

아나콘다는 기본 채널에 패키지가 있는 경우도 있고, 채널을 지정해야 하는 경우도 있습니다. 그래서 검색을 해봐야하는데 이게 꽤 귀찮습니다. 그래서 귀찮음을 날려 버리기 위해 정리 합니다. 따로 yml 파일로 덤프해두는 방법이 있지만, 업데이트를 잊기도 합니다. 설치할때 웹에 정리해두는 편이 더 효율적이라는 것을 깨달았습니다.

### kaggle

kaggle 커맨드라인 인터페이스는 

```sh
pip install kaggle
```

로 설치합니다. 사용자 계정과 비밀번호는 

```json
export KAGGLE_USERNAME=<username>
export KAGGLE_KEY=<password>
```

로 지정할 수 있습니다.

##### kaggle API Token 설치

웹 브라우저로 다음 주소

```
https://www.kaggle.com/<username>/account
```

에 접속합니다. 여기서 `<username>`은 kaggle 계정이름을 지정합니다.

페이지의 중간을 보면 **Create New API Token** 버튼이 있습니다. 이 버튼을 클릭하면 `kaggle.json`파일을 다운로드 합니다. 다운로드가 완료되면 `~/.kaggle` 폴더로 옮깁니다.

이후 파일을 다운로드 하면 `~/.kaggle/competitions/` 폴더에 다운로드 받은 폴더가 생성되고 파일이 있습니다.

### PyQt5 오류

PyQt5에서 `from PyQt5 import Qt`에서  `icu.so` 오류가 발생시

```
ImportError: libicui18n.so.54: cannot open shared object file: No such file or directory
```

강제로 버전을 설치합니다.

```sh
conda install icu=54 --yes
```

그러면 pyqt5가 삭제 됩니다. 다시 pyqt를 설치합니다.

```sh
conda install -c anaconda pyqt5 --yes
```

pyqt5도 사용할 수 있게 됩니다.

### env

### env 목록 보기

```sh
conda info --envs
```

### env 생성

```sh
conda create --name <env_name> python=<python_version>
```

파이썬 3.7이 사용된다면 `<python_version>`은  `python=3.7` 형식으로 지정합니다.

### env 선택 및 시작

리눅스

```sh
source activate <env_name>
```

### env 종료

리눅스

```sh
source deactivate
```

### env 삭제

리눅스

```sh
conda env remove -n <env_name>
```

### env 복사

리눅스

```sh
conda create -n <new_env> --clone <source_env>
```

### env 업데이트

```
conda update -n <env> --all
```

## 참조

- [github: Kaggle-API](https://github.com/Kaggle/kaggle-api)

- [Blog: kaggle api 사용법](https://shakeratos.tistory.com/34)