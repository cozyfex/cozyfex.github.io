---
title: "Kubernetes Deployment"

date: 2021-05-08T15:25:57+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- deployment

keywords:

- kubernetes
- deployment

---

## 알고가자!

`Deployment`는 자동으로 `ReplicaSet`을 생성한다.

## YAML - deployment-definition.yml

```yaml
appVersion: apps/v1
kind: Deployment
metadata: # -> Deployment
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec: # -> Deployment
  template:
    metadata: # -> POD
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec: # -> POD
      containers:
        - name: nginx-continer
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-end
```

## Deployment 생성

```shell
kubectl create -f deployment-definition.yml
```

## Deployment 확인

```shell
# Deployment 확인
kubectl get deployments

# Deployment로 생성된 ReplicaSet 확인
kubectl get replicaset

# POD 확인
kubectl get pods

# POD, ReplicaSet, Deployment 모두 확인
kubectl get all
```

## Deployment 상세정보

```shell
kubectl describe deployment myapp-deployment
```

## 실행중인 Deployment 변경

```shell
kubectl edit deployment myapp-deployment
```

## Deployment 삭제

```shell
kubectl delete deployment myapp-deployment
```
