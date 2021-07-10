---
title: "Kubernetes Authorization Role Based Access Control"

date: 2021-05-24T18:04:01+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- authorization
- rbac
- role based access control

keywords:

- kubernetes
- security
- authorization
- rbac
- role based access control

---

## RBAC

### Create a Role

<sub>`developer-role.yaml`</sub>

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
  - apiGroups: [ "" ]
    resources: [ "pods" ]
    verbs: [ "list", "get", "create", "update", "delete" ]
  - apiGroups: [ "" ]
    resources: [ "ConfigMap" ]
    verbs: [ "create" ]
```

```shell
kubectl create -f developer-role.yaml
```

### Role Binding

<sub>`dev-user-developer-binding.yaml`</sub>

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: devuser-developer-binding
subjects:
  - kind: User
    name: dev-user
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

```shell
kubectl create -f dev-user-developer-binding.yaml
```

## View RBAC

### View Roles

```shell
kubectl get roles
```

### View Role Bindings

```shell
kubectl get rolebindings
```

### Role Detail

```shell
kubectl describe role developer
```

### Role Binding Detail

```shell
kubectl describe rolebinding dev-user-developer-binding
```

## Check Access

### Current User

```shell
kubectl auth can-i create deployments
kubectl auth can-i delete nodes
```

### Specific User

```shell
kubectl auth can-i create deployments --as dev-user
kubectl auth can-i create pods --as dev-user
```

### Specific User and Namespace

```shell
kubectl auth can-i create pods --as dev-user --namespace test
```

## Resource Names

<sub>`developer-role.yaml`</sub>

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
  - apiGroups: [ "" ]
    resources: [ "pods" ]
    verbs: [ "get", "create", "update" ]
    resourceNames: [ "blue", "orange" ]
```


