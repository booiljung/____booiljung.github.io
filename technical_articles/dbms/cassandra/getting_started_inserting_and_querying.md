원문: [Inserting and querying](http://cassandra.apache.org/doc/latest/getting_started/querying.html)

# Inserting and querying

Cassandra API는 Cassandra Query Language 인 [CQL](http://cassandra.apache.org/doc/latest/cql/index.html#cql)입니다. CQL을 사용하려면 다음과 같이 클러스터에 연결해야 합니다.

- cqlsh를 사용하거나,
- 또는 Cassandra 클라이언트 드라이버를 통해서 합니다.

## CQLSH

cqlsh는 CQL을 통해 Cassandra와 상호 작용하기 위한 명령 행 셸 입니다. 모든 Cassandra 패키지와 함께 제공되며, cassandra 실행 파일과 함께 `bin/` 디렉토리에서 찾을 수 있습니다. 명령행에 지정된 단일 노드에 연결합니다. 예 :

```
$ bin/cqlsh localhost
Connected to Test Cluster at localhost:9042.
[cqlsh 5.0.1 | Cassandra 3.8 | CQL spec 3.4.2 | Native protocol v4]
Use HELP for help.
cqlsh> SELECT cluster_name, listen_address FROM system.local;

 cluster_name | listen_address
--------------+----------------
 Test Cluster |      127.0.0.1

(1 rows)
cqlsh>
```

전체 문서는 [cqlsh section](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)을 참조하십시오.

## 클라이언트 드라이버

많은 클라이언트 드라이버가 커뮤니티에 의해 제공되며 알려진 드라이버 목록은 [다음 섹션](http://cassandra.apache.org/doc/latest/drivers.html#client-drivers)에서 제공됩니다. 드라이버 사용 방법에 대한 자세한 내용은 각 드라이버의 설명서를 참조하십시오.

참조:

- [Inserting and querying](http://cassandra.apache.org/doc/latest/getting_started/querying.html)
- [Client drivers](http://cassandra.apache.org/doc/latest/getting_started/drivers.html)

