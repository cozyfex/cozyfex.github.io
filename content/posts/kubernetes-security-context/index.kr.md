---
title: "Kubernetes Security Context"

date: 2021-05-28T15:20:57+09:00

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
YAML 에서, `securityContext` 에 집중  
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
Capabilities 는 오직 컨테이너 레벨에서만 지원된다.  
POD 레벨에서는 지원되지 않는다.  
{{</admonition>}}

