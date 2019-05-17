# VirtualBox에 개발용 우분투 설치 체크리스트

개발용 윈도우 PC에 가벼운 서버를 설치할 경우 관리면에서 불편한 점이 있습니다.  컨테이너를 도입해야 하지만, 리눅스 서브시스템을 설치할 수 있는 윈도우는 고가라 개인에게 부담 스럽습니다. 그렇다고 매번 설치 과정을 반복하기에 공수가 들고, 빠뜨리는 일도 발생 합니다.

- 가상 머신을 끄고 Network 설정을 Bridge Adapter로 설정.

- 우분투를 다운로드 받아 설치.

- [Device > Cliboard > Bidirectional] 로 설정.

- [fcitx 설치](./ubuntu_korean_fcitx_installation/index.md).

- 자동 로그인 설정.

- `sudo apt install nemo` 설치.

- 우분투 `[설정 > Brightness & Lock]`에서 스크린세이버 및 인증 끄기.

- 머신의 `[Devices > Insert Guest Addtions CD Image...]` 메뉴를 선택하여 Guest Additions 설치.

- [Docker 설치](../docker/docker_training.md)

- [호스트 폴더를 게스트에 마운트](./virtual_box_shared_folder.md)

- 우분투 고정 IP 주소 설정 (머신을 복제할 때마다 새로 설정해 주어야 한다).
