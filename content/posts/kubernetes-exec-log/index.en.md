---
title: "Kubernetes exec Log"

date: 2021-07-27T20:16:36+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- exec
- log

keywords:

- kubernetes
- exec
- log

---

## Execute Command in exist POD

```shell
kubectl exec test-pod > ~/test-dmesg -- dmesg
```

## Execute Command Temporary POD

```shell
kubectl run test-pod --image=busybox --restart=Never --rm -it > ~/test.log -- nc -z -v -w 2 webapp-service
```

