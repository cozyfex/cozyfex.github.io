# Kubernetes Deployment


## You must know

`Deployment` is going to create `ReplicaSet` automatically.

## YAML - deployment-definition.yml

```yaml
appVersion: apps/v1
kind: Deployment
metadata: # -> Deployment
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec: # -> Deployment
  template:
    metadata: # -> POD
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec: # -> POD
      containers:
        - name: nginx-continer
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-end
```

## Create Deployment

```shell
kubectl create -f deployment-definition.yml
```

## Show Deployment List

```shell
# Show Deployment
kubectl get deployments

# Show ReplicaSet that is created by Deployment
kubectl get replicaset

# Show PODs that is created by above ReplicaSet
kubectl get pods

# Show all of them
kubectl get all
```

## Show Deployment Detail

```shell
kubectl describe deployment myapp-deployment
```

## Change Running Deployment

```shell
kubectl edit deployment myapp-deployment
```

## Delete Deployment

```shell
kubectl delete deployment myapp-deployment
```

