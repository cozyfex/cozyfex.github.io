---
title: "Kubernetes 명령"

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

### POD 리스트

```shell
kubectl get pods
```

### YAML로 POD 생성하기

```shell
kubectl create -f pod-definition.yml
```

### 명령으로 POD 생성하기

```shell
kubectl run myapp-pod --image nginx
```

### POD 상세정보

```shell
kubectl describe pod myapp-pod
```

### 동작중인 POD 변경하기

```shell
kubectl edit pod myapp-pod
```

### POD 삭제하기

```shell
kubectl delete pod myapp-pod
```

## ReplicaSet

### ReplicaSet 리스트

```shell
kubectl get replicaset
```

### YAML로 ReplicaSet 생성하기

```shell
kubectl create -f rs-definition.yml
```

### ReplicaSet 상세정보

```shell
kubectl describe replicaset myapp-replicaset
```

### 동작중인 ReplicaSet 변경하기

```shell
kubectl edit replicaset myapp-replicaset
```

If you change image and then, you need to delete all the PODs of the ReplicaSet.

### ReplicaSet 삭제하기

```shell
kubectl delete replicaset myapp-replicaset
```




