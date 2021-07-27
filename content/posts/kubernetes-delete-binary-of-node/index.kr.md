---
title: "Kubernetes Node 에서 Binary 삭제"

date: 2021-07-27T21:03:50+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- node
- binary
- delete

keywords:

- kubernetes
- node
- binary
- delete

---

## Port 로 실행중인 프로세스 확인

```shell
netstat -plnt | grep 6666
```

```
tcp6       0      0 :::6666                 :::*        LISTEN      9591/system-atm
```

## Port 로 프로세스 찾기

```shell
lsof -i :6666
```

```
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
system-at 9591 root    3u  IPv6  47760      0t0  TCP *:6666 (LISTEN)
```

## Process ID 로 실행파일 경로 찾기

```shell
ls -lh /proc/9591/exe
```

```
lrwxrwxrwx 1 root root 0 Sep 26 16:10 /proc/9591/exe -> /bin/system-atm
```

## Process 실행 중지

```shell
kill -9 9591
```

## 실행파일 삭제

```shell
rm /bin/system-atm
```

