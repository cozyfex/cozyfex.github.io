---
title: "Linux SYSCALLS"

date: 2021-07-04T21:28:08+09:00

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

## 설치 확인

```shell
which strace
```

```
/usr/bin/strace
```

## 명령 실행으로 SYSCALLS 추적

```shell
strace echo 111
```

```
execve("/usr/bin/echo", ["echo", "111"], 0x7ffc891d5818 /* 8 vars */) = 0
.
.
.
[Output Truncated]
```

| Value | Description |  
|:-|:-|  
| /usr/bin/echo | Command path |  
| ["echo", "111"] | Command and parameters |  
| 0x7ffc891d5818 /* 8 vars */) | Environment values (`env \| wc -l` is 8) |

## 실행중인 프로레스의 SYSCALLS 추적

### Process ID 찾기

```shell
pidof etcd
```

```
3569
```

### Process ID 로 추적

```shell
strace -p 3569
```

## Option

```shell
strace -c echo 111
```

```
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 24.65    0.000261          43         6           pread64
 21.15    0.000224          32         7           mmap
 15.01    0.000159          39         4           mprotect
 11.14    0.000118          39         3           brk
  9.92    0.000105          26         4           close
  5.67    0.000060          60         1           write
  4.72    0.000050          16         3           fstat
  4.15    0.000044          44         1           munmap
  3.59    0.000038          19         2         1 arch_prctl
  0.00    0.000000           0         1           read
  0.00    0.000000           0         1         1 access
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         2           openat
------ ----------- ----------- --------- --------- ----------------
100.00    0.001059                    36         2 total
```
