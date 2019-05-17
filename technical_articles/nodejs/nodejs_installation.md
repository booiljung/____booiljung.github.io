[Up](./index.md)

# Node.js  설치 방법

이 설치 안내서는 2018년 7월 22일 기준입니다.

리눅스를 밀때마다 Node.js를 다시 다운로드 받아야 하고, 사파 개발도구는 코딩보다 설치가 어렵고, 시행착오를 반복 해야 합니다. 그래서 정리 하였습니다.  우분투  16.04 LTS  기준으로 설명합니다.

## 1. Node.js 다운로드

인터넷 브라우저로 nodejs.org 에 접속합니다. 홈 화면에서 최신 버전과 LTS  버전에서 선택할 수 있습니다.

![1532226577195](nodejs_installation.assets/1532226577195.png)

8.x.x LTS 버전은 안정성을 지향합니다. 주로 이 버전을 선택합니다.

## 2. Node.js 압축 해제 설치 

정상적으로 다운로드가 진행되었다면, 다운로드 폴더에 'node-v<version>-linux-x64.tar.xz'를 발견할 수 있을 것입니다. 이 파일을 적절한 디렉토리에 압축을 풉니다. 저의 경우 ~/node에 설치합니다.

## 3. 환경 변수 설정

~/.profile이나 ~/.bashrc 에 다음을 설정 합니다. ~/.bashrc에 설정은 사용자의 셸이 bash를 사용할 경우 입니다.

```shell
export PATH=$PATH:~/node/bin
```

~/.profile 에 설정하였다면 로그아웃후 다시 로그인 하거나

 ```shell
source ~/.profile
 ```

로 실행 하거나, 셸에서 직접  `export ...`로 입력하고 엔터를 눌러도 됩니다.

##  4. 설치 확인

Node Package Manager의 버전을 확인하여 제대로 설치 되었는지 확인 할 수 있습니다.

```shell
npm -v
```











