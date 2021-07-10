---
title: "Linux Open Ports 비활성화"

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

## Listening Ports 확인

```shell
netstat -an | grep -w LISTEN
```

## Service 와 Port

```shell
cat /etc/services | grep -w 53
```
