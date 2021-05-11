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

## You must know

`selector` is to find the matched labels for managing the `POD`s in the `ReplicaSet`.

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
  selector: # -> `selector` is not required by Replication Controller
    matchLabels:
      type: front-end
```

## Create a ReplicaSet

```shell
kubectl create -f rs-definition.yml
```

## Show ReplicaSet List

```shell
kubectl get replicaset
```

## Show ReplicaSet Detail

```shell
kubectl describe replicaset myapp-replicaset
```

## Change Running ReplicaSet

```shell
kubectl edit replicaset myapp-replicaset
```

## Delete ReplicaSet

```shell
kubectl delete replicaset myapp-replicaset
```

## Scale

### Change `replicas` in the YAML file with `replace`

```shell
kubectl replace -f rs-definition.yml
```

### Origin YAML file and `--replicas` option with `scale`

```shell
kubectl scale --replicas=6 -f rs-definition.yml
```

### Change Running ReplicaSet's name and `--replicas` option with `scale`

```shell
kubectl scale --replicas=6 replicaset myapp-replicaset
```

