# Oracle Sequence

시퀀스 생성

```sql
CREATE SEQUENCE <sequence_name>
	[INCREMENT BY <increment_vale>]
	[START WITH <starting_value>]
	[MAXVALUE <max_value> or NOMAXVALUE]
	[MINVALUE <min_value> or NOMINVALUE]
	[CYCLE or NOCYCLE]
	[CACHE or NOCACHE];
```

시퀀스 변경

```sql
ALTER SEQUENCE <sequence_name>
    [INCREMENT BY <increment_vale>]
    [MAXVALUE <max_value> or NOMAXVALUE]
    [MINVALUE <min_value> or NOMINVALUE]
    [CYCLE or NOCYCLE]
    [CACHE or NOCACHE];
```

시퀀스 삭제

```sql
DROP SEQUENCE <sequence_name>;
```

유저의 모든 시퀀스 조회

```sql
SELECT SEQUENCE_NAME, MIN_VALUE, MAX_VALUE, INCREMENT_BY, CYCLE_FLAG
	FROM USER_SEQUENCES;
```

시퀀스 조회 및 카운트

```sql
<sequence_name>.NEXTVAL
```

일반적으로 `INSERT`에서 사용되므로 예를 들면

```sql
INSERT INTO COLUMES ... 
VALUES(<sequence_name>.NEXTVAL, ...)
```



