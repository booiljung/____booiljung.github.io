http://cassandra.apache.org/doc/latest/architecture/dynamo.html

# Architecture: Dynamo

## Replication

키스페이스의 복제 전략은 주어진 토큰 범위의 복제본을 결정합니다. 두 가지 주요 복제 전략은 [SimpleStrategy](http://cassandra.apache.org/doc/latest/architecture/dynamo.html#simple-strategy) 및 [NetworkTopologyStrategy](http://cassandra.apache.org/doc /latest/architecture/dynamo.html#network-topology-strategy)입니다.

### SimpleStrategy

SimpleStrategy는 단일 정수`replication_factor`가 정의 되도록 합니다. 이것은 각 행의 사본을 포함 해야 하는 노드의 수를 결정합니다. 예를 들어, `replication_factor`가 3이면, 세 개의 다른 노드는 각 행의 사본을 저장해야 합니다.

SimpleStrategy는 구성된 모든 데이터 센터 또는 랙을 무시하고 모든 노드를 동일하게 취급합니다. 토큰 범위의 복제본을 결정하기 위해 Cassandra는 관심 있는 토큰 범위부터 링의 토큰을 반복합니다. 각 토큰에 대해 소유 노드가 복제 세트에 추가 되었는지 여부를 점검하고, 소유하지 않은 경우 세트에 추가 됩니다. 이 과정은 `replication_factor` 별개의 노드가 복제본 세트에 추가 될 때까지 계속됩니다.

### NetworkTopologyStrategy

NetworkTopologyStrategy를 사용하면 클러스터의 각 데이터 센터에 복제 펙터를 지정할 수 있습니다. 클러스터가 단일 데이터 센터만 사용하는 경우 NetworkTopologyStrategy를 SimpleStrategy보다 우선적으로 사용하여 클러스터에 새로운 실제 또는 가상 데이터 센터를 나중에 쉽게 추가 할 수 있습니다.

DC별로 복제 요소를 지정할 수 있을뿐만 아니라 NetworkTopologyStrategy는 다른 랙의 데이터 센터에서 복제본을 선택하려고 시도합니다. 랙 수가 DC의 복제 인수보다 크거나 같으면 각 복제본이 다른 랙에서 선택됩니다. 그렇지 않으면 각 랙에 최소한 하나의 복제본이 보관되지만 일부 랙에는 둘 이상의 복제본이 보관 될 수 있습니다.

이 랙 인식 동작에는 잠재적으로 [surprising implications](https://issues.apache.org/jira/browse/CASSANDRA-3810)이 있습니다. 예를 들어 각 랙에 짝수 개의 노드가 없으면 가장 작은 랙의 데이터 로드가 훨씬 커질 수 있습니다. 마찬가지로 단일 노드가 새 랙으로 부트 스트랩되는 경우 전체 링의 복제본으로 간주됩니다. 이러한 이유 때문에 많은 운영자가 단일 "랙"에 모든 노드를 구성하려고합니다.

### Transient Replication

일시적 복제를 사용하면 점진적으로 복구되지 않은 데이터만 복제하도록 복제본의 하위 집합을 구성 할 수 있습니다. 이를 통해 데이터 중복성을 가용성에서 분리 할 수 있습니다. 예를 들어, rf3에 복제 된 키스페이스가 있고 2 개의 일시적인 복제본을 사용하여 rf5로 변경하면 실패한 복제본 한 개를 허용 할 수 없게 되어 저장 용량 사용량이 증가하지 않고 두 개를 허용 할 수 있게 됩니다. 이것은 3 개의 노드가 주어진 토큰 범위에 대한 모든 데이터를 복제하고 다른 노드 2는 증분식으로 복구되지 않은 데이터만 복제하기 때문입니다.

일시적인 복제를 사용하려면 먼저 `cassandra.yaml`에서 활성화 시켜야 합니다. 활성화되면 `SimpleStrategy`와 `NetworkTopologyStrategy`를 모두 일시적으로 데이터를 복제하도록 구성 할 수 있습니다. 복제 인수를`<total_replicas>/<transient_replicas`로 지정하여 구성합니다. SimpleStrategy 및 NetworkTopologyStrategy는 일시적 복제 구성을 지원합니다.

Transiently replicated keyspaces는 `read_repair`가 NONE으로 설정된 테이블을 지원하며 monotonic 읽기는 현재 지원되지 않습니다. 또한 4.0에서 LWT, 로그 된 배치 처리 및 카운터를 사용할 수 없습니다. Transiently 복제 된 키스페이스를 가진 구체화 된 뷰를 결코 사용할 수 없으며 아마 2i를 사용할 수 없을 것입니다.

일시적 복제는 프로덕션 용도로 사용할 준비가 되지 않은 실험적 기능입니다. Cassandra의 숙련 된 사용자는 특정 응용 프로그램의 배포를 완벽하게 검증 할 수 있습니다. 즉, 모든 작업을 쿼리, 데이터, 구성, 운영 관행 및 가용성 요구 사항을 읽고, 쓰고, 제거하고, 제거하고, 다시 작성하고, 복구하고 바꿀 수 있는지 확인하는 것을 의미합니다.

4.next는 LWT, 로깅 된 배치 처리 및 카운터뿐만 아니라 일시적인 복제로 monotonic 읽기를 지원할 것으로 예상됩니다.

## Tunable Consistency

Cassandra는 일관성 레벨(Consistency Levels)을 통해 일관성과 가용성 간의 작업 당 절충안을 지원합니다. 기본적으로 작업의 일관성 수준은 작업을 성공으로 간주하기 위해 코디네이터에 응답 해야 하는 복제본 수를 지정합니다.

다음 일관성 레벨을 사용할 수 있습니다.

- `ONE`

  하나의 복제본 만 응답해야합니다.

- `TWO`

  두 개의 복제본이 응답해야합니다.

- `THREE`

  3 개의 복제본이 응답해야합니다.

- `QUORUM`

  대다수 (n / 2 + 1)의 복제본이 응답해야합니다.

- `ALL`

  모든 복제본이 응답해야합니다.

- `LOCAL_QUORUM`

  로컬 데이터 센터의 복제본 (조정자가 속한 데이터 센터 중 대다수)이 응답해야합니다.

- `EACH_QUORUM`

  각 데이터 센터의 대부분의 복제본이 응답해야합니다.

- `LOCAL_ONE`

  하나의 복제본 만 응답해야합니다. 다중 데이터 센터 클러스터에서는 읽기 요청이 원격 데이터 센터의 복제본으로 보내지지 않습니다.

- `ANY`

  단일 복제본이 응답하거나 코디네이터가 힌트를 저장할 수 있습니다. 힌트가 저장되면 코디네이터는 나중에 힌트를 재생하여 복제본에 전달합니다. 이 일관성 레벨은 쓰기 조작에만 허용됩니다.

쓰기 작업은 일관성 수준에 관계 없이 항상 모든 복제본으로 전송됩니다. 일관성 레벨은 클라이언트에 응답하기 전에 코디네이터가 대기하는 응답 수를 단순히 제어합니다.

읽기 조작의 경우, 코디네이터는 예외적으로 일관성 레벨을 충족시킬만큼 충분한 복제본에 대해 읽기 명령을 발행합니다. 위험한 재시도는 다른 복제본이 지정된 시간 창 내에서 응답하지 않은 경우 추가 복제본에 중복 읽기 요청을 발행 할 수 있습니다.

### Picking Consistency Levels

겹쳐질만큼 높은 읽기 및 쓰기 일관성 수준을 선택하면 "강한"일관성이 발생합니다. 이는 일반적으로 `W + R > RF`로 표현되며, `W`는 쓰기 일관성 레벨이고 `R`은 읽기 일관성 레벨이며 `RF`는 복제 요소입니다. 예를 들어, `RF = 3`이라면, `QUORUM`요청은 3 개의 복제본 중 적어도 2 개의 응답을 필요로합니다. 쓰기와 읽기에 모두 `QUORUM`을 사용하면 최소한 하나의 복제본이 쓰기 요청과 읽기 요청 모두에 참여할 수 있으며, 따라서 최신 쓰기가 읽히게 됩니다. 다중 데이터 센터 환경에서`LOCAL_QUORUM`을 사용하면 더 약하지만 유용한 보증을 제공 할 수 있습니다: 동일한 데이터 센터 내에서 최신 쓰기를 볼 수 있습니다.

이러한 유형의 강한 일관성이 필요하지 않은 경우 처리량, 대기 시간 및 가용성을 향상시키기 위해 `ONE`과 같은 낮은 일관성 수준을 사용할 수 있습니다.
