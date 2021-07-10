---
title: "Kubernetes 환경 변수"

date: 2021-05-17T00:03:11+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- application lifecycle management
- environment variable

keywords:

- kubernetes
- application lifecycle management
- environment variable

---

## Docker

```shell
docker run -e APP_COLOR=blue simple-webapp-color

docker run -e APP_COLOR=green simple-webapp-color

docker run -e APP_COLOR=pink simple-webapp-color
```

## Kubernetes

```yaml
apiVersion: v1
kind:
  Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
        - containerPort: 8080
      env:
        - name: APP_COLOR
          value: pink
```
