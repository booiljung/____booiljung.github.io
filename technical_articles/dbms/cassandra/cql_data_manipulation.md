# Data manipulation of Cassandra

This section describes the statements supported by CQL to insert, update, delete and query data.

## SELECT

Querying data from data is done using a `SELECT` statement:

```
select_statement ::=  SELECT [ JSON | DISTINCT ] ( select_clause | '*' )
                      FROM table_name
                      [ WHERE where_clause ]
                      [ GROUP BY group_by_clause ]
                      [ ORDER BY ordering_clause ]
                      [ PER PARTITION LIMIT (integer | bind_marker) ]
                      [ LIMIT (integer | bind_marker) ]
                      [ ALLOW FILTERING ]
select_clause    ::=  selector [ AS identifier ] ( ',' selector [ AS identifier ] )
selector         ::=  column_name
                      | term
                      | CAST '(' selector AS cql_type ')'
                      | function_name '(' [ selector ( ',' selector )* ] ')'
                      | COUNT '(' '*' ')'
where_clause     ::=  relation ( AND relation )*
relation         ::=  column_name operator term
                      '(' column_name ( ',' column_name )* ')' operator tuple_literal
                      TOKEN '(' column_name ( ',' column_name )* ')' operator term
operator         ::=  '=' | '<' | '>' | '<=' | '>=' | '!=' | IN | CONTAINS | CONTAINS KEY
group_by_clause  ::=  column_name ( ',' column_name )*
ordering_clause  ::=  column_name [ ASC | DESC ] ( ',' column_name [ ASC | DESC ] )*
```

For instance:

```CQL
SELECT name, occupation FROM users WHERE userid IN (199, 200, 207);
SELECT JSON name, occupation FROM users WHERE userid = 199;
SELECT name AS user_name, occupation AS user_occupation FROM users;

SELECT time, value
FROM events
WHERE event_type = 'myEvent'
  AND time > '2011-02-03'
  AND time <= '2012-01-01'

SELECT COUNT (*) AS user_count FROM users;
```

The `SELECT` statements reads one or more columns for one or more rows in a table. It returns a result-set of the rows matching the request, where each row contains the values for the selection corresponding to the query. Additionally, [functions](http://cassandra.apache.org/doc/latest/cql/functions.html#cql-functions) including [aggregation](http://cassandra.apache.org/doc/latest/cql/functions.html#aggregate-functions) ones can be applied to the result.

A `SELECT` statement contains at least a [selection clause](http://cassandra.apache.org/doc/latest/cql/dml.html#selection-clause) and the name of the table on which the selection is on (note that CQL does **not** joins or sub-queries and thus a select statement only apply to a single table). In most case, a select will also have a [where clause](http://cassandra.apache.org/doc/latest/cql/dml.html#where-clause) and it can optionally have additional clauses to [order](http://cassandra.apache.org/doc/latest/cql/dml.html#ordering-clause) or [limit](http://cassandra.apache.org/doc/latest/cql/dml.html#limit-clause) the results. Lastly, [queries that require filtering](http://cassandra.apache.org/doc/latest/cql/dml.html#allow-filtering) can be allowed if the `ALLOW FILTERING` flag is provided.

### Selection clause

The [`select_clause`](http://cassandra.apache.org/doc/latest/cql/dml.html#grammar-token-select_clause) determines which columns needs to be queried and returned in the result-set, as well as any transformation to apply to this result before returning. It consists of a comma-separated list of *selectors* or, alternatively, of the wildcard character (`*`) to select all the columns defined in the table.

#### Selectors

A [`selector`](http://cassandra.apache.org/doc/latest/cql/dml.html#grammar-token-selector) can be one of:

- 선택한 열의 이름으로 해당 열의 값을 검색합니다.
- term는 일반적으로 함수와 같은 다른 셀렉터 안에 중첩되어 사용됩니다 (term가 직접 선택되면 결과 집합의 해당 열은 반환 된 모든 행에 대해이 term의 값을 갖게됩니다).
- 중첩 된 선택기를 (호환 가능한) 타입으로 변환 할 수있는 형변환입니다.
- 인수가 셀렉터 그 자체인 함수 호출. 자세한 내용은 [functions](http://cassandra.apache.org/doc/latest/cql/functions.html#cql-functions) 섹션을 참조하십시오.
- COUNT 함수에 대한 특별한 호출 (COUNT (*))(http://cassandra.apache.org/doc/latest/cql/functions.html#count-function)은 모든 null이 아닌 결과를 계산합니다.

#### Aliases

모든 * 최상위 레벨 * selector는 별칭을 지정할 수도 있습니다 (`AS` 사용). 그렇다면 결과 set에서 해당 열의 이름이 별칭의 이름이 됩니다. 예를 들면 :

```
// Without alias
SELECT intAsBlob(4) FROM t;

//  intAsBlob(4)
// --------------
//  0x00000004

// With alias
SELECT intAsBlob(4) AS four FROM t;

//  four
// ------------
//  0x00000004
```

Note

Currently, aliases aren’t recognized anywhere else in the statement where they are used (not in the `WHERE` clause, not in the `ORDER BY` clause, ...). You must use the orignal column name instead.

#### `WRITETIME` and `TTL` function

Selection은 두 가지 특수 함수 (다른 곳에서는 허용되지 않는)를 지원합니다: `WRITETIME`과`TTL`. 두 함수 모두 하나의 인수 만 취하고 그 인수는 반드시 컬럼 이름이어야합니다 (예를 들어`TTL(3)`은 유효하지 않습니다).

이러한 함수를 사용하면 각 열에 대해 내부적으로 저장된 메타 정보를 검색 할 수 있습니다:

- `WRITETIME`에 대한 열 값의 타임 스탬프.
- 값이 만료되도록 설정된 경우 열의 값에 대해 남은 남은 시간 (초) (그렇지 않으면`null`).

### The `WHERE` clause

The `WHERE` clause specifies which rows must be queried. It is composed of relations on the columns that are part of the `PRIMARY KEY` and/or have a [secondary index](http://cassandra.apache.org/doc/latest/cql/dml.html#createIndexStmt) defined on them.

Not all relations are allowed in a query. For instance, non-equal relations (where `IN` is considered as an equal relation) on a partition key are not supported (but see the use of the `TOKEN` method below to do non-equal queries on the partition key). Moreover, for a given partition key, the clustering columns induce an ordering of rows and relations on them is restricted to the relations that allow to select a **contiguous** (for the ordering) set of rows. For instance, given:

```CQL
CREATE TABLE posts (
    userid text,
    blog_title text,
    posted_at timestamp,
    entry_title text,
    content text,
    category int,
    PRIMARY KEY (userid, blog_title, posted_at)
)
```

The following query is allowed:

```CQL
SELECT entry_title, content FROM posts
 WHERE userid = 'john doe'
   AND blog_title='John''s Blog'
   AND posted_at >= '2012-01-01' AND posted_at < '2012-01-31'
```

But the following one is not, as it does not select a contiguous set of rows (and we suppose no secondary indexes are set):

```
// Needs a blog_title to be set to select ranges of posted_at
SELECT entry_title, content FROM posts
 WHERE userid = 'john doe'
   AND posted_at >= '2012-01-01' AND posted_at < '2012-01-31'
```

관계를 지정할 때 `TOKEN` 함수는 `PARTITION KEY` 칼럼에서 쿼리를 위해 사용될 수 있습니다. 이 경우 행은 값이 아닌 `PARTITION_KEY` 토큰에 따라 선택됩니다. 키의 토큰은 사용중인 partitioner에 따라 다르며, 특히 RandomPartitioner가 의미있는 순서를 생성하지는 않습니다. 또한 partitioner를 주문하는 것은 항상 토큰 값을 바이트 단위로 정렬합니다 (따라서 파티션 키의 타입이 int 인 경우에도, 특히 `token (-1) > token (0)` 경우). 예를들면:

```CQL
SELECT * FROM posts
 WHERE token(userid) > token('tom') AND token(userid) < token('bob')
```

Moreover, the `IN` relation is only allowed on the last column of the partition key and on the last column of the full primary key.

It is also possible to “group” `CLUSTERING COLUMNS` together in a relation using the tuple notation. For instance:

```CQL
SELECT * FROM posts
 WHERE userid = 'john doe'
   AND (blog_title, posted_at) > ('John''s Blog', '2012-01-01')
```

will request all rows that sorts after the one having “John’s Blog” as `blog_tile` and ‘2012-01-01’ for `posted_at` in the clustering order. In particular, rows having a `post_at <= '2012-01-01'` will be returned as long as their `blog_title > 'John''s Blog'`, which would not be the case for:

```CQL
SELECT * FROM posts
 WHERE userid = 'john doe'
   AND blog_title > 'John''s Blog'
   AND posted_at > '2012-01-01'
```

The tuple notation may also be used for `IN` clauses on clustering columns:

```CQL
SELECT * FROM posts
 WHERE userid = 'john doe'
   AND (blog_title, posted_at) IN (('John''s Blog', '2012-01-01'), ('Extreme Chess', '2014-06-01'))
```

The `CONTAINS` operator may only be used on collection columns (lists, sets, and maps). In the case of maps, `CONTAINS` applies to the map values. The `CONTAINS KEY` operator may only be used on map columns and applies to the map keys.

### Grouping results

`GROUP BY` 옵션은 열 세트에 대해 동일한 값을 공유하는 선택된 모든 행을 단일 행으로 압축합니다.

`GROUP BY` 옵션을 사용하면 파티션 키 레벨이나 클러스터링 컬럼 레벨에서만 행을 그룹화 할 수 있습니다. 결과적으로, `GROUP BY` 옵션은 기본 키 순서로 기본 키 열 이름을 인수로 허용합니다. 기본 키 열이 동등 제한에 의해 제한되면 `GROUP BY` 절에 있어야 할 필요는 없습니다.

집계 함수(aggregate function)는 각 그룹에 대해 별도의 값을 생성합니다. `GROUP BY` 절이 지정되지 않으면 집계 함수는 모든 행에 대해 단일 값을 생성합니다.

집계 함수(aggregate function) 없이 열을 선택하면 'GROUP BY'가있는 명령문에서 각 그룹의 첫 번째 값이 반환됩니다.

### Ordering results

`ORDER BY` 절은 리턴 된 결과의 순서를 선택할 수 있도록합니다. 컬럼의 순서와 함께 컬럼 이름의 리스트를 인자로 취합니다. (ascend는 ASC를, descendant는 DESC, ASC는 ASC와 동등하다). 현재 가능한 순서는 테이블에 정의 된 [클러스터링 순서](http://cassandra.apache.org/doc/latest/cql/ddl.html#clustering-order)에 의해 제한됩니다.

- 지정한 `CLUSTERING ORDER`가 없이 테이블이 정의된 경우 허용되는 순서는 클러스터링 열과 그 역순으로 유도되는 순서입니다.
- 그렇지 않으면 허용되는 순서는`CLUSTERING ORDER` 옵션의 순서와 반대 순서입니다.

### Limiting results

`SELECT` 문에 대한`LIMIT` 옵션은 질의에 의해 반환 된 행의 수를 제한하는 반면,`PER PARTITION LIMIT` 옵션은 질의에 의해 주어진 파티션에 대해 리턴 된 행의 수를 제한합니다. 두 가지 유형의 제한은 동일한 명령문에서 사용될 수 있습니다.

### Allowing filtering

기본적으로 CQL은 서버 측 "필터링"을 포함하지 않는 select 쿼리, 즉 모든 (라이브) 레코드 읽기가 결과 세트에서 반환 될지도 모르는 쿼리를 허용합니다. 이러한 "비필터링"쿼리는 질의에 의해 반환 된 데이터의 양 (`LIMIT`를 통해 제어 될 수 있는 데이터의 양)에 비례하는 시간에 실행된다는 의미에서 예측 가능한 성능을 가집니다.

The `ALLOW FILTERING` option allows to explicitly allow (some) queries that require filtering. Please note that a query using `ALLOW FILTERING` may thus have unpredictable performance (for the definition above), i.e. even a query that selects a handful of records **may** exhibit performance that depends on the total amount of data stored in the cluster.

For instance, considering the following table holding user profiles with their year of birth (with a secondary index on it) and country of residence:

```CQL
CREATE TABLE users (
    username text PRIMARY KEY,
    firstname text,
    lastname text,
    birth_year int,
    country text
)

CREATE INDEX ON users(birth_year);
```

Then the following queries are valid:

```CQL
SELECT * FROM users;
SELECT * FROM users WHERE birth_year = 1981;
```

왜냐하면 두 경우 모두 Cassandra는 이러한 쿼리 성능이 반환되는 데이터의 양에 비례한다는 것을 보장하기 때문입니다. 특히 1981년에 사용자가 태어나지 않으면 두 번째 쿼리 성능은 데이터베이스에 저장된 사용자 프로필 수와 관련이 없습니다 (적어도 2 차 인덱스 구현 고려로 인해 이 쿼리는 여전히 숫자에 따라 달라질 수 있음) 저장되는 데이터의 양에 간접적으로 의존하는 클러스터의 노드의 수를 나타냅니다. 그럼에도 불구하고 노드 수는 항상 저장되는 사용자 프로필 수보다 더 낮은 수의 크기입니다. 물론, 두 쿼리 모두 실제로 매우 큰 결과 세트를 반환 할 수 있지만 반환되는 데이터의 양은 항상 `LIMIT`을 추가하여 제어 할 수 있습니다.

However, the following query will be rejected:

```CQL
SELECT * FROM users WHERE birth_year = 1981 AND country = 'FR';
```

왜냐하면 카산드라는 쿼리에 대한 결과가 작더라도 많은 양의 데이터를 스캔 할 필요가 없다는 것을 보장 할 수 없기 때문입니다. 일반적으로 프랑스 출신 인 경우도 1981 년 출생 한 사용자의 모든 색인 항목을 검사합니다. 그러나, "당신이 하고 있는 일을 안다"면, 당신은`ALLOW FILTERING`을 사용하여이 쿼리의 실행을 강제 할 수 있고 따라서 다음 쿼리가 유효합니다:

```cql
SELECT * FROM users WHERE birth_year = 1981 AND country = 'FR' ALLOW FILTERING;
```

## INSERT

Inserting data for a row is done using an `INSERT` statement:

```
insert_statement ::=  INSERT INTO table_name ( names_values | json_clause )
                      [ IF NOT EXISTS ]
                      [ USING update_parameter ( AND update_parameter )* ]
names_values     ::=  names VALUES tuple_literal
json_clause      ::=  JSON string [ DEFAULT ( NULL | UNSET ) ]
names            ::=  '(' column_name ( ',' column_name )* ')'
```

For instance:

```CQL
INSERT INTO NerdMovies (movie, director, main_actor, year)
                VALUES ('Serenity', 'Joss Whedon', 'Nathan Fillion', 2005)
      USING TTL 86400;

INSERT INTO NerdMovies JSON '{"movie": "Serenity",
                              "director": "Joss Whedon",
                              "year": 2005}';
```

`INSERT` 문은 테이블의 주어진 행에 대해 하나 이상의 열을 씁니다. 행이 `PRIMARY KEY`에 의해 식별되기 때문에 적어도 그것을 구성하는 열을 지정 해야 합니다. `VALUES` 구문을 사용할 때 삽입 할 열 목록을 제공해야 합니다. `JSON` 구문을 사용할 때는 선택 사항입니다. 자세한 내용은 [`JSON` 지원](http://cassandra.apache.org/doc/latest/cql/json.html#cql-json) 섹션을 참조하십시오.

SQL과 달리 `INSERT`는 기본적으로 행의 이전 존재를 검사하지 않습니다. 이전에 존재하지 않으면 행이 만들어지고 그렇지 않으면 행이 업데이트됩니다. 또한 생성 또는 업데이트 중 어떤 것이 발생했는지 알 수 있는 방법이 없습니다.

그러나 `IF NOT EXISTS` 조건을 사용하여 행이 삽입 전에 존재하지 않는 경우에만 삽입 할 수 있습니다. 그러나 `IF NOT EXISTS`을 사용하면 무시할 수없는 성능 비용 (내부적으로는 Paxos가 사용됨)이 발생하므로 주의해서 사용해야합니다.

`INSERT`에 대한 모든 업데이트는 원자적이고 분리되어 적용됩니다.

[update_parameter](http://cassandra.apache.org/doc/latest/cql/dml.html#update-parameters)에 대한 정보는 [`UPDATE`](http://cassandra.apache.org/doc/latest/cql/dml.html#grammar-token-update_parameter) 섹션을 참조하십시오.

또한 `INSERT`는 카운터를 지원하지 않지만 `UPDATE`는 카운터를 지원하지 않음에 유의하십시오.

## UPDATE

행 업데이트는 `UPDATE` 문을 사용하여 수행됩니다.

```
update_statement ::=  UPDATE table_name
                      [ USING update_parameter ( AND update_parameter )* ]
                      SET assignment ( ',' assignment )*
                      WHERE where_clause
                      [ IF ( EXISTS | condition ( AND condition )*) ]
update_parameter ::=  ( TIMESTAMP | TTL ) ( integer | bind_marker )
assignment       ::=  simple_selection '=' term
                     | column_name '=' column_name ( '+' | '-' ) term
                     | column_name '=' list_literal '+' column_name
simple_selection ::=  column_name
                     | column_name '[' term ']'
                     | column_name '.' `field_name
condition        ::=  simple_selection operator term
```

For instance:

```CQL
UPDATE NerdMovies USING TTL 400
   SET director   = 'Joss Whedon',
       main_actor = 'Nathan Fillion',
       year       = 2005
 WHERE movie = 'Serenity';

UPDATE UserActions
   SET total = total + 2
   WHERE user = B70DE1D0-9908-4AE3-BE34-5573E5B09F14
     AND action = 'click';
```

UPDATE 문은 테이블의 주어진 행에 대해 하나 이상의 열을 씁니다. [`where_clause`](http://cassandra.apache.org/doc/latest/cql/dml.html#grammar-token-where_clause)는 업데이트 할 행을 선택하는 데 사용되며 `PRIMARY KEY`를 구성하는 모든 열을 포함해야 합니다. 비 기본 키 열은 `SET` 키워드를 사용하여 설정됩니다.

`SQL`과 달리 `UPDATE`는 기본적으로 행의 이전 존재를 검사하지 않습니다 (아래 `IF`제외). 이전에 존재하지 않으면 행이 만들어지고 그렇지 않으면 행이 업데이트 됩니다. 또한 생성 또는 업데이트가 발생했는지 여부를 알 수있는 방법이 없습니다.

그러나 `IF`를 통해 일부 열의 조건을 사용할 수 있습니다.이 경우 조건이 충족되지 않으면 행이 업데이트 되지 않습니다. 그러나 `IF` 조건을 사용하면 무시할 수 없을 정도의 성능 비용을 발생 시킨다는 유의하십시오 (내부적으로 Paxos가 사용됨).

`UPDATE` 문에서 동일한 파티션 키 내의 모든 업데이트는 원자적이고 분리되어 적용됩니다.

Regarding the [`assignment`](http://cassandra.apache.org/doc/latest/cql/dml.html#grammar-token-assignment):

- `c = c + 3` is used to increment/decrement counters. The column name after the ‘=’ sign **must** be the same than the one before the ‘=’ sign. Note that increment/decrement is only allowed on counters, and are the *only* update operations allowed on counters. See the section on [counters](http://cassandra.apache.org/doc/latest/cql/types.html#counters) for details.
- `id = id + <some-collection>` and `id[value1] = value2` are for collections, see the [relevant section](http://cassandra.apache.org/doc/latest/cql/types.html#collections) for details.
- `id.field = 3` is for setting the value of a field on a non-frozen user-defined types. see the [relevant section](http://cassandra.apache.org/doc/latest/cql/types.html#udts) for details.

### Update parameters

The `UPDATE`, `INSERT` (and `DELETE` and `BATCH` for the `TIMESTAMP`) statements support the following parameters:

- `TIMESTAMP`: 작업의 타임 스탬프를 설정합니다. 지정되지 않으면 코디네이터는 명령문 실행 시작시 현재 시간 (마이크로 초)을 시간 소인으로 사용합니다. 일반적으로 적절한 기본값입니다.
- `TTL`: 삽입 된 값에 대한 선택적 TTL (Time To Live)을 초 단위로 지정합니다. 설정하면 삽입 된 값이 지정된 시간 후에 데이터베이스에서 자동으로 제거됩니다. TTL은 삽입 된 값과 관련이 있으며 열 자체는 관련이 없습니다. 이것은 열의 후속 업데이트가 TTL을 재설정한다는 것을 의미합니다 (해당 업데이트에 지정된 TTL에 관계없이). 기본적으로 값은 만료되지 않습니다. TTL 0은 TTL이 없는 것과 같습니다. 테이블에 `default_time_to_live`가 있으면 TTL을 0으로 설정하면 삽입되거나 업데이트 된 값의 TTL이 제거됩니다. `null`의 TTL은 0의 TTL로 삽입하는 것과 같습니다.

## DELETE

Deleting rows or parts of rows uses the `DELETE` statement:

```
delete_statement ::=  DELETE [ simple_selection ( ',' simple_selection ) ]
                      FROM table_name
                      [ USING update_parameter ( AND update_parameter )* ]
                      WHERE where_clause
                      [ IF ( EXISTS | condition ( AND condition )*) ]
```

For instance:

```CQL
DELETE FROM NerdMovies USING TIMESTAMP 1240003134
 WHERE movie = 'Serenity';

DELETE phone FROM Users
 WHERE userid IN (C73DE1D3-AF08-40F3-B124-3FF3E5109F22, B70DE1D0-9908-4AE3-BE34-5573E5B09F14);
```

The `DELETE` statement deletes columns and rows. If column names are provided directly after the `DELETE` keyword, only those columns are deleted from the row indicated by the `WHERE` clause. Otherwise, whole rows are removed.

The `WHERE` clause specifies which rows are to be deleted. Multiple rows may be deleted with one statement by using an `IN` operator. A range of rows may be deleted using an inequality operator (such as `>=`).

`DELETE` supports the `TIMESTAMP` option with the same semantics as in [updates](http://cassandra.apache.org/doc/latest/cql/dml.html#update-parameters).

In a `DELETE` statement, all deletions within the same partition key are applied atomically and in isolation.

A `DELETE` operation can be conditional through the use of an `IF` clause, similar to `UPDATE` and `INSERT` statements. However, as with `INSERT` and `UPDATE` statements, this will incur a non-negligible performance cost (internally, Paxos will be used) and so should be used sparingly.

## BATCH

Multiple `INSERT`, `UPDATE` and `DELETE` can be executed in a single statement by grouping them through a `BATCH` statement:

```
batch_statement        ::=  BEGIN [ UNLOGGED | COUNTER ] BATCH
                            [ USING update_parameter ( AND update_parameter )* ]
                            modification_statement ( ';' modification_statement )*
                            APPLY BATCH
modification_statement ::=  insert_statement | update_statement | delete_statement
```

For instance:

```CQL
BEGIN BATCH
   INSERT INTO users (userid, password, name) VALUES ('user2', 'ch@ngem3b', 'second user');
   UPDATE users SET password = 'ps22dhds' WHERE userid = 'user3';
   INSERT INTO users (userid, password) VALUES ('user4', 'ch@ngem3c');
   DELETE name FROM users WHERE userid = 'user1';
APPLY BATCH;
```

The `BATCH` statement group multiple modification statements (insertions/updates and deletions) into a single statement. It serves several purposes:

- 여러 업데이트를 일괄 처리 할 때 클라이언트와 서버 간 (때로는 서버 코디네이터와 복제본 간) 네트워크 왕복을 절약합니다.
- 주어진 파티션 키에 속한 `BATCH`의 모든 갱신은 고립되어 수행됩니다.
- 기본적으로 배치의 모든 작업은 * logged *로 수행되므로 모든 변이가 결국 완료됩니다 (또는 전혀 수행되지 않습니다). 자세한 내용은 [UNLOGGED batch](http://cassandra.apache.org/doc/latest/cql/dml.html#unlogged-batches)의 참고 사항을 참조하십시오.

Note that:

- `BATCH` 문은 `UPDATE`,`INSERT` 및 `DELETE` 문만 포함 할 수 있습니다 (예를 들어 그 외에는 배치할 수 없습니다).
- 배치는 SQL 트랜잭션을 위한 완전한 아날로그가 아닙니다.
- 각 작업에 대해 timestamp를가 지정되지 않으면 모든 작업이 동일한 timestamp를(자동으로 생성 된 timestamp를 또는 배치 수준에서 제공된 timestamp를)와 함께 적용됩니다. timestamp를 관계의 경우 Cassandra의 충돌 해결 절차로 인해 작업은 `BATCH`문에 나열된 순서와 다른 순서로 적용될 수 있습니다. 특정 조작 순서를 강제 실행하려면 조작 당 timestamp를 지정해야 합니다.
- 단일 파티션에 대한 LOGGED 배치는 UNLOGGED 배치로 최적화로 변환됩니다.

### `UNLOGGED` batches

기본적으로 Cassandra는 배치 처리 로그를 사용하여 배치 처리의 모든 작업이 결국 완료되거나 전혀 수행되지 않도록합니다. 단, 작업은 단일 파티션에서만 격리됩니다. 배치가 여러 파티션에 걸쳐있을 때 배치 원자성에 대한 성능 저하가 있습니다. 이 페널티를 원하지 않는다면 Cassandra에게 `UNLOGGED` 옵션으로 배치 로그를 건너 뛰라고 말할 수 있습니다. `UNLOGGED` 옵션을 사용하면, 실패한 배치는 부분적으로만 패치를 적용 할 수 있습니다.

### `COUNTER` batches

배치 카운터 업데이트에는`COUNTER` 옵션을 사용하십시오. Cassandra의 다른 업데이트와 달리 카운터 업데이트는 멱등(idempotent)이 아닙니다.

