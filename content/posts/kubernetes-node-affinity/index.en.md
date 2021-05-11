---
title: "Kubernetes Node Affinity"

date: 2021-05-11T16:11:34+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- affinity

keywords:

- kubernetes
- scheduler
- affinity

---

## Why do you need Node Affinity?

{{<admonition note "Node Affinity" true>}}  
`nodeSelector` is not enough to set PODs on the specific Node.  
If you want to make some limitations that are more complex than `nodeSelector`, you have to use `affinity`.  
{{</admonition>}}

## Compare `nodeSelector` and `affinity`

<sub>`nodeSelctor` node-selector.yml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx
      image: nginx
  nodeSelector:
    size: Large
```

<sub>`affinity` affinity.yml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Large
```

## `affinity` Types

{{<admonition note "DuringScheduling" true>}}  
The meaning of `schedule` is to bind the `POD` to the Node in Kubernetes.  
So `DuringScheduling` means "How to apply when it's binding".  
{{</admonition>}}

{{<admonition note "DuringExecution" true>}}
`DuringExecution` means "Hwo to apply to the running `POD`s".  
{{</admonition>}}

| Type | Action |  
|:-:|:-:|  
| `required` | Apply the Configuration |  
| `preferred` | Try to apply, but it's not necessary |  
| `ignore` | No Action |

## `operator` Types in `affinity`

| Type | Action |  
|:-:|:-:|  
| In | It is like 'IN' in SQL. The `values` is an array. |  
| NotIn | It is like `NOT IN` in SQL. The `values` is an array. |  
| Exists | Just check the `key` is exists. The `values` is not defined. |

## Examples of `affinity`

{{<admonition note "Scenarios" true>}}  
There are three Nodes.  
Each node has a label that `key` is 'size'.   
The labels are `Large`, `Medium`, and `Small`.

```shell
# Large on node01
kubectl label nodes node01 size=Large

# Medium on node02
kubectl label nodes node02 size=Medium

# Small on node03
kubectl label nodes node03 size=Small

# Check labels
kubectl get nodes --show-labels
```

{{</admonition>}}

### Scheduling PODs to Large Node

#### Use `nodeSelector`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  nodeSelector: Large
```

#### Use `affinity`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Large
```

### Scheduling PODs not to Small Node

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: NotIn
                values:
                  - Small
```

### Scheduling PODs to Medium and Small Node

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Medinum
                  - Small
```

## Deployment with `affinity`

<sub>blue-deployment.yml</sub>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue
spec:
  template:
    metadata:
      name: blue-pod
      labels:
        app: blue-app
    spec:
      containers:
        - name: nginx-container
          image: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: color
                    operator: In
                    values:
                      - blue
  replicas: 3
  selector:
    matchLabels:
      app: blue-app
```

<sub>red-deployment.yml</sub>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: red
spec:
  replicas: 2
  selector:
    matchLabels:
      app: red-app
  template:
    metadata:
      name: red-pod
      labels:
        app: red-app
    spec:
      containers:
        - name: nginx-container
          image: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: node-role.kubernetes.io/master
                    operator: Exists
```
