---
title: "Kubernetes 권한 없이 Secret 얻기"

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

## 마운트 된 Secret

```shell
kubectl exec pod1 -- cat /etc/secret-volume/password
```

## 환경변수의 Secret

```shell
kubectl exec pod2 -- env | grep PASS
```

## Service Account 를 통해서

### Container 에 연결

```shell
kubectl exec -it pod3 -- sh
```

### 컨테이너에서 Service Account 의 마운트 정보 확인

```shell
mount | grep serviceaccount
```

```
tmpfs on /run/secrets/kubernetes.io/serviceaccount type tmpfs (ro,relatime)
```

### 컨테이너에서 Service Account 데이터 확인

```shell
ls /run/secrets/kubernetes.io/serviceaccount
```

```
ca.crt     namespace  token
```

### 컨테이너에서 Kubernetes API 호출해서 Secret 확인

```shell
curl https://kubernetes.default/api/v1/namespaces/restricted/secrets -H "Authorization: Bearer $(cat /run/secrets/kubernetes.io/serviceaccount/token)" -k
```


