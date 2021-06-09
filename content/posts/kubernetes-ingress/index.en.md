---
title: "Kubernetes Ingress"

date: 2021-05-31T19:21:59+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- cluster
- ingress

tags:

- kubernetes
- network
- cni
- cluster
- ingress

keywords:

- kubernetes
- network
- cni
- cluster
- ingress

---

## Ingress

{{<admonition note Ingress true>}}  
An API object that manages external access to the services in a cluster, typically HTTP.  
Ingress may provide load balancing, SSL termination and name-based virtual hosting.  
{{</admonition>}}

{{<image src="ingress-wear-video-directory.png" width="100%">}}

## Ingress Layers

### Deploy - Ingress Controller

{{<admonition note Ingress true>}}  
It's NOT deployed by default.  
{{</admonition>}}

#### Ingress Controller

| Product | Description |  
|:-:|:-:|  
| GCP HTTP(S) Load Balancer(GCE) | Support Kubernetes |  
| NGINX | Support Kubernetes |  
| Contour | - |  
| HAPROXY | - |  
| traefic | - |  
| Istio | - |

#### Deployment

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-ingress-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      name: nginx-ingress
  template:
    metadata:
      labels:
        name: nginx-ingress
    spec:
      containers:
        - name: nginx-ingress-controller
          image: nginx/nginx-ingress
      args:
        - /nginx-ingress-controller
        - --configmap=$(POD_NAMESPACE)/nginx-configuration
      env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          vlueFrom:
            fieldRef:
              fieldPath: metadata.namespace
      ports:
        - name: http
          containerPort: 80
        - name: https
          containerPort: 443
```

#### ConfigMap

- err-log-path
- keep-alive
- ssl-protocols

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configuration
```

#### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-ingress
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
      name: http
    - port: 443
      targetPort: 443
      protocol: TCP
      name: https
  selector:
    name: nginx-ingress
```

#### Auth - Service Account

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nginx-ingress-serviceaccount
```

{{<mermaid>}}

graph TD;  
R(Roles)  
CR(ClusterRoles)  
RB(RoleBindings)

{{</mermaid>}}

### Configure - Ingress Resources

#### Sub Directory

##### `ingress-wear.yaml`

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear
spec:
  backend:
    serviceName: wear-service
    servicePort: 80
```

```shell
kubectl create -f ingress-wear.yaml
```

```shell
kubectl get ingress
```

##### `ingress-wear-video-directory.yaml`

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear-video-directory
spec:
  rules:
    - http:
        paths:
          - path: /wear
            backend:
              serviceName: wear-service
              servicePort: 80
          - path: /video
            backend:
              serviceName: video-service
              servicePort: 80

```

```shell
kubectl create -f ingress-wear-video-directory.yaml
```

```shell
kubectl describe ingress ingress-wear-video-directory
```

#### Sub Domain

##### `ingress-wear-video-domain.yaml`

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear-video-domain
spec:
  rules:
    - host: wear.my-store.com
      http:
        paths:
          - backend:
              serviceName: wear-service
              servicePort: 80
    - host: video.my-store.com
      http:
        paths:
          - backend:
              serviceName: video-service
              servicePort: 80
```

#### Rewrite

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: rewrite-ingress
  namespace: critical-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - http:
        paths:
          - path: /pay
            pathType: Prefix
            backend:
              service:
                name: pay-service
                port:
                  number: 8282
```

## DEMO

### Create a Namespace

```shell
kubectl create namespace ingress-space
```

### Create a ConfigMap

```shell
kubectl -n ingress-space create cm nginx-configuration
```

### Create a Service Account

```shell
kubectl -n ingress-space create sa ingress-serviceaccount
```

### Check Role and RoleBinding

```shell
kubectl -n ingress-space get role,rolebinding
```

### Create Deployment

<sub>ingress-controller.yaml</sub>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-controller
  namespace: ingress-space
spec:
  replicas: 1
  selector:
    matchLabels:
      name: nginx-ingress
  template:
    metadata:
      labels:
        name: nginx-ingress
    spec:
      serviceAccountName: ingress-serviceaccount
      containers:
        - name: nginx-ingress-controller
          image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.21.0
          args:
            - /nginx-ingress-controller
            - --configmap=$(POD_NAMESPACE)/nginx-configuration
            - --default-backend-service=app-space/default-http-backend
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          ports:
            - name: http
              containerPort: 80
            - name: https
              containerPort: 443
```

### Create a Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ingress
  namespace: ingress-space
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
      nodePort: 30080
      name: http
    - port: 443
      targetPort: 443
      protocol: TCP
      name: https
  selector:
    name: nginx-ingress
```

### Create Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-wear-watch
  namespace: ingress-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
    - http:
        paths:
          - path: /wear
            pathType: Prefix
            backend:
              service:
                name: wear-service
                port:
                  number: 8080
          - path: /watch
            pathType: Prefix
            backend:
              service:
                name: video-service
                port:
                  number: 8080
```