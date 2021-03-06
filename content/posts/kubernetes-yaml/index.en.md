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

## Basic YAML Structure in Kubernetes

### Basic YAML Structure

```yaml
apiVersion:
kind:
metadata:


spec:



```

### Version Information

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

### Create a POD with YAML file

```shell
kubectl create -f pod-definition.yml
```

### Create a POD with Command

```shell
kubectl run myapp-pod --image nginx
```

### Show PODs List

```shell
kubectl get pods
```

### Show POD Detail

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

### Create a Replication Controller

```shell
kubectl create -f rc-definition.yml
```

### Show Replication Controller List

```shell
kubectl get replicationcontroller
```

### Show PODs List

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
  selector: # -> `selector` is not required by Replication Controller
    matchLabels:
      type: front-end
```

### Labels and Selector

`selector` is to find the matched labels for managing the PODs.

### Create a ReplicaSet

```shell
kubectl create -f rs-definition.yml
```

### Show ReplicaSet List

```shell
kubectl get replicaset
```

### Show ReplicaSet Detail

```shell
kubectl describe replicaset myapp-replicaset
```

### Show PODs List

```shell
kubectl get pods
```

### Delete ReplicaSet

```shell
kubectl delete replicaset myapp-replicaset
```

### Scale

#### Change `replicas` in the YAML file with `repliace`

```shell
kubectl replace -f rs-definition.yml
```

#### Origin YAML file and `--replicas` option with `scale`

```shell
kubectl scale --replicas=6 -f rs-definition.yml
```

#### Change Running ReplicaSet's name and `--replicas` option with `scale`

```shell
kubectl scale --replicas=6 replicaset myapp-replicaset
```

