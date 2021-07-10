---
title: "Kubernetes Security Context"

date: 2021-05-28T15:20:57+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- context

keywords:

- kubernetes
- security
- context

---

## Container Security Context

```shell
docker run --user=1001 ubunut sleep
```

```shell
docker run --cap-add MAC_ADMIN ubunut
```

## Kubernetes Security Context

{{<admonition note securityContext true>}}  
In YAML, focus on `securityContext`.  
{{</admonition>}}

### POD Context

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
sepc:
  securityContext:
    runAsUser: 1000
  containers:
    - name: ubuntu
      image: ubunut
      command: [ "sleep", "3600" ]
```

### Containers Security Context

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
sepc:
containers:
  - name: ubuntu
    image: ubunut
    command: [ "sleep", "3600" ]
    securityContext:
      runAsUser: 1000
      capabilities:
        add: [ "MAC_ADMIN" ]
```

{{<admonition note capabilities true>}}  
Capabilities are only supported at the container level and not at the POD level.  
{{</admonition>}}

