---
title: "Linux Seccomp"

date: 2021-07-05T20:55:53+09:00

author: CozyFex

categories:

- linux
- seccomp

tags:

- linux
- kubernetes
- seccomp

keywords:

- linux
- kubernetes
- seccomp

---

## Run Container `docker/whalesay`

### Running Test

```shell
docker run docker/whalesay cowsay hello!
```

### Get Shell

```shell
docker run -it --rm docker/whalesay /bin/sh
```

## Get Seccomp Value with PID

### PID

```shell
ps -ef
```

```
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 12:01 pts/0    00:00:00 /bin/sh
root         9     1  0 12:01 pts/0    00:00:00 ps -ef
```

### Check Configured Seccomp Mode

```shell
grep Seccomp /proc/1/status
```

```
Seccomp:	2
Seccomp_filters:	1
```

## Seccomp Mode

| Mode | Description |  
|:-:|:-:|  
| 0 | Disabled |  
| 1 | Strict |  
| 2 | Filtered |

## Restrict SYSCALLS

### Value Descriptions

| Value | Description |  
|:-:|:-|  
| SCMP_ACT_ERRNO | Return Error |  
| SCMP_ACT_ALLOW | Execute the commands |

### `whitelist.json`

```json
{
  "defaultAction": "SCMP_ACT_ERRNO",
  "architectures": [
    "SCMP_ARCH_X86_64",
    "SCMP_ARCH_X86",
    "SCMP_ARCH_X32"
  ],
  "syscalls": [
    {
      "names": [
        "<syscall-1>",
        "<syscall-2>",
        "<syscall-3>"
      ],
      "action": "SCMP_ACT_ALLOW"
    }
  ]
}
```

### `blacklist.json`

```json
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "architectures": [
    "SCMP_ARCH_X86_64",
    "SCMP_ARCH_X86",
    "SCMP_ARCH_X32"
  ],
  "syscalls": [
    {
      "names": [
        "<syscall-1>",
        "<syscall-2>",
        "<syscall-3>"
      ],
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

### Run Docker with Seccomp

```shell
docker run -it --rm --security-opt seccomp=whitelist.json docker/whalesay /bin/sh
```

### Run Docker Unconfined Seccomp

```shell
docker run -it --rm --security-opt seccomp=unconfined docker/whalesay /bin/sh
```

```shell
date -s '19 APR 2012 22:00:00'
```

```
date: cannot set date: Operation not permitted
```

{{<admonition note Unconfined true>}}  
`date` 명령이 아직 실행되지 않습니다.  
그렇기 때문에 `unconfined` 를 설정해도 도커 컨테이너가 기본 설정인 seccomp 로 실행됩니다.  
{{</admonition>}}

## SYSCALL Number and Name

| Number | Name |  
|:-:|:-:|  
| 3 | close |  
| 35 | nanosleep |  
| 72 | fcntl |  
| 138 | fstatfs |  
| 217 | getdents64 |  
| 231 | exit_group |  
| 233 | epoll_ctl |  
| 257 | openat |  
