원문: [Getting started: Configuration](http://cassandra.apache.org/doc/latest/getting_started/configuring.html)

# Getting started: Configuration

싱글 노드의 Canssandra를 구동하려면 `./conf/cassandra.yaml`으로 충분 하며, 변경할 필요가 없습니다.

그러나, 클러스터의 노드에 배치하거나, 다른 장비의 클라이언트가 접속하려면 약간의 파라미터를 변경해야 합니다.

카산드라 구성 파일은 tarball의 `conf` 디렉토리에서 찾을 수 있습니다. 패키지의 경우 구성 파일은 `/etc/cassandra`에 있습니다.

### 주 런타임 속성 (Main runtime properties)

Cassandra의 대부분의 구성은 yaml 등록 정보를 통해 이루어지며 `cassandra.yaml`에서 설정할 수 있습니다. 최소한 다음 속성을 설정하는 것을 고려해야 합니다.

| 속성                    | 설명                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `cluster_name`          | 클러스터의 이름.                                             |
| `seeds`                 | 클러스터 시드의 쉼표로 구분 된 IP 주소 목록.                 |
| `storage_port`          | 이것을 반드시 변경할 필요는 없지만 이 포트를 차단하는 방화벽이 없는지 확인하십시오. |
| `listen_address`        | 노드의 IP 주소는 다른 노드가 이 노드와 통신 할 수 있도록 하므로 변경하는 것이 중요합니다. 또는 Cassandra에게 사용할 인터페이스와 사용할 주소를 연속적으로 알려주기 위해`listen_interface`를 설정할 수 있습니다. 둘 중 하나만 설정하십시오. |
| `native_transport_port` | `storage_port`는 클라이언트가 이 포트에서 Cassandra와 통신 할 때이 포트가 방화벽에 의해 차단되어 있지 않은지 확인하십시오. |

### 디렉토리 위치 변경

다음 yaml 속성은 디렉토리의 위치를 제어합니다:

| 속성                     | 설명                                    |
| ------------------------ | --------------------------------------- |
| `data_file_directories`  | 데이터 파일이있는 하나 이상의 디렉토리. |
| `commitlog_directory`    | commitlog 파일이 있는 디렉토리.         |
| `saved_caches_directory` | 저장된 캐시가 있는 디렉토리.            |
| `hints_directory`        | hint가 있는 디렉토리                    |

성능상의 이유로 디스크가 여러 개인 경우 commitlog 및 데이터 파일을 다른 디스크에 저장하는 것이 좋습니다.

## 환경 변수

힙 크기와 같은 JVM 레벨 설정은 `cassandra-env.sh`에서 설정할 수 있습니다. JVM 커멘드 행 인수를`JVM_OPTS` 환경 변수에 추가 할 수 있습니다. Cassandra가 시작되면 이러한 인수가 JVM으로 전달됩니다.

## 로깅

사용중인 로거는 logback입니다. `logback.xml`을 편집하여 로깅 속성을 변경할 수 있습니다. 기본적으로 INFO 레벨은`system.log` 파일에, 디버그 레벨은 `debug.log` 파일에 기록됩니다. 포그라운드에서 실행 중일 때는 INFO 레벨에서 콘솔에 기록됩니다.

## 참조:

- [Configuring Cassandra](http://cassandra.apache.org/doc/latest/getting_started/configuring.html)

  
