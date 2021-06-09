---
title: "Kubernetes Cluster Roles"

date: 2021-05-24T21:03:52+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- rbac
- role
- cluster
- cluster roles

keywords:

- kubernetes
- security
- rbac
- role
- cluster
- cluster roles

---

## Namespaced

```shell
kubectl api-resources --namespaced=true
```

{{<mermaid>}}  
graph TD;  
P(pods)  
RS(replicasets)  
DP(deployments)  
SV(services)  
{{</mermaid>}}

{{<mermaid>}}  
graph TD;  
J(jobs)  
S(secrets)  
R(roles)  
RB(rolebindings)  
CM(configmaps)  
{{</mermaid>}}

## Cluster Scoped

```shell
kubectl api-resources --namespaced=false
```

{{<mermaid>}}  
graph TD;  
N(nodes)  
PV(PV)  
PVC(PVC)  
CSR(certificatesigningrequests)  
{{</mermaid>}}

{{<mermaid>}}  
graph TD;  
CR(clusterroles)  
CRB(clusterrolebindings)  
NS(namespaces)  
{{</mermaid>}}

## Cluster Role

### Cluster Admin

- Can View Nodes
- Can Create Nodes
- Can Delete Nodes

### Storage Admin

- Can View PVs
- Can Create PVs
- Can Delete PVCs

### Cluster Role YAML

<sub>cluster-admin-role.yaml</sub>

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-administrator
rules:
  - apiGroups: [ "" ]
    resources: [ "nodes" ]
    verbs: [ "list", "get", "create", "delete" ]
```

```shell
kubectl create -f cluster-admin-role.yaml
```

## Cluster Role Binding

### Cluster Admin

- Can View Nodes
- Can Create Nodes
- Can Delete Nodes

### Cluster Role Binding YAML

<sub>cluster-admin-role-binding.yaml</sub>

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-role-binding
subjects:
  - kind: User
    name: cluster-admin
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-administrator
  apiGroup: rbac.authorization.k8s.io
```

```shell
kubectl create -f cluster-admin-role-binding.yaml
```
