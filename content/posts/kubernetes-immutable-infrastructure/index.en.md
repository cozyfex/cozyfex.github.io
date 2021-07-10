---
title: "Kubernetes Immutable Infrastructure"

date: 2021-07-10T17:08:12+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- mutable infrastructure
- immutable infrastructure

keywords:

- kubernetes
- mutable infrastructure
- immutable infrastructure

---

## Mutable Infrastructure

{{<mermaid>}}

graph TD;

CA(Container A) --> VA17[v1.17]

VA17 --> VA18[v1.18]

VA18 --> VA19[v.19]

CB(Container B) --> VB17[v1.17]

VB17 --> VB18[v1.18]

VB18 --> VB19[v.19]

CC(Container C) --> VC17[v1.17]

VC17 --> VC18[v1.18]

VC18 --> E[ERROR!]

{{</mermaid>}}

## Immutable Infrastructure

{{<admonition note "Rolling Update" true>}}  
In Kubernetes, it's `Rolling Update`.  
{{</admonition>}}

## Ensure Immutability of Containers at Runtime

{{<admonition note PodSecurityPolicy true>}}  
In Kubernetes, we can use `PodSecurityPolicy` for that.  
{{</admonition>}}

### `nginx.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      securityContext:
        readOnlyRootFilesystem: true
        privileged: true
      volumeMounts:
        - name: cache-volume
          mountPath: /var/cache/nginx
        - name: runtime-volume
          mountPath: /var/run
  volumes:
    - name: cache-volume
      emptyDir: { }
    - name: runtime-volume
      emptyDir: { }
```

### `psp.yaml`

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: example
spec:
  privileged: false
  readOnlyRootFilesystem: true
  runAsUser:
    rule: RunAsNonRoot
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
```

