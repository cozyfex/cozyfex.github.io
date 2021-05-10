---
title: "Kubernetes Scheduler Bind nodeName"

date: 2021-05-10T12:31:32+09:00

author: CozyFex

categories:

- kubernetes
- kubernetes scheduler
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- nodename

keywords:

- kubernetes
- scheduler
- nodename

---

## How the scheduler works?

{{<admonition note "Scheduler" true>}}  
If the scheduler is running, the scheduler is binding a new `POD` that is undefined `nodeName` to the Node.  
{{</admonition>}}

## POD YAML

<sub>pod-definition.yml</sub>

```yaml
appVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 8080
# nodeName: node02 # -> We don't need to set this value as an default.
```

## Create a POD

```shell
kubectl create -f pod-definition.yml
```

## Scheduler Process

{{<mermaid>}}  
graph TD;  
C(kubectl create) --> R(Read pod-definition.yml)  
R --> F(Find <nodeName>)  
F --> N(No <nodeName>)  
N --> B(Auto binding <nodeName>)  
B --> P(Bind POD to Node)  
{{</mermaid>}}
