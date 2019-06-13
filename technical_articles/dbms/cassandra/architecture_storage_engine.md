http://cassandra.apache.org/doc/latest/architecture/storage_engine.html

# Storage Engine

## CommitLog

커밋 로그는 Cassandra 노드에 로컬인 모든 변이(mutation)에 대한 추가 로그입니다. Cassandra에 쓰여진 모든 데이터는 memtable에 쓰여지기 전에 먼저 커밋 로그에 기록됩니다. 예기치 않은 시스템 종료시 내구성을 제공합니다. 시작시 커밋 로그의 모든 변이가 memtables에 적용됩니다.

모든 변이는 커밋 로그 세그먼트에 저장함으로써 최적화되어 디스크에 쓰는 데 필요한 탐색 횟수를 줄입니다. Commitlog 세그먼트는 "commitlog_segment_size_in_mb"옵션으로 제한되며 크기에 도달하면 새 커밋 로그 세그먼트가 만들어집니다. Commitlog 세그먼트는 모든 데이터가 SSTable로 플러시되면 아카이브, 삭제 또는 재활용 될 수 있습니다. Cassandra가 특정 시점보다 오래된 데이터를 SSTables에 기록하면 Commitlog 세그먼트가 잘립니다. Cassandra를 멈추기 전에 "nodetool drain"을 실행하면 memtables의 모든 내용이 SSTables에 쓰여지고 시작할 때 commitlog와 동기화 할 필요가 제거됩니다.

- `commitlog_segment_size_in_mb`:  기본 크기는 32이지만, 거의 항상 괜찮습니다. commitlog 세그먼트 (commitlog_archiving.properties 참조)를 아카이브하는 경우에는보다 세분화 된 아카이브를 원할 것입니다. 8 또는 16MB가 적당합니다. 최대 변이 크기는 또한 cassandra.yaml의 max_mutation_size_in_kb 설정을 통해 구성 할 수 있습니다. 기본값은 commitlog_segment_size_in_mb * 1024의 절반입니다.

NOTE: `max_mutation_size_in_kb`가 명시적으로 설정되면`commitlog_segment_size_in_mb`는`max_mutation_size_in_kb` / 1024의 2 배 이상으로 설정되어야 합니다

*Default Value:* 32

커밋 로그는 Cassandra 노드에 로컬인 모든 변이에 대한 추가 로그입니다. Cassandra에 쓰여진 모든 데이터는 memtable에 쓰여지기 전에 먼저 커밋 로그에 기록됩니다. 예기치 않은 시스템 종료시 내구성을 제공합니다. 시작할 때, 커밋 로그의 모든 변이가 적용됩니다.

- `commitlog_sync`: `periodic` 또는 `batch`일 수 있습니다.

  - `batch`:  배치 모드에서 Cassandra는 커밋 로그가 디스크에 fsynced 될 때까지 기록하지 않습니다. fsync 간의 `commitlog_sync_batch_window_in_ms`밀리 초를 기다립니다. writer 스레드는 대기 중에 추가 작업을 수행 할 수 없으므로 이 창을 짧게 유지해야 합니다. 같은 이유로 `concurrent_writes`를 늘려야 할 수도 있습니다.

    - `commitlog_sync_batch_window_in_ms`: `batch` fsyncs 사이의 대기 시간

    *Default Value:* 2

  - `periodic`:  `periodic` 모드에서, 쓰기는 즉시 응답됩니다. 그리고 CommitLog는 매 `commitlog_sync_period_in_ms` 마다 단순하게 동기화 됩니다.

    - `commitlog_sync_period_in_ms`: `periodic` fsyncs 상이의 대기 시간

    *Default Value:* 10000

*Default Value:* batch

NOTE: 예상치 못한 종료가 발생하여, 동기화가 지연되면 Cassandra가 동기화 기간 이상을 잃을 수 있습니다. `배치`모드를 사용하는 경우 커밋 로그를 별도의 전용 장치에 저장하는 것이 좋습니다.

- `commitlog_directory`:  이 옵션은 기본적으로 주석으로 처리됩니다. 마그네틱 HDD에서 실행할 때, 이는 데이터 디렉토리와는 별도의 스핀들이어야 합니다. 설정되지 않은 경우 기본 디렉토리는 `$CASSANDRA_HOME/data/commitlog`입니다.

*Default Value:* /var/lib/cassandra/commitlog

- `commitlog_compression`:  커밋 로그에 적용 할 압축. 생략하면 커밋 로그는 압축되지 않은 상태로 기록됩니다. LZ4, Snappy 및 Deflate 압축기가 지원됩니다.

(Default Value: (complex option):

```
#   - class_name: LZ4Compressor
#     parameters:
#         -
```

- `commitlog_total_space_in_mb`: 디스크의 커밋 로그에 사용할 총 공간입니다.

공간이 이 값보다 커지면 Cassandra는 가장 오래된 세그먼트의 모든 dirty CF를 플러시하고 제거합니다. 따라서 전체 커밋 로그 공간이 적으면 덜 활동적인 columnfamiliy에서 더 많은 플러시 활동을 하는 경향이 있습니다.

기본값은 8192보다 작고 commitlog 볼륨의 전체 공간의 1/4입니다.

*Default Value:* 8192

## Memtables

Memtable은 Cassandra 버퍼 쓰기를 하는 메모리 내 구조입니다. 일반적으로 테이블 당 활성 memtable이 하나 있습니다. 결국 memtable은 디스크에 플러시되고 불변의 [SSTables](http://cassandra.apache.org/doc/latest/architecture/storage_engine.html#sstables)가 됩니다. 여러 가지 방법으로 트리거 할 수 있습니다.

- memtables의 메모리 사용량이 구성된 임계 값을 초과합니다. (see `memtable_cleanup_threshold`)
- [CommitLog](http://cassandra.apache.org/doc/latest/architecture/storage_engine.html#commit-log)는 최대 크기에 접근하고 memtable flush를 강제 실행하여 commitlog 세그먼트를 해제합니다

Memtable은 `memtable_allocation_type`에 의존하여 온 힙이나 부분적으로 힙을 저장할 수 있습니다.

## SSTables

SSTables는 Cassandra가 디스크의 데이터를 지속시키기 위해 사용하는 불변의 데이터 파일입니다.

SSTable은 [Memtables](http://cassandra.apache.org/doc/latest/architecture/storage_engine.html#memtables)에서 디스크로 플러시되거나 다른 노드에서 스트리밍되므로 Cassandra는 여러 개의 SSTable을 하나로 결합하는 압축을 트리거합니다. 새 SSTable이 작성되면 이전 SSTable을 제거 할 수 있습니다.

각 SSTable은 별도의 파일에 저장된 여러 구성 요소로 구성됩니다:

- `Data.db`

  실제 데이터, 즉 행의 내용

- `Index.db`

  파티션 키에서 `Data.db` 파일의 위치에 대한 색인 입니다. 넓은 파티션의 경우 파티션의 행에 대한 인덱스도 포함될 수 있습니다.

- `Summary.db`

  `Index.db` 파일의 128 번째 항목마다 샘플링 (기본값).

- `Filter.db`

  SSTable의 파티션 키 블룸 필터.

- `CompressionInfo.db`

  `Data.db` 파일의 압축 청크의 오프셋과 길이에 관한 메타 데이터.

- `Statistics.db`

  타임 스탬프, 삭제 표시, 클러스터링 키, 압축, 복구, 압축, TTL 등에 대한 정보를 포함하여 SSTable에 대한 메타 데이터를 저장합니다.

- `Digest.crc32`

  `Data.db` 파일의 CRC-32 다이제스트.

- `TOC.txt`

  SSTable에 대한 구성 요소 파일의 일반 텍스트 목록입니다.

`Data.db` 파일에서 행은 파티션별로 구성됩니다. 이러한 파티션은 토큰 순서 (즉, 기본 파티션 도구 `Murmur3Partition`이 사용될 때 파티션 키의 해시)로 정렬됩니다. 파티션 내에서 행은 클러스터링 키순으로 저장됩니다.

SSTables는 블록 기반 압축을 사용하여 선택적으로 압축 할 수 있습니다.

## SSTable Versions

This section was created using the following [gist](https://gist.github.com/shyamsalimkumar/49a61e5bc6f403d20c55) which utilized this original [source](http://www.bajb.net/2013/03/cassandra-sstable-format-version-numbers/).

The version numbers, to date are:

### Version 0

- b (0.7.0): added version to sstable filenames
- c (0.7.0): bloom filter component computes hashes over raw key bytes instead of strings
- d (0.7.0): row size in data component becomes a long instead of int
- e (0.7.0): stores undecorated keys in data and index components
- f (0.7.0): switched bloom filter implementations in data component
- g (0.8): tracks flushed-at context in metadata component

### Version 1

- h (1.0): tracks max client timestamp in metadata component
- hb (1.0.3): records compression ration in metadata component
- hc (1.0.4): records partitioner in metadata component
- hd (1.0.10): includes row tombstones in maxtimestamp
- he (1.1.3): includes ancestors generation in metadata component
- hf (1.1.6): marker that replay position corresponds to 1.1.5+ millis-based id (see CASSANDRA-4782)
- ia (1.2.0):
  - column indexes are promoted to the index file
  - records estimated histogram of deletion times in tombstones
  - bloom filter (keys and columns) upgraded to Murmur3
- ib (1.2.1): tracks min client timestamp in metadata component
- ic (1.2.5): omits per-row bloom filter of column names

### Version 2

- ja (2.0.0):
  - super columns are serialized as composites (note that there is no  real format change, this is mostly a marker to know if we should expect  super columns or not. We do need a major version bump however, because  we should not allow streaming of super columns into this new format)
  - tracks max local deletiontime in sstable metadata
  - records bloom_filter_fp_chance in metadata component
  - remove data size and column count from data file (CASSANDRA-4180)
  - tracks max/min column values (according to comparator)
- jb (2.0.1):
  - switch from crc32 to adler32 for compression checksums
  - checksum the compressed data
- ka (2.1.0):
  - new Statistics.db file format
  - index summaries can be downsampled and the sampling level is persisted
  - switch uncompressed checksums to adler32
  - tracks presense of legacy (local and remote) counter shards
- la (2.2.0): new file name format
- lb (2.2.7): commit log lower bound included

### Version 3

- ma (3.0.0):
  - swap bf hash order
  - store rows natively
- mb (3.0.7, 3.7): commit log lower bound included
- mc (3.0.8, 3.9): commit log intervals included

### Example Code

The following example is useful for finding all sstables that do not match the “ib” SSTable version

```
find /var/lib/cassandra/data/ -type f | grep -v -- -ib- | grep -v "/snapshots"
```