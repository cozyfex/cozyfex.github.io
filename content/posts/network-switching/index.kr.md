---
title: "Network Switching"

date: 2021-05-29T14:27:15+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network

tags:

- kubernetes
- network
- switch

keywords:

- kubernetes
- network
- switch

---

## Switching Diagram

{{<image src="network-switching.png" width="100%">}}

## A와 B를 어떻게 연결할까?

### Scenario

{{<mermaid>}}

graph TD;

A(Set IP Address of the Switch to eth0 of A) --> AB(Connect A to B or Connect B to A)  
B(Set IP Address of the Switch to eth0 of B) --> AB

{{</mermaid>}}

### PC A 설정

#### Ethernet 목록 확인

```shell
ip link
```

#### Switch 와 연결된 eth0 에 IP 할당

```shell
ip addr add 192.168.1.10/24 dev eth0
```

### PC B 설정

#### Ethernet 목록 확인

```shell
ip link
```

#### Switch 와 연결된 eth0 에 IP 할당

```shell
ip addr add 192.168.1.11/24 dev eth0
```

### A와 B 간 Ping

#### From A

```shell
ping 192.168.1.11
```

#### From B

```shell
ping 192.168.1.10
```
