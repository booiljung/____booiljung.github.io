# Installation of Kafka on Ubuntu 18.04 LTS

![img](ubuntu_kafka.assets/227E373554FC02CB2D.png)

## 설치

 ### Java, Zookeeper 설치

```sh
sudo apt install openjdk-8-jdk
sudo apt install zookperd
```

### Kafka 계정 생성

```sh
useradd -d /opt/kafka -s /bin/bash kafka
passwd kafka
```

### Kafka 설치

```sh
cd /opt
sudo wget http://www-eu.apache.org/dist/kafka/2.0.0/kafka_2.11-2.0.0.tgz
sudo mkdir -p /opt/kafka
tar -xf kafka_2.11-2.0.0.tgz -C /opt/kafka --strip-components=1
chown -R kafka:kafka /opt/kafka
```







## 참조

- [Apache Kafka](https://epicdevs.com/17)
- [Kafka 설치,설정 - Ubuntu 18.04](https://ssup2.github.io/record/Kafka_%EC%84%A4%EC%B9%98_%EC%8B%A4%ED%96%89_Ubuntu_18.04/)
