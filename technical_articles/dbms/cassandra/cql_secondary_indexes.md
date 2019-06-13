# Secondary Indexes

CQL supports creating secondary indexes on tables, allowing queries on the table to use those indexes. A secondary index is identified by a name defined by:

```
index_name ::=  re('[a-zA-Z_0-9]+')
```

## CREATE INDEX

Creating a secondary index on a table uses the `CREATE INDEX` statement:

```
create_index_statement ::=  CREATE [ CUSTOM ] INDEX [ IF NOT EXISTS ] [ index_name ]
                                ON table_name '(' index_identifier ')'
                                [ USING string [ WITH OPTIONS = map_literal ] ]
index_identifier       ::=  column_name
                           | ( KEYS | VALUES | ENTRIES | FULL ) '(' column_name ')'
```

For instance:

```CQL
CREATE INDEX userIndex ON NerdMovies (user);
CREATE INDEX ON Mutants (abilityId);
CREATE INDEX ON users (keys(favs));
CREATE CUSTOM INDEX ON users (email) USING 'path.to.the.IndexClass';
CREATE CUSTOM INDEX ON users (email) USING 'path.to.the.IndexClass' WITH OPTIONS = {'storage': '/mnt/ssd/indexes/'};
```

The `CREATE INDEX` statement is used to create a new (automatic) secondary index for a given (existing) column in a given table. A name for the index itself can be specified before the `ON` keyword, if desired. If data already exists for the column, it will be indexed asynchronously. After the index is created, new data for the column is indexed automatically at insertion time.

Attempting to create an already existing index will return an error unless the `IF NOT EXISTS` option is used. If it is used, the statement will be a no-op if the index already exists.

### Indexes on Map Keys

[maps](http://cassandra.apache.org/doc/latest/cql/types.html#maps)에 인덱스를 만들 때 키 또는 값을 인덱싱을 할 수 있습니다. 컬럼 식별자가 `keys()` 함수 안에 있다면, 인덱스는 맵 키에 있게 되어 `WHERE` 절에서 `CONTAINS KEY`를 사용할 수 있습니다. 그렇지 않으면 인덱스가 맵 값에 있습니다.

## DROP INDEX

Dropping a secondary index uses the `DROP INDEX` statement:

```
drop_index_statement ::=  DROP INDEX [ IF EXISTS ] index_name
```

The `DROP INDEX` statement is used to drop an existing secondary index. The argument of the statement is the index name, which may optionally specify the keyspace of the index.

If the index does not exists, the statement will return an error, unless `IF EXISTS` is used in which case the operation is a no-op.

