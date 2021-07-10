---
title: "Linux Kernel Modules"

date: 2021-07-04T20:35:31+09:00

author: CozyFex

categories:

- linux
- kernel

tags:

- linux
- kubernetes
- kernel
- module

keywords:

- linux
- kubernetes
- kernel
- module

---

## Load Module

```shell
modprobe pcspkr
```

## Module List

```shell
lsmod
```

## Blacklist Modules

```shell
vi /etc/modprobe.d/blacklist.conf
```

```
blacklist sctp
blacklist dccp
```

```shell
shutdown -r now
```

```shell
lsmod | grep sctp
lsmod | grep dccp
```

