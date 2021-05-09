---
title: "Kubernetes Namespace"

date: 2021-05-08T16:13:23+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- namespace

keywords:

- kubernetes
- namespace

---

## You must know

Kubernetes generate `default` namespace when it's installed.\
And there are `kube-system` and `kube-pulibc` namespaces.

## DNS

### Connect to a service in the same namespace

If you want to connect to a database that is named `db-service` in the same namespace that is `default`.

```javascript
mysql.connect("db-service")
```

### Connect to a service in the another namespace

If you want to connect to a database that is named `db-service` in the another namespace that is `dev`.

```javascript
mysql.connect("db-service.dev.svc.cluster.local")
```

{{<admonition note "cluster.local" true>}}  
Default domain name of the kubernetes cluster  
{{</admonition>}}

{{<admonition note "svc" true>}}  
Sub domain for the service  
{{</admonition>}}

{{<admonition note "dev" true>}}  
Namespace  
{{</admonition>}}

{{<admonition note "db-service" true>}}  
Service name  
{{</admonition>}}

### Show PODs of the specific namespace

```shell
# Show PODs of the `default` namespace
kubectl get pods

# Show PODs of the `kube-system` namespace
kubectl get pods --namespace=kube-system
```

### Create POD to the specific namespace

<sub>pod-definition.yml</sub>

```yaml
# To the `default` namespace
appVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

<sub>dev-pod-definition.yml</sub>

```yaml
# To the `dev` namespace
appVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: dev # -> Namespace
  labels:
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

<sub>Create POD</sub>

```shell
# To the `default` namespace
kubectl create -f pod-definition.yml

# To the `dev` namespace
kubectl create -f pod-definition.yml --namespace=dev
kubectl create -f dev-pod-definition.yml
```

## Create namespace

<sub>namespace-dev.yml</sub>

```yaml
appVersion: v1
kind: Namespace
metadata:
  name: dev
```

<sub>Create namespace</sub>

```shell
# With YAML file
kubectl create -f namesapce-dev.yml

# Command
kubectl create namesapce dev
```

## Switch namespace of the terminal

```shell
kubectl config set-context $(kubectl config current-context) --namespace=dev

# Now, you can get PODs of `dev` namespace without `--namespace` option
kubectl pods
```

## Show PODs of all namespaces

```shell
kubectl get pods --all-namespaces
```

## Resource Quota

<sub>compute-quota.yml</sub>

```yaml
appVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

<sub>Create Quota</sub>

```shell
kubectl create -f compute-quota.yml
```

