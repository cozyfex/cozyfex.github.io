---
title: "Kubernetes Delete Binary of Node"

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

## Check Running Process by Port

```shell
netstat -plnt | grep 6666
```

```
tcp6       0      0 :::6666                 :::*        LISTEN      9591/system-atm
```

## Find Process by Port

```shell
lsof -i :6666
```

```
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
system-at 9591 root    3u  IPv6  47760      0t0  TCP *:6666 (LISTEN)
```

## Find Out the Binary Path by Process ID

```shell
ls -lh /proc/9591/exe
```

```
lrwxrwxrwx 1 root root 0 Sep 26 16:10 /proc/9591/exe -> /bin/system-atm
```

## Kill the Process

```shell
kill -9 9591
```

## Delete the Binary

```shell
rm /bin/system-atm
```

