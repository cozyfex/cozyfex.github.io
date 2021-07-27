---
title: "Kubernetes Network Policy 예제"

date: 2021-07-27T20:40:59+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- networkpolicy
- network
- policy
- egress

keywords:

- kubernetes
- networkpolicy
- network
- policy
- egress

---

## 모든 POD 에서 특정 IP를 목표로 하는 출구 거부

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: pod-deny
  namespace: access
spec:
  podSelector: { }
  policyTypes:
    - Egress
  egress:
    - to:
        - ipBlock:
            cidr: 0.0.0.0/0
            except:
              - 192.168.100.21/32
```

## 특정 POD 들에서 특정 IP를 목표로 하는 출구 허용

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: pod-allow
  namespace: access
spec:
  podSelector:
    matchLabels:
      role: metadata-accessor
  policyTypes:
    - Egress
  egress:
    - to:
        - ipBlock:
            cidr: 192.168.100.21/32
```
