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

## You must know

{{<admonition note "Taint and Toleration" true>}}  
Usually it uses not to bind PODs to specific Node.  
For example, there's a Node that has GPU resources then you want not to bind PODs that don't need GPU resources.  
At this time, you can use taint and toleration.  
{{</admonition>}}

## Taint

{{<admonition note taint true>}}  
`taint` is to apply to the Node.  
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
# Focus on tailing dash(-)
kubectl taint nodes node01 app=myapp:NoSchedule-
```

### Taint Effect

{{<admonition note NoSchedule true>}}  
If there's no toleration in the PODSpec, the scheduler does not bind the POD to the Node.  
It's not affect to exist PODs.  
{{</admonition>}}

{{<admonition note PreferNoSchedule true>}}  
If there's no toleration in the PODSpec, the scheduler try not to bind the POD to the Node, but it's not a necessary.  
There's not enough resources on the cluster, the scheduler could bind the POD to the Node that has taint.  
{{</admonition>}}

{{<admonition note NoExecute true>}}  
If there's no toleration in the PODSpec, the scheduler does not bind the POD to the Node.  
The exists PODs that don't have toleration are terminated.  
{{</admonition>}}

## Toleration

{{<admonition note tolerations true>}}  
`tolerations` is to set PODs.  
In the `tolerations` definitions, you need to set double quotes(") for the values.  
{{</admonition>}}

### POD YAML

```yaml
appVersion: v1
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
If the `operator` is `Equal`, the `value` must set same value of the `taint`.  
`Exists`  
If the `operator` is `Exists`, you must not define the `value`.  
{{</admonition>}}

## When taint and tolerations matched

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
