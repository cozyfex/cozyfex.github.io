---
title: "Kubernetes initContainers"

date: 2021-05-17T01:59:59+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- application lifecycle management
- initcontainers

keywords:

- kubernetes
- application lifecycle management
- initcontainers

---

## What is initContainers?

{{<admonition note initContainers true>}}  
`initContainers` is just executing on starting the `POD`.  
The `initContainers` is run one at a time in sequential order.  
If any of the `initContainers` fail to complete, Kubernetes restarts the `POD` repeatedly until the Init Container succeeds.  
{{</admonition>}}

## initContainers YAML

<sub>pod-definition.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
    - name: myapp-container
      image: nginx
      command: [ "echo", "This is main conatiner." ]
  initContainers:
    - name: init-myservice
      image: nginx
      command: [ "echo", "This is one of initContainers. Init Service." ]
    - name: init-mydb
      image: mysql
      command: [ "echo", "This is one of initContainers. Init Database." ]
```