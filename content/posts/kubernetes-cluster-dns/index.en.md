---
title: "Kubernetes Cluster DNS"

date: 2021-05-30T23:42:03+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- cluster
- dns

tags:

- kubernetes
- network
- cni
- cluster
- dns

keywords:

- kubernetes
- network
- cni
- cluster
- dns

---

## Cluster DNS Structure

{{<image src="cluster-dns-structure.png" width="100%">}}

| Hostname | Namespace | Type | Root | IP Address |  
|:-:|:-:|:-:|:-:|:-:|  
| web-service | apps | svc | cluster.local | 10.107.37.188 |  
| 10-244-2-5 | apps | pod | cluster.local | 10.224.2.5 |  
| 10-244-1-5 | default | pod | cluster.local | 10.224.1.5 |

| Domain | IP Address |  
|:-:|:-:|  
| web-service.apps.svc.cluster.local | 10.107.37.188 |  
| 10-244-2-5.apps.pod.cluster.local | 10.224.2.5 |  
| 10-224-1-5.default.pod.cluster.local | 10.224.1.5 |

{{<admonition note "SVC apps web-service 10.107.37.188" true>}}

```shell
curl http://web-service

Welcome to NGINX!
```

```shell
curl http://web-service.apps.svc.cluster.local

Welcome to NGINX!
```

{{</admonition>}}

{{<admonition note "POD apps web 10.224.2.5" true>}}

```shell
curl http://10-244-2-5

Welcome to NGINX!
```

```shell
curl http://10-244-2-5.apps.pod.cluster.local

Welcome to NGINX!
```

{{</admonition>}}

{{<admonition note "POD default test 10.224.1.5" true>}}

```shell
curl http://10-244-1-5

Welcome to NGINX!
```

```shell
curl http://10-224-1-5.default

Welcome to NGINX!
```

```shell
curl http://10-224-1-5.default.pod

Welcome to NGINX!
```

```shell
curl http://10-224-1-5.default.pod.cluster.local

Welcome to NGINX!
```

```shell
curl http://web-service.apps.svc.cluster.local

Welcome to NGINX!
```

```shell
curl http://10-244-2-5.apps.pod.cluster.local

Welcome to NGINX!
```

{{</admonition>}}

## Cluster DNS Hierarchy

{{<mermaid>}}

graph TD;

R(Root)  
T(Type)  
N(Namespace)  
H(Hostname)

R --> T  
T --> N  
N --> H

C(cluster.local)  
SVC(svc)  
POD(pod)  
SAPP(apps)
PAPP(apps)
DF(default)  
WS(web-service)
IP15(10-244-1-5)  
IP25(10-244-2-5)

C --> SVC  
SVC --> SAPP  
SAPP --> WS  
C --> POD  
POD --> PAPP  
PAPP --> IP25  
POD --> DF  
DF --> IP15

{{</mermaid>}}

