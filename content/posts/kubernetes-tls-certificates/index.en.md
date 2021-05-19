---
title: "Kubernetes TLS Certificates"

date: 2021-05-19T17:59:31+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- tls
- certificate

keywords:

- kubernetes
- security
- tls
- certificate

---

## Server Certificates

{{<admonition note "Server Certificates" true>}}  
To serve security connection with client.  
{{</admonition>}}

## Root Certificates

{{<admonition note "Root Certificates" true>}}  
To sign servers certificates.  
It's called CA(Certificate Authority)  
{{</admonition>}}

## Client Certificates

{{<admonition note "Client Certificates" true>}}  
The server request to client verifying themselves.  
{{</admonition>}}

## Name Convention

|| Certificate(Public Key) | Private Key |  
|:-:|:-:|:-:|  
| Purpose | Encrypt(Lock) | Decrypt |  
| Extension Type | *.crt | *.key |  
|| *.pem | *-key.pem |  
| Examples | server.crt | server.key |  
|| server.pem | server-key.pem |  
|| client.crt | client.key |  
|| client.pem | client-key.pem |

## Kubernetes Certificates Table

| Service | Public Key | Private Key |  
|:-:|:-:|:-:|  
| KUBE-API SERVER | apiserver.crt | apiserver.key |  
| ETCD SERVER | etcdserver.crt | etcdserver.key |  
| KUBELET SERVER | kubelet.crt | kubelet.key |  
| KUBE-SCHEDULER | scheduler.crt | scheduler.key |  
| KUBE-CONTROLLER-MANAGER | controller-manager.crt | controller-manager.key |  
| KUBE-PROXY | kube-proxy.crt | kube-proxy.key |  
| Admin | admin.crt | admin.key |  
| KUBE-API SERVER -> ETCD SERVER | apiserver-etcd-client.crt | apiserver-etcd-client.key |  
| KUBE-API SERVER -> KUBELET SERVER | apiserver-kubelet-client.crt | apiserver-kubelet-client.key |  
| KUBELET SERVER --> KUBE-API SERVER | kubelet-client.crt | kubelet-client.key |

## Kubernetes Certificates Process

{{< mermaid >}}

graph LR;

A(Admin) --> KA(KUBE-API SERVER)

KS(KUBE-SCHEDULER) --> KA

KC(KUBE-CONTROLLER-MANGER) --> KA

KP(KUBE-PROXY) --> KA

ECK[etcdserver.crt/key] --> ET

KA --> AECK[apiserver-etcd-client.crt/key]

AECK --> ET(ETCD SERVER)

KA --> AKCCK[apiserver-kubelet-client.crt/key]

KLCK[kubelet.crt/key] --> KL

AKCCK --> KL(KUBELET SERVER)

KL --> KLCCK[kubelet-client.crt/key]

KLCCK --> KA

KACK[apiserver.crt/key] --> KA

ACK[admin.crt/key] --> A

KSCK[scheduler.crt/key] --> KS

KCCK[controller-manager.crt/key] --> KC

KPCK[kube-proxy.crt/key] --> KP

{{< /mermaid >}}
