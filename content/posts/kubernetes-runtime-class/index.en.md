---
title: "Kubernetes Runtime Class"

date: 2021-07-08T01:13:48+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- docker
- container runtime

tags:

- kubernetes
- k8s
- orchestration
- docker
- container runtime

keywords:

- kubernetes
- k8s
- orchestration
- docker
- container runtime

---

## Container Runtime Handlers

| Runtime | Handler |  
|:-:|:-:|  
| gVisor | runsc |  
| Kata | kata |

## gVisor

### `gvisor.yaml`

```yaml
apiVersion: node.k8s.io/v1beta1
kind: RuntimeClass
metadata:
  name: gvisor
handler: runsc
```

### Create a RuntimeClass

```shell
kubectl create -f gvisor.yaml
```

## Kata

### `kata.yaml`

```yaml
apiVersion: node.k8s.io/v1beta1
kind: RuntimeClass
metadata:
  name: kata
handler: kata
```

### Create a RuntimeClass

```shell
kubectl create -f gvisor.yaml
```

## Apply to POD

### gVisor POD

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  runtimeClassName: gvisor
  containers:
    - image: nginx
      name: nginx
```

### Kata POD

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  runtimeClassName: kata
  containers:
    - image: nginx
      name: nginx
```

## Check Container Runtime

### Connect to the Node

```shell
ssh node01
```

### Grep Process

```shell
pgrep -a nginx
```

```shell
pgrep -a runsc
```

```shell
pgrep -a kata
```

