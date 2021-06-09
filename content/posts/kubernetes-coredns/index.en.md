---
title: "Kubernetes CoreDNS"

date: 2021-05-31T00:40:55+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- cluster
- dns
- coredns

tags:

- kubernetes
- network
- cni
- cluster
- dns
- coredns

keywords:

- kubernetes
- network
- cni
- cluster
- dns
- coredns

---

## CoreDNS

{{<admonition note CoreDNS true>}}  
`CoreDNS` is a DNS server.  
It is written in Go.  
It can be used in a multitude of environments because of its flexibility.  
{{</admonition>}}

## Configuration File Location

```shell
kubectl -n kube-system describe deploy coredns | grep -C2 -i args
```

```
    Ports:       53/UDP, 53/TCP, 9153/TCP
    Host Ports:  0/UDP, 0/TCP, 0/TCP
    Args:
      -conf
      /etc/coredns/Corefile
```

## Corefile

{{<admonition note Corefile true>}}  
`CoreDNS` is passing `Corefile` as a `ConfigMap` to the `POD`s of `CoreDNS`.  
{{</admonition>}}

## Show CoreDNS ConfigMap

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

