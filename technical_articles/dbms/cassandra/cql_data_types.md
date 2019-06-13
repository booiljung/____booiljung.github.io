# Cassandra Query Language

## 주석

```CQL
-- This is a comment
// This is a comment too
/* This is
   a multi-line comment */
```

## 데이터 타입

### 내장 타입

| 타입        | 상수지원        | 설명                                                         |
| ----------- | --------------- | ------------------------------------------------------------ |
| `ascii`     | string          | ASCII 문자열                                                 |
| `bigint`    | integer         | 64비트 부호있는 정수                                         |
| `blog`      | blob            | 임의 바이트들 (검증 없음)                                    |
| `boolean`   | boolean         | `true` 또는 `false`                                          |
| `counter`   | integer         | 카운터 열 (64 비트 부호있는 값). 자세한 내용은 counters를 참조하십시오. |
| `date`      | integer, string | 날짜 (해당 시간 값 없음). 자세한 내용은 아래 date 작업 하기를 참조하십시오. |
| `decimal`   | integer, float  | 가변 정밀도 10 진수                                          |
| `double`    | integer, float  | 64비트 IEEE-754 부동 소수점                                  |
| `duration`  | duration        | 나노초 정밀도의 지속 시간. 자세한 내용은 아래 작업 duration 작업을 참조하십시오. |
| `float`     | integer, float  | 32비트 IEEE-754 부동소수점                                   |
| `inet`      | string          | IP 주소, IPv4 (4 바이트 길이) 또는 IPv6 (16 바이트 길이). inet 상수가 없으므로 IP 주소는 문자열로 입력해야 합니다. |
| `int`       | integer         | 32 비트 부호 있는 정수                                       |
| `smallint`  | integer         | 16 비트 부호 있는 정수                                       |
| `text`      | string          | UTF8로 인코딩된 문자열                                       |
| `time`      | integer, string | 나노초 정밀도를 갖는 시간 (대응하는 날짜 값 없음). 자세한 내용은 아래 time 사용을 참조하십시오. |
| `timestamp` | integer, string | 밀리 초 정밀도를 갖는 타임 스탬프 (날짜 및 시간). 자세한 내용은 아래의 타임 스탬프 작업을 참조하십시오. |
| `timeuuid`  | uuid            | 버전 1 UUID, 일반적으로 "충돌없는"시간 소인으로 사용됩니다. Timeuuid 함수도 참조하십시오. |
| `tinyint`   | integer         | 8 비트 부호 있는 정수                                        |
| `uuid`      | uuid            | 모든 버전의 [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) |
| `varchar`   | string          | UTF8로 인코딩된 문자열                                       |
| `varint`    | integer         | 임의의 정밀도를 가지는 정수                                  |

### 카운터

`counter` 타입은 `counter` 컬럼을 정의하는데 사용 됩니다. 카운터 열은 값이 64 비트 부호 있는 정수이며 두 가지 연산이 지원됩니다 : 증가 및 감소 ([UPDATE 문](http://cassandra.apache.org/doc/latest/cql/ dml.html # update-statement) 참조구문을 참조하십시오). 카운터의 값은 설정할 수 없습니다. 카운터는 처음으로 증가/감소 할 때까지 존재하지 않으며 이전 값이 0 인 것처럼 첫 번째 증가/감소가 이루어집니다.

카운터에는 몇 가지 중요한 제한 사항이 있습니다:

- 테이블의 `PRIMARY KEY`컬럼 부분에는 사용할 수 없습니다.
- 카운터가 포함 된 테이블은 카운터만 포함 할 수 있습니다. 즉, `PRIMARY KEY` 외의 테이블의 모든 열은 `counter`타입만을 가지며, 그렇지 않으면 컬럼이 없습니다.
- 카운터는 만료를 지원하지 않습니다.
- 카운터 삭제는 지원되지만 카운터를 처음 삭제할 때만 작동합니다. 즉, 삭제한 카운터를 다시 업데이트하면 안됩니다 (만일 하게 된다면, 올바른 동작이 보장되지 않습니다).
- 카운터 업데이트는 기본적으로 [idemptotent](https://en.wikipedia.org/wiki/Idempotence)가 아닙니다. 중요한 변경 사항은 카운터 갱신이 예기치 않게 실패한 경우 (시간 초과 또는 코디네이터 노드로의 연결 유실), 클라이언트는 갱신 사항이 적용되었는지 여부를 알 수 없습니다. 특히 업데이트를 재생하면 초과 카운트가 발생할 수도 있고 그렇지 않을 수도 있습니다.

## 타임 스탬프

`timestamp` 타입의 값은 [the epoch](https://en.wikipedia.org/wiki/Unix_time) : 1970 년 1 월 1 일 00:00:00 GMT 이라고 알려진 표준 기본 시간 이후로 밀리 초를 나타내는 64 비트 부호 있는 정수로 인코딩됩니다.

타임 스탬프는 값을 [`integer`](http://cassandra.apache.org/doc/latest/cql/definitions.html#grammar-token-integer)로 사용하거나 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 날짜를 나타내는 [`string`](http://cassandra.apache.org/doc/latest/cql/definitions.html#grammar-token-string)을 사용하여 CQL에 입력 할 수 있습니다. 예를 들어, 아래의 모든 값은 2011년 3월 2일 오전 4시 05 분 (GMT)의 유효한 `timestamp`값 입니다.

- `1299038700000`
- `'2011-02-03 04:05+0000'`
- `'2011-02-03 04:05:00+0000'`
- `'2011-02-03 04:05:00.000+0000'`
- `'2011-02-03T04:05+0000'`
- `'2011-02-03T04:05:00+0000'`
- `'2011-02-03T04:05:00.000+0000'`

위의 `+0000`은 RFC 822 4 자리 표준 시간대 사양입니다. `+0000`은 GMT를 나타냅니다. 미국 태평양 표준시는 `-0800`입니다. 원하는 경우 표준 시간대가 생략 될 수 있으며 (`2011-02-03 04:05:00`), 그렇다면 조정되는 카산드라 노드가 구성되는 시간대로 해석됩니다. 그러나 예상대로 시간대 구성에 의존하는 데는 고유한 어려움이 있으므로 가능한 경우 시간대를 지정해야 합니다.

The time of day may also be omitted (`'2011-02-03'` or `'2011-02-03+0000'`), in which case the time of day will default to 00:00:00 in the specified or default time zone. However, if only the date part is relevant, consider using the [date](http://cassandra.apache.org/doc/latest/cql/types.html#dates) type.

하루 중의 시간을 생략 할 수도 있습니다 (`'2011-02-03'` 또는`'2011-02-03+0000'`). 이 경우 시간은 지정된 시간에 `00:00:00`으로 또는 기본 시간대로 기본 설정됩니다. 그러나 날짜 부분만 관련된 경우 [date](http://cassandra.apache.org/doc/latest/cql/types.html#dates) 타입을 사용해보십시오.

## 날짜 작업

Values of the `date` type are encoded as 32-bit unsigned integers representing a number of days with “the epoch” at the center of the range (2^31). Epoch is January 1st, 1970

As for [timestamp](http://cassandra.apache.org/doc/latest/cql/types.html#timestamps), a date can be input either as an [`integer`](http://cassandra.apache.org/doc/latest/cql/definitions.html#grammar-token-integer) or using a date [`string`](http://cassandra.apache.org/doc/latest/cql/definitions.html#grammar-token-string). In the later case, the format should be `yyyy-mm-dd` (so `'2011-02-03'` for instance).

`date`  타입의 값은 범위의 중심 $2^{31}$의 시작점이 있는 일 수를 나타내는 32 비트 부호 없는 정수로 인코딩 됩니다. 시작점은 1970 년 1 월 1 일입니다.

`timestamp`의 경우, 날짜는 `integer` 또는 날짜 `string`을 사용하여 입력 할 수 있습니다. 후자의 경우 포맷은 `yyyy-mm-dd`이어야합니다 (예를 들어`2011-02-03`).

## 시간 작업

`time` 타입의 값은 자정 이후 나노초의 수를 나타내는 64 비트 부호있는 정수로 인코딩됩니다.

`timestamp` 에 대해서, 시간은 `integer` 또는 시간을 나타내는 `string`을 사용하여 입력 할 수 있습니다. 나중의 경우, 포맷은 `hh:mm:ss[.fffffffff]`이어야 합니다. (초초 정밀도는 옵션이며, 제공된다면 나노초보다 작을 수 있다.) 예를 들어, 다음은 유효한 입력입니다.

- `'08:12:54'`
- `'08:12:54.123'`
- `'08:12:54.123456'`
- `'08:12:54.123456789'`

## 기간 작업

`duration` 타입의 값은 가변 길이의 3개의 부호있는 정수로 인코딩됩니다. 첫 번째 정수는 달의 수를, 두 번째는 일 수를, 세 번째 정수는 나노초의 수를 나타냅니다. 이는 한 달의 일 수가 변경 될 수 있고 일광 절약 시간에 따라 하루에 23 또는 25 시간이 걸릴 수 있기 때문입니다. 내부적으로 월 및 일 수는 32 비트 정수로 디코딩되지만 나노초 수는 64 비트 정수로 디코딩됩니다.

`duration`은 다음과 같이 입력 할 수 있습니다.

> 1. `(quantity unit)+`은 `12h30m`과 같습니다. 단위는 다음과 같습니다 :
>
>    > - `y`: 년수 (12 개월)
>    > - `mo`: 달수 (1 달)
>    > - `w`: 주수 (7 일)
>    > - `d`: 일수 (1 일)
>    > - `h`: 시수 (3,600,000,000,000 나노초)
>    > - `m`: 분수 (60,000,000,000 나노초)
>    > - `s`: 초수 (1,000,000,000 나노초)
>    > - `ms`: 밀리초수 (1,000,000 나노초)
>    > - `us` or `µs` : 마이크로초수 (1000 나노초)
>    > - `ns`: 나노초수 (1 나노초)
>
> 2. ISO 8601 포맷:  `P[n]Y[n]M[n]DT[n]H[n]M[n]S or P[n]W`
>
> 3. ISO 8601 대체 포맷: `P[YYYY]-[MM]-[DD]T[hh]:[mm]:[ss]`

예를들어:

```CQL
INSERT INTO RiderResults (rider, race, result) VALUES ('Christopher Froome', 'Tour de France', 89h4m48s);
INSERT INTO RiderResults (rider, race, result) VALUES ('BARDET Romain', 'Tour de France', PT89H8M53S);
INSERT INTO RiderResults (rider, race, result) VALUES ('QUINTANA Nairo', 'Tour de France', P0000-00-00T89:09:09);
```

기간 열은 테이블의 `PRIMARY KEY`에서 사용할 수 없습니다. 이 제한은 기간을 정렬 할 수 없기 때문에 발생합니다. `1mo`가 날짜 문맥 없이 `29d`보다 더 큰지를 아는 것은 실제로 불가능합니다.

`1d` 지속 시간은 일광 절약 시간을 지원할 수 있도록 지속 시간 유형이 생성되었으므로 `24h`와 동일하지 않습니다.

## 컬렉션

CQL은 Maps, Sets 및 List의 3 종류의 콜렉션을 지원합니다. 이러한 컬렉션의 타입은 다음과 같이 정의됩니다.

```CQL
collection_type ::=  MAP '<' cql_type ',' cql_type '>'
                     | SET '<' cql_type '>'
                     | LIST '<' cql_type '>'
```

그 값은 콜렉션 리터럴을 사용하여 입력 할 수 있습니다.

```
collection_literal ::=  map_literal | set_literal | list_literal
map_literal        ::=  '{' [ term ':' term (',' term : term)* ] '}'
set_literal        ::=  '{' [ term (',' term)* ] '}'
list_literal       ::=  '[' [ term (',' term)* ] ']'
```

그러나`bind_marker` 나 `NULL`도 콜렉션 리터럴 내에서 지원되지 않습니다.

### 주목할만한 특징

컬렉션은 상대적으로 적은 양의 데이터를 저장/역정규화하기 위한 것입니다. "주어진 사용자의 전화 번호", "전자 메일에 적용된 레이블"등과 같이 잘 작동하지만 항목이 무제한으로 늘어날 것으로 예상되는 경우 ( "사용자가 보낸 모든 메시지", 센서에서 등록한 이벤트 "...), 콜렉션은 적절하지 않으며 특정 테이블 (클러스터링 컬럼 포함)을 사용해야 합니다. 구체적으로 (고정되지 않은) 컬렉션에는 다음과 같은 주목할만한 특징과 제한이 있습니다.

- 개별 컬렉션은 내부적으로 인덱싱되지 않습니다. 즉, 콜렉션의 단일 요소에 액세스하는 경우에도 while 콜렉션을 읽어야합니다 (읽기 하나는 내부적으로 호출되지 않음).
- set과 map에 대한 삽입 작업은 내부적으로 쓰기 전 읽기를 발생시키지 않지만 list에 대한 일부 작업은 수행하지 않습니다. 또한 일부 list 작업은 본질적으로 멱등원이 아니며 (자세한 내용은 아래 lists 섹션 참조) 시간 초과 문제가 발생할 경우 다시 시도합니다. 따라서 가능한 경우 list보다 set를 선호하는 것이 좋습니다.

이러한 제한 중 일부는 나중에 제거되거나 개선되지 않을 수도 있지만 대용량 데이터를 저장하기 위해 (단일) 컬렉션을 사용하는 것은 안티패턴 입니다.

### 맵

`map`은 키 - 값 쌍의 (정렬 된) 집합입니다. 여기서 키는 고유하고 맵은 키에 의해 정렬됩니다. 다음을 사용하여 Map을 정의하고 삽입 할 수 있습니다.

```CQL
CREATE TABLE users (
    id text PRIMARY KEY,
    name text,
    favs map<text, text> // A map of text keys, and text values
);

INSERT INTO users (id, name, favs)
           VALUES ('jsmith', 'John Smith', { 'fruit' : 'Apple', 'band' : 'Beatles' });

// Replace the existing map entirely.
UPDATE users SET favs = { 'fruit' : 'Banana' } WHERE id = 'jsmith';
```

또한 맵은 다음을 지원합니다:

- 하나 이상의 요소 업데이트 또는 삽입:

  ```CQL
  UPDATE users SET favs['author'] = 'Ed Poe' WHERE id = 'jsmith';
  UPDATE users SET favs = favs + { 'movie' : 'Cassablanca', 'band' : 'ZZ Top' } WHERE id = 'jsmith';
  ```

- 하나 이상의 요소를 제거합니다 (요소가 없으면 제거하는 것은 아무 작업도 하지 않지만 오류는 발생하지 않습니다).

  ```CQL
  DELETE favs['author'] FROM users WHERE id = 'jsmith';
  UPDATE users SET favs = favs - { 'movie', 'band'} WHERE id = 'jsmith';
  ```

  `map`에서 여러 원소를 지우려면, 그것으로부터`set` 키를 제거하십시오.

마지막으로, TTL은 `INSERT`와 `UPDATE` 모두에 허용되지만, 두 경우 모두 TTL 세트는 새로 삽입/갱신 된 요소에만 적용됩니다. 다른 말로:

```CQL
UPDATE users USING TTL 10 SET favs['color'] = 'green' WHERE id = 'jsmith';
```

`{'color' : 'green'}`레코드에만 TTL을 적용하고 나머지 맵은 영향을받지 않습니다.

### 세트

`set`은 고유 값들의 (정렬 된) 콜렉션입니다. 다음을 사용하여 맵을 정의하고 삽입 할 수 있습니다.

```CQL
CREATE TABLE images (
    name text PRIMARY KEY,
    owner text,
    tags set<text> // A set of text values
);

INSERT INTO images (name, owner, tags)
            VALUES ('cat.jpg', 'jsmith', { 'pet', 'cute' });

// Replace the existing set entirely
UPDATE images SET tags = { 'kitten', 'cat', 'lol' } WHERE name = 'cat.jpg';
```

또한 세트는 다음을 지원합니다.

- 하나 또는 여러 개의 요소를 추가합니다 (집합이기 때문에 이미 존재하는 요소를 삽입하는 것은 아무 작업이 아닙니다).

  ```CQL
  UPDATE images SET tags = tags + { 'gray', 'cuddly' } WHERE name = 'cat.jpg';
  ```

- 하나 이상의 요소 제거 (요소가 없는 경우 요소를 제거하는 것은 아무 작업도하지 않지만 오류는 발생하지 않습니다).

  ```CQL
  UPDATE images SET tags = tags - { 'cat' } WHERE name = 'cat.jpg';
  ```

마지막으로 [maps](http://cassandra.apache.org/doc/latest/cql/types.html#maps)의 경우 TTL은 새로 삽입 된 값에만 적용됩니다.

### 리스트

노트

위에서 언급했듯이 이 절의 끝 부분에서 자세히 설명했듯이 리스트에는 사용하기 전에 고려해야 할 제한 사항과 특정 성능 고려 사항이 있습니다. 일반적으로 목록 대신 [set](http://cassandra.apache.org/doc/latest/cql/types.html#sets)을 사용할 수 있는 경우 항상 세트를 선호합니다.

`list`는 비고유 값의 (정렬 된) 콜렉션입니다. 여기서 요소는 리스트의 위치에 따라 정렬됩니다. 다음을 사용하여 목록을 정의하고 삽입 할 수 있습니다.

```CQL
CREATE TABLE plays (
    id text PRIMARY KEY,
    game text,
    players int,
    scores list<int> // A list of integers
)

INSERT INTO plays (id, game, players, scores)
           VALUES ('123-afde', 'quake', 3, [17, 4, 2]);

// Replace the existing list entirely
UPDATE plays SET scores = [ 3, 9, 4] WHERE id = '123-afde';
```

또한 리스트는 다음을 지원합니다.

- 리스트에 값 덧붙이기 및 추가:

  ```CQL
  UPDATE plays SET players = 5, scores = scores + [ 14, 21 ] WHERE id = '123-afde';
  UPDATE plays SET players = 6, scores = [ 3 ] + scores WHERE id = '123-afde';
  ```

- 리스트의 특정 위치에 값을 설정합니다. 이는 리스트에 해당 위치에 대한 기존 요소가 있거나 오류가 발생하여 리스트가 너무 작음을 의미합니다.

  ```CQL
  UPDATE plays SET scores[1] = 7 WHERE id = '123-afde';
  ```

- 리스트 내의 위치에 의해 요소를 삭제합니다. 이는 리스트에 해당 위치에 대한 기존 요소가 있거나 오류가 발생하여 리스트가 너무 작음을 의미합니다. 또한 작업에서 리스트에서 요소를 제거하면 리스트 크기가 1 씩 감소하여 삭제 된 요소 다음의 모든 요소의 위치가 변경됩니다.

  ```cql
  DELETE scores[1] FROM plays WHERE id = '123-afde';
  ```

- 목록에서 특정 값의 모든 항목을 삭제합니다 (특정 요소가 리스트에서 전혀 발생하지 않으면 단순히 무시되고 오류가 발생하지 않습니다).

  ```CQL
  UPDATE plays SET scores = scores - [ 12, 21 ] WHERE id = '123-afde';
  ```

경고

append와 prepend 연산은 본질적으로 멱등이 아닙니다. 따라서 특히 이러한 작업 시간 제한 중 하나가 초과되면 작업을 다시 시도하는 것이 안전하지 않으며 값을 두 번 붙임/추가 할 수도 있습니다.

경고

위치를 기준으로 요소를 설정 및 제거하고 특정 값의 발생을 제거하면 내부 *read-before-write*가 발생합니다. 따라서 더 천천히 실행되고 평상시의 업데이트보다 더 많은 자원을 필요로합니다 (조건부 작성을 제외하면 비용이 들게됩니다).

마지막으로 [maps](http://cassandra.apache.org/doc/latest/cql/types.html#maps)와 같이 TTL은 새로 삽입 된 값에만 적용됩니다.

## 사용자 정의 타입

CQL은 사용자 정의 타입 (UDT)의 정의를 지원합니다. 이러한 유형은 아래에 설명 된 `create_type_statement`, `alter_type_statement` 및 `drop_type_statement`를 사용하여 생성, 변경, 삭제 될 수 있습니다. 그러나 일단 만들어지면 UDT는 간단히 그 이름으로 불립니다.

```
user_defined_type ::=  udt_name
udt_name          ::=  [ keyspace_name '.' ] identifier
```

### UDT 생성

새로운 사용자 정의 타입의 생성은 다음에 의해 정의 된 `CREATE TYPE` 문을 사용하여 수행됩니다:

```
create_type_statement ::=  CREATE TYPE [ IF NOT EXISTS ] udt_name
                               '(' field_definition ( ',' field_definition )* ')'
field_definition      ::=  identifier cql_type
```

UDT는 이름 (해당 타입의 선언 된 열에 사용됨)을 가지며 이름 지정된 필드와 타입이 지정된 필드의 세트입니다. 필드 이름은 콜렉션 또는 다른 UDT를 포함하여 모든 타입이 될 수 있습니다. 예를 들면 :

```CQL
CREATE TYPE phone (
    country_code int,
    number text,
)

CREATE TYPE address (
    street text,
    city text,
    zip text,
    phones map<text, phone>
)

CREATE TABLE user (
    name text PRIMARY KEY,
    addresses map<text, frozen<address>>
)
```

노트:

- 이미 존재하는 타입을 만들려고 하면 `IF NOT EXISTS` 옵션을 사용하지 않으면 에러가 발생합니다. 이 매개 변수가 사용되면 해당 타입이 이미 존재하면 명령문은 no-op가 됩니다.
- 타입은 본질적으로 그것이 생성 된 키 공간에 바인드 되며 해당 키 공간에서만 사용할 수 있습니다. 생성시, 타입 이름 앞에 키 공간 이름이 있으면 해당 키 공간에 타입 이름이 작성됩니다. 그렇지 않으면, 현재 키 공간에 작성됩니다.
- Cassandra 4.0에서, UDT는 대부분의 경우 frozen되어야 하므로 위의 테이블 정의에서 `frozen<address>`를 사용해야합니다. 자세한 내용은 `frozen` 섹션을 참조하십시오.

### UDT 리터럴

일단 사용자 정의 타입이 생성되면, 값은 UDT 리터럴을 사용하여 입력 될 수 있습니다:

```
udt_literal ::=  '{' identifier ':' term ( ',' identifier ':' term )* '}'
```

즉, UDT 리터럴은 [map](http://cassandra.apache.org/doc/latest/cql/types.html#maps) 리터럴과 비슷하지만 해당 키는 해당 타입의 필드 이름입니다. 예를 들어, 다음을 사용하여 이전 섹션에서 정의한 테이블에 삽입 할 수 있습니다.

```CQL
INSERT INTO user (name, addresses)
          VALUES ('z3 Pr3z1den7', {
              'home' : {
                  street: '1600 Pennsylvania Ave NW',
                  city: 'Washington',
                  zip: '20500',
                  phones: { 'cell' : { country_code: 1, number: '202 456-1111' },
                            'landline' : { country_code: 1, number: '...' } }
              },
              'work' : {
                  street: '1600 Pennsylvania Ave NW',
                  city: 'Washington',
                  zip: '20500',
                  phones: { 'fax' : { country_code: 1, number: '...' } }
              }
          })
```

유효하기 위해서는 UDT 리터럴은 리터럴인 타입으로 정의 된 필드 만 포함해야 하지만 일부 필드는 생략 할 수 있습니다 (이 경우에는 'null'이됩니다).

### UDT 변경

기존의 사용자 정의 타입은 `ALTER TYPE` 문을 사용하여 수정할 수 있습니다 :

```
alter_type_statement    ::=  ALTER TYPE udt_name alter_type_modification
alter_type_modification ::=  ADD field_definition
                             | RENAME identifier TO identifier ( identifier TO identifier )*
```

할 수 있는 일 :

- 유형에 새 필드를 추가하십시오 (`ALTER TYPE address ADD country text`). 이 새로운 필드는 추가 전에 생성 된 모든 값에 대해 `null`이됩니다.
- 유형의 필드 이름을 바꿉니다 (`ALTER TYPE address RENAME zip TO zipcode`).

### UDT 제거

`DROP TYPE` 문을 사용하여 기존의 사용자 정의 유형을 삭제할 수 있습니다:

```
drop_type_statement ::=  DROP TYPE [ IF EXISTS ] udt_name
```

타입을 삭제하면 해당 타입이 즉시 되돌릴 수 없게 됩니다. 그러나 다른 타입, 테이블 또는 함수에서 계속 사용중인 타입을 삭제하려고하면 오류가 발생합니다.

`IF EXISTS`를 지시하지 않고 만약 삭제한 타입이 존재하지 않으면 에러가 반환되고, 이 경우 연산은 아무런 일도 일어나지 않습니다.

## 튜플

또한 CQL은 튜플 및 튜플 타입 (요소가 다른 타입이 될 수 있음)을 지원합니다. 기능상 튜플은 익명 필드가 있는 익명 UDT 일 수 있습니다. 튜플 타입과 튜플 리터럴은 다음과 같이 정의됩니다.

```
tuple_type    ::=  TUPLE '<' cql_type ( ',' cql_type )* '>'
tuple_literal ::=  '(' term ( ',' term )* ')'
```

다음과 같이 사용할 수 있습니다:

```CQL
CREATE TABLE durations (
    event text,
    duration tuple<int, text>,
)

INSERT INTO durations (event, duration) VALUES ('ev1', (3, 'hours'));
```

다른 "작성된" 타입 (콜렉션 및 UDT)과 달리, 튜플은 고정 된 키워드가 필요 없이 항상 고정되어 있으며 전체 튜플을 업데이트하지 않고 튜플의 일부 요소 만 업데이트 할 수는 없습니다. 또한 튜플 리터럴은 튜플 인 유형에서 선언 된 값과 동일한 수의 값을 항상 가져야 합니다 (일부 값은 null 일 수 있지만 명시적으로 그렇게 선언해야 합니다).

## 커스텀 타입

노트

사용자 정의 타입은 대부분 이전 버전과의 호환성을 위해 존재하며 사용법은 권장하지 않습니다. 그것들의 사용법은 사용자 친화적이지 않고 복잡 합니다. 그리고 다른 제공된 타입, 특히 사용자 정의 타입은 거의 항상 충분해야한다.

사용자 정의 타입은 다음에 의해 정의됩니다.

```
custom_type ::=  string
```

커스텀 타입은 서버 측 `AbstractType` 클래스를 확장하고 Cassandra에 의해로드 될 수있는 Java 클래스의 이름을 포함하는`string`입니다 (따라서 Cassandra를 실행하는 모든 노드의 `CLASSPATH`에 있어야합니다). 이 클래스는 유형에 유효한 값과 클러스터링 컬럼에 사용될 때 시간 정렬 방법을 정의합니다. 다른 목적을 위해, 커스텀 타입의 값은 `blob`의 값과 동일하며 특히`blob` 리터럴 구문을 사용하여 입력 할 수 있습니다.

## 참조

- [Apache: Cassandra: Data Types](http://cassandra.apache.org/doc/latest/cql/types.html)