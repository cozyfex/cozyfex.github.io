---
title: "Kubernetes DaemonSet"

date: 2021-05-15T15:54:26+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- daemonset

keywords:

- kubernetes
- daemonset

---

## DaemonSet 이란?

{{<admonition note DaemonSet true>}}  
`DaemonSet` 은 클러스터 내의 모든 노드들에 `POD` 을 배포, 삭제하는 관리를 위한것이다.  
클러스터에 노드가 추가되거나 삭제 될 때, `DaemonSet` 이 `POD` 들을 해당 노드에 추가 또는 삭제를 한다는 것이다.  
{{</admonition>}}

## 쿠버네티스에서 DaemonSet 이 일하는 방법

{{<admonition note "Behavior till v1.12" true>}}  
쿠버네티스는 `nodeName` 속성을 이용한다.  
{{</admonition>}}

{{<admonition note "From v1.12" true>}}  
쿠버네티스는 기본 스케줄러와 `NodeAffinity` 속성을 이용한다.  
{{</admonition>}}

## DaemonSet YAML

<sub>ds-definition.ylm</sub>

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: monitoring-daemon
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: monitoring-agent
  template:
    metadata:
      labels:
        app: monitoring-agent
    spec:
      containers:
        - name: monitoring-agent
          image: monitoring-agent
```

<sub>Easy way to make a YAML</sub>

```shell
kubectl create deployment monitoring-daemon --image=monitoring-agent --dry-run=client -o yaml > ds.yml
```

이후, 위의 파일처럼 변경하면 된다.

## DaemonSet 생성

```shell
kubectl create -f ds-definition.yml
```

## DaemonSet 목록

```shell
kubectl get daemonsets

kubectl get ds
```

## DaemonSet 상세정보

```shell
kubectl describe daemonset monitoring-daemon
```

## 클러스터에 몇개의 POD 들이 배포 되었는지 확인

```shell
# Check Nodes
kubectl get nodes

# Check PODs
kubectl -n kube-system get pods -o wide | grep proxy
```
