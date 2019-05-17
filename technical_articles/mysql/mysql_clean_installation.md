[Up](./index.md)

# MySQL 클린 설치

우분투 16.04 LTS에서 클린 설치하는 방법.

```shell
sudo apt-get purge mysql-server mysql-client mysql-common mysql-server-core-* mysql-client-core-*
sudo rm -rf /etc/mysql /var/lib/mysql
sudo apt-get autoremove
sudo apt-get autoclean
```

