https://aqueduct.io/docs/db/transactions/

# Database Transactions

데이터베이스 트랜잭션에서 여러 `Query<T>`를 실행하는 법을 배우겠습니다.

## Transactions

트랜잭션은 함께 실행되는 일련의 쿼리입니다. 해당 세트의 쿼리 중 하나가 실패하면 이미 실행 된 모든 쿼리가 실패하고 변경 사항이 취소됩니다.

직원 기록을 저장하는 응용 프로그램을 고려하십시오. 각 직원은 부서에 속합니다. 경영진은 두 부서를 완전히 새로운 부서로 통합하기로 결정합니다. 이렇게하려면 응용 프로그램에서 새 부서를 삽입하고 해당 부서에 각 직원의 외래 키 참조를 설정 한 다음 이전 부서를 삭제해야 합니다. 간단한 (그러나 문제가 있는) 구현은 다음과 같이 보일 것이다.

```dart
// Create the new department
final newDepartment = await ctx.insertObject(Department()..name = "New Department");

// Set employee's departments to the new one
final changeDepartmentQuery = Query<Employee>(ctx)
  ..where((e) => e.department.id).oneOf([1, 2])      
  ..values.department.id = newDepartment.id;
await changeDepartmentQuery.update();

// Delete the old ones
final deleteDepartmentQuery = Query<Department>(ctx)
  ..where((e) => e.department.id).oneOf([1, 2]);
await deleteDepartmentQuery.delete();      
```

데이터베이스에 대한 이 변경 사항은 세 가지 별도의 쿼리이지만 'trasfer'이 성공하려면 가장 완벽합니다. 그 중 하나가 실패하면 데이터베이스가 일관성 없는 상태로 남을 수 있습니다. 예를 들어 부서 삭제가 성공했지만 직원을 새 부서로 이전하지 못하면 부서가없는 직원이 많습니다.

데이터베이스 트랜잭션은 쿼리를 하나의 단위로 결합합니다. 트랜잭션의 쿼리가 실패하면 해당 트랜잭션의 이전 쿼리가 되돌리고 나머지 쿼리가 중단됩니다. `ManagedContext.transaction`을 호출하고 클로저 인수에 쿼리를 작성함으로써 트랜잭션을 생성합니다.

```dart
await context.transaction((transaction) async {
  // note that 'transaction' is the context for each of these queries.
  final newDepartment = await transaction.insertObject(Department()..name = "New Department");

  final changeDepartmentQuery = Query<Employee>(transaction)
    ..where((e) => e.department.id).oneOf([1, 2])      
    ..values.department.id = newDepartment.id;
  await changeDepartmentQuery.update();

  final deleteDepartmentQuery = Query<Department>(transaction)
    ..where((e) => e.department.id).oneOf([1, 2]);
  await deleteDepartmentQuery.delete();      
});
```

클로저에는 트랜잭션의 모든 쿼리가 컨텍스트로 사용해야 하는 `transaction` 개체가 있습니다. 이 트랜잭션 컨텍스트를 사용하는 쿼리는 동일한 트랜잭션으로 그룹화됩니다. 일단 모두 성공하면 `transation` 메소드의 퓨처가 완료됩니다. 클로저에서 예외가 발생하면 트랜잭션이 롤백되고 `transaction` 메소드는 해당 예외를 다시 throw합니다.

트랜잭션 컨텍스트를 사용하지 않으면 응용 프로그램이 교착 상태가됩니다.

트랜잭션 클로저 내의 쿼리에서 트랜잭션의 부모 컨텍스트를 사용하면 데이터베이스 연결이 교착 상태가되어 작동을 멈 춥니다.

### Returning Values

트랜잭션 클로저에서 반환 된 값은 호출자에게 반환되므로 트랜잭션 클로저 내에 만들어진 값은 해당 범위에서 벗어날 수 있습니다.

```dart
final employees = [...];
final insertedEmployees = await context.transaction((transaction) async {
  return Future.wait(employees.map((e) => transaction.insertObject(e)));
});
```

### Rollbacks

`Rollback` 객체를 던짐으로써 트랜잭션을 취소하고 트랜잭션 클로저 내에서 언제든지 트랜잭션을 취소 할 수 있습니다.

```dart
try {
  await context.transaction((t) async {
    // do something

    if (somethingIsTrue) {
      throw Rollback("something was true");    
    }

    // do something
  });
} on Rollback catch (rollback) {
  print("${rollback.reason}"); // prints 'something was true'
}
```

롤백하면 트랜잭션이 실패하고 트랜잭션 종결이 중단되고 모든 변경 사항이 되돌려집니다. `transaction` 메쏘드는 에러를 가지고 완료됩니다. 에러 객체는`Rollback`입니다.