# Docker로 개발용 SFTP 운영하기

Docker로 SFTP 파일 서버를 구성하기 위해 테스트 하는 과정을 기록 하였습니다.

## [atmoz/sftp](https://hub.docker.com/r/atmoz/sftp/)

### 간단한 예제

```
docker run -p 22:22 -d atmoz/sftp foo:pass:::upload
```

`foo`: 유저이름, `pass`: 패스워드, `upload`: 유저 폴더.

### 호스트 컴퓨터의 디렉토리를 제공

```
docker run \
    -v /host/upload:/home/foo/upload \
    -p 22:22 -d atmoz/sftp \
    foo:pass:1001
```

### Docker Compose 파일을 사용.

```yaml
sftp:
	image: atmoz/sftp
	volumes:
		- /host/upload:/home/foo/upload
	ports:
		-"22:22"
	command: foo:pass:1001
```

## SSH and SFTP

Directories Structure

```
.
+---docker
|	+---Dockerfile
|	+---entrypoint.sh
|	+---ssh_config
|	+---sshd_config
|	+---user.sh
+---.env
+---docker-compose.yml
```

`.env`:

```
SSH_MASTER_USER=master
SSH_MASTER_PASS=master
```

`docker-compose.yml`:

```yaml
version: '3'
     
services:
    server:
        build:
            context: ./docker
            args:
                SSH_MASTER_USER: ${SSH_MASTER_USER}
                SSH_MASTER_PASS: ${SSH_MASTER_PASS}
        hostname: server
        ports:
            - "2222:22"
        volumes:
            - ./uploads:/uploads
```

`Dockerfile`

```dockerfile
FROM debian:9.5

ARG SSH_MASTER_USER
ARG SSH_MASTER_PASS

RUN apt-get update \
     && apt-get install -y --no-install-recommends \
        nano \
        sudo \
        openssh-server

COPY ssh_config /etc/ssh/ssh_config
COPY sshd_config /etc/ssh/sshd_config

COPY user.sh /usr/local/bin/user.sh
RUN chmod +x /usr/local/bin/user.sh
RUN /usr/local/bin/user.sh
RUN rm /usr/local/bin/user.sh

COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]

CMD tail -f /dev/null
```

`user.sh`:

```
#!/bin/bash
set -e
     
printf "\n\033[0;44m---> Creating SSH master user.\033[0m\n"
     
useradd -m -d /home/${SSH_MASTER_USER} -G ssh ${SSH_MASTER_USER} -s /bin/bash
echo "${SSH_MASTER_USER}:${SSH_MASTER_PASS}" | chpasswd
echo 'PATH="/usr/local/bin:/usr/bin:/bin:/usr/sbin"' >> /home/${SSH_MASTER_USER}/.profile
     
echo "${SSH_MASTER_USER} ALL=NOPASSWD:/bin/rm" >> /etc/sudoers
echo "${SSH_MASTER_USER} ALL=NOPASSWD:/bin/mkdir" >> /etc/sudoers
echo "${SSH_MASTER_USER} ALL=NOPASSWD:/bin/chown" >> /etc/sudoers
echo "${SSH_MASTER_USER} ALL=NOPASSWD:/usr/sbin/useradd" >> /etc/sudoers
echo "${SSH_MASTER_USER} ALL=NOPASSWD:/usr/sbin/deluser" >> /etc/sudoers
echo "${SSH_MASTER_USER} ALL=NOPASSWD:/usr/sbin/chpasswd" >> /etc/sudoers
     
addgroup sftp
     
exec "$@"
```

`entrypoint.sh`:

```
#!/bin/bash
set -e
     
printf "\n\033[0;44m---> Starting the SSH server.\033[0m\n"
     
service ssh start
service ssh status
     
exec "$@"
```

`sshd_config`:

```
ChallengeResponseAuthentication no
UsePAM no
PrintMotd no
X11Forwarding no
AllowTcpForwarding no
AllowAgentForwarding no
PermitTunnel no
     
Subsystem sftp internal-sftp
Match Group sftp
    ChrootDirectory %h
    X11Forwarding no
    AllowTcpForwarding no
    AllowAgentForwarding no
    PermitTunnel no
    ForceCommand internal-sftp
```

`ssh_config`:

```
# Prevents "Are you sure you want to continue connecting (yes/no)?" question while connecting to the server.
# The host IP below is the client machine where the ssh command is issued from.
# Host 192.168.99.*
#   StrictHostKeyChecking no
#    UserKnownHostsFile=/dev/null
     
Host *
    HashKnownHosts yes
    GSSAPIAuthentication yes
```

Run container:

```
$ docker-compose up -d
```

SSH user login

```
ssh master@container-ip -p 22 # Password is master
```

Add a new SFTP user:

```
sudo mkdir /uploads/inanzzz
sudo mkdir /uploads/inanzzz/upload
sudo useradd -d /uploads/inanzzz -G sftp inanzzz -s /usr/sbin/nologin
echo "inanzzz:inanzzz" | sudo chpasswd
sudo chown inanzzz:sftp -R /uploads/inanzzz/upload
```

SFTP user login

```
sftp inanzzz@container-ip
```

##### 참조:

- [atmoz/sftp](https://hub.docker.com/r/atmoz/sftp/)

- [Creating a SSH and SFTP server with docker compose](http://www.inanzzz.com/index.php/post/6fa7/creating-a-ssh-and-sftp-server-with-docker-compose)
