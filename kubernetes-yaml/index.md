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

```shell
kubectl create -f pod-definition.yml
```

```shell
kubectl get pods
```

```shell
kubectl describe pod myapp-pod
```
