---
title: "Kubernetes Multi-Container Pod"

date: 2021-05-17T01:52:48+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- application lifecycle management
- multi-container pod

keywords:

- kubernetes
- application lifecycle management
- multi-container pod

---

## Multi-Container POD

{{<admonition note "Multi-Container Lifecycle">}}  
The containers of the `POD` have same lifecycle.  
{{</admonition>}}

<sub>pod-definition.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels:
    name: simple-webapp
spec:
  containers:
    - name: simple-webapp
      image: simple-webapp
      ports:
        - containerPort: 8080
    - name: log-agent
      image: log-agent
```