---
title: "Kubernetes Node 에 Label 설정"

date: 2021-07-27T20:06:32+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- labeling
- node

keywords:

- kubernetes
- labeling
- node

---

## Label 도움말

```shell
kubectl label -h
```

## Node 에 Label 설정

```shell
kubectl label node <node-name> <key=value>

kubectl label node node01 run=dev
```


