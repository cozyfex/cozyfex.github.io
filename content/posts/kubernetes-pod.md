---
title: "Kubernetes Pod"

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

### POD

`POD` is a unit of service.\
If kubernetes needs to scale out, it replicates `POD` unit.\
It's not to scale out container in a `POD`.\
`POD`은 서비스 단위로 이해하는게 좋다.\
쿠버네티스는 스테일 아웃을 할 때 `POD` 단위로 복제한다.\
`POD` 내부에 컨테이너를 개별로 스케일 아웃 하지 않는다.

#### Manage Containers with Docker

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

#### Manage Containers with POD

<sub>Create POD</sub>

```shell
kubectl run nginx --image nginx
```

<sub>PODs list</sub>

```shell
kubectl get pods
```
