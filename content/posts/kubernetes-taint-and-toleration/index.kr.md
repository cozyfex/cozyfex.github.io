---
title: "Kubernetes Taint and Toleration"

date: 2021-05-10T15:18:20+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- taint
- toleration

keywords:

- kubernetes
- scheduler
- taint
- toleration

---

## 알고가자!

{{<admonition note "Taint and Toleration" true>}}  
주로 특정 Node 에 특정 POD 들을 바인딩 하지 않기 위해 사용한다.  
예를들어, GPU 가 있는 Node 에는 GPU 가 필요하지 않는 POD 들은 바인딩 하지 않을때 사용한다.  
{{</admonition>}}

## Taint

{{<admonition note taint true>}}  
`taint` 는 Node 에 적용하는 것이다.  
{{</admonition>}}

### Commands

<sub>Add taint command structure</sub>

```shell
kubectl taint nodes [node-name] [key]=[value]:[taint-effect]
```

| Name | Example |  
|:-:|:-:|  
| [node-name] | node01 |  
| [key] | app |  
| [value] | myapp |  
| [taint-effect] | NoSchedule / PreferNoSchedule / NoExecute |

<sub>Add taint</sub>

```shell
kubectl taint nodes node01 app=myapp:NoSchedule
```

<sub>Remove taint</sub>

```shell
# - 에 주목하자!
kubectl taint nodes node01 app=myapp:NoSchedule-
```

### Taint Effect

{{<admonition note NoSchedule true>}}  
PODSpec 에 toleration 이 없이면, 스케줄러는 그 POD 을 이 Node 에 바인딩 하지 않는다.  
이 설정은 기존의 POD 들에는 영향을 주지 않는다.  
{{</admonition>}}

{{<admonition note PreferNoSchedule true>}}  
PODSpec 에 toleration 이 없이면, 스케줄러는 그 POD 을 이 Node 에 바인딩 하지 않으려고 하지만, 필요사항은 아니다.  
클러스터에 자원이 부족하면, 스케줄러는 taint 가 적용된 Node 에도 POD 을 바인딩한다.  
{{</admonition>}}

{{<admonition note NoExecute true>}}  
PODSpec 에 toleration 이 없이면, 스케줄러는 그 POD 을 이 Node 에 바인딩 하지 않는다.  
이 설정은 기존의 POD 들을 종료시킨다.  
{{</admonition>}}

## Toleration

{{<admonition note tolerations true>}}  
`tolerations` 은 POD 들에 설정하는 것이다.  
`tolerations` 정의에는 쌍따움표를(")를 사용해야 한다.  
{{</admonition>}}

### POD YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  tolerations:
    - key: "app"
      operator: "Equal"
      value: "myapp"
      effect: "NoSchedule"
```

### operator

{{<admonition note operator true>}}  
`Equal`  
`operator` 가 `Equal` 일때, `value` 는 `taint` 와 일치해야 한다.  
`Exists`  
`operator` 가 `Exists` 일때, `value` 를 정의하지 않아야 한다.  
{{</admonition>}}

## taint 와 tolerations 이 매칭 될 때 

### `operator` = `Equal`

| taint | tolerations |  
|:-:|:-:|  
| key | key |  
| value | value |  
| taint-effect | effect |

### `operator` = `Exists`

| taint | tolerations |  
|:-:|:-:|  
| key | key |  
| taint-effect | effect |
