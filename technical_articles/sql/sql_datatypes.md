[Up](./index.md)

## 데이터 타입

### 날짜 타입

###### MySQL:

- DATE: YYYY-MM-DD
- DATETIME: YYYY-MM-DD HH:MI:SS
- TIMESTAMP: YYYY-MM-DD HH:MI:SS
- YEAR: YYYY 또는 YY

###### SQL Server:

- DATE: YYYY-MM-DD
- DATETIME: YYYY-MM-DD HH:MI:SS
- SMALLDATETIME: YYYY-MM-DD HH:MI:SS
- TIMESTAMP: 유니크한 숫자.

---

### MySQL 데이터 타입:

#### MySQL 문자열 데이터 타입:

- `CHAR(size)`: 고정길이 문자열, 최대 255 문자.
- `VARCHAR(size)`: 가변길이 문자열, 최대 255 문자. 255 문자를 초과하면 `TEXT` 타입이 됩니다.
- `TINYTEXT`: 최대 255 문자의 문자열.
- `TEXT`: 최대 65,535 문자의 문자열.
- `BLOB`: 최대 65,535 바이트의 데이터.
- `MEDIUMTEXT`: 최대 16,777,215 문자의 문자열.
- `MEDIUMBLOB`: 최대 16,777,215 바이트의 데이터.
- `LONGTEXT`: 최대 4,294,967,295 문자의 문자열.
- `LONGBLOB`: 최대 4,294,967,295 바이트의 데이터.
- `ENUM(x,y,z,etc.)`: 최대 65,535 개의 값 리스트.
- `SET`: 문자열 목록으로 64개의 서로 다른 멤버를 가질 수 있으며, 각각은 `,`으로 구분됩니다.

#### MySQL 숫자 데이터 타입:
- `TINYINT(size)`: -128 ~ 127.
- `UNSIGNED TINYINT(size)`: 0 ~ 255.
- `SMALLINT(size)`: -32,768 ~ 32,767.
- `UNSIGNED SMALLINT(size)`: 0 ~ 65,535.
- `MEDIUMINT(size)`: -8,388,608 ~ 8,388,607.
- `UNSIGNED MEDIUMINT(size)`: 0 ~ 16777215.
- `INT(size)`: -2,147,483,648 ~ 2,147,483,647.
- `UNSIGNED INT(size)`: 0 ~ 4,294,967,295.
- `BIGINT(size)`: -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807.
- `UNSIGNED BIGINT(size)`: 0 ~  18,446,744,073,709,551,615.
- `FLOAT(size,d)`: 단정도형.
- `DOUBLE(size,d)`: 배정도형.
- `DECIMAL(size,d)`: 문자열로 저장된 `DECIMAL`.

#### MySQL 날짜 데이터 타입:

- `DATE()`: YYYY-MM-DD.
- `DATETIME()`: YYYY-MM-DD HH:MI:SS.
- `TIMESTAMP()`: 초단위로 저장. YYYY-MM-DD HH:MI:SS (UTC 1970-01-01 00:00:01 부터 2038-01-09 03:14:07 까지)
- `TIME()`: HH:MI:SS.
- `YEAR()`: YYYY 또는 YY.

#### SQL Server 데이터 타입.

##### SQL Server 문자열 데이터 타입.

- `char(n)`: 고정길이 문자열, 최대 8,000 문자.
- `varchar(n)`: 가변길이 문자열, 최대 8,000 문자.
- `varchar(max)`: 가변길이 문자열, 최대 1,073,741,824 문자.
- `text`: 가변 길이 문자열, 최대 2G 텍스트 데이터.
- `nchar`: 고정길이 유니코드 문자열, 최대 4,000 문자.
- `nvarchar`: 가변길이 유니코드 문자열, 최대 4,000 문자.
- `nvarchar(max)`: 가변길이 유니코드 문자열, 536,870,912 문자.
- `ntext`: 가변 길이 유니코드 문자열. 최대 2GB 텍스트 데이터.
- `binary(n)`: 고정길이 바이너리 문자열. 8,000 바이트.
- `varbinary`: 가변길이 바이너리 문자열. 8,000 바이트.
- `varbinary(max)`: 가변길이 바이너리 문자열. 최대 2GB.
- `image`: 가변 길이 바이너리 문자열. 최대 2GB.

##### SQL Server 숫자 데이터 타입.

- `bit`: 0, 1 또는 `NULL`을 갖는 정수. 1 byte.
- `tinyint`: 0 ~ 255 범위의 숫자. 1 byte.
- `smallint`: -32,768 ~ 32,767 범위의 숫자. 2 bytes.
- `int`: -2,147,483,648 ~ 2,147,483,647. 4 bytes.
- `bigint`: -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807. 8 bytes.
- `decimal(p, s)`: 고정소수점. 실수의 값을 정확하기 표현하기 위해 사용. 소수부의 자릿수를 미리 정해두고 고정된 자릿수로만 소수 부분을 표현. 범위는 $-10^{38}$ ~ $10^{37}$. `p`는 최대 자리수이며 1~38이며 기본은 18. `s`는 소수점 아래 자리수를 지정하며 0부터 `p`까지이며 기본은 0.
- `smallmoney`:  -214,748.3648 ~ 214,748.3647 의 금전. 4 바이트.
- `money`: -922,337,203,685,477.5808 to 922,337,203,685,477.5807의 금전. 8 바이트.
- `float(n)`: $-1.79^{308}$ ~ $1.79^{308}$. n이 24이면 4 바이트, n이 25~53이면 8 바이트.
- `real`: $-3.40^{38}$ ~ $3.40^{38}$. 4바이트.

#### SQL Server 날짜 데이터 타입.

- `datetime`: 1월 1일부터 12월 31일까지, 9999. 3.33 밀리초 정밀도. 8바이트.
- `datetime2`: 1월 1일부터 12월 31일까지, 9999. 100 나노초 정밀도. 8바이트.
- `smalldatetime`: 1900년 1월 1일부터 2079년 유월 6일까지. 1분 정밀도. 4바이트.
- `date`: 날짜만 저장. 0001년 1월 1일부터 9999년 12월 31일까지.
- `time`: 시간만 저장. 100 나노초 정밀도.
- `datetimeoffset`: `datetime2`와 동일하며 타임존 옵셋이 추가됨. 8~10바이트.
- `timestamp`: 행이 추가되거나 변경될때 유일한 숫자를 저장.

#### SQL Server 기타 데이터 타입.

- `sql_variant`: 최대 8,000 바이트의 various data types, except text, `ntext` 및 `timestamp`를 저장.
- `uniqueidentifier`: GUID를 저장.
- `xml`: XML 형식의 데이터를 저장. 최대 2GB.
- `cursor`: 데이터베이서 조작시 커서 참조를 저장.
- `table`: 나중의 처리 결과 집합을 저장.

#### Access 데이터 타입.

- `Text`: 텍스트 또는 텍스트와 숫자의 결합을 저장. 최대 255 문자.
- `Memo`: 65,536 길이의 문자를 저장. 정렬 불가능.
- `Byte`: 0 ~ 255 범위의 숫자.
- `Integer`: -32,768 ~ 32,767 범위의 숫자.
- `Long`: -2,147,483,648 ~ 2,147,483,647 범위의 숫자.
- `Singl`e: 단정도 부동소수점.
- `Double`: 배정도 부동소수점.
- `Currency`: 최대 15자리의 금전에 4자리의 자리수. 8바이트. 각 국가별 화폐를 저장할때 유용.
- `AutoNumber`: 1부터 시작하는 고유한 숫자를 자동으로 주어진다.
- `Date` / `Time`:
- `Yes` / `No`:
- `Ole Object`:
- `Hyperlink`:
- `Lookup Wizard`:

