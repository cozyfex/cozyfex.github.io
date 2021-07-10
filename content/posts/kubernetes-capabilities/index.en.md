---
title: "Kubernetes Capabilities"

date: 2021-07-06T16:58:12+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- linux

tags:

- kubernetes
- linux
- capabilities

keywords:

- kubernetes
- linux
- capabilities

---

## Linux Capabilities

### Kernel < 2.2

{{<mermaid>}}

graph LR;

P(Privileged Process)

U(Unprivileged Process)

{{</mermaid>}}

### Kernel >= 2.2

{{<mermaid>}}

graph TD;

P(Privileged Process)

P --> CC[CAP_CHOWN]

P --> CS[CAP_SYS_TIME]

P --> CN[CAP_NET_ADMIN]

P --> CB[CAP_SYS_BOOT]

{{</mermaid>}}

## Linux Capabilities

{{<mermaid>}}

graph TD;

CC(CAP_CHOWN)

CAC(CAP_AUDIT_CONTROL)

CB(CAP_BPF)

CDO(CAP_DAC_OVERRIDE)

CS(CAP_SETGID)

{{</mermaid>}}

{{<mermaid>}}

graph TD;

CF(CAP_FOWNER)

CK(CAP_KILL)

CNA(CAP_NET_ADMIN)

CNR(CAP_NET_RAW)

CST(CAP_SYS_TIME)

{{</mermaid>}}

{{<mermaid>}}

graph TD;

CSU(CAP_SETUID)

CSA(CAP_SYS_ADMIN)

CSB(CAP_SYS_BOOT)

CSP(CAP_SYS_PTRACE)

CWA(CAP_WAKE_ALARM)

{{</mermaid>}}

## Get Capabilities

### Binary

```shell
getcap /usr/bin/ping
```

```
/usr/bin/ping = cap_net_raw+ep
```

### PID

```shell
ps -ef | grep /usr/sbin/sshd | grep –v grep
```

```
root       779     1  0 03:55 ?        00:00:00 /usr/sbin/sshd -D
```

```shell
getpcaps 779
```

```
capabilities for `779': = 
cap_chown,cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_kill,cap_setgid ,cap_setuid,cap_setpcap,cap_linux_immutable,cap_net_bind_service,cap_net_broadcast,cap_n et_admin,cap_net_raw,cap_ipc_lock,cap_ipc_owner,cap_sys_module,cap_sys_rawio,cap_sys_chr oot,cap_sys_ptrace,cap_sys_pacct,cap_sys_admin,cap_sys_boot,cap_sys_nice,cap_sys_resourc e,cap_sys_time,cap_sys_tty_config,cap_mknod,cap_lease,cap_audit_write,cap_audit_control, cap_setfcap,cap_mac_override,cap_mac_admin,cap_syslog,cap_wake_alarm,cap_block_suspend,c ap_audit_read+ep
```

## Kubernetes Definition

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
spec:
  containers:
    - name: ubuntu-sleeper
      image: ubuntu
      command: [ "sleep", "1000" ]
      securityContext:
        capabilities:
          add: [ "SYS_TIME" ]
          drop: [ "CHOWN" ]
```


