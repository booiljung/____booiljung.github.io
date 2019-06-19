https://aqueduct.io/docs/db/modeling_data/

# Modeling Data

In this guide, you will learn how to create types that are mapped to  database tables. At the end of this guide are additional examples of  data model types.

## Defining a Table

In your application, you declare types whose instances are stored in a  database. Each of these types is mapped to a database table, where each  property of the type is a column of the table. For example, consider  modeling newspaper articles, where each article has a unique identifier,  text contents and published date:

```dart
// This is a table definition of an 'article'
class _Article {
  @Column(primaryKey: true)
  int id;

  String contents;

  @Column(indexed: true)
  DateTime publishedDate;
}
```

This plain Dart class is called a *table definition* because it defines a database table named `_Article`. The table has three columns, `id`, `contents`, `publishedDate`. An example of the data stored in this table might look like this:

| id   | contents            | publishedDate           |
| ---- | ------------------- | ----------------------- |
| 1    | Today, the local... | 2018-02-01 00:00:00.000 |
| 2    | In other news, ...  | 2018-03-01 04:30:00.000 |

A property in a table definition can optionally have a `Column`  annotation. This annotation configures the behavior of the associated  database column. If a property doesn't have an annotation, the column  has default behavior. These behaviors are shown in the table below:

| Option        | Type                  | Behavior                                                     | Default                    |
| ------------- | --------------------- | ------------------------------------------------------------ | -------------------------- |
| primaryKey    | `bool`                | sets primary key column                                      | false (not primary key)    |
| databaseType  | `ManagedPropertyType` | sets underlying column type                                  | inferred from Dart type    |
| nullable      | `bool`                | toggles whether column can be null                           | false (not nullable)       |
| unique        | `bool`                | toggles whether column is unique across all rows             | false (not unique)         |
| defaultValue  | `String`              | provides default value for new rows when value is undefined  | null                       |
| indexed       | `bool`                | whether an index should be created for the column            | false (no index)           |
| omitByDefault | `bool`                | whether this column should be left out by default            | false (fetch column value) |
| autoincrement | `bool`                | whether this column's value is automatically generated from a series | false (not generated)      |

You must use either zero or one `Column` annotation per property, and you must set all behaviors in one annotation, e.g.:

```
@Column(nullable: true, unique: true, indexed: true)
int field;
```

The data type of a column is inferred from the Dart type of the property as shown by the following table.

| Dart Type  | General Column Type            | PostgreSQL Column Type |
| ---------- | ------------------------------ | ---------------------- |
| `int`      | integer number                 | `INT` or `SERIAL`      |
| `double`   | floating point number          | `DOUBLE PRECISION`     |
| `String`   | text                           | `TEXT`                 |
| `DateTime` | timestamp                      | `TIMESTAMP`            |
| `bool`     | boolean                        | `BOOLEAN`              |
| `Document` | a JSON object or array         | `JSONB`                |
| Any `enum` | text, restricted to enum cases | `TEXT`                 |

Some types can be represented by many database types; for example, an integer can be stored as 2, 4 or 8 bytes. Use the `databaseType` of a `Column` annotation to specify:

```dart
@Column(databaseType: ManagedType.bigInteger)
int bigNumber;
```

테이블 정의 유형의 유일한 요구 사항은 정확히 하나의 기본 키 특성을 가지고 있다는 것입니다. 기본 키는 데이터베이스 행에 대해 색인화되고 고유 한 식별자이며 `Column` 주석을 통해 설정됩니다.

```dart
@Column(primaryKey: true)
int id;
```

기본 키는 지원되는 모든 데이터 유형이 될 수 있으며 항상 고유하고 색인됩니다. 기본 키가 64 비트 자동 증가 정수가 되는 것이 일반적입니다. `primaryKey` 상수는 이러한 동작을 가진 `Column`의 편의를 위해 존재합니다.

```dart
class _Article {
  @primaryKey // equivalent to @Column(primaryKey: true, databaseType: ManagedType.bigInteger, autoincrement: true)
  int id;
  ...
}
```

Creating Tables

Tables are created in a database by using the `aqueduct`  command line tool to generate and execute migration scripts. The tool  inspects your database types and automatically synchronizes a databases  schema to match your them.

By default, the name of the table definition is the name of the database table. You can configure this with the `Table` annotation.

```dart
@Table(name: "ArticleTable")
class _Article {
  @primaryKey
  int id;

  String contents;

  @Column(indexed: true)
  DateTime publishedDate;
}
```

It is convention that table definitions are *private classes*, that is, their name is prefixed with an underscore (`_`). This convention is discussed later in this guide.

## Defining a Managed Object Subclass

A table definition by itself is just a plain Dart class. You must also declare a `ManagedObject` subclass to bring your table definition to life. Here's an example:

```dart
class Article extends ManagedObject<_Article> implements _Article {}
```

A managed object subclass, also called the *instance type*, is  the object type that you work with in your application code. For  example, when you fetch rows from a database, you will get a list of  managed objects. A managed object subclass declares its table definition  in two places: once as the type argument of its superclass, and again  as an interface it implements.

A managed object subclass inherits all of the properties from its table definition; i.e., an `Article` has an `id`, `contents` and `publishedDate` because `_Article` declares those properties. You create and use instances of a managed object subclass like any other object:

```dart
final article = new Article();
article.text = "Today, ...";
article.publishedDate = DateTime.now();
```

Managed Object Constructors

You can add new constructors to a managed object subclass, but you  must always have a default, no-argument constructor. This default  constructor is used when the ORM creates instances from rows in your  database.

## Modeling Relationships

A managed object can have *relationships* to other managed  objects. For example, an author can have many books, an article can  belong to a newspaper, and an employee can have a manager. In a  relational database, relationships between tables are established by  storing the primary key of a table row in a column of the related table.  This column is a *foreign key reference* to the related table.

When a table has a foreign key reference, it is said to *belong to* the related table. In the example of an employee and manager, the employee *belongs to*  the manager and therefore the employee table has a foreign key  reference to the manager table. The inverse of this statement is also  true: a manager *has* employees. A manager has-many employees - this is called a *has-many relationship*. There are also *has-one relationships* - for example, a country has-one capital.

The following is an example of a country and a has-one relationship to a capital city:

```dart
class City extends ManagedObject<_City> implements _City {}
class _City {
  @primaryKey
  int id;

  @Relate(#capital)
  Country country;
}

class Country extends ManagedObject<_Country> implements _Country {}
class _Country {
  @primaryKey
  int id;

  City capital;
}
```

A relationship is formed between two tables by declaring properties  in both table definition types. The type of those properties is the  related managed object subclass - so a `Country` has a property of type `City`, and a `City` has a property of type `Country`.

Exactly one of those properties must have a `Relate` annotation. The `Relate`  annotation designates the underlying column as a foreign key column. In  this example, the city table has a foreign key column to the country  table. Conceptually, then, a city *belongs to* a country and a  country has-one capital city. A city can only belong to one country  through this relationship, and that is true of all belongs-to  relationship properties.

Foreign Key Column Names

A foreign key column in the database is named by joining the name of  the relationship property and the primary key of the related table with  an underscore. For example, the column in the city table is named `country_id`.

The property without `Relate` is the *inverse* of  the relationship and is conceptually either a has-one or has-many  relationship property. In this example, a country's relationship to its  capital is has-one. A relationship is has-many when the type of the  inverse property is a `ManagedSet`. For example, if we wanted to model a relationship between a country and all of its cities, we'd declare a `ManagedSet<City>` property in the country:

```dart
class City extends ManagedObject<_City> implements _City {}
class _City {
  ...

  @Relate(#cities)
  Country country;
}

class Country extends ManagedObject<_Country> implements _Country {}
class _Country {
  ...

  ManagedSet<City> cities;
}
```

ManagedSet Behavior

A `Relate` property can never be a `ManagedSet`. A `ManagedSet` is a `List`, and therefore can be used in the same way a list is used.

`Relate` 주석에는 적어도 하나의 인수, 즉 inverse 속성의 이름과 일치하는 심볼이 필요 합니다. 이것은 두 관계 속성을 서로 연결하는 것입니다. 첫 번째 예제에서 inverse 속성의 이름이 `capital`이기 때문에 이 인수는 `#capital`입니다. 마찬가지로, '도시'와 '도시'도 마찬가지입니다. 이 페어링 이름이 일치해야합니다. 그렇지 않으면 오류가 발생합니다.

Symbols

기호는 Dart의 이름 식별자입니다. 기호는 클래스, 메서드 또는 속성을 나타낼 수 있습니다. `#name` 구문은 * 기호 리터럴 *입니다.

`Relate` 주석에는 관계를 더 자세히 정의 할 수 있는 선택적 인수가 있습니다. `Colimn`과 마찬가지로 선택적 인수입니다 예:

```
@Relate(#cities, isRequired: true, rule: DeleteRule.cascade)
```

관계가 필요할 수도 있고 선택적 일 수도 있습니다. 예를 들어 `City.country`가 필요한 경우 `City`는 항상 `Country`를 가져야 합니다. 기본적으로 관계는 선택 사항입니다.

관계는 삭제 규칙을 가집니다. 오브젝트가 삭제되면 해당 관계에 속하는 모든 오브젝트가 이 규칙의 적용을받습니다. 다음 표는 규칙과 동작을 보여줍니다.

| Rule              | Behavior                         | Example                                                      |
| ----------------- | -------------------------------- | ------------------------------------------------------------ |
| nullify (default) | inverse is set to null           | When deleting an author, its articles' author becomes null   |
| cascade           | related objects are also deleted | When deleting an author, its articles are deleted            |
| restrict          | delete fails                     | When attempting to delete an author with articles, the delete operation fails |
| default           | inverse set to a default value   | When deleting an author, its articles author is set to the default value of the column |

## Special Behaviors

### Enum Types

열거 유형은 테이블 정의에서 특성을 선언하는 데 사용될 수 있습니다. 데이터베이스는 열을 열거 형의 문자열 표현으로 저장합니다. 다음은 사용자가 관리자 또는 일반 사용자가 될 수 있는 예입니다.

```dart
enum UserType {
  admin, user
}

class User extends ManagedObject<_User> implements _User {}
class _User {
  @primaryKey
  int id;

  String name;

  UserType role;
}

var query = Query<User>(context)
  ..values.name = "Bob"
  ..values.role = UserType.admin;
final bob = await query.insert();

query = Query<User>(context)
  ..where((u) => u.role).equalTo(UserType.admin);
final allAdmins = await query.fetch();
```

데이터베이스에서`role` 컬럼은 문자열로 저장됩니다. 그 값은 "admin"또는 "user"입니다.

### Private Variables

테이블 정의의 전용 변수는 오브젝트의 직렬화 된 표현에서 이를 제거합니다. private 변수는 데이터베이스 질의를 할 때 항상 가져 오지만 `read`를 호출 할 때는 읽지 않고 `asMap`을 호출 할 때는 쓰지 않습니다. 이 두 메소드는 요청 본문에서 관리 객체를 읽거나 응답 본문에 작성할 때 호출됩니다.

### Transient Properties

managed object 하위 클래스에서 선언 된 속성은 데이터베이스에 저장되지 않으므로 * transient *라고합니다. 예를 들어 이름과 성을 별개의 열로 저장하는 `Author` 형식을 생각해보십시오. 데이터베이스에 'full name'을 중복 저장하는 대신 일시적인 속성은 이름과 성을 결합 할 수 있습니다.

```dart
class Author extends ManagedObject<_Author> implements _Author {
  String get name => "$firstName $lastName";
  set name(String fullName) {
    firstName = fullName.split(" ").first;
    lastName = fullName.split(" ").last;
  }
}
class _Author {
  @primaryKey
  int id;

  String firstName;
  String lastName;
}
```

기본적으로 요청 본문에서 객체를 읽거나 객체를 응답 본문에 쓸 때 일시적 속성은 무시됩니다. 자세한 내용은 [serialization](https://aqueduct.io/docs/db/serialization/) 가이드를 참조하십시오. 응답 본문에서 읽거나 응답 본문에 쓰거나 둘 다 읽을 수 있도록 `Serialize`로 일시적인 속성에 주석을 달 수 있습니다. 예 :

```dart
class Author extends ManagedObject<_Author> implements _Author {
  @Serialize()
  String get name => "$firstName $lastName";

  @Serialize()
  set name(String fullName) {
    firstName = fullName.split(" ").first;
    lastName = fullName.split(" ").last;
  }
}
```

이런 식으로 getter, setter 및 속성을 serialize하도록 선언 할 수 있습니다. 프로퍼티를 선언 할 때, 당신은 `Serialize`에 대한 인자로 그것을 제어 할 수 있습니다 :

```dart
class Author extends ManagedObject<_Author> implements _Author {
  @Serialize(input: false, output: true)
  bool isCurrentlyPromoted;
}
```

## Project File Structure

managed object 하위 클래스와 해당 테이블 정의를 함께 * 엔티티 *라고합니다. 각 엔티티는 동일한 파일에서 선언되어야 하고, 테이블 정의는 프로젝트의 다른 곳에서 사용되는 것을 방지하기 위해 `_` 접두사가 붙어야 합니다. 파일 하나당 하나의 엔티티를 선언하고, 모든 엔티티를 프로젝트의`lib/model/`디렉토리에 저장하는 것이 바람직합니다.

모델 정의가 선언 된 파일은 Aqueduct 툴링에서 볼 수 있어야 합니다. 정상적인 상황에서는 다음과 같은 이유로 자동으로 발생합니다.

1. Aqueduct 도구는 라이브러리 파일에서 (직접 또는 임시로) 가져온 모든 파일을 찾을 수 있습니다.
2. 라이브러리 파일은 기본적으로 `ApplicationChannel`이 선언 된 파일을 볼 수 있습니다.
3. application channel 파일은 링크 된 모든 컨트롤러를 가져와야합니다.
4. Your controllers must import any model file they use.

`aqueduct` CLI를 사용하여 데이터베이스 마이그레이션 스크립트를 생성하면, 발견 된 응용 프로그램의 모든 `ManagedObject`를 보고 합니다. 특정 유형이 나열되어 있지 않으면 해당 유형을 사용하고 있지 않음을 알 수 있습니다. tooling이 찾을 수없는 특정 모델 파일을 볼 수 있게 하려면`channel.dart` 파일로 가져올 수 있습니다.

## Examples

### Example: One-to-Many Relationship

An author has many books:

```dart
class Author extends ManagedObject<_Author> implements _Author {}
class _Author {
  @primaryKey
  int id;

  String name;

  ManagedSet<Book> books;
}

class Book extends ManagedObject<_Book> implements _Book {}
class _Book {
  @primaryKey
  int id;

  String name;

  @Relate(#books)
  Author author;
}
```

To insert an author and a book associated with that author:

```
final authorQuery = Query<Author>(context)
  ..values.name = "Fred";
final author = await authorQuery.insert();

final bookQuery = Query<Book>(context)
  ..values.name = "Title"
  ..values.author.id = author.id;
final book = await bookQuery.insert();  
```

To fetch authors and their books:

```dart
final query = Query<Author>(context)
  ..join(set: (a) => a.books);
final authors = await query.fetch();
```

To fetch a book and their full author object:

```dart
final query = Query<Book>(context)
  ..where((b) => b.id).equalTo(1)
  ..join(object: (a) => a.author);
final books = await query.fetch();
```

### Example: One-to-One Relationship

```dart
class Country extends ManagedObject<_Country> implements _Country {}
class _Country {
  @primaryKey
  int id;

  String name;

  City capital;
}

class City extends ManagedObject<_City> implements _City {}
class _City {
  @primaryKey
  int id;

  String name;

  @Relate(#capital)
  Country country;
}
```

To fetch a country and its capital:

```dart
final query = Query<Country>(context)
  ..where((c) => c.id).equalTo(1)
  ..join(object: (a) => a.capital);
final countries = await query.fetch();
```

### Example: Many-to-Many Relationship

```dart
class Team extends ManagedObject<_Team> implements _Team {}
class _Team {
  @primaryKey
  int id;

  String name;

  ManagedSet<TeamPlayer> teamPlayers;
}

// This type is a join table
class TeamPlayer extends ManagedObject<_TeamPlayer> implements _TeamPlayer {}
class _TeamPlayer {
  @primaryKey
  int id;  

  @Relate(#teamPlayers)
  Team team;

  @Relate(#teamPlayers)
  Player player;
}

class Player extends ManagedObject<_Player> implements _Player {}
class _Player {
  @primaryKey
  int id;

  String name;

  ManagedSet<TeamPlayer> teamPlayers;
}
```

To fetch a team and its players:

```dart
// Note that the final join is not cascaded from the Team query,
// but from the Query created by joining with TeamPlayer
final query = Query<Team>(context)
  ..where((t) => t.id).equalTo(1)
  ..join(set: (t) => t.teamPlayers).join(object: (tp) => tp.player);
final team = await query.fetchOne();
```

The structure of this object is:

```json
{
  "id": 1,
  "name": "Badgers",
  "teamPlayers": [
    {
      "id": 1,
      "team": {
        "id": 1
      },
      "player": {
        "id": 1,
        "name": "Fred"
      }
    }
  ]
}
```

이 구조를 여러 가지 방법으로 전개 할 수 있습니다. 가장 간단한 형태로, `ManagedObject` 서브 클래스에 `Serialize` - annotated transient 프로퍼티를 추가하고, fetch 할 때마다 객체에서 조인 테이블을 제거하고 플레이어를 transient 프로퍼티에 배치하십시오.

```dart
class Team extends ManagedObject<_Team> implements _Team {
  @Serialize(input: false, output: true)
  List<Player> players;
}

final team = ...;
team.players = team.teamPlayers.map((t) => t.player).toList();
// Remove teamPlayers; it is redundant
team.backing.removeProperty("teamPlayers");
```

### Example: Hierarchical Relationships (Self Referencing)

계층적 관계는 다른 모든 관계와 동일한 규칙을 따르지만 같은 유형의 외래 키 속성과 반전을 선언하십시오.

```dart
class Person extends ManagedObject<_Person> implements _Person {}
class _Person {
  @primaryKey
  int id;

  String name;

  ManagedSet<Person> children;

  @Relate(#children)
  Person parent;
}
```

