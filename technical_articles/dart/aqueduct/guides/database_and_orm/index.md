https://aqueduct.io/docs/db/

# Overview

## Tasks

Aqueduct의 ORM은 데이터를 데이터베이스 테이블에 저장하고 테이블 행을 Dart 개체에 매핑합니다.

응용 프로그램 코드에서 `ManagedObject<T>`의 하위 클래스를 선언하여 응용 프로그램에서 사용하는 데이터베이스 테이블을 정의합니다. 이러한 유형의 속성에는 데이터베이스의 테이블 동작을 사용자 정의하기 위해 `Column` 및`Validate`와 같은 주석이 있습니다.

응용 프로그램은 응용 프로그램에 대한 데이터베이스 액세스를 관리하는 초기화 중에 ManagedContext 서비스 객체를 만듭니다. 이 서비스는 데이터베이스 쿼리를 만드는 컨트롤러에 주입됩니다.

`Query<T>`의 인스턴스는 데이터베이스에서 데이터를 삽입, 갱신, 읽기 및 삭제하기 위해 작성됩니다. `Query<T>`는 데이터베이스 행에 대해 필터링, 조인, 페이징, 정렬 및 집계 함수 수행을 위한 많은 구성 가능한 옵션을 가지고 있습니다.

`aqueduct db` 명령 줄 도구는 응용 프로그램이 연결하는 데이터베이스를 관리합니다. 이 도구는 응용 프로그램의 요구 사항에 맞도록 데이터베이스의 스키마를 갱신하는 마이그레이션 스크립트를 작성하고 실행합니다.

Aqueduct와 함께 작업하는 데 필요한 PostgreSQL의 최소 버전은 9.6입니다.

- [Connecting to a Database from Aqueduct](connecting_to_a_database_from_aqueduct.md)
- [Modeling Data](modeling_data_and_relationshops.md)
- [ManagedObject Serialization and Deserialization](managed_object_serialization_and_deserialization.md)
- [Inserting, Updating, Deleting and Fetching Objects](basic_queries.md)
- [Filtering, Joins, Paging and Reduce](advanced_queries_filtering_joins_paging_and_reduce.md)
- [Executing Queries in a Transaction](database_transactions.md)
- [Validating Data](validating_data.md)
- [Database Migration and Tooling](database_migration_and_tooling.md)
- [JSON Document Columns and Operations](json_document_storage.md)