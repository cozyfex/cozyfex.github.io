---
title: "Kubernetes Service Networking"

date: 2021-05-30T22:51:58+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- service

tags:

- kubernetes
- network
- cni
- service

keywords:

- kubernetes
- network
- cni
- service

---

## Service

## Kube Proxy Mode

| Mode | Description |  
|:-:|:-|  
| userspace | User space |  
| iptables | IP tables |  
| ipvs | IPVS |

## 설정된 IP 주소 범위 확인

```shell
# Structure
kubectl -n kube-system logs <pod-name> <container-name>

# Example
kubectl -n kube-system logs weave-net-nz46j weave
```

```
DEBU: 2021/05/30 14:01:47.663912 [kube-peers] Checking peer "ca:08:8c:41:28:c9" against list &{[{06:a9:f6:0f:2d:d6 controlplane} {ca:08:8c:41:28:c9 node01}]}
INFO: 2021/05/30 14:01:47.759590 Command line options: map[conn-limit:200 datapath:datapath db-prefix:/weavedb/weave-net docker-api: expect-npc:true http-addr:127.0.0.1:6784 ipalloc-init:consensus=1 ipalloc-range:10.32.0.0/12 metrics-addr:0.0.0.0:6782 name:ca:08:8c:41:28:c9 nickname:node01 no-dns:true no-masq-local:true port:6783]
INFO: 2021/05/30 14:01:47.759624 weave  2.8.1
INFO: 2021/05/30 14:01:48.234624 Re-exposing 10.44.0.0/12 on bridge "weave"
INFO: 2021/05/30 14:01:48.297234 Bridge type is bridged_fastdp
INFO: 2021/05/30 14:01:48.297464 Communication between peers is unencrypted.
INFO: 2021/05/30 14:01:48.329228 Our name is ca:08:8c:41:28:c9(node01)
INFO: 2021/05/30 14:01:48.329262 Launch detected - using supplied peer list: [172.17.0.10]
INFO: 2021/05/30 14:01:48.329303 Using "no-masq-local" LocalRangeTracker
INFO: 2021/05/30 14:01:48.329311 Checking for pre-existing addresses on weave bridge
INFO: 2021/05/30 14:01:48.331162 weave bridge has address 10.44.0.0/12
INFO: 2021/05/30 14:01:48.335212 adding entry 10.44.0.0/14 to weaver-no-masq-local of 0
INFO: 2021/05/30 14:01:48.336388 added entry 10.44.0.0/14 to weaver-no-masq-local of 0
INFO: 2021/05/30 14:01:48.337196 [allocator ca:08:8c:41:28:c9] Initialising with persisted data
INFO: 2021/05/30 14:01:48.337250 Sniffing traffic on datapath (via ODP)
INFO: 2021/05/30 14:01:48.337645 ->[172.17.0.10:6783] attempting connection
INFO: 2021/05/30 14:01:48.341061 ->[172.17.0.10:6783|06:a9:f6:0f:2d:d6(controlplane)]: connection ready; using protocol version 2
INFO: 2021/05/30 14:01:48.341306 overlay_switch ->[06:a9:f6:0f:2d:d6(controlplane)] using fastdp
INFO: 2021/05/30 14:01:48.341392 ->[172.17.0.10:6783|06:a9:f6:0f:2d:d6(controlplane)]: connection added (new peer)
INFO: 2021/05/30 14:01:48.348396 Listening for HTTP control messages on 127.0.0.1:6784
INFO: 2021/05/30 14:01:48.348418 Listening for metrics requests on 0.0.0.0:6782
INFO: 2021/05/30 14:01:48.442239 ->[172.17.0.10:6783|06:a9:f6:0f:2d:d6(controlplane)]: connection fully established
INFO: 2021/05/30 14:01:48.443791 sleeve ->[172.17.0.10:6783|06:a9:f6:0f:2d:d6(controlplane)]: Effective MTU verified at 1438
INFO: 2021/05/30 14:01:48.788859 [kube-peers] Added myself to peer list &{[{06:a9:f6:0f:2d:d6 controlplane} {ca:08:8c:41:28:c9 node01}]}
DEBU: 2021/05/30 14:01:48.799144 [kube-peers] Nodes that have disappeared: map[]
10.44.0.0
DEBU: 2021/05/30 14:01:48.881790 registering for updates for node delete events
INFO: 2021/05/30 14:01:50.394216 Discovered remote MAC aa:f0:4e:d2:14:fc at 06:a9:f6:0f:2d:d6(controlplane)
INFO: 2021/05/30 14:02:58.932741 Discovered remote MAC 62:76:26:b7:75:f2 at 06:a9:f6:0f:2d:d6(controlplane)
```

## 클러스터에서 설정된 서비스 IP 범위 확인

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep cluster-ip-range
```

```
    - --service-cluster-ip-range=10.96.0.0/12
```

## 설정된 Proxy Type 확인

```shell
# Structure
kubectl -n kube-system logs <kube-proxy-pod>

# Example
kubectl -n kube-system logs kube-proxy-p9wjb
```

```
I0530 13:37:32.534799       1 node.go:136] Successfully retrieved node IP: 172.17.0.10
I0530 13:37:32.534885       1 server_others.go:111] kube-proxy node IP is an IPv4 address (172.17.0.10), assume IPv4 operation
W0530 13:37:32.849559       1 server_others.go:579] Unknown proxy mode "", assuming iptables proxy
I0530 13:37:32.849755       1 server_others.go:186] Using iptables Proxier.
I0530 13:37:32.850696       1 server.go:650] Version: v1.19.0
I0530 13:37:32.851088       1 conntrack.go:100] Set sysctl 'net/netfilter/nf_conntrack_max' to 131072
I0530 13:37:32.851124       1 conntrack.go:52] Setting nf_conntrack_max to 131072
I0530 13:37:32.851395       1 conntrack.go:83] Setting conntrack hashsize to 32768
I0530 13:37:32.860483       1 conntrack.go:100] Set sysctl 'net/netfilter/nf_conntrack_tcp_timeout_established' to 86400
I0530 13:37:32.860553       1 conntrack.go:100] Set sysctl 'net/netfilter/nf_conntrack_tcp_timeout_close_wait' to 3600
I0530 13:37:32.861246       1 config.go:315] Starting service config controller
I0530 13:37:32.861279       1 shared_informer.go:240] Waiting for caches to sync for service config
I0530 13:37:32.861335       1 config.go:224] Starting endpoint slice config controller
I0530 13:37:32.861345       1 shared_informer.go:240] Waiting for caches to sync for endpoint slice config
I0530 13:37:32.961495       1 shared_informer.go:247] Caches are synced for endpoint slice config 
I0530 13:37:32.961536       1 shared_informer.go:247] Caches are synced for service config 
```

## Service 의 IP Tables 확인

```shell
iptables –L –t net | grep db-service
```

```
KUBE-SVC-XA5OGUC7YRHOS3PU   tcp -- anywhere 10.103.132.104    /* default/db-service: cluster IP */ tcp dpt:3306
DNAT                        tcp -- anywhere anywhere          /* default/db-service: */ tcp to:10.244.1.2:3306
KUBE-SEP-JBWCWHHQM57V2WN7   all -- anywhere anywhere          /* default/db-service: */
```

## Proxy Log 확인

```shell
cat /var/log/kube-proxy.log
```

```
I0307 04:29:29.883941       1 server_others.go:140] Using iptables Proxier.
I0307 04:29:29.912037       1 server_others.go:174] Tearing down inactive rules.
I0307 04:29:30.027360       1 server.go:448] Version: v1.11.8
I0307 04:29:30.049773       1 conntrack.go:98] Set sysctl 'net/netfilter/nf_conntrack_max' to 131072
I0307 04:29:30.049945       1 conntrack.go:52] Setting nf_conntrack_max to 131072
I0307 04:29:30.050701       1 conntrack.go:83] Setting conntrack hashsize to 32768
I0307 04:29:30.050701       1 proxier.go:294] Addming new service "default/db-service:3306" at 10.103.132.104:3306/TCP
```

