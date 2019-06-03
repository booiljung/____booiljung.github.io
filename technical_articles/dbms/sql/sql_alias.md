[Up](./index.md)

## 별칭

테이블이나 컬럼에 대해 별칭을 부여하며, 가독성을 위해 사용 되기도 하며, 쿼리 중에만 존재합니다.
복잡한 연산이 적용된 테이블이나 컬럼을 다룰때 유용합니다.

##### 별칭 컬럼 구문

선택된 컬럼에 대해 별칭을 부여 합니다.

```sql
SELECT column_name AS alias_name
FROM table_name;
```

#### 별칭 테이블 구문

선택된 테이블에 대해 별칭을 부여 합니다.

```sql
SELECT column_name(s)
FROM table_name AS alias_name;
```

