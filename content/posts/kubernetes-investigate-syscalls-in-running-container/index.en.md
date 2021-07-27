---
title: "Kubernetes Investigate Syscalls in Running Container"

date: 2021-07-27T20:48:50+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- syscalls

keywords:

- kubernetes
- syscalls

---

## Find the Container

```shell
docker ps | grep collector1
```

```
3e07aee08a48        registry.killer.sh:5000/collector1     "./collector1-process"   14 seconds ago       Up 13 seconds                           k8s_c_collector1-59ddbd6c7f-vvwgt_team-yellow_099822ad-2dfc-4963-bfc7-d806e00c0daf_0
```

## Find the Process ID by the Container ID

```shell
ps aux | grep collector1-process
```

## Strace the Process

```shell
strace -p 10991
```

```
strace: Process 10991 attached
restart_syscall(<... resuming interrupted futex ...>) = -1 ETIMEDOUT (Connection timed out)
futex(0x4ad5d0, FUTEX_WAKE, 1)          = 1
kill(666, SIGTERM)                      = -1 ESRCH (No such process)
futex(0xc420030948, FUTEX_WAKE, 1)      = 1
futex(0x4afe80, FUTEX_WAIT, 0, {tv_sec=0, tv_nsec=999998945}) = -1 ETIMEDOUT (Connection timed out)
```

