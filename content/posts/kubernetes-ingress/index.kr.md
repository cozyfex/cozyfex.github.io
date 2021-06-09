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
외부에서 클러스터의 서비스들로 접근을 관리하는 API 객체이다. 일반적으로 HTTP 이다.  
Ingress 는 로드밸렁싱, SSL 종료, 이름 기반 가상 호스팅을 제공할 수 있다.  
{{</admonition>}}

{{<image src="ingress-wear-video-directory.png" width="100%">}}

## Ingress Layers

### Deploy - Ingress Controller

{{<admonition note Ingress true>}}  
Ingress 는 기본적으로 배포되지 않는다.  
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

### Namespace 생성

```shell
kubectl create namespace ingress-space
```

### ConfigMap 생성

```shell
kubectl -n ingress-space create cm nginx-configuration
```

### Service Account 생성

```shell
kubectl -n ingress-space create sa ingress-serviceaccount
```

### Role 과 RoleBinding 확인

```shell
kubectl -n ingress-space get role,rolebinding
```

### Deployment 생성

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

### Service 생성

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

### Ingress 생성

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