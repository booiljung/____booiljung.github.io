# Zookeeper: Overview

![ZooKeeper](https://zookeeper.apache.org/doc/r3.5.5/images/zookeeper_small.gif)

원문: [Zookeeper: Overview](https://zookeeper.apache.org/doc/r3.5.5/zookeeperOver.html)

## ZooKeeper : 분산 응용 프로그램을 위한 분산 조정 서비스

ZooKeeper는 분산 애플리케이션을 위한 분산형 오픈 소스 코디네이션 서비스입니다. 분산 응용 프로그램이 빌드, 동기화, 구성 유지 관리 및 그룹 및 네이밍을 위해 고급 서비스를 구현할 수 있는 간단한 프리미티브 집합을 제공합니다. 프로그램하기 쉽도록 고안 되었으며 익숙한 파일 시스템의 디렉토리 트리 구조를 기반으로 한 데이터 모델을 사용합니다. 그것은 자바에서 실행하고 자바와 C 모두 바인딩되어 있습니다.

조정 서비스는 바르게 하기에 어렵다고 악명 높습니다. 경쟁 조건이나 교착 상태와 같은 오류가 발생하기 쉽습니다. ZooKeeper의 동기는 분산 응용 프로그램에서 처음부터 조정 서비스를 구현해야하는 부담을 덜어주는 것입니다.

### 디자인 목표

**ZooKeeper 단순합니다.** ZooKeeper를 사용하면 분산 프로세스가 표준 파일 시스템과 유사하게 구성된 공유 계층적 네임스페이스를 통해 서로 조정할 수 있습니다. 이름 공간은 ZooKeeper 용어로 znodes라고하는 데이터 레지스터로 구성되며 파일과 디렉토리와 유사합니다. 저장용으로 설계된 일반적인 파일 시스템과 달리 ZooKeeper 데이터는 메모리에 보관되므로 ZooKeeper는 처리량이 높고 대기 시간이 짧습니다.

ZooKeeper 구현은 고성능, 고가용성, 엄격하게 명령 된 액세스에 대한 프리미엄을 부여합니다. ZooKeeper의 성능 측면은 대규모 분산 시스템에서 사용할 수 있음을 의미합니다. 안정성 측면에서는 단일 지점에서 오류가 발생하지 않도록 합니다. 엄격한 순서는 클라이언트에서 정교한 동기화 프리미티브를 구현할 수 있음을 의미합니다.

**ZooKeeper가 사본이 만들어 집니다.** ZooKeeper 자체가 분산된 프로세스와 마찬가지로 ZooKeeper 자체는 앙상블이라는 호스트 세트를 통해 사본이 만들어 집니다.

![ZooKeeper Service](https://zookeeper.apache.org/doc/r3.5.5/images/zkservice.jpg)

ZooKeeper 서비스를 구성하는 서버는 모두 서로에 대해 알고 있어야 합니다. 영구 저장소에 트랜잭션 로그 및 스냅샷과 함께 상태의 메모리 내 이미지를 유지 관리합니다. 대부분의 서버를 사용할 수있는 한 ZooKeeper 서비스를 사용할 수 있습니다.

클라이언트는 하나의 ZooKeeper 서버에 연결됩니다. 클라이언트는 요청을 보내고, 응답을 받고, 감시 이벤트를 수신하고, 하트 비트를 보내는 TCP 연결을 유지 관리합니다. 서버에 대한 TCP 연결이 끊어지면 클라이언트는 다른 서버에 연결합니다.

**ZooKeeper은 순서가 부여 됩니다.** ZooKeeper는 각 ZooKeeper 트랜잭션의 순서를 반영하는 숫자로 각 업데이트를 스탬프 처리합니다. 후속 연산은 순서를 사용하여 동기화 프리미티브와 같은 상위 수준의 추상화를 구현할 수 있습니다.

**ZooKeeper은 빠릅니다.** 특히 "읽기-주도" 작업 부하가 빠릅니다. ZooKeeper 응용 프로그램은 수천 대의 컴퓨터에서 실행되며 읽기가 쓰기보다 일반적으로 약 10 : 1의 비율에서 가장 잘 수행됩니다.

#### 데이터 모델 및 계층적 네임스페이스

ZooKeeper가 제공하는 이름 공간은 표준 파일 시스템과 비슷합니다. 이름은 슬래시 (/)로 구분된 경로 요소의 순서 입니다. ZooKeeper의 이름 공간에 있는 모든 노드는 경로로 식별됩니다.

**ZooKeeper의 계층 적 네임 스페이스**

![ZooKeeper's Hierarchical Namespace](https://zookeeper.apache.org/doc/r3.5.5/images/zknamespace.jpg)

표준 파일 시스템과 달리 ZooKeeper 네임스페이스의 각 노드는 자식뿐만 아니라 ZooKeeper 네임스페이스와 관련된 데이터를 가질 수 있습니다. 파일을 디렉토리로 사용할 수 있는 파일 시스템을 갖는 것과 같습니다. (ZooKeeper는 상태 정보, 구성, 위치 정보 등 좌표 데이터를 저장하도록 설계되었으므로 각 노드에 저장된 데이터는 일반적으로 바이트에서 킬로바이트 범위로 작습니다.) 우리는 znode라는 용어를 사용하여 우리가 ZooKeeper 데이터 노드에 대해 이야기하고 있습니다.

znode들은 캐시 유효성 검사 및 조정 된 업데이트를 허용하기 위해 데이터 변경, ACL 변경 및 타임 스탬프에 대한 버전 번호가 포함 된 통계 구조를 유지합니다. znode의 데이터가 변경 될 때마다 버전 번호가 증가합니다. 예를 들어 클라이언트가 데이터를 검색 할 때마다 데이터 버전도 수신됩니다.

네임 스페이스의 각 znode에 저장된 데이터는 원자적(atomic)으로 읽고 쓰여집니다. 읽기는 znode와 관련된 모든 데이터 바이트를 가져오고 쓰기는 모든 데이터를 대체합니다. 각 노드에는 누가 작업을 수행 할 수 있는지를 제한하는 액세스 제어 목록 (ACL)이 있습니다.

ZooKeeper는 ephemeral 노드라는 개념도 가지고 있습니다. 이 znode는 znode를 작성한 세션이 활성 상태 인 동안 존재합니다. 세션이 끝나면 z 노드가 삭제됩니다. ephemeral 노드는 [tbd]를 구현하고자 할 때 유용합니다.

#### 조건부 업데이트 및 감시(watch)

ZooKeeper는 감시 개념을 지원합니다. 클라이언트는 znode에서 watch를 설정할 수 있습니다. znode가 변경되면 watch가 트리거되고 제거됩니다. 감시 장치가 트리거 되면 클라이언트는 znode가 변경되었다는 패킷을 수신합니다. 클라이언트와 ZooKeeper 서버 중 하나의 연결이 끊어지면 클라이언트는 로컬 알림을 받게됩니다. 이것들은 [tbd]에 사용될 수 있습니다.

### 보증

ZooKeeper는 매우 빠르고 간단합니다. 그러나이 목표는 동기화와 같은 보다 복잡한 서비스를 구성하기위한 기초가 되기 때문에 일련의 보장을 제공합니다. 이것들은:

- 순차적 일관성 (Sequential Consistency) - 클라이언트의 업데이트는 전송 된 순서대로 적용됩니다.
- 원자성 (Atomicity) - 업데이트가 성공하거나 실패합니다. 부분적인 결과는 없습니다.
- 안정성 (Reliability) - 일단 업데이트가 적용되면 클라이언트가 업데이트를 덮어 쓸 때까지 그 시점부터 지속됩니다.
- 적시성 (Timeliness) - 시스템에 대한 고객의 관점은 특정 시간 범위 내에서 최신 상태로 유지됩니다.

이들에 대한 자세한 내용과 사용 방법은 [tbd]를 참조하십시오.

### 단순한 API

ZooKeeper의 설계 목표 중 하나는 매우 간단한 프로그래밍 인터페이스를 제공하는 것입니다. 결과적으로 다음 작업 만 지원합니다.

|              |                                         |
| ------------ | --------------------------------------- |
| create       | 트리의 한 위치에 노드를 생성합니다.     |
| delete       | 노드를 삭제합니다.                      |
| exists       | 노드가 특정 위치에 있는지 테스트합니다. |
| get data     | 노드에서 데이터를 읽습니다.             |
| set data     | 노드에 데이터 쓰기                      |
| get children | 노드의 자식 목록을 가져옵니다.          |
| sync         | 데이터가 전달 될 때까지 대기합니다.     |

이들에 대한 심층적인 토론과 더 높은 수준의 작업을 구현하는 데 사용할 수있는 방법에 대해서는 [tbd]를 참조하십시오.

### 구현

ZooKeeper 구성 요소는 ZooKeeper 서비스의 상위 구성 요소를 보여줍니다. 요청 처리기(request processor)를 제외하고 ZooKeeper 서비스를 구성하는 각 서버는 각 구성 요소의 자체 복사본을 복제합니다.

![ZooKeeper Components](https://zookeeper.apache.org/doc/r3.5.5/images/zkcomponents.jpg)

복제된 데이터베이스는 전체 데이터 트리가 포함 된 메모리 내 데이터베이스입니다. 업데이트는 복구를 위해 디스크에 기록되고 쓰기는 디스크에 직렬화 되어 메모리 내 데이터베이스에 적용됩니다.

모든 ZooKeeper 서버 서비스 클라이언트. 클라이언트는 정확히 하나의 서버에 연결하여 요청을 제출합니다. 읽기 요청은 각 서버 데이터베이스의 로컬 복제본에서 처리됩니다. 서비스 상태를 변경하는 요청, 쓰기 요청은 계약 프로토콜에 의해 처리됩니다.

계약 프로토콜의 일부로 클라이언트의 모든 쓰기 요청은 리더라고 하는 단일 서버로 전달됩니다. 추종자 (follower)라고 불리는 나머지 ZooKeeper 서버는 리더로부터 메시지 제안을 받고 메시지 전달에 동의합니다. 메시징 계층은 실패한 리더를 대체하고 추종자를 리더와 동기화합니다.

ZooKeeper는 맞춤 원자 메시지 프로토콜(custom atomic messaging protocol)을 사용합니다. 메시징 계층이 절대적인 수준이기 때문에 ZooKeeper는 로컬 복제본이 절대 갈라지지 않도록 보장 할 수 있습니다. 리더는 쓰기 요청을 받으면 쓰기가 적용될 때 시스템 상태를 계산하고이를이 새 상태를 캡처하는 트랜잭션으로 변환합니다.

### 사용

ZooKeeper 프로그래밍 인터페이스는 의도적으로 간단합니다. 그러나 동기화 프리미티브, 그룹 멤버쉽, 소유권 등과 같은 고차원 오퍼레이션을 구현할 수 있습니다. 일부 분산 어플리케이션은 백서 및 비디오 프리젠 테이션의 용도를 추가하는 데 사용했습니다. 자세한 내용은 [tdb]를 참조하십시오.

### 성능

ZooKeeper는 고성능으로 설계되었습니다. 그러나 그것입니까? ZooKeeper의 Yahoo! 팀 개발 팀 결과 연구 결과에 따르면 (읽기/쓰기 비율에 따라 ZooKeeper 처리량을 참조하십시오.) 쓰기가 모든 서버의 상태를 동기화하기 때문에 쓰기가 읽기보다 많은 응용 프로그램에서는 특히 높은 성능을 발휘합니다. (일반적으로 코디네이션 서비스에서는 아웃 넘버링 쓰기를 읽습니다.)

![ZooKeeper Throughput as the Read-Write Ratio Varies](https://zookeeper.apache.org/doc/r3.5.5/images/zkperfRW-3.2.jpg)



읽기 - 쓰기 비율에 따른 ZooKeeper 처리량은 듀얼 2Ghz Xeon 및 2 개의 SATA 15K RPM 드라이브가있는 서버에서 실행되는 ZooKeeper 릴리스 3.2의 처리량 그래프입니다. 하나의 드라이브가 전용 ZooKeeper 로그 장치로 사용되었습니다. 스냅샷은 OS 드라이브에 기록되었습니다. 쓰기 요청은 1K 회 기록이었으며 1K 회 읽기가 있었습니다. "서버"는 ZooKeeper 앙상블의 크기, 서비스를 구성하는 서버의 수를 나타냅니다. 약 30 개의 다른 서버가 클라이언트를 시뮬레이션하는 데 사용되었습니다. ZooKeeper 앙상블은 리더가 클라이언트의 연결을 허용하지 않도록 구성되었습니다.
**노트:**

- 버전 3.2에서는 r/w 성능이 이전 3.1 릴리스와 비교하여 ~ 2 배 향상되었습니다.

벤치 마크는 또한 신뢰할 수 있음을 나타냅니다. 오류 존재의 안정성은 배포가 다양한 오류에 어떻게 대응하는지 보여줍니다. 그림에 표시된 이벤트는 다음과 같습니다.

- Follower의 실패와 회복
- 다른 Follower의 실패와 회복
- Leader의 실패
- 두개의 Folloewr의 실패와 회복
- 다른 Leader의 실패

### 신뢰성(Reliability)

실패가 주입됨에 따라 시간이 지남에 따라 시스템의 동작을 표시하기 위해 우리는 7 대의 컴퓨터로 구성된 ZooKeeper 서비스를 실행했습니다. 이전과 동일한 포화 벤치마크(saturation benchmark)를 실행했지만 이번에는 쓰기 비율을 30 %로 일정하게 유지했습니다. 이는 예상되는 작업 부하의 보수(conservative) 비율입니다.

![Reliability in the Presence of Errors](https://zookeeper.apache.org/doc/r3.5.5/images/zkperfreliability.jpg)

이 그래프에서 몇 가지 중요한 관찰 사항이 있습니다. 첫째, Follwer가 실패하고 빠르게 복구되면 ZooKeeper는 실패에도 불구하고 높은 처리량을 유지할 수 있습니다. 그러나 더 중요한 것은 Leader election 알고리즘을 통해 시스템이 처리량이 크게 떨어지는 것을 방지 할 수있을만큼 빠르게 복구 할 수 있다는 것입니다. 우리의 관찰에서, ZooKeeper는 새로운 Leader를 선출하는데 200ms도 안 걸립니다. 셋째, Follower가 복구되면 ZooKeeper는 요청 처리를 시작하면 다시 처리량을 높일 수 있습니다.

### Zookeeper 프로젝트

ZooKeeper는 많은 산업 분야에서 성공적으로 사용 되었습니다. Yahoo!에서 사용됩니다. Yahoo!에 대한 조정 및 오류 복구 서비스 Message Broker는 복제 및 데이터 전달을 위해 수천 가지 주제를 관리하는 확장성이 뛰어난 게시 - 가입 시스템입니다. Yahoo!에 대한 가져 오기 서비스에서 사용됩니다. 크롤러는 장애 복구를 관리합니다. 다수의 야후! 광고 시스템은 ZooKeeper를 사용하여 안정적인 서비스를 구현합니다.

모든 사용자와 개발자는 커뮤니티에 가입하고 전문 지식을 제공하는 것이 좋습니다. 자세한 내용은 [Apache에서 Zookeeper 프로젝트](http://zookeeper.apache.org/)를 참조하십시오.

## Operation



| Operation     | ACL      | 설명                                                         |
| ------------- | -------- | ------------------------------------------------------------ |
| `ceate`       | `Create` | znode를 생성하며 반드시 상위 znode가 존재해야 합니다.        |
| `delete`      | `Delete` | znode를 삭제합니다. 반드시 하위 znode가 없어야 합니다.       |
| `exists`      | `Read`   | znode가 존재하는지 테스트 하고, 있으면 메타데이터를 반환합니다. |
| `getACL`      | `Admin`  | znode의 ACL 권한을 읽습니다.                                 |
| `setACL`      | `Admin`  | znode의 ACL 권한을 설정합니다.                               |
| `getChildren` | `Read`   | 자식 znode 목록을 얻습니다.                                  |
| `getData`     | `Read`   | znode로 관계된 데이터를 읽습니다.                            |
| `setData`     | `Write`  | znode로 관계된 데이터를 설정합니다                           |
| `sync`        |          | 클라이언트의 znode의 뷰를 동기화 합니다.                     |



## Watcher

Operation시 Watcher를 지정하여, znode가 변경 되면 지정한 Watcher에 이벤트가 통지 됩니다. 이때 Watcher는 일시적이므로 매 Operation이 Watcher를 지정해야 이벤트가 통지됩니다.



Watcher 그림.



이벤트의 종류는 아래와 같습니다.

| Operation     | create znode | create child        | delete znode | delete child        | setData         |
| ------------- | ------------ | ------------------- | ------------ | ------------------- | --------------- |
| `exists`      | NodeCreated  |                     | NodeDeleted  |                     | NodeDataChanged |
| `getData`     |              |                     | NodeDeleted  |                     | NodeDataChanged |
| `getChildren` |              | NodeChildrenChanged | NodeDeleted  | NodeChildrenChanged | exists          |

