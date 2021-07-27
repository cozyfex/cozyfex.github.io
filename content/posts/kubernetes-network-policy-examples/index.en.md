---
title: "Kubernetes Network Policy Examples"

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

## Deny All POD Egress to Specific IP

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

## Allow Specific PODs Egress to Specific IP

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
