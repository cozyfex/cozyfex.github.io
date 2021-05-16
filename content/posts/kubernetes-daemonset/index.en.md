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

## What is the DaemonSet?

{{<admonition note DaemonSet true>}}  
The `DaemonSet` is working for managing `POD`s that you need to deploy or delete all Nodes in the cluster.  
That means, when you add a Node in the cluster or delete one of them, `DeaemonSet` is deploying or deleting `POD` to the Node.  
{{</admonition>}}

## How to work DaemonSet in Kubernetes

{{<admonition note "Behavior till v1.12" true>}}  
The Kubernetes uses `nodeName` property.  
{{</admonition>}}

{{<admonition note "From v1.12" true>}}  
The Kubernetes uses default scheduler and `NodeAffinity` property.  
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

After then, you can change the file like above.

## Create a DaemonSet

```shell
kubectl create -f ds-definition.yml
```

## DaemonSet List

```shell
kubectl get daemonsets

kubectl get ds
```

## DaemonSet Detail

```shell
kubectl describe daemonset monitoring-daemon
```

## Check How many PODs deploy to the cluster

```shell
# Check Nodes
kubectl get nodes

# Check PODs
kubectl -n kube-system get pods -o wide | grep proxy
```
