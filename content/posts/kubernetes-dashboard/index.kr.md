---
title: "Kubernetes Dashboard"

date: 2021-07-04T17:02:14+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- dashboard

keywords:

- kubernetes
- dashboard

---

## Kubernetes Web UI

{{<admonition note "Web UI" true>}}  
기본적으로 대시보드는 노트북을 포함한 클러스터에서 액세스할 수 있습니다.  
[Kubernetes Web UI](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)  
{{</admonition>}}

## Deploying

```shell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
```

## Run Proxy

```shell
kubectl proxy
```

## Access Dashboard

[http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)

## Dashboard Authentication

{{<admonition note "Web UI" true>}}  
대시보드로 인증하는 방법을 찾을 수 있습니다.  
{{</admonition>}}

