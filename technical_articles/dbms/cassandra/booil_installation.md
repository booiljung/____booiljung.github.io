# Getting Started: Installation

## 설치

Ubuntu 18.04 LTS에서 Cassandra 설치하려면 다음 패키지가 설치 되어 있어야 합니다:

```
sudo apt install -y curl 
```

그리고 JDK:

```
sudo apt install -y openjdk-11-jdk 
또는
sudo apt install -y openjdk-8-jdk
```

#### Debian에서 설치

Apache 저장소를 `/etc/apt/sources.list.d`에 추가 합니다:

```
echo "deb http://www.apache.org/dist/cassandra/debian 36x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
```

Apache Cassandra 저장소 키를 추가 합니다:

```
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
```

저장소들을 업데이트 합니다.

```
sudo apt update -y
```

##### 만일 아래 오류가 발생한다면

```
GPG error: http://www.apache.org 36x InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY A278B781FE4B2BDA
```

`A278B781FE4B2BDA` 키를 추가합니다.

```
sudo apt-key adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA
```

키를 추가 하였으면 다시 저장소 업데이트를 해줍니다.

```
sudo apt update -y
```

Cassandra를 설치합니다.

```
sudo apt install -y cassandra
```

#### Debian에서 일괄 설치 셸스크립트

위 과정을 셸스크립트로 작성하였습니다.

```
sudo apt install -y curl
sudo apt install -y openjdk-11-jdk 
echo "deb http://www.apache.org/dist/cassandra/debian 36x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update -y
sudo apt install -y curl openjdk-11-jdk
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
sudo apt update -y
sudo apt adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA
sudo apt install -y cassandra
```

#### 서비스 상태

서비스가 구동되고 있는지 확인 합니다.

```sh
$ sudo service cassandra status
```

싱글 노드로 구동하겠다면 기본 설정으로 구동할 수 있습니다.

```sh
$ sudo service cassandra start
```

노드 상태를 확인합니다.

```sh
$ sudo nodetool status
```

클라이언트 CLI를 실행합니다.

```sh
$ cqlsh
```

## 참조:

- [Apache: Cassandra: Documentation: Installing](http://cassandra.apache.org/doc/latest/getting_started/installing.html)
- [Apache: Cassandra: Package installation directories](https://docs.datastax.com/en/archived/cassandra/3.0/cassandra/install/referenceInstallLocatePkg.html)



