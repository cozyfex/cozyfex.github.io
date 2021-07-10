---
title: "Kubernetes Cluster Upgrade Process"

date: 2021-05-17T21:41:08+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- cluster maintenance
- cluster upgrade process

keywords:

- kubernetes
- cluster maintenance
- cluster upgrade process

---

## Kubernetes Core Components Versioning Rule

| Component | Versioning Rule | Example |  
|:-:|:-:|:-:|  
| kube-apiserver | X | v1.10 | | controller-manager | X-1 | v1.9 or v1.10 |  
| kube-scheduler | X-1 | v1.9 or v1.10 |  
| kubelet | X-2 | v1.8 or v1.9 or v.110 |  
| kube-proxy | X-2 | v1.8 or v1.9 or v.110 |  
| kubectl | X+1 > X-1 | v1.10 |

## Kubernetes Supported Versions

{{<admonition note Versions true>}}  
Kubernetes support recent 3 minor versions.  
{{</admonition>}}

| Version | Whether to Support |  
|:-:|:-:|  
| v1.10 | <span style="color: red;">Un-Supported</span> |  
| v1.11 | <span style="color: red;">Un-Supported</span> |  
| v1.12 | <span style="color: green;">Supported</span> |  
| v1.13 | <span style="color: green;">Supported</span> |  
| v1.14 | <span style="color: green;">Supported</span> |

## Upgrade Recommendation Process

{{<admonition note Recommendation true>}}  
Kubernetes recommend upgrading to one minor up version at a time.  
At this time, let's assume v1.10 to v1.14  
{{</admonition>}}

### First(v1.10 -> v1.11)

{{<mermaid>}}  
graph LR;  
v0(v1.10) --> v1(v1.11)  
{{</mermaid>}}

### Second(v1.11 -> v1.12)

{{<mermaid>}}  
graph LR;  
v1(v1.11) --> v2(v1.12)  
{{</mermaid>}}

### Third(v1.12 -> v1.13)

{{<mermaid>}}  
graph LR;  
v2(v1.12) --> v3(v1.13)  
{{</mermaid>}}

### Fourth(v1.13 -> v1.14)

{{<mermaid>}}  
graph LR;  
v3(v1.13) --> v4(v1.14)  
{{</mermaid>}}

## Upgrade Methods

### Cloud Services

{{<admonition note Note true>}}  
You just need few clicks.  
{{</admonition>}}

### Use `kubeadm`

```shell
kubeadm upgrade plane
```

```shell
kubeadm upgrade apply
```

### The Hard Way

{{<admonition note Note true>}}  
This means, manual upgrade.  
{{</admonition>}}

## Upgrade with `kubeadm`

### Strategy

{{<admonition success "Common" true>}}  
All strategies is to upgrade Master Node first.  
{{</admonition>}}

{{<admonition note "At Once" true>}}  
After upgrading Master Node, upgrade all Worker Nodes at once.  
{{</admonition>}}

{{<admonition note "Each Worker Node" true>}}  
After upgrading Master Node, upgrade A Worker Node at a time.  
{{</admonition>}}

{{<admonition note "Replace Worker Node" true>}}  
After upgrading Master Node, create A New Worker Node and exchange with exist A Old Version Worker Node.  
{{</admonition>}}

### Upgrade Process

{{<admonition note "Upgrade Process" true>}}  
Check this official [document](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)
{{</admonition>}}

#### Check Token

```shell
kubeadm token list
```

#### Check OS

```shell
cat /etc/*release*
```

#### Version Current Check

```shell
kubectl get nodes
```

#### Find Upgrade Version Using OS Package Manager

<sub>Ubuntu</sub>

```shell
apt update
apt-cache madison kubeadm
# find the latest 1.21 version in the list
# it should look like 1.21.x-00, where x is the latest patch
```

<sub>CentOS</sub>

```shell
yum list --showduplicates kubeadm --disableexcludes=kubernetes
# find the latest 1.21 version in the list
# it should look like 1.21.x-0, where x is the latest patch
```

#### Upgrade `kubeadm`

##### Using OS Package Manager

<sub>Ubuntu</sub>

```shell
apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=1.21.x-00 && \
apt-mark hold kubeadm
```

<sub>CentOS</sub>

```shell
yum install -y kubeadm-1.21.x-0 --disableexcludes=kubernetes
```

##### Check `kubeadm` Version

```shell
kubeadm version
```

##### Check `kubeadm` Upgrade Plan

```shell
kubeadm upgrade plan
```

##### Apply Upgrade `kubeadm`

```shell
# On Master Node
sudo kubeadm upgrade apply v1.21.x

# On Worker Nodes
sudo kubeadm upgrade node
```

#### `drain` Node

```shell
kubectl drain master --ignore-daemonsets
```

#### Upgrade `kubelet` and `kubectl`

##### Using OS Package Manager

<sub>Ubuntu</sub>

```shell
apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.21.x-00 kubectl=1.21.x-00 && \
apt-mark hold kubelet kubectl
```

<sub>CentOS</sub>

```shell
yum install -y kubelet-1.21.x-0 kubectl-1.21.x-0 --disableexcludes=kubernetes
```

##### Restart `kubelet`

```shell
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

#### `uncordon` Node

```shell
kubectl uncordon master
```

## Simple Process

### `drain` Node

```shell
kubectl drain <node-name>
```

### Check Upgrade Plan

```shell
kubeadm upgrade plan
```

### Upgrade `kubeadm`

```shell
apt-get upgrade -y kubeadm=1.12.0-00
```

### Apply `kubeadm`

```shell
# On Master Node
kubeadm upgrade apply v1.12.0

# On Worker Nodes
kubeadm upgrade node
```

### Check Nodes

```shell
kubectl get nodes
```

### Upgrade `kubelet` Component

```shell
apt-get upgrade -y kubelet=1.12.0-00
```

### Restart `kubelet` Component

```shell
systemctl restart kubelet
```

### `uncordon` Node

```shell
# On Master Node
kubectl uncordon <node-name>
```

## Simple Diagram

{{<mermaid>}}

graph TD;

D(kubectl drain <node-name>) --> C(kubeadm upgrade plan)

C --> U(apt-get upgrade -y kubeadm=1.12.0-00)

U -->|On Master Node| A1(kubeadm upgrade apply v1.12.0)

U -->|On Worker Nodes| A2(kubeadm upgrade node)

A1 --> CN(kubectl get nodes)

A2 --> CN(kubectl get nodes)

CN -->|Upgrade Component| UL(apt-get upgrade -y kubelet=1.12.0-00)

UL -->|Restart Component| R(systemctl restart kubelet)

R -->|On Master Node| UC(kubectl uncordon <node-name>)

{{</mermaid>}}
