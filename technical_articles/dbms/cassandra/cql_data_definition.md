# Data definition of Cassandra 

CQL은 *테이블*에 데이터를 저장하며 스키마는 테이블에 있는 데이터의 레이아웃을 정의하며 테이블은 *키스페이스*에 그룹화됩니다. 키스페이스는 포함 된 모든 테이블에 적용되는 많은 옵션을 정의합니다. 가장 중요한 옵션은 키스페이스에서 사용되는 복제 전략입니다. 일반적으로 하나의 키스페이스을 *애플리케이션*으로 사용하는 것이 좋습니다. 따라서 다수의 클러스터는 하나의 키스페이스만 정의 할 수 있습니다.

이 절에서는 해당 키스페이스 및 테이블을 작성, 수정 및 제거하는 데 사용되는 명령문에 대해 설명합니다.

## Common definitions

키스페이스 및 테이블의 이름은, 다음의 문법에 의해 정의됩니다.

```
keyspace_name ::=  name
table_name    ::=  [ keyspace_name '.' ] name
name          ::=  unquoted_name | quoted_name
unquoted_name ::=  re('[a-zA-Z_0-9]{1, 48}')
quoted_name   ::=  '"' unquoted_name '"'
```

키스페이스과 테이블 이름은 영숫자 문자로만 구성되어야 하며 비어서는 안되며 크기는 48 자로 제한됩니다 (이 제한은 주로 파일 이름 (키스페이스과 테이블 이름을 포함 할 수 있음)이 특정 파일의 제한을 초과하지 않도록 하기 위해 존재합니다 시스템). 기본적으로 키스페이스과 테이블 이름은 대소 문자를 구별하지 않고 (`myTable`은`mytable`과 같습니다) 대문자와 소문자를 구분할 수 있습니다 (``myTable``은`mytable`과 다릅니다).

또한 테이블은 항상 키스페이스의 일부이며 테이블 이름은 해당 키스페이스의 일부로 완전히 규정 될 수 있습니다. is가 정규화되지 않은 경우 테이블은 *current* 키스페이스에 있다고 가정합니다 ([USE statement](http://cassandra.apache.org/doc/latest/cql/ddl.html#use-statement 참조) )).

또한 열의 유효한 이름은 다음과 같이 간단히 정의됩니다.

```
column_name ::=  identifier
```

또한 다음 절에서 사용할 명령문 옵션의 개념을 정의합니다.

```
options ::=  option ( AND option )*
option  ::=  identifier '=' ( identifier | constant | map_literal )
```

## CREATE KEYSPACE

키스페이스는 `CREATE KEYSPACE` 문을 사용하여 생성됩니다 :

```CQL
create_keyspace_statement ::=  CREATE KEYSPACE [ IF NOT EXISTS ] keyspace_name WITH options
```

예를들어:

```CQL
CREATE KEYSPACE excelsior
    WITH replication = {'class': 'SimpleStrategy', 'replication_factor' : 3};

CREATE KEYSPACE excalibur
    WITH replication = {'class': 'NetworkTopologyStrategy', 'DC1' : 1, 'DC2' : 3}
    AND durable_writes = false;
```

`IF NOT EXIST` 옵션을 사용하지 않고 이미 존재하는 키스페이스를 생성하려고 시도하면 오류가 반환됩니다. 키스페이스가 이미 존재하면 명령문은 no-op가 됩니다.

지원되는`options`는 다음과 같습니다 :

| name             | kind     | mandatory | default | description                                                  |
| ---------------- | -------- | --------- | ------- | ------------------------------------------------------------ |
| `replication`    | *map*    | yes       |         | 키스페이스에 사용할 복제 전략 및 옵션 (아래 참조).           |
| `durable_writes` | *simple* | no        | true    | 이 키스페이스의 업데이트에 커밋 로그를 사용할지 여부 (사용자의 책임하에 이 옵션을 비활성화하십시오!) |

`replication` 속성은 필수이고 최소한 사용할 복제 전략 클래스를 정의하는``class`` 하위 옵션을 포함해야합니다. 나머지 하위 옵션은 사용되는 복제 전략에 따라 다릅니다. 기본적으로 카산드라는 다음과 같은 '클래스'를 지원합니다.

### `SimpleStrategy`

데이터가 전체 클러스터에 분산되도록 복제 요소를 정의하는 간단한 전략입니다. 이것은 일반적으로 데이터 센터 레이아웃을 존중하지 않으며 크게 달라지는 쿼리 대기 시간을 초래할 수 있기 때문에 일반적으로 프러덕션을 위한 현명한 선택이 아닙니다. 프로덕션 준비 전략은`NetworkTopologyStrategy`를 참조하십시오. `SimpleStrategy`는 하나의 필수 인수를 지원합니다 :

| sub-option             | type | since | description              |
| ---------------------- | ---- | ----- | ------------------------ |
| `'replication_factor'` | int  | all   | 범위 당 저장할 복제본 수 |

### `NetworkTopologyStrategy`

각 데이터 센터마다 복제 요소를 독립적으로 설정할 수있는 프로덕션 준비 복제 전략입니다. 나머지 하위 옵션은 키 - 값 쌍입니다. 여기서 키는 데이터 센터 이름이고 값은 관련된 복제 요소입니다. 옵션 :

| sub-option             | type | since | description                                                  |
| ---------------------- | ---- | ----- | ------------------------------------------------------------ |
| `'<datacenter>'`       | int  | all   | 제공된 데이터 센터에서 범위 당 저장할 복제본 수입니다.       |
| `'replication_factor'` | int  | 4.0   | 특별히 제공되지 않은 경우 데이터 센터 당 기본값으로 사용할 복제본 수입니다. 항상 기존 정의 또는 명시 적 데이터 센터 설정을 사용합니다. 예를 들어 데이터 센터 당 복제본을 세 개 만들려면 값을 3으로 지정합니다. |

키 공간을 변경하고`replication_factor` 를 제공 할 때, 자동 확장은 안전을 위해 *새로운 데이터 센터를 추가* 할 뿐이며, 기존 데이터 센터를 변경하지 않거나 더 이상 클러스터에 없더라도 제거하지 않습니다. 여전히`replication_factor`를 제공하면서 데이터 센터를 제거하려면 복제본을 0으로 만들고자하는 데이터 센터를 명시적으로 제로로 만듭니다.

'DC1'과 'DC2'라는 두 데이터 센터가있는 데이터 센터 자동 확장의 예 :

```CQL
CREATE KEYSPACE excalibur
    WITH replication = {'class': 'NetworkTopologyStrategy', 'replication_factor' : 3}

DESCRIBE KEYSPACE excalibur
    CREATE KEYSPACE excalibur WITH replication = {'class': 'NetworkTopologyStrategy', 'DC1': '3', 'DC2': '3'} AND durable_writes = true;
```

데이터 센터 자동 확장 및 재정의 예제 :

```CQL
CREATE KEYSPACE excalibur
    WITH replication = {'class': 'NetworkTopologyStrategy', 'replication_factor' : 3, 'DC2': 2}

DESCRIBE KEYSPACE excalibur
    CREATE KEYSPACE excalibur WITH replication = {'class': 'NetworkTopologyStrategy', 'DC1': '3', 'DC2': '2'} AND durable_writes = true;
```

`replication_factor`를 사용하는 동안 데이터 센터를 제외시키는 예 :

```CQL
CREATE KEYSPACE excalibur
    WITH replication = {'class': 'NetworkTopologyStrategy', 'replication_factor' : 3, 'DC2': 0} ;

DESCRIBE KEYSPACE excalibur
    CREATE KEYSPACE excalibur WITH replication = {'class': 'NetworkTopologyStrategy', 'DC1': '3'} AND durable_writes = true;`
```

[transient replication](http://cassandra.apache.org/doc/latest/architecture/dynamo.html#transient-replication)가 활성화 된 경우 복제 요소를 정의하여 `SimpleStrategy` 및 `NetworkTopologyStrategy`에 대해 `<total_replicas>/<transient_replicas>` 포맷으로 임시 복제본을 구성 할 수 있습니다. 

예를 들어,이 키스페이스는 DC1에 3 개의 복제본을 가지며 그 중 하나는 일시적이며 DC2에는 5 개의 복제본이 있으며 그 중 2 개는 일시적입니다:

```CQL
CREATE KEYSPACE some_keysopace
           WITH replication = {'class': 'NetworkTopologyStrategy', 'DC1' : '3/1'', 'DC2' : '5/2'};
```

## USE

`USE` 문은 *current* 키스페이스 (실행되는 *연결*)을 변경할 수 있습니다. CQL의 여러 객체는 키스페이스 (테이블, 사용자 정의 유형, 함수 등)에 바인딩되며 현재 키스페이스는 해당 객체가 완전한 이름 없이 참조 될 때 사용되는 기본 키스페이스입니다 (즉, 접두사 키스페이스 이름). `USE` 문은 키스페이스을 현재 인수로 지정하기만하면 됩니다 :

```
use_statement ::=  USE keyspace_name
```

## ALTER KEYSPACE

`ALTER KEYSPACE` 문은 키스페이스의 옵션을 수정할 수 있습니다:

```
alter_keyspace_statement ::=  ALTER KEYSPACE keyspace_name WITH options
```

예를 들어:

```CQL
ALTER KEYSPACE Excelsior
    WITH replication = {'class': 'SimpleStrategy', 'replication_factor' : 4};
```

The supported options are the same than for [creating a keyspace](http://cassandra.apache.org/doc/latest/cql/ddl.html#create-keyspace-statement).

## DROP KEYSPACE

키스페이스를 삭제하는 것은 `DROP KEYSPACE` 문을 사용하여 수행 할 수 있습니다 :

```
drop_keyspace_statement ::=  DROP KEYSPACE [ IF EXISTS ] keyspace_name
```

예를 들어

```CQL
DROP KEYSPACE Excelsior;
```

키스페이스을 삭제하면 모든 테이블, UTD와 그 안에 있는 함수 및 해당 테이블에 포함 된 모든 데이터를 포함하여 해당 키스페이스를 즉시 되돌릴 수 없게 제거됩니다.

키스페이스가 존재하지 않는다면, `IF EXISTS`가 사용되지 않는 한, 명령문은 오류를 반환 할 것입니다.

## CREATE TABLE

새로운 테이블을 생성하면 `CREATE TABLE` 문을 사용합니다:

```
create_table_statement ::=  CREATE TABLE [ IF NOT EXISTS ] table_name
                            '('
                                column_definition
                                ( ',' column_definition )*
                                [ ',' PRIMARY KEY '(' primary_key ')' ]
                            ')' [ WITH table_options ]
column_definition      ::=  column_name cql_type [ STATIC ] [ PRIMARY KEY]
primary_key            ::=  partition_key [ ',' clustering_columns ]
partition_key          ::=  column_name
                            | '(' column_name ( ',' column_name )* ')'
clustering_columns     ::=  column_name ( ',' column_name )*
table_options          ::=  COMPACT STORAGE [ AND table_options ]
                            | CLUSTERING ORDER BY '(' clustering_order ')' [ AND table_options ]
                            | options
clustering_order       ::=  column_name (ASC | DESC) ( ',' column_name (ASC | DESC) )*
```

예를 들어:

```CQL
CREATE TABLE monkeySpecies (
    species text PRIMARY KEY,
    common_name text,
    population varint,
    average_size int
) WITH comment='Important biological records';

CREATE TABLE timeline (
    userid uuid,
    posted_month int,
    posted_time uuid,
    body text,
    posted_by text,
    PRIMARY KEY (userid, posted_month, posted_time)
) WITH compaction = { 'class' : 'LeveledCompactionStrategy' };

CREATE TABLE loads (
    machine inet,
    cpu int,
    mtime timeuuid,
    load float,
    PRIMARY KEY ((machine, cpu), mtime)
) WITH CLUSTERING ORDER BY (mtime DESC);
```

A CQL table has a name and is composed of a set of *rows*. Creating a table amounts to defining which columns the rows will be composed, which of those columns compose the primary key, as well as optional options for the table.

`IF NOT EXISTS` 지시문을 사용하지 않으면 이미 존재하는 테이블을 생성하려고 시도하면 에러가 반환됩니다. 이 매개 변수를 사용하면 테이블이 이미 있으면 명령문은 no-op가됩니다.

CQL 테이블의 모든 행에는 테이블을 만들 때 정의 된 사전 정의 된 열 집합이 있습니다 (또는 나중에 alter 문을 사용하여 추가됩니다).

`column_definition`은 주로 정의 된 컬럼의 이름과 타입으로 이루어져 있습니다.이 타입은 그 컬럼에 대해 받아 들여지는 값을 제한합니다. 또한 열 정의에는 다음 수정자가 있을 수 있습니다.

- `STATIC`

  컬럼을 [static column](http://cassandra.apache.org/doc/latest/cql/ddl.html#static-columns) 으로 선언합니다.

- `PRIMARY KEY`

  이 열을 테이블의 [primary key](http://cassandra.apache.org/doc/latest/cql/ddl.html#primary-key)의 유일한 구성 요소로 선언합니다.

일부 컬럼은 테이블 정의에서`정적`으로 선언 될 수 있습니다. 정적인 컬럼은 동일한 파티션에 속한 모든 행 ( "partition key"가 동일 함)에 의해 "공유"됩니다. 예를 들면 :

```CQL
CREATE TABLE t (
    pk int,
    t int,
    v text,
    s text static,
    PRIMARY KEY (pk, t)
);

INSERT INTO t (pk, t, v, s) VALUES (0, 0, 'val0', 'static0');
INSERT INTO t (pk, t, v, s) VALUES (0, 1, 'val1', 'static1');

SELECT * FROM t;
   pk | t | v      | s
  ----+---+--------+-----------
   0  | 0 | 'val0' | 'static1'
   0  | 1 | 'val1' | 'static1'
```

알 수 있듯이, `s` 값은 파티션의 두 행 (그 예에서 파티션 키는 `pk`, 두 행은 같은 파티션에 있음)에 대해 동일합니다 (`static1`). 두번째 삽입 `s`의 값을 오버라이드 (override) 했습니다.

다음 제한 사항으로서 static 컬럼 사용 :

- `COMPACT STORAGE` 옵션이 있는 테이블 (아래 참조)은 사용할 수 없습니다.
- 클러스터링 컬럼이 없는 테이블은 static 컬럼을 가질 수 없습니다 (컬럼을 클러스터하지 않고 모든 파티션에는 하나의 행만 있으므로 모든 컬럼은 본질적으로 static입니다).
- `PRIMARY KEY`이 아닌 컬럼들만 static 일 수 있습니다.

테이블 내에서 행은 `PRIMARY KEY`에 의해 고유하게 식별되므로 모든 테이블은 `PRIMARY KEY`를 정의해야합니다 (단 하나만 정의해야 함). `PRIMARY KEY` 정의는 표에 정의 된 하나 이상의 열로 구성됩니다. 구문적으로, 기본 키는`PRIMARY KEY` 키워드 뒤에 괄호 안에 쉼표로 구분 된 쉼표로 구분 된 목록의 목록이 정의되어 있지만 기본 키가 하나의 열만있는 경우 다른 방법으로는`PRIMARY KEY 키워드. 기본 키 정의의 열 순서가 중요합니다.

CQL 기본 키는 두 부분으로 구성됩니다:

- partion key 부분. 기본 키 정의의 첫 번째 구성 요소입니다. 단일 열이거나 추가 괄호를 사용하여 여러 열일 수 있습니다. 테이블에는 항상 최소한 파티션 키가 있습니다. 가능한 가장 작은 테이블 정의는 다음과 같습니다.

  ```CQL
  CREATE TABLE t (k text PRIMARY KEY);
  ```

- [clustering columns] (http://cassandra.apache.org/doc/latest/cql/ddl.html#clustering-columns). 그것들은 기본 키 정의의 첫 번째 구성 요소 다음에 오는 열이며 해당 열의 순서는 *클러스터링 순서*를 정의합니다.

기본 키 정의의 몇 가지 예는 다음과 같습니다.

- `PRIMARY KEY (a)`: `a` 는 파티션 키이고 클러스터링 컬럼은 없습니다.
- `PRIMARY KEY (a, b, c)` : `a` a는 파티션 키이고 'b`와'c '는 클러스터링 컬럼입니다.
- `PRIMARY KEY ((a, b), c)` : `a` 와 `b` 는 파티션 키로 결합(composed)되었고, (이것을 결합된(composed) 파티션키라고 부릅니다.) `c`는 클러스터링 컬럼입니다.

테이블 내에서 CQL은 *파티션*의 개념을 정의합니다. 파티션은 단순히 파티션 키에 대해 동일한 값을 공유하는 행 세트입니다. 파티션 키가 여러 컬럼으로 구성된 경우 행은 동일한 파티션에만 속합니다. 모든 파티션 키 컬럼에 동일한 값이 있습니다. 예를 들어, 다음 테이블의 정의와 콘텐트가 주어진 경우 :

```CQL
CREATE TABLE t (
    a int,
    b int,
    c int,
    d int,
    PRIMARY KEY ((a, b), c, d)
);

SELECT * FROM t;
   a | b | c | d
  ---+---+---+---
   0 | 0 | 0 | 0    // row 1
   0 | 0 | 1 | 1    // row 2
   0 | 1 | 2 | 2    // row 3
   0 | 1 | 3 | 3    // row 4
   1 | 1 | 4 | 4    // row 5
```

`row 1`과 `row 2`는 같은 파티션에 있고,`row 3`과`row 4`도 같은 파티션 (그러나 다른 파티션)에 있고`row 5`는 또 다른 파티션에 있습니다.

테이블에는 항상 파티션 키가 있고 테이블에 클러스터링 컬럼이 없으면 기본 키가 행을 고유하게 식별하고 기본 키가 파티션과 동일하기 때문에 키 클러스터링 컬럼이 없는 경우 해당 테이블의 모든 파티션은 단일 행으로 만 구성됩니다.

파티션의 가장 중요한 특성은 동일한 파티션에 속한 모든 행이 동일한 복제본 세트에 저장된다는 것입니다. 즉, 테이블의 파티션 키는 클러스터에서 어떤 행이 함께 로컬라이즈 될 것인지를 정의하므로 함께 가져와야하는 행이 동일한 파티션에 있으므로 파티션 키를 현명하게 선택하는 것이 중요합니다. (해당 행을 쿼리하면 최소한의 노드에 연결해야 합니다).

그러나 파티션 키를 공유하는 모든 행이 동일한 복제 노드 세트에 저장되도록 보장되므로 너무 많은 데이터를 그룹화하는 파티션 키가 핫스팟을 만들 수 있습니다.

파티션의 또 다른 유용한 특성은 데이터를 쓸 때 단일 파티션에 속한 모든 업데이트가 *atomically* 및 *isolation*에서 완료된다는 것입니다. 이는 파티션 전체에 해당하지 않습니다.

테이블의 파티션 키와 클러스터링 컬럼을 적절히 선택하는 것은 아마도 Cassandra의 데이터 모델링에서 가장 중요한 부분 중 하나 일 것이며, 수행 할 수 있는 쿼리와 효율성에 큰 영향을 미칩니다.

테이블의 클러스터링 컬럼은 해당 테이블의 파티션에 대한 클러스터링 순서를 정의합니다. 주어진 파티션의 경우 클러스터링 순서에 따라 모든 행이 카산드라 내부에서 물리적으로 정렬됩니다. 예를 들어, 주어진 :

```CQL
CREATE TABLE t (
    a int,
    b int,
    c int,
    PRIMARY KEY (a, b, c)
);

SELECT * FROM t;
   a | b | c
  ---+---+---
   0 | 0 | 4     // row 1
   0 | 1 | 9     // row 2
   0 | 2 | 2     // row 3
   0 | 3 | 3     // row 4
```

(같은 파티션에 속하는) 행들은 모두 `b` 컬럼의 값의 순서대로 (내부적으로 위에 표시된 순서대로) 내부적으로 저장됩니다. 따라서 테이블의 파티션 키가 동일한 복제본 세트의 행을 그룹화 할 수있는 경우 클러스터링 열은 해당 행이 복제본에 저장되는 방법을 제어합니다. 이러한 정렬은 파티션 내의 행 범위 검색을 허용합니다 (예 : 위의 예제에서 `SELECT * FROM t WHERE a = 0 AND b> 1 AND b <= 3`)는 매우 효율적입니다.

CQL 테이블에는 생성시 설정할 수 있는 옵션이 많이 있습니다 (대부분 나중에 변경 가능). 이 옵션은 `WITH` 키워드 다음에 지정됩니다.

이 옵션들 중에서 다음에 언급하는 두 가지는 생성 후에 변경 될 수 없으며 테이블에 대해 수행 할 수있는 쿼리에 영향을 줄 수 없습니다: `COMPACT STORAGE` 옵션과 `CLUSTERING ORDER` 옵션. 그것들과 테이블의 다른 옵션들은 다음 섹션에서 설명합니다.

경고

Cassandra 3.0 이후로 컴팩트 테이블은 내부적으로 (동일한 스키마에 대해) 비 압축 컴팩트 테이블과 동일한 레이아웃을 가지고 있으며 테이블 컴팩트만 선언하면 테이블 정의 및 사용에 인위적인 제한이 생깁니다. 이는 역사적인 이유로 존재하며 이전 버전과의 호환성을 위해 보존됩니다. `COMPACT STORAGE`는 Cassandra 4.0에서 제거 할 수 없으므로 `COMPACT STORAGE` 옵션을 사용하여 새 테이블을 생성하는 것이 좋습니다.

*compact* 테이블은 `COMPACT STORAGE` 옵션으로 정의 된 테이블입니다. 이 옵션은 CQL 버전 3 이전에 생성 된 정의에 대한 이전 버전과의 호환성을 위해서만 유지되며 새 테이블에 사용하면 안됩니다. 이 옵션을 사용하여 테이블을 선언하면 테이블에 제한이 생기는데 이는 크게 임의적입니다 (역사적 이유로 존재 함). 그 제한들 중에:

- 컴팩트 테이블은 콜렉션이나 스태틱 컬럼을 사용할 수 없습니다.
- 컴팩트 테이블에 적어도 하나의 클러스터링 컬럼이 있는 경우에는 기본 키 컬럼 바깥에 정확하게 하나의 컬럼이 있어야합니다. 이것은 특히 생성 후 열을 추가하거나 제거 할 수 없다는 것을 의미합니다.
- 컴팩트 테이블은 작성할 수 있는 인덱스가 제한되어 있으며 구체화 된 뷰를 작성할 수 없습니다.

테이블의 클러스터링 오더는 해당 테이블의 클러스터링 컬럼에 의해 정의됩니다. 기본적으로 이러한 순서는 해당 클러스터링 오더의 자연 순서(natural order)에 따라 결정되지만 `CLUSTERING ORDER`를 사용하면 클러스터링 오더를 변경하여 일부 (잠재적으로 전체) 열에 역방향 자연 순서(reverse natural order)를 사용할 수 있습니다.

`CLUSTERING ORDER` 옵션은 클러스터링 컬럼의 쉼표로 구분 된 리스트를 취합니다. 각 컬럼은 `ASC`(증가, natural order) 또는`DESC` (감소, 예를 들어 reverse natural order) . 특히 `CLUSTERING ORDER` 옵션을 사용하지 않을 경우 기본값은`ASC` 수정자를 사용하는 모든 클러스터링 컬럼에서 옵션을 사용하는 것과 동일합니다.

이 옵션은 기본적으로 스토리지 엔진이 행을 저장하는 순서를 변경하지만 3 가지 가시적 인 결과가 있다는 힌트입니다.

- 그것은 그 테이블에서`selects`을 위해 허용되는 `ORDER BY` 절을 제한합니다.  You can only order results by the clustering order or the reverse clustering order. Meaning that if a table has 2 clustering column `a` and `b` and you defined `WITH CLUSTERING ORDER (a DESC, b ASC)`, then in queries you will be allowed to use `ORDER BY (a DESC, b ASC)` and (reverse clustering order) `ORDER BY (a ASC, b DESC)` but **not** `ORDER BY (a ASC, b ASC)` (nor `ORDER BY (a DESC, b DESC)`).
- it also change the default order of results when queried (if no `ORDER BY` is provided). Results are always returned in clustering order (within a partition).


- 역방향 클러스터링 순서의 쿼리가 순방향 클러스터링 순서의 쿼리보다 느려지므로 일부 쿼리에 작은 성능 영향을 줍니다. 실제로 이것은 열의 자연 순서가 역순 인 쿼리를 계획하는 경우 (예 : 최신 데이터부터 오래된 데이터를 원할 때처럼 시계열과 공통적인 경우), 내림차순 클러스터링 순서를 선언하는 최적화입니다 .

Todo

review (misses cdc if nothing else) and link to proper categories when appropriate (compaction for instance)

테이블은 다음 옵션을 지원합니다:

| option                        | kind     | default      | description                                                  |
| ----------------------------- | -------- | ------------ | ------------------------------------------------------------ |
| `comment`                     | *simple* | none         | 자유형식의 사람이 읽을 수 있는 주석.                         |
| `speculative_retry`           | *simple* | 99PERCENTILE | [Speculative retry options](http://cassandra.apache.org/doc/latest/cql/ddl.html#speculative-retry-options). |
| `additional_write_policy`     | *simple* | 99PERCENTILE | [Speculative retry options](http://cassandra.apache.org/doc/latest/cql/ddl.html#speculative-retry-options). |
| `gc_grace_seconds`            | *simple* | 864000       | garbage collection tombstones (deletion marker)를 기다리는 시간. |
| `bloom_filter_fp_chance`      | *simple* | 0.00075      | sstable 블룸 필터의 거짓 긍정의 목표 확률. 상기 블룸 필터는 제공된 확률을 제공하도록 크기가 정해집니다 (따라서 블룸 필터의 메모리 및 디스크상의 크기에 영향을 미치는이 값을 낮춤) |
| `default_time_to_live`        | *simple* | 0            | 테이블의 기본 만료 시간 ( "TTL")입니다.                      |
| `compaction`                  | *map*    | *see below*  | [Compaction options](http://cassandra.apache.org/doc/latest/cql/ddl.html#cql-compaction-options). |
| `compression`                 | *map*    | *see below*  | [Compression options](http://cassandra.apache.org/doc/latest/cql/ddl.html#cql-compression-options). |
| `caching`                     | *map*    | *see below*  | [Caching options](http://cassandra.apache.org/doc/latest/cql/ddl.html#cql-caching-options). |
| `memtable_flush_period_in_ms` | *simple* | 0            | Cassandra가 memtables를 디스크로 플러시하기 전의 시간 (ms)입니다. |
| `read_repair`                 | *simple* | BLOCKING     | read repair 동작을 설정합니다 (아래 참조).                   |

기본적으로 카산드라 읽기 코디네이터는 일관성 레벨을 충족시키는 데 필요한만큼의 복제본 (일관성 레벨이 'ONE'인 경우), 'QUORUM'의 쿼럼 (quorum) 등을 쿼리합니다. `speculative_retry`는 코디네이터가 추가 복제본을 쿼리 할 수 있는시기를 결정합니다. 복제본이 느리거나 응답이 없는 경우에 유용합니다. `additional_write_policy`는 값싼 쿼럼 쓰기가 일시적인 복제본을 포함하도록 업그레이드 될 임계 값을 지정합니다. 다음은 유효한 값입니다 (대소 문자를 구분하지 않음).

이 설정은 이미 모든 복제본을 쿼리하기 때문에 일관성 수준이 `ALL`인 읽기에는 영향을 미치지 않습니다.

추가 복제본을 자주 읽으면 클러스터 성능이 저하 될 수 있습니다. 의심 스러우면 기본값 인 `99PERCENTILE`을 유지하십시오.

The `compaction` options must at least define the `'class'` sub-option, that defines the compaction strategy class to use. The default supported class are `'SizeTieredCompactionStrategy'` ([STCS](http://cassandra.apache.org/doc/latest/operating/compaction.html#stcs)), `'LeveledCompactionStrategy'` ([LCS](http://cassandra.apache.org/doc/latest/operating/compaction.html#lcs)) and `'TimeWindowCompactionStrategy'` ([TWCS](http://cassandra.apache.org/doc/latest/operating/compaction.html#twcs)) (the `'DateTieredCompactionStrategy'` is also supported but is deprecated and `'TimeWindowCompactionStrategy'` should be preferred instead). Custom strategy can be provided by specifying the full class name as a [string constant](http://cassandra.apache.org/doc/latest/cql/definitions.html#constants).

All default strategies support a number of [common options](http://cassandra.apache.org/doc/latest/operating/compaction.html#compaction-options), as well as options specific to the strategy chosen (see the section corresponding to your strategy for details: [STCS](http://cassandra.apache.org/doc/latest/operating/compaction.html#stcs-options), [LCS](http://cassandra.apache.org/doc/latest/operating/compaction.html#lcs-options) and [TWCS](http://cassandra.apache.org/doc/latest/operating/compaction.html#twcs)).

The `compression` options define if and how the sstables of the table are compressed. The following sub-options are available:

| Option               | Default       | Description                                                  |
| -------------------- | ------------- | ------------------------------------------------------------ |
| `class`              | LZ4Compressor | The compression algorithm to use. Default compressor are: LZ4Compressor, SnappyCompressor and DeflateCompressor. Use `'enabled' : false` to disable compression. Custom compressor can be provided by specifying the full class name as a “string constant”:#constants. |
| `enabled`            | true          | Enable/disable sstable compression.                          |
| `chunk_length_in_kb` | 64            | On disk SSTables are compressed by block (to allow random reads). This defines the size (in KB) of said block. Bigger values may improve the compression rate, but increases the minimum size of data to be read from disk for a read |
| `crc_check_chance`   | 1.0           | When compression is enabled, each compressed block includes a checksum of that block for the purpose of detecting disk bitrot and avoiding the propagation of corruption to other replica. This option defines the probability with which those checksums are checked during read. By default they are always checked. Set to 0 to disable checksum checking and to 0.5 for instance to check them every other read   \| |

For instance, to create a table with LZ4Compressor and a chunk_lenth_in_kb of 4KB:

```CQL
CREATE TABLE simple (
   id int,
   key text,
   value text,
   PRIMARY KEY (key, value)
) with compression = {'class': 'LZ4Compressor', 'chunk_length_in_kb': 4};
```

`caching` 옵션은 테이블에 대한 key cache와 row cache를 모두 설정할 수 있게 합니다. 다음과 같은 하위 옵션을 사용할 수 있습니다.

| Option               | Default | Description                                                  |
| -------------------- | ------- | ------------------------------------------------------------ |
| `keys`               | ALL     | Whether to cache keys (“key cache”) for this table. Valid values are: `ALL` and `NONE`. |
| `rows_per_partition` | NONE    | The amount of rows to cache per partition (“row cache”). If an integer `n` is specified, the first `n` queried rows of a partition will be cached. Other possible options are `ALL`, to cache all rows of a queried partition, or `NONE` to disable row caching.n: |

예를 들어 키 캐시와 파티션 당 10 개의 행을 모두 포함하는 테이블을 만들려면 다음과 같이하십시오.

```CQL
CREATE TABLE simple (
	id int,
	key text,
	value text,
	PRIMARY KEY (key, value)
) WITH caching = {'keys': 'ALL', 'rows_per_partition': 10};
```

`read_repair` 옵션은 다양한 성능과 일관성 동작을 튜닝 할 수 있도록 read repair 동작을 설정합니다. 두 가지 일관성 속성은 읽기 복구 동작의 영향을 받습니다.

- Monotonic Quorum Reads : `BLOCKING`에 의해 제공됩니다. monotonic quorum reads은 어떤 경우에는 읽기가 제 시간에 되돌아 오는 것을 방지합니다. monotonic quorum reads가 제공되지 않고 쓰기가 복제본의 쿼럼에 도달하지 못하면 하나의 읽기에서 볼 수 있고 다음 읽기에서 사라질 수 있습니다.
- Write Atomicity: Provided by `NONE`. Write atomicity prevents reads from returning partially applied writes. Cassandra attempts to provide partition level write atomicity, but since only the data covered by a SELECT statement is repaired by a read repair, read repair can break write atomicity when data is read at a more granular level than it is written. For example read repair can break write atomicity if you write multiple rows to a clustered partition in a batch, but then select a single row by specifying the clustering column in a SELECT statement.

The available read repair settings are:

The default setting. When `read_repair` is set to `BLOCKING`, and a read repair is triggered, the read will block on writes sent to other replicas until the CL is reached by the writes. Provides monotonic quorum reads, but not partition level write atomicity

When `read_repair` is set to `NONE`, the coordinator will reconcile any differences between replicas, but will not attempt to repair them. Provides partition level write atomicity, but not monotonic quorum reads.

- Adding new columns (see `ALTER TABLE` below) is a constant time operation. There is thus no need to try to anticipate future usage when creating a table.

## ALTER TABLE

Altering an existing table uses the `ALTER TABLE` statement:

```
alter_table_statement   ::=  ALTER TABLE table_name alter_table_instruction
alter_table_instruction ::=  ADD column_name cql_type ( ',' column_name cql_type )*
                             | DROP column_name ( column_name )*
                             | WITH options
```

For instance:

```CQL
ALTER TABLE addamsFamily ADD gravesite varchar;

ALTER TABLE addamsFamily
       WITH comment = 'A most excellent and useful table';
```

The `ALTER TABLE` statement can:

- Add new column(s) to the table (through the `ADD` instruction). Note that the primary key of a table cannot be changed and thus newly added column will, by extension, never be part of the primary key. Also note that [compact tables](http://cassandra.apache.org/doc/latest/cql/ddl.html#compact-tables) have restrictions regarding column addition. Note that this is constant (in the amount of data the cluster contains) time operation.
- Remove column(s) from the table. This drops both the column and all its content, but note that while the column becomes immediately unavailable, its content is only removed lazily during compaction. Please also see the warnings below. Due to lazy removal, the altering itself is a constant (in the amount of data removed or contained in the cluster) time operation.
- Change some of the table options (through the `WITH` instruction). The [supported options](http://cassandra.apache.org/doc/latest/cql/ddl.html#create-table-options) are the same that when creating a table (outside of `COMPACT STORAGE` and `CLUSTERING ORDER` that cannot be changed after creation). Note that setting any `compaction` sub-options has the effect of erasing all previous `compaction` options, so you need to re-specify all the sub-options if you want to keep them. The same note applies to the set of `compression` sub-options.

Warning

Dropping a column assumes that the timestamps used for the value of this column are “real” timestamp in microseconds. Using “real” timestamps in microseconds is the default is and is **strongly** recommended but as Cassandra allows the client to provide any timestamp on any table it is theoretically possible to use another convention. Please be aware that if you do so, dropping a column will not work correctly.

Warning

Once a column is dropped, it is allowed to re-add a column with the same name than the dropped one **unless** the type of the dropped column was a (non-frozen) column (due to an internal technical limitation).

## DROP TABLE

Dropping a table uses the `DROP TABLE` statement:

```
drop_table_statement ::=  DROP TABLE [ IF EXISTS ] table_name
```

Dropping a table results in the immediate, irreversible removal of the table, including all data it contains.

If the table does not exist, the statement will return an error, unless `IF EXISTS` is used in which case the operation is a no-op.

## TRUNCATE

A table can be truncated using the `TRUNCATE` statement:

```
truncate_statement ::=  TRUNCATE [ TABLE ] table_name
```

Note that `TRUNCATE TABLE foo` is allowed for consistency with other DDL statements but tables are the only object that can be truncated currently and so the `TABLE` keyword can be omitted.

Truncating a table permanently removes all existing data from the table, but without removing the table itself.