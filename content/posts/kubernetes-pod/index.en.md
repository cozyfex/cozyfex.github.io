---
title: "Kubernetes POD"

date: 2021-05-07T12:54:42+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- k8s
- pod

keywords:

- kubernetes
- k8s
- pod

---

## You must know

`POD` is a unit of service.\
If kubernetes needs to scale out, it replicates `POD` unit.\
It's not to scale out container in a `POD`.

## Manage Containers with Docker

```shell
# app1
docker run python-app
# app2
docker run python-app
# app3
docker run python-app
# app4
docker run python-app

docker run helper -link app1
docker run helper -link app2
docker run helper -link app3
docker run helper -link app4
```

## Manage Containers with POD

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
