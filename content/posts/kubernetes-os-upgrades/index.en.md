---
title: "Kubernetes OS Upgrades"

date: 2021-05-17T02:26:09+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- cluster maintenance
- os upgrade

keywords:

- kubernetes
- cluster maintenance
- os upgrade

---

## When the Node is Offline

{{<admonition note Note true>}}  
Kubernetes wait 5 minutes that is configured in Controller Manager.  
If the Node is still offline after 5 minutes, Kubernetes will create the `POD`s to other Nodes.  
After then the Node comes to online, the Node is empty.  
{{</admonition>}}

### Configure Eviction Time

```shell
kube-controller-manager --pod-eviction-timeout=5m0s
```

## cordon Node

{{<admonition note cordon true>}}  
The `cordon` makes the Node is not possible to schedule any `POD`.  
Mark the Node unscheduled.  
After `cordon`, you cannot use `describe`
{{</admonition>}}

```shell
# Structure
kubectl cordon <node-name>

# Example
kubectl cordon node01
```

## uncordon Node

{{<admonition note uncordon true>}}  
The `uncordon` makes the Node is possible to schedule the `POD`s.  
Mark the Node scheduled.  
{{</admonition>}}

```shell
# Structure
kubectl uncordon <node-name>

# Example
kubectl uncordon node01
```

## drain Node

{{<admonition note drain true>}}  
The `drain` is going to move the `POD` to other Nodes.  
The `drain` is executing `cordon` together.  
{{</admonition>}}

```shell
# Structure
kubectl drain <node-name>

# Example
kubectl drain node01
```

## OS Upgrade Process

{{<mermaid>}}  
stateDiagram  
[*] --> drain  
drain --> Upgrade  
Upgrade --> uncordon  
uncordon --> [*]  
{{</mermaid>}}
