원문: [ZooKeeper Getting Started Guide](https://zookeeper.apache.org/doc/r3.5.5/zookeeperStarted.html)

# ZooKeeper Getting Started Guide

## Getting Started: ZooKeeper로 분산 응용 프로그램 조정

이 문서에는 ZooKeeper를 빨리 시작 할 수있는 정보가 포함되어 있습니다. 이 도구는 주로 ZooKeeper 서버에 대한 간단한 설치 지침, 실행 중인지 확인하는 몇 가지 명령 및 간단한 프로그래밍 예제가 포함되어 있습니다. 마지막으로 편의상 복잡한 설치 (예 : 복제 배포 실행 및 트랜잭션 로그 최적화)에 대한 몇 가지 섹션이 있습니다. 그러나 상용 배포에 대한 전체 지침은 [ZooKeeper 관리자 가이드](https://zookeeper.apache.org/doc/r3.5.5/zookeeperAdmin.html)를 참조하십시오.

### 전제 조건

관리 가이드에서 [시스템 요구 사항](https://zookeeper.apache.org/doc/r3.5.5/zookeeperAdmin.html#sc_systemReq)을 참조하십시오.

### 다운로드

ZooKeeper 배포본을 얻으려면 Apache 다운로드 미러에서 최신 [stable](http://zookeeper.apache.org/releases.html) 버전을 다운로드하십시오.

### 독립 실행형

독립 실행형 모드에서 ZooKeeper 서버를 설정하는 것은 간단합니다. 서버는 단일 JAR 파일에 포함되어 있으므로 설치는 configuration 작성으로 구성됩니다.

stable ZooKeeper를 다운로드 한 다음, 압축을 풀고 루트로 이동하십시오.

ZooKeeper를 시작하려면 설정 파일이 필요합니다. 다음은 샘플입니다 **conf/zoo.cfg**에서 생성하십시오 :

```
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
```

이 파일은 무엇이든 호출 할 수 있지만 이 토론에서는 **conf/zoo.cfg**라고 부릅니다. **dataDir** 값을 변경하여 기존 (공백으로 시작) 디렉토리를 지정하십시오. 다음은 각 필드의 의미입니다.

- **tickTime** : ZooKeeper에서 사용하는 기본 시간 단위 (밀리 초)입니다. 하트 비트를 수행하는 데 사용되며 최소 세션 시간 초과는 tickTime의 두 배가됩니다.
- **dataDir** : 메모리 내 데이터베이스 스냅 샷을 저장할 위치 및 달리 지정하지 않는 한 데이터베이스에 대한 업데이트의 트랜잭션 로그.
- **clientPort** : 클라이언트 연결을 listen하는 포트

이제 구성 파일을 만들었으므로 ZooKeeper를 시작할 수 있습니다:

```
bin/zkServer.sh start
```

ZooKeeper는 log4j를 사용하여 메시지를 기록합니다. 자세한 내용은 프로그래머 가이드의 [Logging](https://zookeeper.apache.org/doc/r3.5.5/zookeeperProgrammers.html#Logging) 섹션을 참조하십시오. log4j 구성에 따라 콘솔에 오는 로그 메시지 (기본값) 및/또는 로그 파일이 표시됩니다.

여기에 설명 된 단계는 독립 실행형 모드에서 ZooKeeper를 실행합니다. 복제가 없으므로 ZooKeeper 프로세스가 실패하면 서비스가 중단됩니다. 대부분의 개발 상황에서는 문제가 없지만 ZooKeeper를 복제 모드로 실행하려면 [Running Replicated ZooKeeper](https://zookeeper.apache.org/doc/r3.5.5/zookeeperStarted.html#sc_RunningReplicatedZooKeeper)를 참조하십시오.

### ZooKeeper 저장소 관리

장기간 운영되는 시스템의 경우 ZooKeeper 스토리지는 외부에서 관리해야합니다 (dataDir 및 logs). 자세한 내용은 [maintenance](https://zookeeper.apache.org/doc/r3.5.5/zookeeperAdmin.html#sc_maintenance) 섹션을 참조하십시오.

### ZooKeeper에 접속하기

```
$ bin/zkCli.sh -server 127.0.0.1:2181
```

이를 통해 파일과 같은 간단한 작업을 수행 할 수 있습니다.

일단 접속하면 다음과 같은 내용이 표시됩니다:

```
Connecting to localhost:2181
log4j:WARN No appenders could be found for logger (org.apache.zookeeper.ZooKeeper).
log4j:WARN Please initialize the log4j system properly.
Welcome to ZooKeeper!
JLine support is enabled
[zkshell: 0]
```

셸에서`help`를 입력하여 다음과 같이 클라이언트에서 실행할 수있는 명령 목록을 가져옵니다:

```
[zkshell: 0] help
ZooKeeper host:port cmd args
    get path [watch]
    ls path [watch]
    set path data [version]
    delquota [-n|-b] path
    quit
    printwatches on|off
    create path data acl
    stat path [watch]
    listquota path
    history
    setAcl path acl
    getAcl path
    sync path
    redo cmdno
    addauth scheme auth
    delete path [version]
    deleteall path
    setquota -n|-b val path
```

여기에서 간단한 명령을 사용하여 이 간단한 명령행 인터페이스에 대한 느낌을 얻을 수 있습니다. 먼저`ls`에서와 같이 list 명령을 실행하여 시작합니다.

```
[zkshell: 8] ls /
[zookeeper]
```

다음으로 `create/zk_test my_data`를 실행하여 새로운 znode를 만듭니다. 그러면 새 znode가 만들어지고 문자열 "my_data"가 노드와 연결됩니다. 이것을 보세요:

```
[zkshell: 9] create /zk_test my_data
Created /zk_test
```

다른 `ls /`명령을 실행하여 디렉토리가 어떻게 보이는지 확인하십시오 :

```
[zkshell: 11] ls /
[zookeeper, zk_test]
```

이제 zk_test 디렉토리가 생성되었음을 알 수 있습니다.

다음과 같이`get` 명령을 실행하여 데이터가 znode와 연관되어 있는지 확인하십시오.

```
[zkshell: 12] get /zk_test
my_data
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 5
mtime = Fri Jun 05 13:57:06 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0
dataLength = 7
numChildren = 0
```

다음과 같이`set` 명령을 실행하여 zk_test와 관련된 데이터를 변경할 수 있습니다 :

```
[zkshell: 14] set /zk_test junk
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 6
mtime = Fri Jun 05 14:01:52 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0
dataLength = 4
numChildren = 0
[zkshell: 15] get /zk_test
junk
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 6
mtime = Fri Jun 05 14:01:52 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0
dataLength = 4
numChildren = 0
```

(데이터를 설정 한 후에`get '을했는지, 실제로 변경되었는지 알 수 있습니다.

마지막으로 다음을 실행하여 노드를 '삭제'합시다:

```
[zkshell: 16] delete /zk_test
[zkshell: 17] ls /
[zookeeper]
[zkshell: 18]
```

그게 지금이야. 자세한 내용은이 문서의 나머지 부분을 읽고 [프로그래머 가이드](https://zookeeper.apache.org/doc/r3.5.5/zookeeperProgrammers.html)를 참조하십시오.

### ZooKeeper에 프로그래밍

ZooKeeper에는 Java 바인딩과 C 바인딩이 있습니다. 기능적으로 동일합니다. C 바인딩은 단일 스레드와 다중 스레드의 두 가지 베리에이션으로 존재합니다. 이들은 메시징 루프가 수행되는 방법만 다릅니다. 자세한 내용은 다른 API를 사용하는 샘플 코드에 대해서는 [Programming Examples in the ZooKeeper Programmer's Guide](https://zookeeper.apache.org/doc/r3.5.5/zookeeperProgrammers.html#ch_programStructureWithExample)를 참조하십시오.

### 복제된 ZooKeeper 실행

독립 실행형 모드에서 ZooKeeper를 실행하면 평가, 개발 및 테스트에 편리합니다. 그러나 프로덕션 환경에서는 ZooKeeper를 복제 모드로 실행해야 합니다. 동일한 응용 프로그램에 있는 복제된 서버 그룹을 *쿼럼(quorum)*이라고 하며 복제 모드에서는 쿼럼의 모든 서버에 동일한 구성 파일의 복사본이 있습니다.

###### Note

> 복제 모드의 경우 최소 세 대의 서버가 필요하며 홀수 개의 서버가 있는 것이 좋습니다. 서버가 두 개인 경우 하나가 실패하면 다수 쿼럼을 구성 할 수 있는 시스템이 충분하지 않은 상황에 처해 있습니다. 두 개의 서버는 본질적으로 단일 서버보다 안정적이지 않습니다. 단일 장애 지점이 두 개 있기 때문입니다.

복제 된 모드에 필요한 **conf/zoo.cfg** 파일은 독립 실행형 모드에서 사용 된 것과 비슷하지만 약간의 차이점이 있습니다. 다음은 그 예입니다.

```
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
initLimit=5
syncLimit=2
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
```

새로운 항목인 **initLimit**는 시간 제한입니다. ZooKeeper는 쿼럼의 ZooKeeper 서버가 리더에 연결해야하는 시간을 제한하는 데 사용합니다. **syncLimit** 항목은 서버가 리더와 얼마나 오래 될 수 있는지를 제한합니다.

이 두 시간 초과를 사용하면 **tickTime**을 사용하여 시간 단위를 지정합니다. 이 예제에서 initLimit에 대한 시간 초과는 2000 밀리 초 (tick) 또는 10 초에 5 틱입니다.

*server.X* 형식의 항목은 ZooKeeper 서비스를 구성하는 서버를 나열합니다. 서버가 시작되면 데이터 디렉토리에서 *myid* 파일을 찾아서 어떤 서버인지 알게 됩니다. 이 파일에는 서버 번호가 ASCII 형식으로 들어 있습니다.

마지막으로 각 서버 이름 뒤에 두 개의 포트 번호 ( "2888"및 "3888")를 적어 두십시오. 피어는 이전 포트를 사용하여 다른 피어에 연결합니다. 이러한 연결은 피어가 예를 들어 업데이트 순서에 동의 할 수 있도록 해야 합니다. 보다 구체적으로, ZooKeeper 서버는 이 포트를 사용하여 Follower를 Leader와 연결합니다. 새로운 Leader가 생기면 Follower는이 포트를 사용하여 Leader와 TCP 연결을 엽니다. 기본 Leader 선거에서도 TCP를 사용하기 때문에 현재 Leader 선출을 위해 다른 포트가 필요합니다. 이것은 서버 항목의 두 번째 포트입니다.

###### Note

> 단일 시스템에서 여러 서버를 테스트하려면 서버 이름을 고유 한 쿼럼 및 리더 선택 포트 (예 : 위의 예에서는 2888 : 3888, 2889 : 3889, 2890 : 3890)와 함께 *localhost*로 지정하십시오. 그 서버의 설정 파일. 물론 별도의 _dataDir_s와 고유 한 _clientPort_s도 필요합니다 (위의 복제 예제에서는 단일 *localhost*에서 실행 중이지만 세 개의 구성 파일이 있음).
>
> 단일 시스템에 여러 서버를 설치해도 중복성이 생성되지는 않습니다. 기계가 죽는 원인이 발생하면 모든 Zookeeper 서버가 오프라인 상태가 됩니다. 전체 중복성을 위해서는 각 서버마다 자체 시스템이 있어야합니다. 완전히 별도의 물리적 서버이어야 합니다. 동일한 물리적 호스트에있는 여러 가상 시스템은 해당 호스트의 전체 장애에 여전히 취약합니다.

### 추가 최적화

크게 성능을 향상시킬 수 있는 몇 가지 다른 구성 매개 변수가 있습니다:

- 업데이트 지연을 줄이려면 전용 트랜잭션 로그 디렉터리가 있어야 합니다. 기본적으로 트랜잭션 로그는 데이터 스냅 샷 및 *myid* 파일과 같은 디렉토리에 저장됩니다. dataLogDir 매개 변수는 트랜잭션 로그에 사용할 다른 디렉토리를 나타냅니다.
- *[tbd: what is the other config param?]*