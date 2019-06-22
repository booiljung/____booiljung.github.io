https://aqueduct.io/docs/db/modeling_data/

# Modeling Data

이 가이드에서는 데이터베이스 테이블에 매핑되는 형식을 만드는 방법에 대해 설명합니다. 이 가이드의 끝에는 데이터 모델 유형의 추가 예제가 있습니다.

## Defining a Table

응용 프로그램에서 인스턴스가 데이터베이스에 저장되는 형식을 선언합니다. 이러한 각 유형은 데이터베이스 테이블에 매핑됩니다. 여기서 유형의 각 속성은 테이블의 열입니다. 예를 들어, 신문 기사를 모델링하는 것을 고려해보십시오. 각 기사는 고유 한 식별자, 텍스트 내용 및 게시 날짜가 있습니다.

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

이 평범한 Dart 클래스는 `_Article`이라는 이름의 데이터베이스 테이블을 정의하기 때문에 테이블 정의라고 부릅니다. 이 테이블은 `id`, `contents`, `publishedDate`의 3 개의 열을 가지고 있습니다. 이 테이블에 저장된 데이터의 예는 다음과 같습니다.

| id   | contents            | publishedDate           |
| ---- | ------------------- | ----------------------- |
| 1    | Today, the local... | 2018-02-01 00:00:00.000 |
| 2    | In other news, ...  | 2018-03-01 04:30:00.000 |

테이블 정의의 속성은 선택적으로 `Column` 어노테이션을 가질 수 있습니다. 이 어노테이션은 연관된 데이터베이스 컬럼의 작동을 구성합니다. 속성에 주석이 없으면 열의 기본 비헤이비어가 있습니다. 이러한 행동은 아래 표에 나와 있습니다.

| Option        | Type                  | Behavior                                                    | Default                    |
| ------------- | --------------------- | ----------------------------------------------------------- | -------------------------- |
| primaryKey    | `bool`                | sets primary key column                                     | false (not primary key)    |
| databaseType  | `ManagedPropertyType` | sets underlying column type                                 | inferred from Dart type    |
| nullable      | `bool`                | toggles whether column can be null                          | false (not nullable)       |
| unique        | `bool`                | toggles whether column is unique across all rows            | false (not unique)         |
| defaultValue  | `String`              | provides default value for new rows when value is undefined | null                       |
| indexed       | `bool`                | whether an index should be created for the column           | false (no index)           |
| omitByDefault | `bool`                | 이 열을 기본적으로 생략해야 하는지 여부                     | false (fetch column value) |
| autoincrement | `bool`                | 이 열의 값이 series에서 자동으로 생성되는지 여부            | false (not generated)      |

속성 당 0 또는 1 개의 `Column` 주석을 사용해야하며, 하나의 주석에 모든 동작을 설정해야합니다 (예 :

```dart
@Column(nullable: true, unique: true, indexed: true)
int field;
```

열의 데이터 유형은 다음 표에 표시된대로 속성의 dart 유형에서 유추됩니다.

| Dart Type  | General Column Type            | PostgreSQL Column Type |
| ---------- | ------------------------------ | ---------------------- |
| `int`      | integer number                 | `INT` or `SERIAL`      |
| `double`   | floating point number          | `DOUBLE PRECISION`     |
| `String`   | text                           | `TEXT`                 |
| `DateTime` | timestamp                      | `TIMESTAMP`            |
| `bool`     | boolean                        | `BOOLEAN`              |
| `Document` | a JSON object or array         | `JSONB`                |
| Any `enum` | text, restricted to enum cases | `TEXT`                 |

일부 유형은 많은 데이터베이스 유형으로 표현 될 수 있습니다. 예를 들어 정수는 2, 4 또는 8 바이트로 저장할 수 있습니다. `Column` 어노테이션의`databaseType`을 사용하여 다음을 지정하십시오:

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

테이블은 `aqueduct` 명령 행 도구를 사용하여 이주 스크립트를 생성하고 실행함으로써 데이터베이스에 작성됩니다. 이 도구는 데이터베이스 유형을 검사하고 데이터베이스 유형을 자동으로 동기화하여 일치시킵니다.

기본적으로 테이블 정의의 이름은 데이터베이스 테이블의 이름입니다. 이것을 `Table` 어노테이션으로 설정할 수 있습니다.

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

테이블 정의는 * private 클래스 *입니다. 즉, 이름 앞에 밑줄 (`_`)이 붙습니다. 이 규칙은이 가이드의 뒷부분에서 설명합니다.

## Defining a Managed Object Subclass

테이블 정의 자체는 단순한 Dart 클래스 일뿐입니다. 또한 테이블 정의를 구현하기 위해 `ManagedObject` 서브 클래스를 선언해야 합니다. 다음은 그 예입니다.

```dart
class Article extends ManagedObject<_Article> implements _Article {}
```

* 인스턴스 유형이라고도 하는 관리 객체 하위 클래스는 응용 프로그램 코드에서 작업하는 객체 유형입니다. 예를 들어 데이터베이스에서 행을 가져 오는 경우 관리 객체 목록을 가져옵니다. 관리 대상 객체 하위 클래스는 해당 테이블 정의를 수퍼 클래스의 유형 인수로 한 번, 다시 구현하는 인터페이스의 두 위치에서 선언합니다.

관리 객체 하위 클래스는 테이블 정의에서 모든 속성을 상속받습니다. `Article`은`id`,`contents`,`publishedDate`를 가지고 있습니다.`_Article`은 그 속성을 선언하기 때문입니다. 다른 객체와 마찬가지로 관리 객체 하위 클래스의 인스턴스를 만들고 사용할 수 있습니다.

```dart
final article = new Article();
article.text = "Today, ...";
article.publishedDate = DateTime.now();
```

Managed Object Constructors

관리 객체 하위 클래스에 새 생성자를 추가 할 수 있지만 항상 인수가 없는 기본 생성자가 있어야합니다. 이 기본 생성자는 ORM이 데이터베이스의 행에서 인스턴스를 만들 때 사용됩니다.

## Modeling Relationships

관리 오브젝트는 다른 관리 오브젝트와 * 관계 *를 가질 수 있습니다. 예를 들어 저자에게는 책이 많고 기사는 신문에 속할 수 있으며 직원에게는 관리자가 있을 수 있습니다. 관계형 데이터베이스에서 테이블 간의 관계는 테이블 행의 기본 키를 관련 테이블의 열에 저장하여 설정됩니다. 이 열은 관련 테이블에 대한 * 외래 키 참조 *입니다.

테이블에 외래 키 참조가 있는 경우 관련 테이블에 속한다고 합니다. 직원 및 관리자의 예에서 직원 *은 관리자에 속하므로 직원 테이블에는 manager 테이블에 대한 외 부 키 참조가 있습니다. 이 명령문의 반대는 또한 사실입니다. 관리자는 종업원입니다. 관리자는 많은 직원을 보유하고 있습니다. 많은 관계라고합니다. 또한 has-one relationship 이 있습니다. 예를 들어, 각 국가는 하나의 수도를 가지고 있습니다.

다음은 수도 도시에 대한 국가와 하나의 관계의 예입니다.

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

두 테이블 정의 유형 모두에서 특성을 선언하여 두 테이블간에 관계가 형성됩니다. 이러한 속성의 유형은 관련 관리 객체 하위 클래스이므로 `Contry`는 `City` 유형의 속성을 가지며 `City`는 `Contry`유형의 속성을 갖습니다.

정확히 그 속성 중 하나에는 `Relate` 주석이 있어야 합니다. `Relate` 주석은 기본 컬럼을 외래 키 컬럼으로 지정합니다. 이 예제에서 city 테이블에는 country 테이블에 대한 외래 키 열이 있습니다. 개념적으로, 그때 city는 한 contry에 속하고 한 contry는 한 개의 capital city에 속합니다. city는 이 관계를 통해 한 contry에 속할 수 있으며 이는 모든 속한 관계 속성에 해당됩니다.

Foreign Key Column Names

관계형 속성의 이름과 관련 테이블의 기본 키를 밑줄로 결합하여 데이터베이스의 외래 키 열 이름을 지정합니다. 예를 들어, city 테이블의 열 이름은 `country_id`입니다.

`Relate`가 없는 속성은 관계의 inverse 이며 개념적으로 has-one 또는 has-many 관계 속성입니다. 이 예에서 자본과 국가의 관계는 1입니다. inverse 속성의 타입이 `ManagedSet` 일 때 관계는 has-many입니다. 예를 들어 한 contry와 모든 city 간의 관계를 모델링하고 싶다면 contry에 `ManagedSet<City>`속성을 선언해야합니다.

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

`Relate` 속성은 절대로 `ManagedSet`이 될 수 없습니다. `ManagedSet`은 `List`이므로 리스트가 사용 된 것과 같은 방식으로 사용될 수 있습니다.

`Relate` 주석에는 적어도 하나의 인수, 즉 inverse 속성의 이름과 일치하는 심볼이 필요 합니다. 이것은 두 관계 속성을 서로 연결하는 것입니다. 첫 번째 예제에서 inverse 속성의 이름이 `capital`이기 때문에 이 인수는 `#capital`입니다. 마찬가지로, '도시'와 '도시'도 마찬가지입니다. 이 페어링 이름이 일치해야합니다. 그렇지 않으면 오류가 발생합니다.

Symbols

기호는 Dart의 이름 식별자입니다. 기호는 클래스, 메서드 또는 속성을 나타낼 수 있습니다. `#name` 구문은 * 기호 리터럴 *입니다.

`Relate` 주석에는 관계를 더 자세히 정의 할 수 있는 선택적 인수가 있습니다. `Colimn`과 마찬가지로 선택적 인수입니다 예:

```
@Relate(#cities, isRequired: true, rule: DeleteRule.cascade)
```

관계가 필요할 수도 있고 선택적 일 수도 있습니다. 예를 들어 `City.country`가 필요한 경우 `City`는 항상 `Country`를 가져야 합니다. 기본적으로 관계는 선택 사항입니다.

관계는 삭제 규칙을 가집니다. 오브젝트가 삭제되면 해당 관계에 속하는 모든 오브젝트가 이 규칙의 적용을 받습니다. 다음 표는 규칙과 동작을 보여줍니다.

| Rule              | Behavior                         | Example                                                      |
| ----------------- | -------------------------------- | ------------------------------------------------------------ |
| nullify (default) | inverse is set to null           | 저자를 삭제할 때 기사의 저자는 null이 됩니다.                |
| cascade           | related objects are also deleted | 작성자를 삭제할 때 기사가 삭제됩니다.                        |
| restrict          | delete fails                     | 아티클이 있는 작성자를 삭제하려고 하면 삭제 작업이 실패합니다. |
| default           | inverse set to a default value   | 작성자를 삭제할 때 기사 작성자는 열의 기본값으로 설정됩니다. |

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

managed object 하위 클래스와 해당 테이블 정의를 함께 * 엔티티 *라고합니다. 각 엔티티는 동일한 파일에서 선언되어야 하고, 테이블 정의는 프로젝트의 다른 곳에서 사용되는 것을 방지하기 위해 `_` 접두사가 붙어야 합니다. 파일 하나당 하나의 엔티티를 선언하고, 모든 엔티티를 프로젝트의 `lib/model/`디렉토리에 저장하는 것이 바람직합니다.

모델 정의가 선언 된 파일은 Aqueduct 툴링에서 볼 수 있어야 합니다. 정상적인 상황에서는 다음과 같은 이유로 자동으로 발생합니다.

1. Aqueduct 도구는 라이브러리 파일에서 (직접 또는 임시로) 가져온 모든 파일을 찾을 수 있습니다.
2. 라이브러리 파일은 기본적으로 `ApplicationChannel`이 선언 된 파일을 볼 수 있습니다.
3. application channel 파일은 링크 된 모든 컨트롤러를 가져와야합니다.
4. Your controllers must import any model file they use.

`aqueduct` CLI를 사용하여 데이터베이스 마이그레이션 스크립트를 생성하면, 발견 된 응용 프로그램의 모든 `ManagedObject`를 보고 합니다. 특정 유형이 나열되어 있지 않으면 해당 유형을 사용하고 있지 않음을 알 수 있습니다. tooling이 찾을 수없는 특정 모델 파일을 볼 수 있게 하려면`channel.dart` 파일로 가져올 수 있습니다.

## Examples

### Example: One-to-Many Relationship

저자는 많은 책을 가지고 있습니다:

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

저자와 그 저자와 관련된 서적을 삽입하려면 :

```
final authorQuery = Query<Author>(context)
  ..values.name = "Fred";
final author = await authorQuery.insert();

final bookQuery = Query<Book>(context)
  ..values.name = "Title"
  ..values.author.id = author.id;
final book = await bookQuery.insert();  
```

작성자와 도서 가져 오기:

```dart
final query = Query<Author>(context)
  ..join(set: (a) => a.books);
final authors = await query.fetch();
```

책과 전체 작성자 오브젝트를 가져 오려면 다음을 수행하십시오:

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

국가와 수도를 가져 오려면 :

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

