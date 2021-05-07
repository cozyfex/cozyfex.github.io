# Kubernetes YAML


### Basic YAML Structure in Kubernetes

<sub>Basic YAML Structure</sub>

```yaml
apiVersion:
kind:
metadata:


spec:



```

| Kind       | Version |
| ---------- | ------- |
| POD        | v1      |
| Service    | v1      |
| ReplicaSet | apps/v1 |
| Deployment | apps/v1 |

#### POD YAML

<sub>pod-definition.yml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-continer
      image: nginx
```

<sub>Create POD with YAML file</sub>

```shell
kubectl create -f pod-definition.yml
```

<sub>Create POD with command</sub>

```shell
kubectl run myapp-pod --image nginx
```

<sub>Show PODs list</sub>

```shell
kubectl get pods
```

<sub>Show POD detail</sub>

```shell
kubectl describe pod myapp-pod
```
