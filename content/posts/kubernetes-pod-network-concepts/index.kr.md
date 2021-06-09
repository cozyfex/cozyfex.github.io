---
title: "Kubernetes POD Network Concepts"

date: 2021-05-30T19:45:09+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- pod

tags:

- kubernetes
- network
- pod

keywords:

- kubernetes
- network
- pod

---

## POD Network Model

| Concept |  
|:-|  
| POD 은 하나의 IP 주소를 가져야 한다. |  
| POD 은 같은 노드에서 모든 다른 POD 들과 연결 가능하여야 한다. |  
| POD 은 NAT 없이 다른 노드들의 모든 다른 POD 들과 연결 가능하여야 한다. |
