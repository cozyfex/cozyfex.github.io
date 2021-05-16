---
title: "Kubernetes Static Pod"

date: 2021-05-15T16:43:11+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- static pod

keywords:

- kubernetes
- static pod

---

## Static PODs vs DaemonSets

| Static PODs | DaemonSets |  
|:-:|:-:|  
| Created by kubelet | Created by kube-api server (DaemonSet Controller) |  
| Deploy Control Plane components as Static PODs | Deploy Monitoring Agents, Logging Agents on Nodes |  
| Ignore kube-scheduler | Ignore kube-scheduler |

## Default Static PODs Location

{{<admonition note "Static PODs" true>}} When created or deleted a YAML file in the `/etc/kubernetes/manifests`, the PODs will be created or deleted automatically.  
{{</admonition>}}

```shell
cd /etc/kubernetes/manifests
```

## Check How many PODs exist in the Node

```shell
ls /etc/kubernetes/manifests
```

## Imperative

```shell
kubectl run --restart=Never --image=nginx --dry-run=client -o yaml --command sleep 1000 > /etc/kubernetes/manifests/nginx.yaml
```

## Check Static PODs Configuration

### Find a Node of the POD

{{<admonition note "How to figure out it's a static POD?" true>}}  
In a `POD`'s name, there is a postfix like `-master` that is `-<node-name>`.  
{{</admonition>}}

```shell
kubectl get pods -o wide

kubectl get pods --all-namespaces -o wite | grep "\-master"
```

### Connect to the Node

```shell
ssh <node-name>
```

### Find the Location of the Configuration

```shell
# Check '--config' option
ps -ef | grep kubelet

```

### Check `staticPodPath` Option

```shell
# Check 'staticPodPath'
grep -i staticpod /var/lib/kubelet/config.yaml
```
