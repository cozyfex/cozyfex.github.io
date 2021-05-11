---
title: "Kubernetes YAML"

date: 2021-05-07T13:08:31+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- yaml
- command

keywords:

- kubernetes
- yaml
- command

---

## 쿠버네티스의 YAML 기본구조

### 기본 YAML 구조

```yaml
apiVersion:
kind:
metadata:


spec:



```

### 버전 정보

| Kind       | Version |
| ---------- | ------- |
| POD        | v1      |
| Service    | v1      |
| ReplicaSet | apps/v1 |
| Deployment | apps/v1 |

## POD YAML

### pod-definition.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-continer
      image: nginx
```

### YAML 파일로 POD 생성

```shell
kubectl create -f pod-definition.yml
```

### 명령으로 POD 생성

```shell
kubectl run myapp-pod --image nginx
```

### POD 확인

```shell
kubectl get pods
```

### POD 상세정보

```shell
kubectl describe pod myapp-pod
```

## Replication Controller YAML

### rc-definition.yml

```yaml
apiVersion: v1
kind: ReplicationController
metadata: # -> Replication Controller
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec: # -> Replication Controller
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
```

### Replication Controller 생성

```shell
kubectl create -f rc-definition.yml
```

### Replication Controller 확인

```shell
kubectl get replicationcontroller
```

### POD 확인

```shell
kubectl get pods
```

## ReplicaSet

### rs-definition.yml

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

### Labels과 Selector

`selector`는 레이블과 매칭하여 POD을 관리 위한것이다.

### ReplicaSet 생성

```shell
kubectl create -f rs-definition.yml
```

### ReplicaSet 확인

```shell
kubectl get replicaset
```

### ReplicaSet 상세정보

```shell
kubectl describe replicaset myapp-replicaset
```

### POD 확인

```shell
kubectl get pods
```

### ReplicaSet 삭제

```shell
kubectl delete replicaset myapp-replicaset
```

### Scale

#### `replace`와 YAML의 `replicas`로 변경

```shell
kubectl replace -f rs-definition.yml
```

#### 변경되지 않은 YAML 파일과 `scale`의 `--replicas` 옵션으로 변경

```shell
kubectl scale --replicas=6 -f rs-definition.yml
```

#### 기존의 ReplicaSet의 이름과 `scale`의 `--replicas` 옵션으로 변경

```shell
kubectl scale --replicas=6 replicaset myapp-replicaset
```
