---
title: "Kubernetes nodeSelector"

date: 2021-05-10T17:19:19+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- nodeselector

keywords:

- kubernetes
- scheduler
- nodeselector

---

## Add label to the Node

### Commands

<sub>Add label to node command structure</sub>

```shell
kubectl label nodes [node-name] [label-key]=[label-value]
```

| Name | Example |  
|:-:|:-:|  
| [node-name] | node-1 |  
| [label-key] | size |  
| [label-value] | Large |

<sub>Add label to node command</sub>

```shell
kubectl label nodes node-1 size=Large
```

## Select the Node in PODSpec

<sub>pod-definition.yml</sub>

```yaml
appVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: data-processor
      image: data-processor
  nodeSelector:
    size: Large
```

```shell
kubectl apply -f pod-definition.yml
```

