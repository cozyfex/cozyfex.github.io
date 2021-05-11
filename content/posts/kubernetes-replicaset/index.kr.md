---
title: "Kubernetes ReplicaSet"

date: 2021-05-08T15:23:38+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- replicaset

keywords:

- kubernetes
- replicaset

---

## 알고가자!

`ReplicaSet`이 레이블이 매칭되는 `POD`들을 관리 위해서 `selector`가 있다.

## YAML - rs-definition.yml

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata: # -> ReplicaSet
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec: # -> ReplicaSet
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
  selector: # -> `selector`는 Replication Controller에서는 요구되지 않는다.
    matchLabels:
      type: front-end
```

## ReplicaSet 생성

```shell
kubectl create -f rs-definition.yml
```

## ReplicaSet 확인

```shell
kubectl get replicaset
```

## ReplicaSet 상세정보

```shell
kubectl describe replicaset myapp-replicaset
```

## 실행중인 ReplicaSet 변경

```shell
kubectl edit replicaset myapp-replicaset
```

## ReplicaSet 삭제

```shell
kubectl delete replicaset myapp-replicaset
```

## Scale

### `replace`와 YAML의 `replicas`로 변경

```shell
kubectl replace -f rs-definition.yml
```

### 변경되지 않은 YAML 파일과 `scale`의 `--replicas` 옵션으로 변경

```shell
kubectl scale --replicas=6 -f rs-definition.yml
```

### 기존의 ReplicaSet의 이름과 `scale`의 `--replicas` 옵션으로 변경

```shell
kubectl scale --replicas=6 replicaset myapp-replicaset
```
