---
title: "Docker Securing Daemon"

date: 2021-07-04T18:29:23+09:00

author: CozyFex

categories:

- docker

tags:

- docker
- kubernetes
- security

keywords:

- docker
- kubernetes
- security

---

## 기본 보안

* 비밀번호 기반 인증 비활성화
* SSH Key 기반 인증 활상화

## 인증 없이

### 인증 파일들

| Location | Type | File |  
|:-:|:-|:-|  
| Server | CRT | /var/docker/server.pem |  
| Server | Key | /var/docker/serverkey.pem |

### Daemon 설정

```shell
cat /etc/docker/daemon.json
```

```json
{
  "hosts": [
    "tcp://192.168.1.10:2376"
  ],
  "tls": true,
  "tlscert": "/var/docker/server.pem",
  "tlskey": "/var/docker/serverkey.pem"
}
```

### Client Docker CLI

```shell
docker --tls ps
```

## 인증과 함께

### 인증 파일들

| Location | Type | File |  
|:-:|:-|:-|  
| Server | CA | /var/docker/cacert.pem |  
| Server | CRT | /var/docker/server.pem |  
| Server | Key | /var/docker/serverkey.pem |  
| Client | CA | ~/.docker/cacert.pem |  
| Client | CRT | ~/.docker/client.pem |  
| Client | Key | ~/.docker/clientkey.pem |

### Daemon 설정

```shell
cat /etc/docker/daemon.json
```

```json
{
  "hosts": [
    "tcp://192.168.1.10:2376"
  ],
  "tlscert": "/var/docker/server.pem",
  "tlskey": "/var/docker/serverkey.pem",
  "tlsverify": true,
  "tlscacert": "/var/docker/cacert.pem"
}
```

### Client Docker CLI

```shell
docker --tlsverify \\
       --tlscacert=~/.docker/cacert.pem \\
       --tlscert=~/.docker/client.pem \\
       --tlskey=~/.docker/clientkey.pem
       ps
```