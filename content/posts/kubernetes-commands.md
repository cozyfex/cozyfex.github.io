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

### POD

<sub>POD List</sub>

```shell
kubectl get pods
```

<sub>Create POD with YAML</sub>

```shell
kubectl create -f pod-definition.yml
```

<sub>Create POD with Command</sub>

```shell
kubectl run myapp-pod --image nginx
```

<sub>POD Detail</sub>

```shell
kubectl describe pod myapp-pod
```

<sub>Change running POD</sub>

```shell
kubectl edit pod myapp-pod
```

<sub>Delete POD</sub>

```shell
kubectl delete pod myapp-pod
```



