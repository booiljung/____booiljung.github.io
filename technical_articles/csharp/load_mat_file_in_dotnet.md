[Up](./index.md)

# .NET에서 .mat 파일 로드

2019년 4월 25일

.mat 파일은 MATLAB의 데이터 파일입니다. .NET에서 .mat 파일을 로드 하려면 Accord.NET 프레임워크를 사용합니다. 예제코드는 아래와 같습니다.

```c#
var reader = new MatReader("example.mat");
var str = reader["structure"]["string"].GetValue();
foreach (var field in reader.Fields)
	Console.WriteLine(field.Key);
foreach (var field in structure.Fields)
	Console.WriteLine(field.Key);
var type = structure["a"].Type;
byte[,] a = structure["a"].GetValue<byte[,]>();
```

Accord.NET은 Accord.Math, Accord.IO에 의존합니다. NuGet에서 설치할 수 있습니다.



## 참조

- [MatReader Class](http://accord-framework.net/docs/html/T_Accord_IO_MatReader.htm)
- [Reading matrices form MATLAB(.mat) in C#](http://crsouza.com/2017/08/04/reading-matrices-from-matlab-mat-in-c/)