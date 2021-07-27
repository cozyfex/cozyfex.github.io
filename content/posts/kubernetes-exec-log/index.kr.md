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

## 기존 POD 에서 명령 실행해서 로그 남기기

```shell
kubectl exec test-pod > ~/test-dmesg -- dmesg
```

## 임시 POD 에서 명령 실행해서 로그 남기기

```shell
kubectl run test-pod --image=busybox --restart=Never --rm -it > ~/test.log -- nc -z -v -w 2 webapp-service
```

