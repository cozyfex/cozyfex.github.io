---
title: "Linux SSH 경화"

date: 2021-07-04T19:15:27+09:00

author: CozyFex

categories:

- linux
- ssh
- hardening

tags:

- linux
- kubernetes
- ssh
- hardening

keywords:

- linux
- kubernetes
- ssh
- hardening

---

## Username 과 Password 로 SSH 연결

```shell
ssh <hostname OR IP Address>
```

```shell
ssh <user>@<hostname OR IP Address>
```

## Public 과 Private Key 로 SSH 연결

### Key 생성

```shell
ssh-keygen -t rsa
```

### Public Key 를 Server 로 복사

```shell
ssh-copy-id mark@node01
```

### Server 에서 복사된 Public Key 확인

```shell
cat /home/mark/.ssh/authorized_keys
```

### 서버 접속

```shell
ssh node01
```

## SSH Service 경화

```shell
vi /etc/ssh/sshd_config
```

```
...
PermitRootLogin no
PasswordAuthentication no
...
```

```shell
systemctl restart sshd
```



