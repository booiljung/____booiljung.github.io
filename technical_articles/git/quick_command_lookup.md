# git Quick command lookup

#### Installation

Ubuntu에서 CLI를 준비하고, 먼저 업데이트를 합니다.

```sh
sudo apt update
```

git을 설치 합니다.

```sh
sudo apt install git
```

#### 전역 사용자와 이메일 구성

git의 최초 설정을 합니다. 다음은 전역 유저 이름과 이메일을 구성합니다.

```sh
git config --global user.name "your name"
git config --global user.email "your email"
```

다음은 전역 구성 정보를 조회합니다.

```sh
git config --global --list
```

#### 저장소별 사용자와 이메일 구성

먼저 해당 저장소 폴더로 이동합니다. 그리고 사용자 이름과 이메일을 구성합니다.

```sh
git config user.name "your name"
git config user.email "your email"
```

다음은 저장소별 구성 정보를 조회합니다.

```sh
git config --list
```

#### 원격 저장소 비밀번호 캐시.

github나 bitbucket 같은 원격 저장소를 사용할 경우 비밀번호 입력을 요구 합니다. 때로 불편하기도 한데요. 일정 시간동안 비밀번호를 시스템에 저장하고, 비밀번호 입력을 생략할 수 있습니다. 리눅스의 경우 CLI에서 다음을 설정하면 됩니다.

```shell
git config --global credential.helper 'cache --timeout=500000'
```

이후 비밀번호를 입력하면 지정한 초단위 시간만큼 비밀번호를 캐시에 저장되며,지정한 시간동안 비밀번호를 다시 입력할 필요가 없습니다. 단, 운영체제를 다시 시작하면 이 설정은 삭제 됩니다. `.profile` 등에 설정하여 로그인시마다 설정 할 수 있습니다.

#### 새 저장소 만들기

새 폴더를 만든 후 저장소를 생성합니다.

```sh
git init
```

#### 저장소를 복제해오기

다음은 원격 저장소를 복제해 옵니다.

```sh
git clone <remote-repository>
```

#### 원격 저장소를 추가합니다.

다음은 원격 저장소를 추가합니다.

```sh
git remote add <remote-repository>
```

#### 파일

##### 새로운 파일 추가

다음은 커밋하고자하는 변경된 파일을 스테이지 합니다.

```sh
git add <file>
```

다수의 파일들을 추가 합니다.

```sh
git add -p <file1> <file2> <file3> ...
```

인터렉티브 모드로 파일을 추가 합니다. 다음을 실행하면 편집기가 실행되고 편집후 저장합니다.

```sh
git add -i
```

다음은 추적되는 파일의 변경 사항을 스테이지 합니다.

```sh
git add -u <path1> <path2> <path3> ...
```

#### 커밋

다음은 수정되고 추적되는 모든 파일의 변경사항을 스테이지하고 커밋합니다.

```sh
git commit -m "<message>" -a
```

다음은 이전 커밋을 수정하고 커밋 메시지를 재사용합니다.

```sh
git commit -C HEAD --amend
```

#### Checkout

지정한 브랜치를 작업합니다.

```sh
git checkout branch
```

다음은 지정한 커밋을 작업합니다.

```sh
git checkout <position>
```

여기서 `position`은 `commit` 또는 `tag` 또는 `branch` 입니다.

다음은 현재 수정된 내용을 되돌립니다.

```sh
git checkout HEAD <file1> <file2> <file3>...
```

#### Diff

현재 작업 트리와 인덱스의 차이점을 표시합니다.

```sh
git diff
```

인덱스와 저장소의 차이점을 표시합니다.

```sh
git diff -cached
```

작업 트리와 저장소의 차이점을 표시합니다.

```sh
git diff HEAD
```

작업트리와 특정 위치간의 차이점을 표시합니다.

```sh
git diff <start-position>
```

두 위치 사이의 차이점을 표시합니다.

```sh
git diff <position1> <position2>
```

두 위치 사이의 통계를 표시합니다.

```sh
git diff --stat <position1> <position2>
```

#### Blame

파일의 커밋 정보를 줄단위로 표시합니다.

```sh
git blame <file>
```

파일의 줄 단위의 복사, 붙여 넣기, 이동 정보를 표시합니다.

```sh
git blame -M <file>
```

파일의 줄 단위 이동과 원본 파일 정보를 표시합니다.

```sh
git blame -C -C <file>
```

#### 이력

모든 이력을 표시합니다.

```sh
git log
```

변경 사항을 보여주는 패치와 함께 이력을 표시합니다.

```sh
git log -p
```

3개의 이력 항목만 표시합니다.

```sh
git log -3
```

6개월 동안의 커밋 로그를 표시합니다.

```sh
git log --since="6 hours"
```

5일 전까지의 커밋 로그만을 표시합니다.

```sh
git log --before="2 days"
```

HEAD보다 이젠의 3개의 로그를 표시합니다. 다음 3개의 예제는 동일한 결과를 표시합니다.

```sh
git log -1 HEAD~3
git log -1 HEAD^^^
git log -1 HEAD~1^^
```

두 지점 사이의 커밋 로그를 표시합니다.

```sh
git log <position>...<position>
```

각 로그의 이력을 한줄씩 표시합니다.

```sh
git log --pretty=oneline
```

각 항목마다 영향 받은 줄의 통계를 봅니다.

```sh
git log --stat
```

커밋할 시점의 파일 상태를 봅니다.

```sh
git log --name-status
```

로그에서 복사와 붙여 놓은 정보를 표시합니다.

```sh
git log -C -C -p -1 <position>
```

#### Branch

브랜치는 커밋들의 링크로 별도의 피처를 개발할때 생성하고 완료되면 마스터 등에 합칩니다. git의 브랜치는 별도의 리소스를 차지하지 않으므로 자주 만들수록 좋습니다.

##### 보기

다음은 로컬 브랜치 목록을 표시합니다.

```sh
git branch
```

다음은 원격 브랜치 목록을 표시합니다

```sh
git branch -r
```

로컬과 원격 브랜치 모든 목록을 표시합니다.

```sh
git branch -a
```

##### 브랜치 생성하기

```sh
git branch <branch>
```

원격 브랜치에서 지역 브랜치 생성합니다.

```sh
git branch <new branch> <remote branch>
```

원격 위치에서 지역 브랜치 생성합니다.

```sh
git branch <new branch> <remote position>
```

##### 브랜치에서 작업하기

```sh
git checkout <branch>
```

작업중인 브랜치에서 새로운 브랜치를 생성하고 체크아웃 합니다.

```sh
git checkout -b <new branch>
```

다른 시작지점에서 새로운 브랜치를 생성합니다.

```sh
git branch <new branch> <point>
```

기존의 위치에 새로운 브랜치로 덮어 씁니다.

```
git branch -f <exist branch> <point>
```

브랜치를 옮기거나 브랜치 이름을 변경합니다.

```
git checkout -m <exit branch> <new branch>
```

이때 새로운 브랜치가 없을 경우

```
git checkout -M <exist branch> <new branch>
```

##### 브랜치 합치기

소스 브랜치에서 대상 브랜치를 가져와 합칩니다 .

```sh
git checkout <master>
git merge <branch>
```

커밋하지 않고 합칩니다.

```sh
git merge --no-commit <branch>
```

##### 브랜치 삭제

```sh
git branch -d <branch>
```

삭제할 브랜치가 현재 브랜치에 합쳐졌을 경우에는 

```sh
git branch -D <branch>
```

#### Rebase

피쳐를 만들기 위한 브랜치들은 생성되고 언젠가 닫게 됩니다. 많은 브랜치의 흔적들은 히스토리를 어지럽게 합니다. 리베이스는 브랜치를 하나의 길로 정리합니다.

##### 리베이스

대상 브랜치에서 소스 브랜치로 리베이스합니다.

```
git checkout <branch>
git rebase <master>
```

#### HEAD

`HEAD`는 현재 `checkout`된 작업중인 커밋을 말합니다. 일반적으로 `HEAD`는 각 작업중인 브랜치의 마지막 커밋을 가리키지만, 특정 커밋을 작업하는 경우 브랜치 끝이 아닐 수 있습니다. 

#### 해시참조

`^` 은 지정한 커밋의 부모 커밋. 다음은 `mater`의 마지막 커밋의 부모 커밋을 뜻합니다.

```sh
master^
```

`^^` 지정한 커밋의 조부모 커밋. 다음은 `master`의 마지막 커밋의 조부모 커밋을 뜻합니다.

```
master^^
```

다음은 `master`커밋의 마지막에서 두번째 커밋을 작업합니다.

```sh
git checkout master^
```

다음은 현재 작업중인 커밋의 부모 커밋을 작업합니다.

```sh
git checkout HEAD^
```

`~`은 지정한 커밋의 조상 커밋 레벨을 지정합니다.

다음은 `HEAD`에서 4번째 조상의 커밋을 작업합니다.

```sh
git checkout HEAD~4
```

`HEAD`의 부모의 커밋은 `HEAD~1`입니다.

```sh
git checkout HEAD~1
```

#### 브랜치 강제로 이동

-f 옵션은 강제를 지정합니다. 다음은 `master` 브랜치를 `HEAD`커밋의 3번째 조상으로 강제로 이동합니다.

```sh
git branch -f master HEAD~3
```

#### Reset

로컬에서 현재 작업중인 브랜치를 마지막 커밋을 하지 않은 것처럼 지정한 이전의 커밋으로 되돌리는 것을 말합니다. `HEAD`와 브랜치가 동시에 이동합니다.

다음은 커밋 되지 않고 스테이징된 변경사항 재설정합니다.

```
git reset HEAD <file1> <file2> <file3> ...
```

다음은 현재 커밋에서 첫번째 커밋으로로 옮깁니다. 

```sh
git reset HEAD~1
```

#### Revert

리셋은 참조를 옮기는 것으로 로컬에서만 적용할 수 있습니다. 원격에서 되돌리려면 `revert`를 사용해야 합니다. 다음은 변경분을 되돌리고, 이 되돌린 내용을 다른 사람들과 공유합니다.

다음은 현재 작업 중인 커밋의 부모 커밋을 가져와 새로운 커밋을 만듭니다.

```sh
git revert HEAD
```

#### Cherry-Pick

`cherry-pick`은 지정한 커밋을 현재 작업중 브랜치 아래로 가져옵니다.

```sh
git cherry-pick <Commit1> <Commit2> <...>
```

#### Interactive Rebase

인터렉티브 리베이스는 `-i` 옵션을 지정하여 편집기를 통해 리베이스를 편집하고 적용합니다. 

```sh
git rebase -i <commit>
```

다음은 헤더로부터 4개의 커밋을 리베이스 합니다.

```sh
git rebase -i HEAD~4
```

### Remote

#### Clone

저장소를 복제 합니다.

```sh
git clone <repository>
```

마지막 200여개의 커밋만 복제합니다.

```sh
git clone --depth 200 <repository>
```

#### Remote

새로운 원격 저장소를 추가 합니다.

```sh
git remote add <remote-repository> <repository-url>
```

원격 저장소에서 원격 브랜치를 삭제합니다.

```sh
git remote prune <remote repository>
```

원격 저장소를 제거하고 관련 브랜치도 제거합니다.

```sh
git remote rm <remote repository>
```

#### fetch

원격 저장소에 커밋들을 작업에 합치지 않고 로컬 가져옵니다.

```sh
git fetch
```

원격 저장소에서 커밋들을 작업에 합치지 않고 로컬로 가져 옵니다.

```sh
git fetch <remote repository>
```

##### pull

원격 저장소에서 fetch를 하고 merge를 합니다.

```sh
git pull <remote repository>
```

origin 저장소에서 변경 사항을 가져와 fetch를 하고 merge를 합니다.

```sh
git pull
```

##### push

로컬 저장소의 브랜치를 원격 브랜치에 밀어 넣습니다.

```sh
git push <remote repository> <local branch>:<remote branch>
```

지역 브랜치를 동일한 이름의 원격 브랜치에 밀어 넣습니다.

```sh
git push <remote repository> <local branch>
```

## 참조

- [git-scm.com](<https://git-scm.com/docs>)

- [Git 명령어 정리]([https://medium.com/@joongwon/git-git-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%A0%95%EB%A6%AC-c25b421ecdbd](https://medium.com/@joongwon/git-git-명령어-정리-c25b421ecdbd))

- [누구나 쉽게 이해할 수 있는 Git 입문](<https://backlog.com/git-tutorial/kr/>)

- [learngitbranching.js.org](https://learngitbranching.js.org/)

