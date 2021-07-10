---
title: "Kubernetes Seccomp"

date: 2021-07-05T21:25:35+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
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

## Check Seccomp in Docker

```shell
docker run r.j3ss.co/amicontained amicontained
```

```
Container Runtime: docker
Has Namespaces:
	pid: true
	user: false
AppArmor Profile: unconfined
Capabilities:
	BOUNDING -> chown dac_override fowner fsetid kill setgid setuid setpcap net_bind_service net_raw sys_chroot mknod audit_write setfcap
Seccomp: filtering
Blocked Syscalls (61):
	MSGRCV SYSLOG SETPGID SETSID USELIB USTAT SYSFS VHANGUP PIVOT_ROOT _SYSCTL ACCT SETTIMEOFDAY MOUNT UMOUNT2 SWAPON SWAPOFF REBOOT SETHOSTNAME SETDOMAINNAME IOPL IOPERM CREATE_MODULE INIT_MODULE DELETE_MODULE GET_KERNEL_SYMS QUERY_MODULE QUOTACTL NFSSERVCTL GETPMSG PUTPMSG AFS_SYSCALL TUXCALL SECURITY LOOKUP_DCOOKIE CLOCK_SETTIME VSERVER MBIND SET_MEMPOLICY GET_MEMPOLICY KEXEC_LOAD ADD_KEY REQUEST_KEY KEYCTL MIGRATE_PAGES UNSHARE MOVE_PAGES PERF_EVENT_OPEN FANOTIFY_INIT NAME_TO_HANDLE_AT OPEN_BY_HANDLE_AT SETNS PROCESS_VM_READV PROCESS_VM_WRITEV KCMP FINIT_MODULE KEXEC_FILE_LOAD BPF USERFAULTFD PKEY_MPROTECT PKEY_ALLOC PKEY_FREE
Looking for Docker.sock
```

{{<admonition note Seccomp true>}}  
`Seccomp: filtering`
`Blocked Syscalls (61):`
{{</admonition>}}

## Check Seccomp in Kubernetes

### Create a POD

```shell
kubectl run amicontained --image r.j3ss.co/amicontained amicontained -- amicontained
```

### Check Logs

```shell
kubectl logs amicontained
```

```
Container Runtime: docker
Has Namespaces:
        pid: true
        user: false
AppArmor Profile: docker-default (enforce)
Capabilities:
        BOUNDING -> chown dac_override fowner fsetid kill setgid setuid setpcap net_bind_service net_raw sys_chroot mknod audit_write setfcap
Seccomp: disabled
```

{{<admonition note Seccomp true>}}  
`Seccomp: disabled`
{{</admonition>}}

## Definitions

### `RuntimeDefault`

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: amicontained
  name: amicontained
spec:
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  containers:
    - command:
        - amicontained
      image: r.j3ss.co/amicontained
      name: amicontained
      securityContext:
        allowPrivilegeEscalation: false
```

```shell
kubectl create -f amicontained.yaml
```

```shell
kubectl logs amicontained
```

```
Container Runtime: docker
Has Namespaces:
        pid: true
        user: false
AppArmor Profile: docker-default (enforce)
Capabilities:
        BOUNDING -> chown dac_override fowner fsetid kill setgid setuid setpcap net_bind_service net_raw sys_chroot mknod audit_write setfcap
Seccomp: filtering
Blocked Syscalls (63):
        SYSLOG SETPGID SETSID USELIB USTAT SYSFS VHANGUP PIVOT_ROOT _SYSCTL ACCT SETTIMEOFDAY MOUNT UMOUNT2 SWAPON SWAPOFF REBOOT SETHOSTNAME SETDOMAINNAME IOPL IOPERM CREATE_MODULE INIT_MODULE DELETE_MODULE GET_KERNEL_SYMS QUERY_MODULE QUOTACTL NFSSERVCTL GETPMSG PUTPMSG AFS_SYSCALL TUXCALL SECURITY LOOKUP_DCOOKIE CLOCK_SETTIME VSERVER MBIND SET_MEMPOLICY GET_MEMPOLICY KEXEC_LOAD ADD_KEY REQUEST_KEY KEYCTL MIGRATE_PAGES UNSHARE MOVE_PAGES PERF_EVENT_OPEN FANOTIFY_INIT NAME_TO_HANDLE_AT OPEN_BY_HANDLE_AT CLOCK_ADJTIME SETNS PROCESS_VM_READV PROCESS_VM_WRITEV KCMP FINIT_MODULE KEXEC_FILE_LOAD BPF USERFAULTFD MEMBARRIER PKEY_MPROTECT PKEY_ALLOC PKEY_FREE RSEQ
Looking for Docker.sock
```

{{<admonition note Seccomp true>}}  
`Seccomp: filtering`
`Blocked Syscalls (63):`
{{</admonition>}}

### `Localhost`

```shell
cat /var/lib/kubelet/seccomp/profiles/audit.json
```

```json
{
  "defaultAction": "SCMP_ACT_LOG"
}
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: test-audit
  name: test-audit
spec:
  securityContext:
    seccompProfile:
      type: Localhost
      localhostProfile: profiles/audit.json
  containers:
    - command: [ "bash", "-c", "echo 'I just made some syscalls' && sleep 100" ]
      image: ubuntu
      name: ubuntu
      securityContext:
        allowPrivilegeEscalation: false
```

{{<admonition note localhostProfile true>}}  
The base directory of `localhostProfile` is `/var/lib/kubelet/seccomp`.  
{{</admonition>}}