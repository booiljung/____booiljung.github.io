# Cassandra

Ubuntu 18.04에서 Cassandra 설치하려면 다음 패키지가 설치 되어 있어야 합니다.

```
sudo apt install -y curl 
```

그리고 JDK

```
sudo apt install -y openjdk-11-jdk 
```

또는

```
sudo apt install -y openjdk-8-jdk
```

이며, 일괄 설치하려면 다음 스크립트를 실행 합니다.

```
echo "deb http://www.apache.org/dist/cassandra/debian 36x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update -y
sudo apt install -y curl openjdk-11-jdk
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
sudo apt update -y
sudo apt adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA
sudo apt install -y cassandra
```

