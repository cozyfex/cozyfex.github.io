---
title: "Kubernetes Services"

date: 2021-05-09T14:20:07+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- services

keywords:

- kubernetes
- services

---

## What is the Services in the Kubernetes?

The Services are ways to communicate users to frontend, frontend to backend, and backend to extra datasource.  
The Services enable loose coupling in between microservices in application.  
The Services are in the `labels`/`selector`
matched Nodes and so you can access to the Nodes with configured Port.  
The Service that is in the Node can access `labels`/`selector` matched PODs with the configured Port in the Node.

## Services Types

{{<admonition note NodePort true>}}  
The Service makes POD access to other PODs on the Node.  
{{</admonition>}}

{{<admonition note ClusterIP true>}}  
The Service create virtual IP inside cluster to enable communication between difference services such as frontend server
to backend server.  
The cluster is a group of PODs that are created by same configuration(YAML or commands, replicas).  
{{</admonition>}}

{{<admonition note LoadBalancer true>}}  
Support Load Balance in Application.  
{{</admonition>}}

## NodePort

{{<admonition note TargetPort true>}}  
The Target is the POD.  
The TargetPort is a port of the POD.  
The POD is in the Node.  
The Service Target Port.  
{{</admonition>}}

{{<admonition note Port true>}}  
The Service is in the Node.  
The Service's port to go to the target port.  
{{</admonition>}}

{{<admonition note NodePort true>}}  
The NodePort is a port of the Node.  
The ranges are 30000-32767.  
{{</admonition>}}

### YAML

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
    - name: nginx-container
      image: nginx
```

<sub>service-definition.yml</sub>

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector: # -> This is labels from pod-definition.yml
    app: myapp
    type: front-end
```

### Commands

#### Create a Service

```shell
kubectl create -f service-definition.yml
```

#### Show Service List

```shell
kubectl get services
```

## ClusterIP

### YAML

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
    - name: nginx-container
      image: nginx
```

<sub>service-definition.yml</sub>

```yaml
apiVersion: v1
kind: Service
metadata:
  name: back-end
spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  selector: # -> This is labels from pod-definition.yml
    app: myapp
    type: front-end
```

### Commands

#### Create a Service

```shell
kubectl create -f service-definition.yml
```

#### Show Service List

```shell
kubectl get services
```

## LoadBalancer

### YAML

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
    - name: nginx-container
      image: nginx
```

<sub>service-definition.yml</sub>

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer # -> When you use Cloud Services as Google, AWS and Azure, that's it!
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector: # -> This is labels from pod-definition.yml
    app: myapp
    type: front-end
```

### Commands

#### Create a Service

```shell
kubectl create -f service-definition.yml
```

#### Show Service List

```shell
kubectl get services
```

## Commands

### Show Service List

```shell
kubectl get services
```

### Show Service Detail

```shell
kubectl describe service myapp-service
```
