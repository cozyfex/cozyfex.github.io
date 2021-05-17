---
title: "Kubernetes 모니터링"

date: 2021-05-16T19:03:33+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- monitoring

keywords:

- kubernetes
- monitoring

---

## 모니터링 서비스

- [METRICS SERVER](https://github.com/kubernetes-sigs/metrics-server)
- [Prometheus](https://prometheus.io/)
- [Elastic Stack](https://www.elastic.co/)
- [DATADOG](https://www.datadoghq.com/)
- [dynatrace](https://www.dynatrace.com/)

{{<admonition note HEAPSTER true>}}  
`HEAPSTER` 은 쿠버네티스의 모니터링 서비스지만, 지금은 비추천한다.  
{{</admonition>}}

## METRICS SERVER

`METRICS SERVER` 는 IN-MEMORY 서비스 이다.  
그러므로 클러스터 기록은 확인 할 수 없다.

### METRICS SERVER 설치

#### minikube

```shell
minikube addons enable metrics-server
```

#### 그 외

 [METRICS SERVER](https://github.com/kubernetes-sigs/metrics-server) 의 Installation 부분 확인  

```shell
# Install all YAML files in current directory.
kubectl create -f .
```

### METRICS SERVER 명령

#### Top Nodes

```shell
watch kubectl top nodes
watch "kubectl top nodes"
```

#### Top PODs

```shell
watch kubectl top pods
watch "kubectl top pods"
```
