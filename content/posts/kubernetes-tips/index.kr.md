---
title: "Kubernetes Tips"

date: 2021-06-25T19:49:30+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- tips

keywords:

- kubernetes
- tips

---

## Kubernetes Usage Config

### Alias in `~/.bashrc`

```
alias k="kubectl"
alias kc="kubectl config get-contexts"
alias kn="kubectl config set-context --current --namespace="
```

### Output YAML in `~/.bashrc`

```
export do="--dry-run=client -o=yaml"
```

### Vim Config in `~/.vimrc`

```
set tabstop=2
set expandtab
set shiftwidth=2
```

## After editing *.service

```shell
systemctl daemon-reload
```

## StatefulSet

```shell
k get sts
```

## Kubernetes Resource Usage

```shell
k top node
k top pod --container=true
```

## Manually Scheduling without Scheduler

{{<admonition note nodeName true>}}  
The `nodeName` field is for that.  
{{</admonition>}}

## Replace Command

```shell
k replace -f nginx.yaml --force
```

## Affinity

### POD Affinity

{{<admonition note "POD Affinity" true>}}  
Inter-pod affinity and anti-affinity allow you to constrain which nodes your pod is eligible to be scheduled based on labels on pods that are already running on the node rather than based on labels on nodes.  
{{</admonition>}}

### Node Affinity

{{<admonition note "Node Affinity" true>}}  
Node affinity is conceptually similar to `nodeSelector` -- it allows you to constrain which nodes your pod is eligible to be scheduled on, based on labels on the node.  
{{</admonition>}}

## CIDR

### POD CIDR

```shell
k describe node <node-name>
```

### Service CIDR

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

## Events

```shell
k get ev
```

## API Resources

```shell
k api-resources
```

## Find First to be Terminated

```shell
k describe pod | less -p Requests
```

## JSONPath

```shell
k get pods -o=jsonpath="{range .items[*]}{.metadata.name} {.status.qosClass}{'\n'}"
```

## Create a YAML with Command

```shell
k run tmp-api-contact --image=curlimages/curl:7.65.3 $do --command > curl.yaml -- sh -c 'sleep 1d'
```

## Quick Delete a POD

```shell
k delete pod nginx --grace-period 0 --force
```

