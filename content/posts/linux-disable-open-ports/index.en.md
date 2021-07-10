---
title: "Linux Disable Open Ports"

date: 2021-07-04T20:40:27+09:00

author: CozyFex

categories:

- linux

tags:

- linux
- kubernetes
- ports

keywords:

- linux
- kubernetes
- ports

---

## Check Listening Ports

```shell
netstat -an | grep -w LISTEN
```

## Service with Port

```shell
cat /etc/services | grep -w 53
```
