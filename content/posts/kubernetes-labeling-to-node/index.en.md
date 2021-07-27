---
title: "Kubernetes Labeling to Node"

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

## Label Help

```shell
kubectl label -h
```

## Labeling to Node

```shell
kubectl label node <node-name> <key=value>

kubectl label node node01 run=dev
```


