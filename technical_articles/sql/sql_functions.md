[Up](./index.md)

## 함수

##### `MIN()` 함수 구문

선택된 컬럼에서 가장 작은 값을 반환 합니다.

```sql
SELECT MIN(column_name)
FROM table_name
WHERE condition;
```

##### `MAX()` 구문

선택된 컬럼에서 가장 큰 값을 반환 합니다.

```sql
SELECT MAX(column_name)
FROM table_name
WHERE condition;
```

### `COUNT()`, `AVG()`, `SUM()` 함수

##### `COUNT()` 구문

지정한 컬럼의 레코드 수량을 반환합니다.

```sql
SELECT COUNT(column_name)
FROM table_name
WHERE condition;
```

##### `AVG()` 구문

지정한 숫자 컬럼의 평균을 반환합니다.

```sql
SELECT AVG(column_name)
FROM table_name
WHERE condition;
```

### `SUM()` 구문

지정한 숫자 컬럼의 합을 반환합니다.

```sql
SELECT SUM(column_name)
FROM table_name
WHERE condition;
```

## SQL 내장 함수

### MySQL 내장함수

#### MySQL 문자열 관련 내장 함수

- `ASCII`: 지정한 문자의 ASCII를 반환.
- `CHAR_LENGTH`: 지정한 문자열의 길이를 반환.
- `CHARACER_LENGTH`: 지정한 문자열의 길이를 반환.
- `CONCAT`: 두개 이상의 식을 연결.
- `CONCAT_WS`: 두개 이상의 식을 연결하고 그것을 추가.
- `FIELD`: 목록의 값들에서 해당 값의 위치를 반환.
- `FIND_IN_SET`: `SET`에서 주어진 문자열의 위치를 반환.
- `FORMAT`: `#,###.##`으로 포맷.
- `INSERT`: 문자열에 서브문자열을 삽입.
- `INSTR`: 문자열에서 다른 문자열이 처음 나타나는 위치를 반환.
- `LCASE`: 소문자로 변환.
- `LEFT`: 문자열을 좌측을 기준으로 주어진 길이만큼 추출.
- `LENGTH`: 주어진 문자열의 길이를 바이트 단위로 반환.
- `LOCATE`: 문자열에서 다른 문자열이 처음 나타나는 위치를 반환.
- `LOWER`: 문자열을 소문자로 변환.
- `LPAD`: 문자열의 좌측에 주어진 문자 및 주어진 길이로 패딩.
- `LTRIM`: 문자열의 좌측의 공백 문자를 제거.
- `MID`: 문자열에서 하위 문자열을 추출.
- `POSITION`: 문자열에서 부분 문자열의 위치를 반환.
- `PREEAT`: 주어진 문자열을 주어진 횟수만큼 반복 연결.
- `REPLACE`: 문자열에서 주어진 다른 문자열을 대치.
- `REVERSE`: 문자열의 순서를 뒤집음.
- `RIGHT`: 문자열의 우측을 기준으로 주어진 길이 만큼 추출.
- `RPAD`: 문자열의 우측에 주어진 문자 및 주어진 길이로 패딩.
- `RTRIM`: 문자열의 우측의 공백 문자를 제거.
- `SPACE`: 지정한 길이의 공백 문자열을 반환.
- `STRCMP`: 두개의 문자열을 비교.
- `SUBSTR`: 문자열의 어느 위치라도 지정하여 부분 문자열을 추출.
- `SUBSTRING`: 문자열의 어느 위치라도 지정하여 부분 문자열을 추출.
- `SUBSTRING_INDEX`: 구분 기호 이전의 문자열의 부분 문자열을 반환.
- `TRIM`: 문자열의 시작과 끝에서 공백 문자를 제거.
- `UCASE`: 문자열을 대문자로 변환.
- `UPPER`: 문자열을 대문자로 변환.

#### MySQL 숫자 관련 내장 함수

- `ABS`:
- `ACOS`:
- `ASIN`:
- `ATAN`:
- `ATNA2`:
- `AVG`:
- `CEIL`:
- `CEILING`:
- `COS`:
- `COT`:
- `COUNT`:
- `DEGREES`:
- `DIV`:
- `EXP`:
- `FLOOR`:
- `GREATEST`:
- `LEAST`:
- `LN`:
- `LOG`:
- `LOG10`:
- `LOG2`:
- `MAX`:
- `MIN`:
- `MOD`:
- `PI`:
- `POW`:
- `POWER`:
- `RADIANS`:
- `RAND`:
- `ROUND`:
- `SIGN`:
- `SIN`:
- `SQRT`:
- `SUM`:
- `TAN`:
- `TRUNCATE`:

#### MySQL 날짜 관련 함수

- `ADDDATE`:
- `ADDTIME`:
- `CURDATE`:
- `CURRENT_DATE`:
- `CURRENT_TIME`:
- `CURRENT_TIMESTAMP`:
- `CURTIME`:
- `DATE`:
- `DATEDIFF`:
- `DATE_ADD`:
- `DATE_FORMAT`:
- `DATE_SUB`:
- `DAY`:
- `DAYNAME`:
- `DAYOFMONTH`:
- `DAYOFWEEK`:
- `DAYOFYEAR`:
- `EXTRACT`:
- `FROM_DAYS`:
- `HOUR`:
- `LAS_DAY`:
- `LOCALTIME`:
- `LOCALTIMESTAMP`:
- `MAKEDATE`:
- `MAKETIME`:
- `MICROSECONDS`:
- `MINUTE`:
- `MONTH`:
- `MONTHNAME`:
- `NOW`:
- `PERIOD_ADD`:
- `PERIOD_DIFF`:
- `QUARTER`:
- `SECOND`:
- `SEC_TO_TIME`:
- `STR_TO_DATE`:
- `SUBDATE`:
- `SUBTIME`:
- `TIME`:
- `TIME_FORMAT`:
- `TIME_TO_SEC`:
- `TIMEDIFF`:
- `TIMESTAMP`:
- `TO_DAYS`:
- `WEEK`:
- `WEEKDAY`:
- `WEEKOFYEAR`:
- `YEAR`:
- `YEARWEEK`:

#### MySQL 고급 함수

- `BIN`:
- `BINARY`:
- `CASE`:
- `CAST`:
- `COALESCE`:
- `CONNECTION_ID`:
- `CONV`:
- `CONVERT`:
- `CURRENT_USER`:
- `DATABASE`:
- `IF`:
- `IFNULL`:
- `ISNULL`:
- `LAST_INSERT_ID`:
- `NULLIF`:
- `SESSION_USER`:
- `SYSTEM_USER`:
- `USER`:
- `VERSION`:

### SQL Server 함수

#### SQL Server 문자열 관련 함수.

- `ASCII`:
- `CHAR`:
- `CHARINDEX`:
- `CONCAT`:
- `concat with +`:
- `DATELENGTH`:
- `LEFT`:
- `LEN`:
- `LOWER`:
- `LTRIM`:
- `NCHAR`:
- `PAINDEX`:
- `REPLACE`:
- `RIGHT`:
- `RTRIM`:
- `SPACE`:
- `STR`:
- `STUFF`:
- `SUBSTRING`:
- `UPPER`:

#### SQL Server 숫자 관련 함수.

- `ABS`:
- `AVG`:
- `CEILNG`:
- `COUNT`:
- `FLOOR`:
- `MAX`:
- `MIN`:
- `RAND`:
- `ROUND`:
- `SIGN`:
- `SUM`:

#### SQL Server 날짜 관련 함수.

- `CURRENT_TIMESTAMP`:
- `DATEADD`:
- `DATEDIFF`:
- `DATENAME`:
- `DATEPART`:
- `DAY`:
- `GETDATE`:
- `GETUTCDATE`:
- `MONTH`:
- `YEAR`:

#### SQL Server 변환 함수.

- `CAST`:
- `CONVERT`:

#### SQL Server 고급 함수. 

- `COALESCE`:
- `CURRENT_USER`:
- `ISDATe`:
- `ISNULL`:
- `ISNUMERIC`:
- `NULLIF`:
- `SESSION_USER`:
- `SESSIONPROPERTY`:
- `SYSTEM_USER`:
- `USER_NAME`:

### Oracle 함수

#### Oracle 문자열 함수.

- `ASCII`:
- `ASCIISTR`:
- `CHR`:
- `COMPOSE`:
- `CONCAT`:
- `||`:
- `CONVERT`:
- `DECOMPOSE`:
- `DUMP`:
- `INITCAP`:
- `INSTR`:
- `INSTR2`:
- `INSTR4`:
- `INSTRB`:
- `INSTRC`:
- `LENGTH`:
- `LENGTH2`:
- `LENGTH4`:
- `LENGTHB`:
- `LENGTHC`:
- `LOWER`:
- `LPAD`:
- `LTRIM`:
- `NCHR`:
- `REGEXP_INSTR`:
- `REGEXP_REPLACE`:
- `REGEXP_SUBSTR`:
- `REPLACE`:
- `RPAD`:
- `RTRIM`:
- `SOUNDEX`:
- `SUBSTR`:
- `TRANSLATE`:
- `TRIM`:
- `UPPER`:
- `VSIZE`:
- `ABS`:
- `ACOS`:
- `ASIN`:
- `ATAN`:
- `ATAN2`:
- `AVG`:
- `BITAND`:
- `CEIL`:
- `COS`:
- `COSH`:
- `COUNT`:
- `EXP`:
- `FLOOR`:
- `GREATEST`:
- `LEAST`:
- `LN`:
- `LOG`:
- `MAX`:
- `MEDIAN`:
- `MIN`:
- `MOD`:
- `POWER`:
- `REGEXP_COUNT`:
- `REMAINDER`:
- `ROUND`:
- `ROWNUM`:
- `SIGN`:
- `SIN`:
- `SQRT`:
- `SUM`:
- `TAN`:
- `TANH`:
- `TRUNC`:

#### Oracle 날짜 함수.

- `ADD_MONTHS`:
- `CURRENT_DATE`:
- `CURRENT_TIMESTAMP`:
- `DBTIMEZONE`:
- `EXTRACT`:
- `LAST_DAY`:
- `LOCALTIMESTAMP`:
- `MONTH_BETWEEN`:
- `NEW_TIME`:
- `NEXT_DAY`:
- `ROUND`:
- `SESSIONTIMEZONE`:
- `SYSDATE`:
- `SYSTIMESTAMP`:
- `TRUNC`:
- `TZ_OFFSET`:

