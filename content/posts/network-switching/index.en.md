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

## How to Connect A and B?

### Scenario

{{<mermaid>}}

graph TD;

A(Assign IP Address of the Switch to eth0 of A) --> AB(Connect A to B or Connect B to A)  
B(Assign IP Address of the Switch to eth0 of B) --> AB

{{</mermaid>}}

### Setting PC A

#### Check Ethernet List

```shell
ip link
```

#### Assign IP Address to eth0 that is connected with the Switch

```shell
ip addr add 192.168.1.10/24 dev eth0
```

### Setting PC B

#### Check Ethernet List

```shell
ip link
```

#### Assign IP Address to eth0 that is connected with the Switch

```shell
ip addr add 192.168.1.11/24 dev eth0
```

### Ping Between A and B

#### From A

```shell
ping 192.168.1.11
```

#### From B

```shell
ping 192.168.1.10
```
