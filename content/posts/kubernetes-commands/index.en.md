---
title: "Kubernetes Commands"

date: 2021-05-07T14:15:04+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- command
- kubectl

keywords:

- kubernetes
- command
- kubectl

---

## Command Tip!

```shell
# Create a POD with nginx image
kubectl run nginx --image=nginx

# Generate POD Manifest YAML file without create(--dry-run)
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yml


# Create a Deployment
kubectl create deployment --image=nginx nginx

# Generate Deployment Manifest YAML file without create(--dry-run)
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yml

# Generate Deployment Manifest YAML file without create(--dry-run) with 4 replicas(--replicas=4)
kubectl create deployment --image=nginx --replicas=4 nginx --dry-run -o yaml > nginx-deployment.yml
```

## POD

### POD List

```shell
# Basic
kubectl get pods

# Wide
kubectl get pods -o wide

# Selector
kubectl get pods --selector key=value

# Selector with no headers and count PODs
kubectl get pods --selector key=value --no-headers | wc -l
```

### Create a POD with YAML

```shell
kubectl create -f pod-definition.yml
```

### Create a POD with Command

```shell
kubectl run myapp-pod --image nginx
```

### POD Detail

```shell
kubectl describe pod myapp-pod
```

### Change Running POD

```shell
kubectl edit pod myapp-pod
```

### Delete POD

```shell
kubectl delete pod myapp-pod
```

### Generate a YAML file from and exist POD

```shell
kubectl get pod myapp-pod -o yaml > my-new-pod.yml
```

## ReplicaSet

### ReplicaSet List

```shell
kubectl get replicaset
```

### Create a ReplicaSet with YAML

```shell
kubectl create -f rs-definition.yml
```

### ReplicaSet Detail

```shell
kubectl describe replicaset myapp-replicaset
```

### Change Running ReplicaSet

```shell
kubectl edit replicaset myapp-replicaset
```

If you change image and then, you need to delete all the PODs of the ReplicaSet.

### Delete ReplicaSet

```shell
kubectl delete replicaset myapp-replicaset
```

## Node

### Node List

```shell
# Default
kubectl get nodes

# Show Labels
kubectl get nodes --show-labels
```

### Set label to Node

<sub>Set label to Node Command Structure</sub>

```shell
kubectl label nodes [node-name] [key]=[value]
```

| Name | Example |  
|:-:|:-:|  
| [node-name] | node01 |  
| [key] | size |  
| [value] | Large |

<sub>Set label to Node Command</sub>

```shell
kubectl label nodes node01 size=Large
```




