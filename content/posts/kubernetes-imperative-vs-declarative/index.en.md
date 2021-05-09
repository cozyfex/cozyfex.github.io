---
title: "Kubernetes Imperative vs Declarative"

date: 2021-05-09T15:51:31+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- object
- imperative
- declarative

keywords:

- kubernetes
- object
- imperative
- declarative

---

## Two ways to manage Objects in Kubernetes

{{<admonition note Imperative true>}}  
Manage with Commands  
You need to aware current state always.  
{{</admonition>}}

{{<admonition note Declarative true>}}  
Manage with Files  
{{</admonition>}}

## Imperative

### Commands

#### Create Objects

```shell
# Create a POD and Run
kubectl run --image=nginx nginx

# Create a Deployment
kubectl create deployment --image nginx nginx

# Expose and Create a Deployment
kubectl expose deployment nginx --port=80
```

#### Update Objects

```shell
# Edit Deployment
kubectl edit deployment nginx

# Scale Deployment
kubectl scale deployment nginx --replicas=5

# Update Image Deployment
kubectl set image deployment nginx nginx=nginx:1.18
```

#### Commands with YAML

```shell
# Create with YAML
kubectl create -f nginx.yml

# Replace with YAML
kubectl replace --force -f nginx.yml

# Delete with YAML
kubectl delete -f nginx.yml
```

## Declarative

### Commands

{{<admonition note "kubectl apply" true>}}  
`apply` is figuring out the objects exist automatically.  
{{</admonition>}}

```shell
# Apply a file
kubectl apply -f nginx.yml

# Apply files
kubectl apply -f /path/to/files
```

## Common Command Options

### --dry-run

{{<admonition note "--dry-run" true>}}  
`--dry-run` is for testing your command.  
`--dry-run=client` is to execute the command without create objects.  
{{</admonition>}}

### -o yaml

{{<admonition note "-o yaml" true>}}  
`-o yaml` will output the resource definition in YAML format on the screen.  
{{</admonition>}}

## Example Commands

### POD

```shell
# Create a NGINX POD
kubectl run nginx --image=nginx

# Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
kubectl run nginx --image=nginx  --dry-run=client -o yaml
```

### Deployment

```shell
# Create a Deployment
kubectl create deployment --image=nginx nginx

# Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml

# Generate Deployment with 4 Replicas
kubectl create deployment nginx --image=nginx --replicas=4

# You can also scale a deployment using the kubectl scale command.
kubectl scale deployment nginx --replicas=4

# Another way to do this is to save the YAML definition to a file.
kubectl create deployment nginx --image=nginx--dry-run=client -o yaml > nginx-deployment.yaml
```

### Service

```shell
# Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
# (This will automatically use the pod's labels as selectors)

# Or
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
# (This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

# Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:
kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml
# (This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

# Or
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml
# (This will not use the pods labels as selectors)
```

{{<admonition note "Note" true>}} Both the above commands have their own challenges. While one of it cannot accept a
selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to
specify a node port, generate a definition file using the same command and manually input the nodeport before creating
the service.  
{{</admonition>}}