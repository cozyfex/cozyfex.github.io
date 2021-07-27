---
title: "Kubernetes Implement Own TLS Certificate For Ingress"

date: 2021-07-27T20:54:01+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- ingress
- tls

keywords:

- kubernetes
- ingress
- tls

---

## Create TLS Secret

```shell
kubectl create secret tls tls-secret --key tls.key --cert tls.crt
```

## Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
  generation: 1
  name: secure
  namespace: team-pink
...
...
spec:
  tls: # add
    - hosts: # add
        - ingress.test   # add
      secretName: tls-secret    # add
  rules:
    - host: ingress.test
      http:
        paths:
          - backend:
              service:
                name: secure-app
                port: 80
            path: /app
            pathType: ImplementationSpecific
          - backend:
              service:
                name: secure-api
                port: 80
            path: /api
            pathType: ImplementationSpecific
...
```
