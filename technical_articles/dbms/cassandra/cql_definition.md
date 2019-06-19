http://cassandra.apache.org/doc/latest/cql/definitions.html

# Definitions

## Conventions

CQL 구문을 지정하는 데 도움이 되도록이 문서에서 다음 규칙을 사용합니다.

- Language rules will be given in an informal [BNF variant](http://en.wikipedia.org/wiki/Backus–Naur_Form#Variants) notation. In particular, we’ll use square brakets (`[ item ]`) for optional items, `*` and `+` for repeated items (where `+` imply at least one).
- The grammar will also use the following convention for convenience: non-terminal term will be lowercase (and link to their definition) while terminal keywords will be provided “all caps”. Note however that keywords are [Identifiers and keywords](http://cassandra.apache.org/doc/latest/cql/definitions.html#identifiers) and are thus case insensitive in practice. We will also define some early construction using regexp, which we’ll indicate with `re(<some regular expression>)`.
- The grammar is provided for documentation purposes and leave some minor details out.  For instance, the comma on the last column definition in a `CREATE TABLE` statement is optional but supported if present even though the grammar in this document suggests otherwise. Also, not everything accepted by the grammar is necessarily valid CQL.
- References to keywords or pieces of CQL code in running text will be shown in a `fixed-width font`.

## Identifiers and keywords

The CQL language uses *identifiers* (or *names*) to identify tables, columns and other objects. An identifier is a token matching the regular expression `[a-zA-Z][a-zA-Z0-9_]*`.

A number of such identifiers, like `SELECT` or `WITH`, are *keywords*. They have a fixed meaning for the language and most are reserved. The list of those keywords can be found in [Appendix A: CQL Keywords](http://cassandra.apache.org/doc/latest/cql/appendices.html#appendix-a).

Identifiers and (unquoted) keywords are case insensitive. Thus `SELECT` is the same than `select` or `sElEcT`, and `myId` is the same than `myid` or `MYID`. A convention often used (in particular by the samples of this documentation) is to use upper case for keywords and lower case for other identifiers.

There is a second kind of identifiers called *quoted identifiers* defined by enclosing an arbitrary sequence of characters (non empty) in double-quotes(`"`). Quoted identifiers are never keywords. Thus `"select"` is not a reserved keyword and can be used to refer to a column (note that using this is particularly advised), while `select` would raise a parsing error. Also, contrarily to unquoted identifiers and keywords, quoted identifiers are case sensitive (`"My Quoted Id"` is *different* from `"my quoted id"`). A fully lowercase quoted identifier that matches `[a-zA-Z][a-zA-Z0-9_]*` is however *equivalent* to the unquoted identifier obtained by removing the double-quote (so `"myid"` is equivalent to `myid` and to `myId` but different from `"myId"`).  Inside a quoted identifier, the double-quote character can be repeated to escape it, so `"foo "" bar"` is a valid identifier.

Note

*quoted identifiers* allows to declare columns with arbitrary names, and those can sometime clash with specific names used by the server. For instance, when using conditional update, the server will respond with a result-set containing a special result named `"[applied]"`. If you’ve declared a column with such a name, this could potentially confuse some tools and should be avoided. In general, unquoted identifiers should be preferred but if you use quoted identifiers, it is strongly advised to avoid any name enclosed by squared brackets (like `"[applied]"`) and any name that looks like a function call (like `"f(x)"`).

More formally, we have:

```
identifier          ::=  unquoted_identifier | quoted_identifier
unquoted_identifier ::=  re('[a-zA-Z][a-zA-Z0-9_]*')
quoted_identifier   ::=  '"' (any character where " can appear if doubled)+ '"'
```

## Constants

CQL defines the following kind of *constants*:

```
constant ::=  string | integer | float | boolean | uuid | blob | NULL
string   ::=  '\'' (any character where ' can appear if doubled)+ '\''
              '$$' (any character other than '$$') '$$'
integer  ::=  re('-?[0-9]+')
float    ::=  re('-?[0-9]+(\.[0-9]*)?([eE][+-]?[0-9+])?') | NAN | INFINITY
boolean  ::=  TRUE | FALSE
uuid     ::=  hex{8}-hex{4}-hex{4}-hex{4}-hex{12}
hex      ::=  re("[0-9a-fA-F]")
blob     ::=  '0' ('x' | 'X') hex+
```

In other words:

- A string constant is an arbitrary sequence of characters enclosed by single-quote(`'`). A single-quote can be included by repeating it, e.g. `'It''s raining today'`. Those are not to be confused with quoted [Identifiers and keywords](http://cassandra.apache.org/doc/latest/cql/definitions.html#identifiers) that use double-quotes. Alternatively, a string can be defined by enclosing the arbitrary sequence of characters by two dollar characters, in which case single-quote can be used without escaping (`$$It's raining today$$`). That latter form is often used when defining [user-defined functions](http://cassandra.apache.org/doc/latest/cql/functions.html#udfs) to avoid having to escape single-quote characters in function body (as they are more likely to occur than `$$`).
- Integer, float and boolean constant are defined as expected. Note however than float allows the special `NaN` and `Infinity` constants.
- CQL supports [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) constants.
- Blobs content are provided in hexadecimal and prefixed by `0x`.
- The special `NULL` constant denotes the absence of value.

For how these constants are typed, see the [Data Types](http://cassandra.apache.org/doc/latest/cql/types.html#data-types) section.

## Terms

CQL has the notion of a *term*, which denotes the kind of values that CQL support. Terms are defined by:

```
term                 ::=  constant | literal | function_call | arithmetic_operation | type_hint | bind_marker
literal              ::=  collection_literal | udt_literal | tuple_literal
function_call        ::=  identifier '(' [ term (',' term)* ] ')'
arithmetic_operation ::=  '-' term | term ('+' | '-' | '*' | '/' | '%') term
type_hint            ::=  '(' cql_type `)` term
bind_marker          ::=  '?' | ':' identifier
```

A term is thus one of:

- A [constant](http://cassandra.apache.org/doc/latest/cql/definitions.html#constants).
- A literal for either [a collection](http://cassandra.apache.org/doc/latest/cql/types.html#collections), [a user-defined type](http://cassandra.apache.org/doc/latest/cql/types.html#udts) or [a tuple](http://cassandra.apache.org/doc/latest/cql/types.html#tuples) (see the linked sections for details).
- A function call: see [the section on functions](http://cassandra.apache.org/doc/latest/cql/functions.html#cql-functions) for details on which [native function](http://cassandra.apache.org/doc/latest/cql/functions.html#native-functions) exists and how to define your own [user-defined ones](http://cassandra.apache.org/doc/latest/cql/functions.html#udfs).
- An arithmetic operation between terms. see [the section on arithmetic operations](http://cassandra.apache.org/doc/latest/cql/operators.html#arithmetic-operators)
- A *type hint*: see the related section for details.
- A bind marker, which denotes a variable to be bound at execution time. See the section on [Prepared Statements](http://cassandra.apache.org/doc/latest/cql/definitions.html#prepared-statements) for details. A bind marker can be either anonymous (`?`) or named (`:some_name`). The latter form provides a more convenient way to refer to the variable for binding it and should generally be preferred.

## Comments

A comment in CQL is a line beginning by either double dashes (`--`) or double slash (`//`).

Multi-line comments are also supported through enclosure within `/*` and `*/` (but nesting is not supported).

```
-- This is a comment
// This is a comment too
/* This is
   a multi-line comment */
```

## Statements

CQL consists of statements that can be divided in the following categories:

- [Data Definition](http://cassandra.apache.org/doc/latest/cql/ddl.html#data-definition) statements, to define and change how the data is stored (keyspaces and tables).
- [Data Manipulation](http://cassandra.apache.org/doc/latest/cql/dml.html#data-manipulation) statements, for selecting, inserting and deleting data.
- [Secondary Indexes](http://cassandra.apache.org/doc/latest/cql/indexes.html#secondary-indexes) statements.
- [Materialized Views](http://cassandra.apache.org/doc/latest/cql/mvs.html#materialized-views) statements.
- [Database Roles](http://cassandra.apache.org/doc/latest/cql/security.html#cql-roles) statements.
- [Permissions](http://cassandra.apache.org/doc/latest/cql/security.html#cql-permissions) statements.
- [User-Defined Functions](http://cassandra.apache.org/doc/latest/cql/functions.html#udfs) statements.
- [User-Defined Types](http://cassandra.apache.org/doc/latest/cql/types.html#udts) statements.
- [Triggers](http://cassandra.apache.org/doc/latest/cql/triggers.html#cql-triggers) statements.

All the statements are listed below and are described in the rest of this documentation (see links above):

```
cql_statement                ::=  statement [ ';' ]
statement                    ::=  ddl_statement
                                  | dml_statement
                                  | secondary_index_statement
                                  | materialized_view_statement
                                  | role_or_permission_statement
                                  | udf_statement
                                  | udt_statement
                                  | trigger_statement
ddl_statement                ::=  use_statement
                                  | create_keyspace_statement
                                  | alter_keyspace_statement
                                  | drop_keyspace_statement
                                  | create_table_statement
                                  | alter_table_statement
                                  | drop_table_statement
                                  | truncate_statement
dml_statement                ::=  select_statement
                                  | insert_statement
                                  | update_statement
                                  | delete_statement
                                  | batch_statement
secondary_index_statement    ::=  create_index_statement
                                  | drop_index_statement
materialized_view_statement  ::=  create_materialized_view_statement
                                  | drop_materialized_view_statement
role_or_permission_statement ::=  create_role_statement
                                  | alter_role_statement
                                  | drop_role_statement
                                  | grant_role_statement
                                  | revoke_role_statement
                                  | list_roles_statement
                                  | grant_permission_statement
                                  | revoke_permission_statement
                                  | list_permissions_statement
                                  | create_user_statement
                                  | alter_user_statement
                                  | drop_user_statement
                                  | list_users_statement
udf_statement                ::=  create_function_statement
                                  | drop_function_statement
                                  | create_aggregate_statement
                                  | drop_aggregate_statement
udt_statement                ::=  create_type_statement
                                  | alter_type_statement
                                  | drop_type_statement
trigger_statement            ::=  create_trigger_statement
                                  | drop_trigger_statement
```

## Prepared Statements

CQL supports *prepared statements*. Prepared statements are an optimization that allows to parse a query only once but execute it multiple times with different concrete values.

Any statement that uses at least one bind marker (see [`bind_marker`](http://cassandra.apache.org/doc/latest/cql/definitions.html#grammar-token-bind_marker)) will need to be *prepared*. After which the statement can be *executed* by provided concrete values for each of its marker. The exact details of how a statement is prepared and then executed depends on the CQL driver used and you should refer to your driver documentation.
