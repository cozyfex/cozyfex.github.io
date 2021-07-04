---
title: "Linux SSH Hardening"

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

## SSH with Username and Password

```shell
ssh <hostname OR IP Address>
```

```shell
ssh <user>@<hostname OR IP Address>
```

## SSH with Public and Private Key

### Generate Keys

```shell
ssh-keygen -t rsa
```

### Copy Public Key to the Server

```shell
ssh-copy-id mark@node01
```

### Check the Copied Public Key on the Server

```shell
cat /home/mark/.ssh/authorized_keys
```

### Access the Server

```shell
ssh node01
```

## Hardening SSH Service

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



