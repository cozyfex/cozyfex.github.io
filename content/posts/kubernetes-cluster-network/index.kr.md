---
title: "Kubernetes Cluster Network"

date: 2021-05-30T18:55:16+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- cluster

tags:

- kubernetes
- network
- cluster
- port

keywords:

- kubernetes
- network
- cluster
- port

---

## Kubernetes Cluster Network Ports

{{<admonition note "Required Ports" true>}}  
공식 문서 [Ports](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#check-required-ports)
{{</admonition>}}

{{<image src="cluster-network-ports.png" width="100%">}}

## Kubernetes Network Model Plugins

{{<admonition note Plugins true>}}  
[공식 문서](https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-networking-model)  
{{</admonition>}}

## 설정된 네트워크 인터페이스 찾기

### Master Node 의 내부 IP

```shell
kubectl get nodes -o wide
```

```
NAME           STATUS   ROLES                  AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane,master   10m     v1.20.0   10.96.191.3   <none>        Ubuntu 18.04.5 LTS   5.4.0-1043-gcp   docker://19.3.0
node01         Ready    <none>                 9m16s   v1.20.0   10.96.191.6   <none>        Ubuntu 18.04.5 LTS   5.4.0-1043-gcp   docker://19.3.0
```

### 네트워크 인터페이스 확인

```shell
ip a | grep -C2 10.96.191.3
```

```
60585: eth0@if60586: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default 
    link/ether 02:42:0a:60:bf:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.96.191.3/24 brd 10.96.191.255 scope global eth0
       valid_lft forever preferred_lft forever
60587: eth1@if60588: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
```

`eth0` 네트워크 인터페이스를 찾았다!

### `eth0` 상세

```shell
ip link show eth0
```

### Worker Node 의 MAC 주소 확인

```shell
arp
arp <node-name>
arp node01
```

```
Address                  HWtype  HWaddress           Flags Mask            Iface
10.96.191.5              ether   02:42:0a:60:bf:04   C                     eth0
```

### Default Gateway 확인

```shell
# Show All Gateway
ip route
ip route show

# Specific Gateway
ip route show default
```

### Ports 확인

```shell
netstat -nlpt
```

```
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:45341         0.0.0.0:*               LISTEN      4999/kubelet        
tcp        0      0 127.0.0.1:10248         0.0.0.0:*               LISTEN      4999/kubelet        
tcp        0      0 127.0.0.1:10249         0.0.0.0:*               LISTEN      6233/kube-proxy     
tcp        0      0 127.0.0.1:2379          0.0.0.0:*               LISTEN      3863/etcd           
tcp        0      0 10.96.191.3:2379        0.0.0.0:*               LISTEN      3863/etcd           
tcp        0      0 127.0.0.11:32907        0.0.0.0:*               LISTEN      -                   
tcp        0      0 10.96.191.3:2380        0.0.0.0:*               LISTEN      3863/etcd           
tcp        0      0 127.0.0.1:2381          0.0.0.0:*               LISTEN      3863/etcd           
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      692/ttyd            
tcp        0      0 127.0.0.1:10257         0.0.0.0:*               LISTEN      4008/kube-controlle 
tcp        0      0 127.0.0.1:10259         0.0.0.0:*               LISTEN      4015/kube-scheduler 
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      602/systemd-resolve 
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      765/sshd            
tcp6       0      0 :::10250                :::*                    LISTEN      4999/kubelet        
tcp6       0      0 :::6443                 :::*                    LISTEN      3939/kube-apiserver 
tcp6       0      0 :::10256                :::*                    LISTEN      6233/kube-proxy     
tcp6       0      0 :::22                   :::*                    LISTEN      765/sshd            
tcp6       0      0 :::8888                 :::*                    LISTEN      5290/kubectl        
```

