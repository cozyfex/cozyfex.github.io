---
title: "Kubernetes CNI Weave"

date: 2021-05-30T20:14:52+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- cni
- weave

tags:

- kubernetes
- network
- cni
- container network interface
- weave
- weave works

keywords:

- kubernetes
- network
- cni
- container network interface
- weave
- weave works

---

## Install Weave Net

{{<admonition note "Weave Net" true>}}  
You can check [Weave Net](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/) official installation.  
{{</admonition>}}

```shell
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

## Weave Net Objects

### POD List

```shell
kubectl -n kube-system get pods | grep weave
```

### DaemonSet

```shell
kubectl -n kube-system get ds | grep weave
```

## Weave Net IPAM(IP Address Management)

### IPAM Types

| Type | Description |  
|:-:|:-|  
| DHCP | Dynamic Host Configuration Protocol |  
| host-local | Localhost |

### Check Default Gateway of Another Node

```shell
# Structure
kubectl -n <namespace> exec <pod-name> -- <command>

# Example
kubectl -n kube-system exec weave-net-gcjhh -- ip route
```

```
Defaulting container name to weave.
Use 'kubectl describe pod/weave-net-gcjhh -n kube-system' to see all of the containers in this pod.
default via 172.17.0.1 dev ens3 
10.32.0.0/12 dev weave proto kernel scope link src 10.38.0.0 
172.17.0.0/16 dev ens3 proto kernel scope link src 172.17.0.45 
172.18.0.0/24 dev docker0 proto kernel scope link src 172.18.0.1 linkdown 
```