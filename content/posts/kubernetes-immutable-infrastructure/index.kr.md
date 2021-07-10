---
title: "Kubernetes 불변 인프라"

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
Kubernetes 에서, `Rolling Update` 이다.  
{{</admonition>}}

## 런타임 시 컨테이너의 불변성 보장

{{<admonition note PodSecurityPolicy true>}}  
Kubernetes 에서, 이것을 위해 `PodSecurityPolicy` 를 이용할 수 있다.  
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

