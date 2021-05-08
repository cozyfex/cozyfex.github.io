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
# Create POD with nginx image
kubectl run nginx --image=nginx

# Generate POD Manifest YAML file without create(--dry-run)
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yml


# Create Deployment
kubectl create deployment --image=nginx nginx

# Generate Deployment Manifest YAML file without create(--dry-run)
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yml

# Generate Deployment Manifest YAML file without create(--dry-run) with 4 replicas(--replicas=4)
kubectl create deployment --image=nginx --replicas=4 nginx --dry-run -o yaml > nginx-deployment.yml
```

## POD

### POD List

```shell
kubectl get pods
```

### Create POD with YAML

```shell
kubectl create -f pod-definition.yml
```

### Create POD with Command

```shell
kubectl run myapp-pod --image nginx
```

### POD Detail

```shell
kubectl describe pod myapp-pod
```

### Change running POD

```shell
kubectl edit pod myapp-pod
```

### Delete POD

```shell
kubectl delete pod myapp-pod
```

## ReplicaSet

### ReplicaSet List

```shell
kubectl get replicaset
```

### Create ReplicaSet with YAML

```shell
kubectl create -f rs-definition.yml
```

### ReplicaSet Detail

```shell
kubectl describe replicaset myapp-replicaset
```

### Change running ReplicaSet

```shell
kubectl edit replicaset myapp-replicaset
```

If you change image and then, you need to delete all the PODs of the ReplicaSet.

### Delete ReplicaSet

```shell
kubectl delete replicaset myapp-replicaset
```




