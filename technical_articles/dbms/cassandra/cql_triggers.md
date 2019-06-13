http://cassandra.apache.org/doc/latest/cql/triggers.html

# Triggers

Triggers are identified by a name defined by:

```
trigger_name ::=  identifier
```

## CREATE TRIGGER

Creating a new trigger uses the `CREATE TRIGGER` statement:

```
create_trigger_statement ::=  CREATE TRIGGER [ IF NOT EXISTS ] trigger_name
                                  ON table_name
                                  USING string
```

For instance:

```CQL
CREATE TRIGGER myTrigger ON myTable USING 'org.apache.cassandra.triggers.InvertedIndex';
```

트리거를 구성하는 실제 논리는 Java (JVM) 언어로 작성 될 수 있으며 데이터베이스 외부에 존재합니다. 트리거 코드를 Cassandra 설치 디렉토리의 `lib/triggers` 서브 디렉토리에 넣고, 클러스터 시작 중에 로드하고, 클러스터에 참여하는 모든 노드에 트리거 코드를 위치 시킵니다. 요청 된 DML 문이 발생하기 전에 테이블에 정의된 트리거가 실행되어 트랜잭션의 원자성을 보장합니다.

## DROP TRIGGER

Dropping a trigger uses the `DROP TRIGGER` statement:

```
drop_trigger_statement ::=  DROP TRIGGER [ IF EXISTS ] trigger_name ON table_name
```

For instance:

```CQL
DROP TRIGGER myTrigger ON myTable;
```

