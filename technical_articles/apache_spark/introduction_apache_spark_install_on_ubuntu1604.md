[Up](index.md)

# 아파치 스파크(Apache Spark)의 소개와 우분투 16.04 LTS에 설치

[스파크](http://spark.apache.org/)는 UC 버클리의 AMPLab에 의해 탄생하여, 아파치 소프트웨어 재단(Apache Software Foundation)에 기증되었고, 관리되고 있습니다.

스파크는 클러스터의 여러 노드로 프로그램으르 분배하고, 그 위에서 동작하는 프로그램을 개발 할 수 있도록 개발된 오픈 소스 프레임워크입니다. 데이터 과학자가 분산 프로그래밍을 할 수 있게 도와주는 최초의 오픈 소스 소프트웨어 입니다.

[맵리듀스(map-reduce)](http://www.flowdas.com/wp-content/uploads/2009/10/mapreduce-osdi041.pdf)는 스파크의 선배라고 할 수 있는데요. 수십만 개의 노드를 활용하는 병렬 처리를 간단히 구현할 수 있는 모델로 대량의 데이터 연산 분야에 일대 혁신을 가져 왔습니다. 맵리듀스는 선형에 가까운 확장성을 보여주는데요. 데이터가 많아지면 그만큼 노드를 추가하여 같은 시간에 일을 마무리 할 수 있습니다. 노드가 많아지면 오류도 증가하는 데요. 맵리듀스는 일을 작은 작업 단위로 나누고, 작업 단위에서 오류가 나더라도 전체에 영향을 주지 않도록 오류를 처리합니다.

스파크는 맵리듀스의 선형 확장성과 결함 포용성(fault tolerance)을 유지하면서, 세가지 중요한 개선을 이뤄 냈습니다.

1. 맵 단계 후 리듀스 단계를 지키지 않아도 되며, 일반적인 방향성 비순환 그래프(Directed Acycle Graph; DAG) 형태로 정의한 연산들을 실행할 수 있습니다. 맵리듀스는 중간 연산 결과를 분산 파일 시스템에 반드시 저장해야 하는데, 스파크는 파이프라인의 다음 단계로 중간 결과를 넘길 수 있습니다.
2. 사용자가 연산을 자연스럽게 표현 할 수 있도록 풍부한 변환 방식을 제공하여 처리 능력을 확장하고 있습니다. 개발자 중심의 기능을 제공하여 몇줄의 코드만으로 복잡한 파이프라인을 구현할 수 있는 간소한 API를 제공 합니다.
3. 스파크의 데이터셋(dataset)과 데이터 프레임(dataframe)을 통한 추상화는 개발자가 데이터 처리 파이프라인의 어느 지점에서라도 데이터를 클러스터의 메모리로 저장 할 수 있도록 해줍니다. 이후 단계에서 같은 데이터를 사용해야 한다면 데이터를 다시 연산하거나 디스크로부터 다시 읽을 필요가 없습니다. 이것은 데이터를 반복하여 스캔하는 경우에 유용하며 사용자에게 빠르게 응답하는 반응형 애플리케이션에 적합합니다.

데이터 분석에서 발생하는 병목은 CPU, 디스크, 네트워크 같은 컴퓨팅 환경이 아니라 분석가의 생산성에 달려 있음을 이해한다면 스파크는 좋은 해답이라고 할 수 있습니다. 전처리부터 모델 평가까지 단일 프로그래밍 환경에서 수행 하는 것은 개발 속도에 큰 잇점을 줍니다.

이상 내용은 한빛 미디어의 오라일리 번역서 [아홉가지 사례로 익히는 고급 스파크 분석(Advanced Analystic with Spark)](http://www.hanbit.co.kr/media/books/book_view.html?p_code=B2901427500)의 내용을 [참조](http://preview2.hanbit.co.kr/books/qekj/) 하였습니다.

다음은 우분투 16.04 LTS 에서 스파크 설치해 보겠습니다. 하둡(Hadoop)과 스파크는 자바가상머신(Java Virtual Machine; JVM) 위에서 구동됩니다. 그래서 Java를 설치하는데요. 먼저 자바가 설치되어 있는지 확인 합니다.

```sh
$ java -version
```

자바가 설치되어 있지 않으면 자바를 설치 합니다.

```sh
$ sudo apt update
$ sudo apt install default-jdk
```

스파크는 C, Java, Python 등 여러 언어를 지원하는 래퍼를 가지고 있는데요. 기본 언어는 스칼라 입니다. 스칼라가 설치되어 있지 않다면 스칼라를 설치 합니다.

```sh
$ sudo apt install scala
```

스칼라를 설치하였다면 스칼라가 제대로 설치 되어 있는지 확인 합니다.

```sh
$ scala -version
```

설치되었다면 아래와 같습니다.

```sh
Scala code runner version 2.11.6 -- Copyright 2002-2013, LAMP/EPFL
```

버전은 스칼라 버전에 따라 다를 수 있습니다. 다음은 스칼라를 실행해 보겠습니다.

```sh
$ scala
```

스칼라를 실행하면 REPL에 들어 가게 됩니다.

```sh
Welcome to Scala version 2.11.6 (OpenJDK 64-Bit Server VM, Java 1.8.0_151).
Type in expressions to have them evaluated.
Type :help for more information.
scala _
```

첫 스칼라 코드를 실행해 보겠습니다.

```scala
scala> println(“Hello, World!”)
```

엔터를 치면

```sh
Hello, World!
```

가 REPL에 출력 됩니다. 스칼라를 종료하려면 :q를 입력하고 엔터를 칩니다.

```scala
:q
```

스칼라 테스트가 끝났습니다. 다음은 스파크를 설치해 보겠습니다. [spark.apche.org](https://spark.apache.org/downloads.html)에서 스파크를 다운로드 받습니다. 우분투라면 ~/Downloads 폴더에 파일이 있습니다. 스파크 파일명은 버전에 따라 다를 수 있습니다. 2018년 3월 30일 현재 spark-2.3.0-bin-hadoop2.7.tgz 를 받을 수 있습니다.

다음은 다운로드 받은 스파크 파일의 압축을 해제 합니다.

```sh
$ tar xvf spark-2.3.0-bin-hadoop2.7.tgz
```

압축을 해제하면 다음 폴더로 이동 합니다.

```sh
$ cd spark-2.3.0-bin-hadoop2.7/bin
```

그리고 스파크 REPL을 시작합니다.

```sh
$ ./spark-shell
```

2018년 3월 30일 현재 다음과 같이 웰컴 텍스트가 표시 됩니다.

```sh
Spark context Web UI available at http://???.???.??.?:4040
Spark context available as 'sc' (master = local[*], app id = local-1522375767174).
Spark session available as 'spark'.
Welcome to
                    __
     / /  _ ___/ /__
    \ \/ _ \/ _ `/ __/  '/
   /_/ ./_,// /_/_\   version 2.3.0
      /_/
         
Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_151)
Type in expressions to have them evaluated.
Type :help for more information.
scala
```

REPL에서 스칼라로 첫 코드를 작성해 보겠습니다. 

```scala
scala> println(“Hello, Spark!”)
```

```sh
Hello, Spark!
```

## 참조

- [Sandy Ryza, Uri Laserson, Josh Wills, Sean Owen 2017, Advanced Analystic with Spark Second Edition, O’Reilly Media](http://shop.oreilly.com/product/0636920056591.do)
- [박상은, 권한철, 서양주 2018, 아홉가지 사례로 익히는 고급 스파크 분석 제2판, 한빛 미디어](http://www.hanbit.co.kr/media/books/book_view.html?p_code=B2901427500)
- [Tutorial Point](https://www.tutorialspoint.com/apache_spark/apache_spark_installation.htm)
- [Jose Marcial Portilla](https://medium.com/@josemarcialportilla/installing-scala-and-spark-on-ubuntu-5665ee4b62b1)