---
title: "Kubernetes Network Security"

date: 2021-05-28T16:26:29+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- network
- ingress
- egress

keywords:

- kubernetes
- security
- network
- ingress
- egress

---

## Network Policy Commands

### Network Policy List

```shell
kubectl get networkplicy
```

### Network Policy Detail

```shell
kubectl describe networkpolicy <networkpolicy-name>
```

## YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
    - Egress
  egress:
    - to:
        - podSelector:
            matchLabels:
              name: payroll
      ports:
        - protocol: TCP
          port: 8080
    - to:
        - podSelector:
            matchLabels:
              name: mysql
      ports:
        - protocol: TCP
          port: 3306
```

## System Diagram

{{<image src="kubernetes-network-security.png" width="100%">}}

## Network Policy

{{<image src="kubernetes-network-security-policy-sample.png" width="100%">}}

### Ingress `podSelector`

{{<image src="kubernetes-network-security-pod-selector.png" width="100%">}}

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              name: api-pod
      ports:
        - protocol: TCP
          port: 3306
```

### Ingress `namespaceSelector`

{{<image src="kubernetes-network-security-namespace-selector.png" width="100%">}}

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: prod
      ports:
        - protocol: TCP
          port: 3306
```

### Ingress `ipBlock`

{{<image src="kubernetes-network-security-ip-block.png" width="100%">}}

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - ipBlock:
            cidr: 192.168.5.10/32
      ports:
        - protocol: TCP
          port: 3306
```

### Ingress `podSelector` AND `namespaceSelector`

{{<image src="kubernetes-network-security-pod-and-namespace.png" width="100%">}}

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              name: api-pod
          namespaceSelector:
            matchLabels:
              name: prod
      ports:
        - protocol: TCP
          port: 3306
```

### Ingress `podSelector` OR `namespaceSelector`

{{<image src="kubernetes-network-security-pod-or-namespace.png" width="100%">}}

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              name: api-pod
        - namespaceSelector:
            matchLabels:
              name: prod
      ports:
        - protocol: TCP
          port: 3306
```

### Ingress (`podSelector` AND `namespaceSelector`) OR `ipBlock`

{{<image src="kubernetes-network-security-pod-and-namespace-or-ipblock.png" width="100%">}}

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              name: api-pod
          namespaceSelector:
            matchLabels:
              name: prod
        - ipBlock:
            cidr: 192.168.5.10/32
      ports:
        - protocol: TCP
          port: 3306
```

### Ingress (`podSelector` AND `namespaceSelector`) OR `ipBlock` AND Egress `ipBlock`

{{<image src="kubernetes-network-security-egress-ip-block.png" width="100%">}}

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              name: api-pod
          namespaceSelector:
            matchLabels:
              name: prod
        - ipBlock:
            cidr: 192.168.5.10/32
      ports:
        - protocol: TCP
          port: 3306
  egress:
    - to:
        - ipBlock:
            cidr: 192.168.5.10/32
      ports:
        - protocol: TCP
          port: 80
```