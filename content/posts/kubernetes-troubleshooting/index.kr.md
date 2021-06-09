---
title: "Kubernetes Troubleshooting"

date: 2021-06-05T19:04:31+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- troubleshooting

tags:

- kubernetes
- troubleshooting
- application
- control-plane
- worker node
- network

keywords:

- kubernetes
- troubleshooting
- application
- control-plane
- worker node
- network

---

## Application Troubleshooting

### Service Status 확인

```shell
curl http://web-service-ip:node-port
```

```
curl: (7) Failed to connect to web-service-ip port node-port: Connection timed out
```

```shell
kubectl describe svc nginx-deployment
```

```
Name:                     nginx-deployment
Namespace:                default
Labels:                   app=nginx
Annotations:              <none>
Selector:                 app=nginx
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.99.164.69
IPs:                      10.99.164.69
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32377/TCP
Endpoints:                10.38.0.1:80,10.46.0.1:80,10.46.0.2:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

{{<admonition note "Check Point!" true>}}  
`Selector` 와 `Endpoints` 를 확인하자.  
{{</admonition>}}

### POD 확인

```shell
kubectl get pods
```

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx                               1/1     Running   0          40m
nginx-deployment-588c8fc79c-9n8ns   1/1     Running   0          25m
nginx-deployment-588c8fc79c-g56l8   1/1     Running   0          25m
nginx-deployment-588c8fc79c-zx88c   1/1     Running   0          22m
```

```shell
kubectl describe pod nginx-deployment-588c8fc79c-9n8ns
```

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx                               1/1     Running   0          40m
nginx-deployment-588c8fc79c-9n8ns   1/1     Running   0          25m
nginx-deployment-588c8fc79c-g56l8   1/1     Running   0          25m
nginx-deployment-588c8fc79c-zx88c   1/1     Running   0          22m
cozyfex@master01:~$ kubectl describe pod nginx-deployment-588c8fc79c-9n8ns
Name:         nginx-deployment-588c8fc79c-9n8ns
Namespace:    default
Priority:     0
Node:         node01/10.0.2.20
Start Time:   Sat, 05 Jun 2021 09:49:07 +0000
Labels:       app=nginx
              pod-template-hash=588c8fc79c
Annotations:  <none>
Status:       Running
IP:           10.38.0.1
IPs:
  IP:           10.38.0.1
Controlled By:  ReplicaSet/nginx-deployment-588c8fc79c
Containers:
  nginx-continaer:
    Container ID:   docker://cd01500188e99587c916dfa80b76ad473c77a6e63edf8a95101a973bd07caa45
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:6d75c99af15565a301e48297fa2d121e15d80ad526f8369c526324f0f7ccb750
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 05 Jun 2021 09:49:12 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-85qt8 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-85qt8:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  26m   default-scheduler  Successfully assigned default/nginx-deployment-588c8fc79c-9n8ns to node01
  Normal  Pulling    26m   kubelet            Pulling image "nginx"
  Normal  Pulled     26m   kubelet            Successfully pulled image "nginx" in 4.067766683s
  Normal  Created    26m   kubelet            Created container nginx-continaer
  Normal  Started    26m   kubelet            Started container nginx-continaer
```

```shell
kubectl logs nginx-deployment-588c8fc79c-9n8ns -f
```

```
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2021/06/05 09:49:12 [notice] 1#1: using the "epoll" event method
2021/06/05 09:49:12 [notice] 1#1: nginx/1.21.0
2021/06/05 09:49:12 [notice] 1#1: built by gcc 8.3.0 (Debian 8.3.0-6)
2021/06/05 09:49:12 [notice] 1#1: OS: Linux 5.4.0-73-generic
2021/06/05 09:49:12 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2021/06/05 09:49:12 [notice] 1#1: start worker processes
2021/06/05 09:49:12 [notice] 1#1: start worker process 32
2021/06/05 09:49:12 [notice] 1#1: start worker process 33

```

## Control-Plane Troubleshooting

### POD 들 확인

```shell
kubectl -n kube-system get pods
```

### POD Logs 확인

```shell
kubectl -n kube-system logs kube-apiserver-master01
```

```
I0605 10:16:01.097348       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
I0605 10:16:33.568771       1 client.go:360] parsed scheme: "passthrough"
I0605 10:16:33.568819       1 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{https://127.0.0.1:2379  <nil> 0 <nil>}] <nil> <nil>}
I0605 10:16:33.568828       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
I0605 10:17:10.625041       1 client.go:360] parsed scheme: "passthrough"
I0605 10:17:10.625350       1 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{https://127.0.0.1:2379  <nil> 0 <nil>}] <nil> <nil>}
I0605 10:17:10.625502       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
I0605 10:17:43.058295       1 client.go:360] parsed scheme: "passthrough"
I0605 10:17:43.058576       1 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{https://127.0.0.1:2379  <nil> 0 <nil>}] <nil> <nil>}
I0605 10:17:43.058671       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
I0605 10:18:25.933021       1 client.go:360] parsed scheme: "passthrough"
I0605 10:18:25.933251       1 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{https://127.0.0.1:2379  <nil> 0 <nil>}] <nil> <nil>}
I0605 10:18:25.933325       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
I0605 10:19:06.900362       1 client.go:360] parsed scheme: "passthrough"
I0605 10:19:06.900437       1 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{https://127.0.0.1:2379  <nil> 0 <nil>}] <nil> <nil>}
I0605 10:19:06.900449       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
I0605 10:19:38.801946       1 client.go:360] parsed scheme: "passthrough"
I0605 10:19:38.801988       1 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{https://127.0.0.1:2379  <nil> 0 <nil>}] <nil> <nil>}
I0605 10:19:38.801996       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
I0605 10:20:10.404108       1 client.go:360] parsed scheme: "passthrough"
I0605 10:20:10.404264       1 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{https://127.0.0.1:2379  <nil> 0 <nil>}] <nil> <nil>}
I0605 10:20:10.404401       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
I0605 10:20:48.514912       1 client.go:360] parsed scheme: "passthrough"
I0605 10:20:48.515586       1 passthrough.go:48] ccResolverWrapper: sending update to cc: {[{https://127.0.0.1:2379  <nil> 0 <nil>}] <nil> <nil>}
I0605 10:20:48.516205       1 clientconn.go:948] ClientConn switching balancer to "pick_first"
```

## Worker Node Troubleshooting

### Node Status 확인

```shell
kubectl get nodes
```

```
NAME       STATUS   ROLES                  AGE     VERSION
master01   Ready    control-plane,master   41h     v1.21.1
master02   Ready    control-plane,master   41h     v1.21.1
node01     Ready    <none>                 9h      v1.21.1
node02     Ready    <none>                 3h50m   v1.21.1
```

```shell
kubectl describe node node01
```

### Node System 확인

#### Node IP 확인

```shell
kubectl get nodes -o wide
```

```
NAME       STATUS   ROLES                  AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
master01   Ready    control-plane,master   41h     v1.21.1   10.0.2.10     <none>        Ubuntu 20.04.2 LTS   5.4.0-74-generic   docker://20.10.2
master02   Ready    control-plane,master   41h     v1.21.1   10.0.2.11     <none>        Ubuntu 20.04.2 LTS   5.4.0-74-generic   docker://20.10.2
node01     Ready    <none>                 9h      v1.21.1   10.0.2.20     <none>        Ubuntu 20.04.2 LTS   5.4.0-73-generic   docker://20.10.2
node02     Ready    <none>                 3h52m   v1.21.1   10.0.2.21     <none>        Ubuntu 20.04.2 LTS   5.4.0-73-generic   docker://20.10.2
```

#### SSH Node

```shell
ssh 10.0.2.20
```

#### TOP

```shell
top
```

```
top - 10:32:04 up  2:05,  1 user,  load average: 0.25, 0.23, 0.23
Tasks: 137 total,   1 running, 136 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.8 us,  0.7 sy,  0.0 ni, 98.3 id,  0.2 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   1987.5 total,    758.5 free,    386.2 used,    842.8 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   1479.2 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
    720 root      20   0 1939300 102728  69372 S   2.3   5.0   3:22.61 kubelet
    732 root      20   0 1332544 114156  51392 S   0.7   5.6   1:03.30 dockerd
    184 root     -51   0       0      0      0 S   0.3   0.0   0:01.19 irq/18-vmwgfx
    731 root      20   0 1043376  52000  26880 S   0.3   2.6   0:09.57 containerd
   8467 root      20   0  747376  39592  29948 S   0.3   1.9   0:02.16 kube-proxy
      1 root      20   0  102208  11776   8480 S   0.0   0.6   0:03.21 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.01 kthreadd
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H
      9 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percpu_wq
     10 root      20   0       0      0      0 S   0.0   0.0   0:00.69 ksoftirqd/0
     11 root      20   0       0      0      0 I   0.0   0.0   0:05.14 rcu_sched
     12 root      rt   0       0      0      0 S   0.0   0.0   0:00.06 migration/0
     13 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0
     14 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0
     15 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1
     16 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1
     17 root      rt   0       0      0      0 S   0.0   0.0   0:00.49 migration/1
     18 root      20   0       0      0      0 S   0.0   0.0   0:00.40 ksoftirqd/1
     20 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/1:0H-kblockd
     21 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kdevtmpfs
     22 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 netns
     23 root      20   0       0      0      0 S   0.0   0.0   0:00.00 rcu_tasks_kthre
     24 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kauditd
     25 root      20   0       0      0      0 S   0.0   0.0   0:00.00 khungtaskd
     26 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
     27 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 writeback
     28 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kcompactd0
     29 root      25   5       0      0      0 S   0.0   0.0   0:00.00 ksmd
     30 root      39  19       0      0      0 S   0.0   0.0   0:00.10 khugepaged
     77 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kintegrityd
```

#### Disk

```shell
df -h
```

```
Filesystem                         Size  Used Avail Use% Mounted on
udev                               951M     0  951M   0% /dev
tmpfs                              199M  1.9M  197M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  8.8G  5.8G  2.6G  70% /
tmpfs                              994M     0  994M   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
tmpfs                              994M     0  994M   0% /sys/fs/cgroup
/dev/sda2                          976M  105M  805M  12% /boot
/dev/loop0                          70M   70M     0 100% /snap/lxd/19188
/dev/loop1                          56M   56M     0 100% /snap/core18/2066
/dev/loop2                          32M   32M     0 100% /snap/snapd/10707
/dev/loop3                          33M   33M     0 100% /snap/snapd/12057
/dev/loop4                          68M   68M     0 100% /snap/lxd/20326
/dev/loop5                          56M   56M     0 100% /snap/core18/1944
tmpfs                              199M     0  199M   0% /run/user/1000
```

### Kubelet Status 확인

```shell
sudo systemctl status kubelet.service
```

```
kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Sat 2021-06-05 08:27:09 UTC; 2h 7min ago
       Docs: https://kubernetes.io/docs/home/
   Main PID: 720 (kubelet)
      Tasks: 17 (limit: 2280)
     Memory: 133.7M
     CGroup: /system.slice/kubelet.service
             └─720 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.4.1
```

```shell
sudo journalctl -u kubelet
```

```
-- Logs begin at Wed 2021-06-02 14:12:27 UTC, end at Sat 2021-06-05 10:37:01 UTC. --
Jun 02 17:54:58 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Jun 02 17:54:58 node01 systemd[1]: kubelet.service: Current command vanished from the unit file, execu>
Jun 02 17:54:59 node01 systemd[1]: Stopping kubelet: The Kubernetes Node Agent...
Jun 02 17:54:59 node01 systemd[1]: kubelet.service: Succeeded.
Jun 02 17:54:59 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Jun 02 17:54:59 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Jun 02 17:54:59 node01 kubelet[5435]: E0602 17:54:59.303634    5435 server.go:204] "Failed to load kub>
Jun 02 17:54:59 node01 systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE
Jun 02 17:54:59 node01 systemd[1]: kubelet.service: Failed with result 'exit-code'.
Jun 02 17:55:09 node01 systemd[1]: kubelet.service: Scheduled restart job, restart counter is at 1.
Jun 02 17:55:09 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Jun 02 17:55:09 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Jun 02 17:55:09 node01 kubelet[5659]: E0602 17:55:09.755118    5659 server.go:204] "Failed to load kub>
Jun 02 17:55:09 node01 systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE
Jun 02 17:55:09 node01 systemd[1]: kubelet.service: Failed with result 'exit-code'.
Jun 02 17:55:19 node01 systemd[1]: kubelet.service: Scheduled restart job, restart counter is at 2.
Jun 02 17:55:19 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Jun 02 17:55:19 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Jun 02 17:55:19 node01 kubelet[5681]: E0602 17:55:19.943578    5681 server.go:204] "Failed to load kub>
Jun 02 17:55:19 node01 systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE
Jun 02 17:55:19 node01 systemd[1]: kubelet.service: Failed with result 'exit-code'.
Jun 02 17:55:30 node01 systemd[1]: kubelet.service: Scheduled restart job, restart counter is at 3.
Jun 02 17:55:30 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Jun 02 17:55:30 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Jun 02 17:55:30 node01 kubelet[5703]: E0602 17:55:30.187078    5703 server.go:204] "Failed to load kub>
Jun 02 17:55:30 node01 systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE
Jun 02 17:55:30 node01 systemd[1]: kubelet.service: Failed with result 'exit-code'.
Jun 02 17:55:40 node01 systemd[1]: kubelet.service: Scheduled restart job, restart counter is at 4.
Jun 02 17:55:40 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Jun 02 17:55:40 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Jun 02 17:55:40 node01 kubelet[5729]: E0602 17:55:40.759998    5729 server.go:204] "Failed to load kub>
Jun 02 17:55:40 node01 systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE
Jun 02 17:55:40 node01 systemd[1]: kubelet.service: Failed with result 'exit-code'.
Jun 02 17:55:50 node01 systemd[1]: kubelet.service: Scheduled restart job, restart counter is at 5.
Jun 02 17:55:50 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Jun 02 17:55:50 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Jun 02 17:55:50 node01 kubelet[5779]: E0602 17:55:50.914734    5779 server.go:204] "Failed to load kub>
Jun 02 17:55:50 node01 systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE
Jun 02 17:55:50 node01 systemd[1]: kubelet.service: Failed with result 'exit-code'.
Jun 02 17:56:01 node01 systemd[1]: kubelet.service: Scheduled restart job, restart counter is at 6.
Jun 02 17:56:01 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Jun 02 17:56:01 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
```

### Certificates 확인

```shell
openssl x509 -in /etc/kubernetes/pki/ca.crt -text
```

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 0 (0x0)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Jun  3 16:37:22 2021 GMT
            Not After : Jun  1 16:37:22 2031 GMT
        Subject: CN = kubernetes
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:c2:68:73:03:f7:89:ca:d1:3d:76:ca:16:a0:e4:
                    bc:df:31:4b:59:54:29:a1:ae:43:b0:1a:6b:e0:ef:
                    91:e2:73:31:a4:cb:de:e8:95:25:71:e7:65:16:cb:
                    40:5b:da:00:e5:14:6f:01:e4:7e:42:08:d4:53:d7:
                    f4:f0:2f:07:07:84:76:73:b1:3c:fb:e5:bc:26:f0:
                    8e:bf:f8:0e:52:5d:05:b6:33:5e:2b:37:6c:e6:a9:
                    b5:2b:9c:e9:90:32:b1:d8:5a:d9:b0:22:8e:4f:66:
                    12:68:8c:7a:47:7c:f6:e2:75:29:79:c9:07:d2:36:
                    dd:c4:fb:b8:f1:2b:07:d1:27:29:8b:cb:14:2b:b5:
                    14:81:d8:73:2b:8c:5c:83:ce:fb:b9:be:f1:c5:7c:
                    53:cc:17:0f:a3:44:0d:7e:fc:34:ef:4e:07:bd:9b:
                    03:db:3b:b3:f2:86:09:70:2e:89:18:38:25:a5:49:
                    1f:7c:e6:73:9d:ba:05:d2:58:69:7b:f6:97:58:36:
                    63:b4:a2:8f:69:2b:0c:d8:ea:9d:e4:3f:ac:fa:6b:
                    c0:5e:74:5c:42:68:a7:e3:6d:16:ce:d2:82:99:38:
                    c4:aa:2a:ac:f4:35:01:2d:e0:78:bf:f1:29:f9:c9:
                    4e:41:6d:36:98:a4:30:f9:a1:39:86:cb:03:44:a3:
                    b7:39
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Certificate Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier:
                D6:18:A8:92:E0:C1:08:B1:2D:D4:3A:FF:74:45:C5:B0:F6:A0:80:94
    Signature Algorithm: sha256WithRSAEncryption
         ac:a1:69:f8:5f:c8:64:4b:8f:53:95:b1:f4:db:87:3b:de:7c:
         83:70:69:62:66:77:3c:6f:ce:3b:0d:c9:24:4f:6f:29:94:d8:
         cb:2b:2a:ed:b2:d2:b6:48:1f:2d:68:a5:ac:50:7d:2a:47:50:
         07:0d:fd:da:2f:bc:63:af:5b:ef:a4:48:a4:ef:5f:51:0f:1f:
         a1:6a:53:38:e0:65:9d:f6:d4:01:6a:fe:6e:0e:9e:bd:61:90:
         11:30:6f:be:5d:f4:f1:d5:99:3d:6b:76:ce:89:c8:c3:af:b4:
         88:0c:3e:af:fc:9b:72:5c:99:42:4f:28:45:78:f8:9d:c7:07:
         3d:6d:11:fd:07:6f:5b:7d:dd:90:57:4e:0f:b5:09:92:65:99:
         6f:13:61:e6:cc:51:2a:55:d1:f7:c2:35:0d:1f:5a:0f:6e:fa:
         eb:54:2e:2b:40:e4:fc:87:cf:8e:fe:11:f8:99:7c:6c:3f:1a:
         ce:20:03:57:56:77:45:5c:2b:14:7f:18:cc:d5:24:d3:54:13:
         21:12:b9:80:0d:07:14:07:c0:52:eb:d0:de:49:0d:58:c7:84:
         7f:87:2a:2a:cb:53:f0:e6:c8:f2:f2:19:5a:27:63:05:6b:7b:
         8b:87:12:62:4d:a0:38:2a:e4:65:65:38:a1:42:dd:cd:22:98:
         ba:5b:1c:1b
-----BEGIN CERTIFICATE-----
MIIC5zCCAc+gAwIBAgIBADANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwprdWJl
cm5ldGVzMB4XDTIxMDYwMzE2MzcyMloXDTMxMDYwMTE2MzcyMlowFTETMBEGA1UE
AxMKa3ViZXJuZXRlczCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAMJo
cwP3icrRPXbKFqDkvN8xS1lUKaGuQ7Aaa+DvkeJzMaTL3uiVJXHnZRbLQFvaAOUU
bwHkfkII1FPX9PAvBweEdnOxPPvlvCbwjr/4DlJdBbYzXis3bOaptSuc6ZAysdha
2bAijk9mEmiMekd89uJ1KXnJB9I23cT7uPErB9EnKYvLFCu1FIHYcyuMXIPO+7m+
8cV8U8wXD6NEDX78NO9OB72bA9s7s/KGCXAuiRg4JaVJH3zmc526BdJYaXv2l1g2
Y7Sij2krDNjqneQ/rPprwF50XEJop+NtFs7Sgpk4xKoqrPQ1AS3geL/xKfnJTkFt
NpikMPmhOYbLA0SjtzkCAwEAAaNCMEAwDgYDVR0PAQH/BAQDAgKkMA8GA1UdEwEB
/wQFMAMBAf8wHQYDVR0OBBYEFNYYqJLgwQixLdQ6/3RFxbD2oICUMA0GCSqGSIb3
DQEBCwUAA4IBAQCsoWn4X8hkS49TlbH024c73nyDcGliZnc8b847DckkT28plNjL
KyrtstK2SB8taKWsUH0qR1AHDf3aL7xjr1vvpEik719RDx+halM44GWd9tQBav5u
Dp69YZARMG++XfTx1Zk9a3bOicjDr7SIDD6v/JtyXJlCTyhFePidxwc9bRH9B29b
fd2QV04PtQmSZZlvE2HmzFEqVdH3wjUNH1oPbvrrVC4rQOT8h8+O/hH4mXxsPxrO
IANXVndFXCsUfxjM1STTVBMhErmADQcUB8BS69DeSQ1Yx4R/hyoqy1Pw5sjy8hla
J2MFa3uLhxJiTaA4KuRlZTihQt3NIpi6Wxwb
-----END CERTIFICATE-----
```

## Network Troubleshooting

### Kubernetes 에서 Network Plugin

{{<admonition note "Plugins Execute Responsibility" true>}}  
`kubelet` 이 플러그인 실행의 책임이 있다.  
{{</admonition>}}

| `kubelet` Network Option | Description |  
|:-:|:-|  
| `-cni-bin-dir` | `kubelet` 은 시작할 때 플러그인이 있는지, 이 디렉토리를 조사한다. |  
| `-network-plugin` | `cni-bin-dir` 에서 사용팔 네트워크 플러그인 |

### CNI Plugins

#### Weave Net

[Integrating Kubernetes via the Addon](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/)

```shell
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

#### Flannel

[Flannel](https://github.com/flannel-io/flannel#flannel)

```shell
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

#### Calico

[Quickstart for Calico on Kubernetes](https://docs.projectcalico.org/getting-started/kubernetes/quickstart)

```shell
kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
```

### DNS in Kubernetes

{{<admonition note CoreDNS true>}}  
Kubernetes 는 DNS 서버를 위해 `CoreDNS` 를 사용한다.  
{{</admonition>}}

#### CoreDNS Deployment

```shell
kubectl -n kube-system get deploy
```

```
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
coredns   2/2     2            2           42h
```

```shell
kubectl -n kube-system describe deploy coredns
```

```
Name:                   coredns
Namespace:              kube-system
CreationTimestamp:      Thu, 03 Jun 2021 16:37:50 +0000
Labels:                 k8s-app=kube-dns
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               k8s-app=kube-dns
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 25% max surge
Pod Template:
  Labels:           k8s-app=kube-dns
  Service Account:  coredns
  Containers:
   coredns:
    Image:       k8s.gcr.io/coredns/coredns:v1.8.0
    Ports:       53/UDP, 53/TCP, 9153/TCP
    Host Ports:  0/UDP, 0/TCP, 0/TCP
    Args:
      -conf
      /etc/coredns/Corefile
    Limits:
      memory:  170Mi
    Requests:
      cpu:        100m
      memory:     70Mi
    Liveness:     http-get http://:8080/health delay=60s timeout=5s period=10s #success=1 #failure=5
    Readiness:    http-get http://:8181/ready delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /etc/coredns from config-volume (ro)
  Volumes:
   config-volume:
    Type:               ConfigMap (a volume populated by a ConfigMap)
    Name:               coredns
    Optional:           false
  Priority Class Name:  system-cluster-critical
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   coredns-558bd4d5db (2/2 replicas created)
Events:          <none>
```

{{<admonition note "Check Point!" true>}}  
`Labels` 를 확인하자!  
`k8s-app=kube-dns` 이다.  
{{</admonition>}}

#### CoreDNS ServiceAccount

```shell
kubectl -n kube-system get sa | grep coredns
```

```
NAME                                 SECRETS   AGE
coredns                              1         42h
```

```shell
kubectl -n kube-system describe sa coredns
```

```
Name:                coredns
Namespace:           kube-system
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   coredns-token-qvpb6
Tokens:              coredns-token-qvpb6
Events:              <none>
```

#### CoreDNS Service

```shell
kubectl -n kube-system get svc -o wide
```

```
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE   SELECTOR
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   42h   k8s-app=kube-dns
```

{{<admonition note "Check Point!" true>}}  
`SELECTOR` 를 확인하자!  
위에서 봤던 `k8s-app=kube-dns` 이다.  
{{</admonition>}}

#### CoreDNS ClusterRole

##### system:coredns

```shell
kubectl -n kube-system get clusterrole | grep coredns
```

```
system:coredns                                                         2021-06-03T16:37:50Z
```

```shell
kubectl -n kube-system describe clusterrole system:coredns
```

```
Name:         system:coredns
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  nodes       []                 []              [get]
  endpoints   []                 []              [list watch]
  namespaces  []                 []              [list watch]
  pods        []                 []              [list watch]
  services    []                 []              [list watch]
```

##### system:kube-dns

```shell
kubectl -n kube-system get clusterrole | grep kube-dns
```

```
system:kube-dns                                                        2021-06-03T16:37:45Z
```

```shell
kubectl -n kube-system describe clusterrole system:kube-dns
```

```
Name:         system:kube-dns
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  endpoints  []                 []              [list watch]
  services   []                 []              [list watch]
```

#### CoreDNS ClusterRoleBinding

##### system:coredns

```shell
kubectl -n kube-system get clusterrolebinding | grep system:coredns
```

```
NAME                                                   ROLE                                                                               AGE
system:coredns                                         ClusterRole/system:coredns                                                         42h
```

```shell
kubectl -n kube-system describe clusterrolebinding system:coredns
```

```
Name:         system:coredns
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  system:coredns
Subjects:
  Kind            Name     Namespace
  ----            ----     ---------
  ServiceAccount  coredns  kube-system
```

##### system:kube-dns

```shell
kubectl -n kube-system get clusterrolebinding | grep system:kube-dns
```

```
NAME                                                   ROLE                                                                               AGE
system:kube-dns                                        ClusterRole/system:kube-dns                                                        42h
```

```shell
kubectl -n kube-system describe clusterrolebinding system:kube-dns
```

```
Name:         system:kube-dns
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
Role:
  Kind:  ClusterRole
  Name:  system:kube-dns
Subjects:
  Kind            Name      Namespace
  ----            ----      ---------
  ServiceAccount  kube-dns  kube-system
```

#### CoreDNS ConfigMap

```shell
kubectl -n kube-system get cm
```

```
NAME                                 DATA   AGE
coredns                              1      42h
```

```shell
kubectl -n kube-system describe cm coredns
```

```
Name:         coredns
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Data
====
Corefile:
----
.:53 {
    errors
    health {
       lameduck 5s
    }
    ready
    kubernetes cluster.local in-addr.arpa ip6.arpa {
       pods insecure
       fallthrough in-addr.arpa ip6.arpa
       ttl 30
    }
    prometheus :9153
    forward . /etc/resolv.conf {
       max_concurrent 1000
    }
    cache 30
    loop
    reload
    loadbalance
}

Events:  <none>
```

{{<admonition note "Check Point!" true>}}  
53번 포트를 사용하는 `CoreDNS` 를 확인할 수 있다.  
{{</admonition>}}

#### CoreDNS Troubleshooting

##### CoreDNS PODs Pending

{{<admonition note "Check Point!" true>}}  
네트워크 플러그인이 설치되었는지 확인하자!  
{{</admonition>}}

##### CoreDNS PODs CrashLoopBackOff or Error

{{<admonition note "Check Point!" true>}}

이 케이스에서는, SELinux 와 오래된 버전의 Docker 를 사용하고 있을 것이라고 예상된다.  
이 문제를 해결하기 위해서는, 아래의 옵션들을 시도해 볼 수 있다.

1. 최신 버전의 Docker 로 업그레이드
2. SELinux 비활성화
3. CoreDNS` Deployment 의 `allowPrivilegeEscalation` 를 true 로 수정

```shell
kubectl -n kube-system get deployment coredns -o yaml | \
    sed 's/allowPrivilegeEscalation: false/allowPrivilegeEscalation: true/g' | \
    kubectl apply -f -
```

4. CoreDNS POD 들에서 루프가 있으면 `CrashLoopBackOff` 상태의 원인이 된다.
    - kubelet YAML 설정파일에 아래 내용 추가

        - `resolveConf`: path-to-real-resolv-conf-file

    - 호스트 노드에서 로컬 DNS 캐쉬를 비활성화하고, `/etc/resolv.conf` 를 원본으로 복원한다.
    - Corefile 수정하고, 포워드를 `. /etc/resolv.conf` 로 교체한다.

{{</admonition>}}

##### 유요한 `endpoints`

```shell
kubectl -n kube-system get ep
```

```
NAME       ENDPOINTS                                            AGE
kube-dns   10.40.0.1:53,10.40.0.2:53,10.40.0.1:53 + 3 more...   43h
```

### Kube-Proxy

{{<admonition note "kube-proxy" true>}}  
`kube-proxy` 는 클러스터에서 각가의 노드에서 실행되는 네트워크 정책이다.  
`kube-proxy` 는 노드들에서 네트워크 규칙을 관리한다.  
`kubeadm` 으로 설정된 클러스터에서 `DaemonSet` 으로 구성된 `kube-proxy` 를 볼 수 있다.  
`kube-proxy` 는 서비스들과 각각의 서비스와 연결된 엔드포인트를 감시할 책임이 있다.  
{{</admonition>}}

{{<admonition note "Check Point!" true>}}

`kube-proxy` 가 뭔가 잘못되었을 때, 아래의 사항들을 확인해보자.

1. `ConfigMap` 중 `kube-proxy` 확인
2. `config.conf` 의 `Data` 섹션을 확인
3. `DaemonSet` 중 `kube-proxy` 확인
4. `ConfigMap` 중 `kube-proxy` 에서 `Volumes` 과 `Mounts` 정확한지 확인
5. `--config=/var/lib/kube-proxy/config.conf` 가 정확한지 확인

{{</admonition>}}

```shell
kubectl -n kube-system describe ds kube-proxy
```

```
Name:           kube-proxy
Selector:       k8s-app=kube-proxy
Node-Selector:  kubernetes.io/os=linux
Labels:         k8s-app=kube-proxy
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 4
Current Number of Nodes Scheduled: 4
Number of Nodes Scheduled with Up-to-date Pods: 4
Number of Nodes Scheduled with Available Pods: 4
Number of Nodes Misscheduled: 0
Pods Status:  4 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           k8s-app=kube-proxy
  Service Account:  kube-proxy
  Containers:
   kube-proxy:
    Image:      k8s.gcr.io/kube-proxy:v1.21.1
    Port:       <none>
    Host Port:  <none>
    Command:
      /usr/local/bin/kube-proxy
      --config=/var/lib/kube-proxy/config.conf
      --hostname-override=$(NODE_NAME)
    Environment:
      NODE_NAME:   (v1:spec.nodeName)
    Mounts:
      /lib/modules from lib-modules (ro)
      /run/xtables.lock from xtables-lock (rw)
      /var/lib/kube-proxy from kube-proxy (rw)
  Volumes:
   kube-proxy:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      kube-proxy
    Optional:  false
   xtables-lock:
    Type:          HostPath (bare host directory volume)
    Path:          /run/xtables.lock
    HostPathType:  FileOrCreate
   lib-modules:
    Type:               HostPath (bare host directory volume)
    Path:               /lib/modules
    HostPathType:
  Priority Class Name:  system-node-critical
Events:                 <none>
```

{{<admonition note "Check Point!" true>}}  
`Command` 를 확인.  
설정파일은 `/var/lib/kube-proxy/config.conf` 이다.  
{{</admonition>}}

#### `kube-system` Namespace 에서 `kube-proxy` POD 들 확인

```shell
kubectl get pods --all-namespaces | grep kube-proxy
```

```
NAMESPACE     NAME                                READY   STATUS    RESTARTS   AGE
kube-system   kube-proxy-cmph2                    1/1     Running   2          11h
kube-system   kube-proxy-drxbm                    1/1     Running   1          5h26m
kube-system   kube-proxy-qcwh7                    1/1     Running   5          43h
kube-system   kube-proxy-tgdzj                    1/1     Running   3          43h
```

#### `kube-proxy` Logs 확인

```shell
kubectl -n kube-system logs kube-proxy-cmph2
```

#### ConfigMap 중 `kube-proxy`

```shell
kubectl -n kube-system describe cm kube-proxy
```

```
Name:         kube-proxy
Namespace:    kube-system
Labels:       app=kube-proxy
Annotations:  kubeadm.kubernetes.io/component-config.hash: sha256:abbfdfdf2193cde1470588f464d713dc0078905117ab32580f202e7970a922a3

Data
====
config.conf:
----
apiVersion: kubeproxy.config.k8s.io/v1alpha1
bindAddress: 0.0.0.0
bindAddressHardFail: false
clientConnection:
  acceptContentTypes: ""
  burst: 0
  contentType: ""
  kubeconfig: /var/lib/kube-proxy/kubeconfig.conf
  qps: 0
clusterCIDR: ""
configSyncPeriod: 0s
conntrack:
  maxPerCore: null
  min: null
  tcpCloseWaitTimeout: null
  tcpEstablishedTimeout: null
detectLocalMode: ""
enableProfiling: false
healthzBindAddress: ""
hostnameOverride: ""
iptables:
  masqueradeAll: false
  masqueradeBit: null
  minSyncPeriod: 0s
  syncPeriod: 0s
ipvs:
  excludeCIDRs: null
  minSyncPeriod: 0s
  scheduler: ""
  strictARP: false
  syncPeriod: 0s
  tcpFinTimeout: 0s
  tcpTimeout: 0s
  udpTimeout: 0s
kind: KubeProxyConfiguration
metricsBindAddress: ""
mode: ""
nodePortAddresses: null
oomScoreAdj: null
portRange: ""
showHiddenMetricsForVersion: ""
udpIdleTimeout: 0s
winkernel:
  enableDSR: false
  networkName: ""
  sourceVip: ""
kubeconfig.conf:
----
apiVersion: v1
kind: Config
clusters:
- cluster:
    certificate-authority: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    server: https://10.0.2.100:1644
  name: default
contexts:
- context:
    cluster: default
    namespace: default
    user: default
  name: default
current-context: default
users:
- name: default
  user:
    tokenFile: /var/run/secrets/kubernetes.io/serviceaccount/token
Events:  <none>
```

{{<admonition note "Check Point!" true>}}

```
Data
====
config.conf:
----
```

{{</admonition>}}

#### DaemonSet 중 `kube-proxy`

```shell
kubectl -n kube-system describe ds kube-proxy
```

```
Name:           kube-proxy
Selector:       k8s-app=kube-proxy
Node-Selector:  kubernetes.io/os=linux
Labels:         k8s-app=kube-proxy
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 4
Current Number of Nodes Scheduled: 4
Number of Nodes Scheduled with Up-to-date Pods: 4
Number of Nodes Scheduled with Available Pods: 4
Number of Nodes Misscheduled: 0
Pods Status:  4 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           k8s-app=kube-proxy
  Service Account:  kube-proxy
  Containers:
   kube-proxy:
    Image:      k8s.gcr.io/kube-proxy:v1.21.1
    Port:       <none>
    Host Port:  <none>
    Command:
      /usr/local/bin/kube-proxy
      --config=/var/lib/kube-proxy/config.conf
      --hostname-override=$(NODE_NAME)
    Environment:
      NODE_NAME:   (v1:spec.nodeName)
    Mounts:
      /lib/modules from lib-modules (ro)
      /run/xtables.lock from xtables-lock (rw)
      /var/lib/kube-proxy from kube-proxy (rw)
  Volumes:
   kube-proxy:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      kube-proxy
    Optional:  false
   xtables-lock:
    Type:          HostPath (bare host directory volume)
    Path:          /run/xtables.lock
    HostPathType:  FileOrCreate
   lib-modules:
    Type:               HostPath (bare host directory volume)
    Path:               /lib/modules
    HostPathType:
  Priority Class Name:  system-node-critical
Events:                 <none>
```

{{<admonition note "Check Point!" true>}}

```
      /var/lib/kube-proxy from kube-proxy (rw)
```

```
  Volumes:
   kube-proxy:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      kube-proxy
    Optional:  false
```

{{</admonition>}}

### `kube-dns` 의 엔드포인트

{{<admonition note "Check Point!" true>}}  
이 섹션에서는, `Endpoints` 의 `labels` 와 `Service` 의 `selector` 를 확인  
{{</admonition>}}

```shell
kubectl get ep -A
```

```
NAMESPACE     NAME               ENDPOINTS                                            AGE
default       kubernetes         10.0.2.10:6443,10.0.2.11:6443                        47h
default       nginx-deployment   10.38.0.1:80,10.46.0.1:80,10.46.0.2:80               6h9m
kube-system   kube-dns           10.40.0.1:53,10.40.0.2:53,10.40.0.1:53 + 3 more...   47h
```

#### EndPoint Labels

```shell
kubectl -n kube-system describe ep kube-dns
```

```
Name:         kube-dns
Namespace:    kube-system
Labels:       k8s-app=kube-dns
              kubernetes.io/cluster-service=true
              kubernetes.io/name=CoreDNS
Annotations:  endpoints.kubernetes.io/last-change-trigger-time: 2021-06-05T16:07:49Z
Subsets:
  Addresses:          10.40.0.1,10.40.0.2
  NotReadyAddresses:  <none>
  Ports:
    Name     Port  Protocol
    ----     ----  --------
    dns-tcp  53    TCP
    dns      53    UDP
    metrics  9153  TCP

Events:  <none>
```

{{<admonition note "Check Point!" true>}}  
`Labels` 확인  
{{</admonition>}}

#### Service Selector

```shell
kubectl -n kube-system get svc -o wide
```

```
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE   SELECTOR
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   47h   k8s-app=kube-dns
```

{{<admonition note "Check Point!" true>}}  
`SELECTOR` 확인  
{{</admonition>}}



